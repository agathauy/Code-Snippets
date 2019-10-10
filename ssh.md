
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
sudo ufw enable
sudo ufw status verbose


sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
# sudo ufw allow 6379/tcp
# sudo ufw allow 5432/tcp
```
