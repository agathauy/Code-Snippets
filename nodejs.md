# PM2
```
pm2 start npm --name "insert-name" -- start
```


# Upon EACCESS during npm install -g
```
ls -la /usr/local/lib/node_modules
whoami

sudo chown -R [owner]:[owner] /usr/local/lib/node_modules

OR

sudo chown -R ownerName: /usr/local/lib/node_modules

OR

sudo chown -R $USER /usr/local/lib/node_modules

```