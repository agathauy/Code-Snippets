
## Adding and deleting users
https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-a-centos-7-server
https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-centos-quickstart

If you are signed in as the root user, you can create a new user at any time by typing:
```
adduser username
```
If you are signed in as a non-root user who has been given sudo privileges, as demonstrated in the next section of this tutorial, you can add a new user by typing:
```
sudo adduser username
```
Next, you’ll need to give your user a password so that they can log in. To do so, use the passwd command:
```
passwd username
```

Use the usermod command to add the user to the wheel group.
```
usermod -aG wheel username
```

Test sudo access on new user account
Use the su command to switch to the new user account.
```
su - username
```

Test sudo privilege:
```
sudo ls -la /root
```


