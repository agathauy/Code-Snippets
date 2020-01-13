- [See Cent OS version](#see-cent-os-version)
- [INITIAL SETUP FOR SETTING UP INSTANCES](#initial-setup-for-setting-up-instances)
  - [Configure locale](#configure-locale)
  - [Configure private and public keys](#configure-private-and-public-keys)
  - [iptables config](#iptables-config)
- [If there's permission error with accessing upstream server with nginx](#if-theres-permission-error-with-accessing-upstream-server-with-nginx)
- [PostgreSQL 10 Installation on Cent OS](#postgresql-10-installation-on-cent-os)
  - [Others](#others)
- [Install PostGIS](#install-postgis)
  - [Create Roles](#create-roles)
  - [Create DB](#create-db)
  - [To add postgis to DB](#to-add-postgis-to-db)
- [Configure Postgres to accept login from users aside from postgres](#configure-postgres-to-accept-login-from-users-aside-from-postgres)
- [Node JS install on Cent OS](#node-js-install-on-cent-os)
- [Python 3 and Pip install on Cent OS](#python-3-and-pip-install-on-cent-os)
  - [For installing python 2](#for-installing-python-2)
  - [For installing python 3](#for-installing-python-3)
  - [Creating virtualenv](#creating-virtualenv)
- [Install Git](#install-git)
- [Install Nginx](#install-nginx)
  - [Setting up nginx server block configs](#setting-up-nginx-server-block-configs)
  - [Let's Encrypt Certbot config](#lets-encrypt-certbot-config)
- [Node JS addtl configs](#node-js-addtl-configs)
- [Redis](#redis)

# See Cent OS version
```
cat /etc/os-release

```

# INITIAL SETUP FOR SETTING UP INSTANCES

## Configure locale
```
 sudo nano /etc/locale.conf
```
Change the contents to the ff because the initial config is invalid
```
LANG="en_US.utf8"
```
Check if it is changed
```
localectl status
```

If above still won't work somehow
Do this in ~/.bash_profile
```
export LC_ALL=en_US.utf8
export LANG=en_US.utf8
```

```
source ~/.bash_profile
```


## Configure private and public keys
https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos7


In client:
```
ssh-keygen -t rsa
cat ./example.pub | ssh username@ip_addr "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >>  ~/.ssh/authorized_keys"
```

In host server:
```
sudo nano /etc/ssh/sshd_config

# Edit the ff
PasswordAuthentication no
AllowUsers username
```

```
sudo systemctl reload sshd.service
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
```

If the the addng of new user authorized keys for ssh won't work, log in as root then repeat actions
```
sudo su
chmod 600 /home/temp_user/.ssh/authorized_keys
chmod 700 /home/temp_user/.ssh
chown -R temp_user:temp_user /home/temp_user/.ssh
```


## iptables config
https://www.linode.com/docs/security/firewalls/control-network-traffic-with-iptables/

See iptables chain
```
sudo iptables -L
```


For below configuration, ssh was rerouted to 40022 instead of 22.
Changed below on `/etc/ssh/sshd_config`
```

# If you want to change the port on a SELinux system, you have to tell
# SELinux about this change.
# semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
Protocol 2
Port 40022
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::
```

Used on the servers
`/etc/sysconfig/iptables`
```
# IPtables config for baremetal servers
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]

##Accept pings
-A INPUT -p icmp --icmp-type echo-request -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p icmp --icmp-type echo-reply -j ACCEPT
-A INPUT -p icmp --icmp-type destination-unreachable -j ACCEPT
-A INPUT -p icmp --icmp-type time-exceeded -j ACCEPT

##Reject known local networks
-A INPUT -i eth0 -s 172.16.0.0/12 -j LOG --log-prefix "IP DROP SPOOF B: "
-A INPUT -i eth0 -s 192.168.0.0/16 -j LOG --log-prefix "IP DROP SPOOF C: "
-A INPUT -i eth0 -s 224.0.0.0/4 -j LOG --log-prefix "IP DROP MULTICAST D: "
-A INPUT -i eth0 -s 240.0.0.0/5 -j LOG --log-prefix "IP DROP SPOOF E: "
-A INPUT -i eth0 -d 127.0.0.0/8 -j LOG --log-prefix "IP DROP LOOPBACK: "

## Allow HTTP and HTTPS connections from anywhere
-A INPUT -p tcp --dport 80 -m state --state NEW -j ACCEPT
-A INPUT -p tcp --dport 443 -m state --state NEW -j ACCEPT

## Allow remote access for postgresql port
-A INPUT -p tcp --dport 5432 -m state --state NEW -j ACCEPT

##Accept any established connections
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

##Accept SSH
-A INPUT -m state --state NEW -m tcp -p tcp --dport 40022 -j ACCEPT

# Log what was incoming but denied (optional but useful).
#-A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables_INPUT_denied: " --log-level 7


# Reject inbound and forwarding
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
-A INPUT -j LOG
-A INPUT -j DROP
COMMIT


```

After editing iptable
```
systemctl restart sshd
systemctl restart iptables
systemctl enable iptables
```


# If there's permission error with accessing upstream server with nginx
https://stackoverflow.com/questions/23948527/13-permission-denied-while-connecting-to-upstreamnginx
```
getsebool -a | grep httpd
setsebool httpd_can_network_connect on -P

```


# PostgreSQL 10 Installation on Cent OS
https://tecadmin.net/install-postgresql-server-centos/
```
## CentOS/RHEL - 7
rpm -Uvh https://yum.postgresql.org/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm

# Install PostgreSQL 10 Server
yum install postgresql10-server postgresql10 postgresql10-contrib

# Initialize PGDATA
/usr/pgsql-10/bin/postgresql-10-setup initdb

# For CentOS/RHEL 7 and Fedora

systemctl start postgresql-10.service
systemctl enable postgresql-10.service

# In Linux by default, a user named postgres is created once PostgreSQL is installed.
# Set password for the user just in case
sudo passwd postgres

# Verify PostgreSQL Installation
su - postgres -c "psql"

# You may create a password for user postgres for security purpose.
postgres=# \password postgres

```
## Others
```
# Install netstat
yum whatprovides netstat
yum -y install net-tools


# Confirm the PostgreSQL listening on port 5432 using netstat command.
netstat -antup | grep 5432

```


# Install PostGIS
```
# Add for dependencies
sudo yum -y install epel-release

# Cent OS Add repository
# https://yum.postgresql.org/repopackages.php#pg10
sudo yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# Install postgis based on repository
# installs for psql 10, postgis version 2.5
yum install postgis25_10

```


## Create Roles
https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2

https://www.codementor.io/engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb
```
sudo su - postgres
psql

# Get list of roles
\du

# Create roles
CREATE ROLE temp_user;
# CREATE ROLE username WITH LOGIN PASSWORD 'quoted password' [OPTIONS]
\password <username>

# Alter role to create db
ALTER ROLE temp_user CREATEDB; 
ALTER ROLE temp_user SUPERUSER;
ALTER ROLE temp_user WITH LOGIN;


```

## Create DB
```
CREATE DATABASE temp_database;

GRANT ALL PRIVILEGES ON DATABASE temp_database TO temp_user;
\list
\connect temp_database
\dt 
```


## To add postgis to DB
```

# Add extension
CREATE EXTENSION postgis;

# Find postgis version
SELECT PostGIS_version();
```

# Configure Postgres to accept login from users aside from postgres
While inside psql, enter following to find hba_file
```
show hba_file ;
```

Edit the conf file and edit the following from `ident` to `md5`
```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Restart the service
```
sudo systemctl restart postgresql-10.service

```


# Node JS install on Cent OS
https://linuxize.com/post/how-to-install-node-js-on-centos-7/
```
sudo yum install gcc-c++ make
curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -
sudo yum install nodejs
node --version
```

# Python 3 and Pip install on Cent OS
## For installing python 2
```
# If not yet installed
# sudo yum -y install epel-release

sudo yum -y install python-virtualenv
sudo yum -y install python-virtualenvwrapper
sudo yum -y install python-pip


# The first step is to define the location of your virtual environments and load virtualenvwrapper.
export WORKON_HOME=~/.virtualenvs
source /usr/bin/virtualenvwrapper.sh
```

## For installing python 3
https://www.rosehosting.com/blog/how-to-install-python-3-6-4-on-centos-7/
```
sudo yum install -y https://centos7.iuscommunity.org/ius-release.rpm
sudo yum update
sudo yum install -y python36u python36u-libs python36u-devel python36u-pip
python3.6 -V

```

## Creating virtualenv
```
mkvirtualenv -p python3.6 temp_venv
deactivate
workon temp_venv

```

Other useful virtualenvwrapper commands include:

lsvirtualenv - List available virtual environments.
cdvirtualenv - Change your current directory into the activated virtual environment.
rmvirtualenv - Remove a virtual environment.

# Install Git
```
yum install git

```

# Install Nginx
https://www.cyberciti.biz/faq/how-to-install-and-use-nginx-on-centos-7-rhel-7/
```
# Create file 
sudo nano /etc/yum.repos.d/nginx.repo

```

Add the ff:
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
gpgcheck=0
enabled=1
```

```
sudo yum update
sudo yum install nginx

```

```
sudo systemctl start nginx
# sudo systemctl stop nginx
# sudo systemctl restart nginx
sudo systemctl status nginx
```

https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-on-centos-7
```
sudo mkdir /etc/nginx/sites-available
sudo mkdir /etc/nginx/sites-enabled
```


Edit the following file:
```
sudo nano /etc/nginx/nginx.conf
```

Add these lines to the end of the http {} block:
```
include /etc/nginx/sites-enabled/*.conf;
server_names_hash_bucket_size 64;
```

```
sudo cp /etc/nginx/conf.d/default.conf /etc/nginx/sites-available/example.com.conf
```

Example config api.temp-server.com.conf
```
server {
    listen 80;
    server_name api.temp-server.com;

    location / {
        proxy_pass http://localhost:3002;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
     }


}
```

## Setting up nginx server block configs
```
sudo ln -s /etc/nginx/sites-available/api.temp-server.com.conf /etc/nginx/sites-enabled/api.temp-server.com.conf
sudo systemctl reload nginx
```
## Let's Encrypt Certbot config
https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-centos-7
```
sudo yum install certbot python2-certbot-nginx mod_ssl
```

Adding certificate
```
sudo certbot --nginx -d api.temp-server.com
```

# Node JS addtl configs
```
sudo npm install -g pm2
```

# Redis
https://www.linode.com/docs/databases/redis/install-and-configure-redis-on-centos-7/

```
sudo yum install redis
sudo systemctl start redis
sudo systemctl enable redis

```
