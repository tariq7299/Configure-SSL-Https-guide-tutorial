
ssh root@153.92.223.86

123456789@Olive

sudo apt-get update

sudo apt-get install certbot python3-certbot-nginx

<!-- Make sure nginx is installed and running -->
nginx -v
sudo systemctl status nginx


<!-- Check if configuration file exists and running -->
ls -al /etc/nginx/sites-available/default

<!-- Check if server has domain and it is accessble by browser -->
nslookup olive.com.sa
<!-- or -->
www.olive.com.sa

<!-- Output -->
```bash
Server:		192.168.1.1
Address:	192.168.1.1#53

Non-authoritative answer:
Name:	olive.com.sa
Address: 153.92.223.86
```


<!-- Try to acces it in url directly -->
not found in browser



<!--  -->

sudo vim /etc/nginx/sites-available/olive.com.sa
```bash
    upstream flask-backend {
        server 153.92.223.86:5001;
    }

    server {
        listen 443 ssl http2;
        server_name www.olive.com.sa olive.com.sa;

        location / {
            proxy_pass http://flask-backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

    server {
        listen 80;
        server_name www.olive.com.sa olive.com.sa;
        return 301 https://$server_name$request_uri;
    }
```
```bash
   server {
    listen 80;
    server_name olive.com.sa www.olive.com.sa;

    root /var/www/olive.com.sa;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
```bash
server {
    listen 80;
    server_name olive.com.sa www.olive.com.sa;

        location /.well-known/acme-challenge/ {
            allow all;
            auth_basic off;
            alias /var/www/olive.com.sa/;
            try_files $uri =404;
        }

    root /var/www/olive.com.sa;
    index index.html;

}
```

```bash
server {
    listen 80;
    server_name olive.com.sa www.olive.com.sa;
    

    # return 301 https://$server_name$request_uri;
}
```
```bash
server {

    listen 443 ssl http2;
    server_name olive.com.sa www.olive.com.sa;

    ssl_certificate /etc/ssl/olive.com.sa/certificate.crt;
    ssl_certificate_key /etc/ssl/olive.com.sa/private.key;
    ssl_trusted_certificate /etc/ssl/olive.com.sa/ca_bundle.crt;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    add_header Strict-Transport-Security "max-age=31536000" always;

    root /var/www/olive.com.sa;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

     location / {
        proxy_pass http://153.92.223.86:5001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Optional: add HSTS (HTTP Strict Transport Security) header
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Optional: redirect HTTP to HTTPS
    if ($scheme != "https") {
        return 301 https://$host$request_uri;
    }
}
```

```bash
server {
    listen 443 ssl;
    server_name olive.com.sa;

    ssl_certificate /etc/ssl/olive.com.sa/certificate.crt;
    ssl_certificate_key /etc/ssl/olive.com.sa/private.key;
    ssl_trusted_certificate /etc/ssl/olive.com.sa/ca_bundle.crt;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://your_backend_server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Optional: add HSTS (HTTP Strict Transport Security) header
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Optional: redirect HTTP to HTTPS
    if ($scheme != "https") {
        return 301 https://$host$request_uri;
    }
}
```
```bash

server {
        listen 80 default_server;
    listen [::]:80 default_server;
    root /var/www/html;
    index index.html
    server_name olive.com.sa;
    
   location /.well-known/acme-challenge/ 
   {
            allow all;
            auth_basic off;
            alias /var/www/html;
            try_files $uri =404;
            root /var/www/html
        }
}
```

sudo mkdir -p /var/www/olive.com.sa

sudo nano /var/www/olive.com.sa/index.html

<!DOCTYPE html>
<html>
<head>
    <title>Welcome to olive.com.sa</title>
</head>
<body>
    <h1>Welcome to olive.com.sa</h1>
    <p>This site is working!</p>
</body>
</html>

sudo chown -R www-data:www-data /var/www/olive.com.sa
sudo chmod -R 755 /var/www/olive.com.sa

sudo ln -s /etc/nginx/sites-available/olive.com.sa /etc/nginx/sites-enabled/

sudo nginx -t

sudo systemctl reload nginx

/var/www/html/index.html

sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html


sudo apt update
sudo apt install python3 python3-venv libaugeas0

<!-- Remove certpot -->
sudo apt-get remove certbot
sudo apt-get purge certbot
sudo apt install certbot python3-certbot-nginx

sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip

sudo /opt/certbot/bin/pip install certbot certbot-nginx

<!-- Next, create a symbolic link so that Certbot can be executed from any path: -->
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot


source ~/.bashrc

sudo certbot --nginx


echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo certbot renew -q" | sudo tee -a /etc/crontab > /dev/null




# TESTING


sudo certbot --nginx --staging -d olive.com.sa -d www.olive.com.sa
sudo certbot --nginx --staging -v -d olive.com.sa -d www.olive.com.sa
sudo vim /etc/nginx/sites-available/olive.com.sa
sudo nginx -t;sudo systemctl reload nginx
certbot certonly --webroot -w /var/www/certbot -d olive.com.sa -d www.olive.com.sa
mkdir -p /var/www/certbot/.well-known/acme-challenge/
sudo chmod -R 777 /var/www/certbot/.well-known/acme-challenge/
sudo chown -R www-data:www-data /var/www/certbot/.well-known/acme-challenge/
sudo systemctl restart nginx
ls -la /var/www/olive.com.sa/.well-known/acme-challenge/
mkdir -p /var/www/html/.well-known/acme-challenge/
sudo chmod -R 777 /var/www/html/.well-known/acme-challenge/
sudo chown -R www-data:www-data /var/www/html/.well-known/acme-challenge/
nginx -t && nginx -s reload
ls -la /etc/nginx/conf.d

sudo tail -f /var/log/nginx/error.log
Fayez.mco@gmail.com
Fayez$53

https://153.92.223.86/api/admin/get_users/1
https://153.92.223.86/api/superadmin/get_users


# Prolbem
```bash
root@srv502591:/home/lawyer_app_backend# sudo certbot --nginx --staging -d olive.com.sa -d www.olive.com.sa
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Requesting a certificate for olive.com.sa and www.olive.com.sa

Certbot failed to authenticate some domains (authenticator: nginx). The Certificate Authority reported these problems:
  Domain: olive.com.sa
  Type:   unauthorized
  Detail: 2a02:4780:1d:c695:c574:a037:b392:74c7: Invalid response from http://olive.com.sa/.well-known/acme-challenge/T19OtDTw-JbaoNC9BF9OOhc3M8r24wZHYLpuI3uj1N8: 404

  Domain: www.olive.com.sa
  Type:   unauthorized
  Detail: 2a02:4780:1d:903d:7873:ce7f:783d:eff: Invalid response from http://www.olive.com.sa/.well-known/acme-challenge/gTnc7r2Y6dCQOz1SaVBuywwWa7z65hrI8OjKjCHfbgU: 404

Hint: The Certificate Authority failed to verify the temporary nginx configuration changes made by Certbot. Ensure the listed domains point to this nginx server and that it is accessible from the internet.

Some challenges have failed.
Ask for help or search for solutions at https://community.letsencrypt.org. See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
root@srv502591:/home/lawyer_app_backend# 

```

## Solution 1
    - Make everyone read write and make all permission
    -sudo chmod -R 777 /var/www/html 
## SOlution 2
    - location /.well-known/acme-challenge/ {
    allow all;
    root /var/www/html;
}

Example: If a request comes in for http://yourdomain.com/.well-known/acme-challenge/somefilename, Nginx will look for the file at /var/www/html/.well-known/acme-challenge/somefilename.




# Remove nginx completey
sudo systemctl stop nginx
sudo apt remove nginx nginx-common nginx-full
sudo apt purge nginx nginx-common nginx-full
sudo apt autoremove

sudo rm -rf /etc/nginx
sudo rm -rf /var/log/nginx
sudo rm -rf /var/www/html

sudo apt update
sudo apt install nginx
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl enable nginx
