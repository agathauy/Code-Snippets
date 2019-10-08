# Nginx configs

## Example conf file in /etc/nginx/sites-avaliable

```

server {
    listen 80 default_server;
    server_name <insert server name>;  

    #access_log  /var/log/nginx/access.log ;
    #error_log /var/log/nginx/error.log debug;



 location / {
        proxy_pass http://localhost:<INSERT PORT NUMBER>;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
     }


}

```

## Symlink of conf file
```
sudo ln -s /etc/nginx/sites-available/test_sever /etc/nginx/sites-enabled/test_server
sudo service nginx restart
# or sudo systemctl reload nginx

```

## Other nginx
```
# verify nginx edits
sudo nginx -t

```

## UFW config
Below if to only allow https traffic 
```
sudo ufw status
# possibly for https traffic
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
```

## Let's Encrypt Certbot config
Adding to instance
- This package is said to renew cert twice a day, still need to check if need cron job as before it's needed
```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx 
```

Adding certificate
```
sudo certbot --nginx -d example.com -d www.example.com
```

Test renewal
```
sudo certbot renew --dry-run
```
