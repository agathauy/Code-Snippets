
# SSH

- [SSH](#ssh)
  - [SSH with keyfile](#ssh-with-keyfile)
  - [Configure private and public keys](#configure-private-and-public-keys)
  - [Set up ufw](#set-up-ufw)

## SSH with keyfile
```
ssh -i <keyfile> username@IPAddr
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

## Set up ufw
For Cent OS
```
sudo yum install epel-release -y
sudo yum install --enablerepo="epel" ufw -y
```


https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-18-04

```
sudo ufw enable
sudo ufw status verbose

# Disable
sudo ufw disable

# Reset
sudo ufw reset


sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
# sudo ufw allow 6379/tcp
# sudo ufw allow 5432/tcp
```
```
# Specific port ranges
sudo ufw allow 6000:6007/tcp
sudo ufw allow 6000:6007/udp

# Specific IP addresses
sudo ufw allow from 203.0.113.4
sudo ufw allow from 203.0.113.4 to any port 22

sudo ufw allow from 203.0.113.0/24

# Deny
sudo ufw deny http
sudo ufw deny from 203.0.113.4

# DElete
sudo ufw status numbered
sudo ufw delete 2

sudo ufw delete allow http

sudo ufw delete allow 80

```