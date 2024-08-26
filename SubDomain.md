**Create a new subdomain**  
From hostiger admin panel `https://hpanel.hostinger.com/websites/olive.com.sa/domains/subdomains?redirectLocation=side_menu` open it up and then create a subdomain from `olive.com.sa` called `lawyer.com.sa` (you can skip assigning a custom folder in `public_html` as this is related to *wordpress*)

**Create an `A` record from `https://hpanel.hostinger.com/external-domain/olive.com.sa/dns` that will point to `153.92.223.86` (VPS server IP)**    

```bash
Type: A
Name: laywer.olive.com.sa #or lawyer
Points to: 153.92.223.86 #(same vps server ip you that also points to the parent domain of `olive.com.sa`)
TTL: 1800 #For example (Not very important)
```

*NOTE*: This will point to the same VPS server as `olive.com.sa` and that is completely fine ! as we want to `olice.com.sa` to serve our wordpress app and `lawyer.olive.com.sa` subdomain to serve our laywer flask API
*NOTE*: Sometimes after you create the `A` record `lawyer.olive.com.sa` wil not be assined ! so you have to check in the list of DNS servers if the new A has been added or not ! if not ! then add it again !
*NOTE*: Sometimes after you add the `A` record then you will not find `laywer.olive.com.sa` (or maybe the original domain of `olive.com.sa` is not working!) working ! and that is completey fine ! as you can wait up to 24h !

**Create ssl certificate**  
Go to https://zerossl.com/

login: admin@olive.com.sa
password: <zeroSslPass>

Follow steps and choose free certificate

Choose a CSR automatic way and don't create it by yourself

Choose 90 days (the only free options)

Choose DNS verification then do what it says 

Doqnlaod the ssl certificate zip file

**Create the following dirs to store the certifiactes**  
`sudo mkdir -p /etc/ssl/lawyer.olive.com.sa/`

**From your personal device (the one you downloaded the certificate for) copy your certifiactes to ssl dir found in server**   

`scp ca_bundle.crt certificate.crt private.key root@153.92.223.86:/etc/ssl/lawyer.olive.com.sa/`

**Change some permissions**  

```bash
sudo chown root:root /etc/ssl/lawyer.olive.com.sa/*
sudo chmod 600 /etc/ssl/lawyer.olive.com.sa/*
```

**Create a nginx configuration file**  
`sudo vim /etc/nginx/sites-available/lawyer.olive.com.sa`

```bash
server {
    # Listen to port 443 to accept https/ssl requests
    listen 443 ssl http2;
    # Your sudomain name
    server_name lawyer.olive.com.sa;

    # path of ssl/CA certificates

    # Same certifaicate is use in `etc/nginx/conf.d/olive.com.sa.conf`
    ssl_certificate /etc/ssl/lawyer.olive.com.sa/certificate.crt;
    ssl_certificate_key /etc/ssl/lawyer.olive.com.sa/private.key;
    ssl_trusted_certificate /etc/ssl/lawyer.olive.com.sa/ca_bundle.crt;

    # Some ssl bullshit
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://153.92.223.86:5001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # HSTS (HTTP Strict Transport Security) header
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name lawyer.olive.com.sa;
    return 301 https://$host$request_uri;
}
```
*NOTE*: That there is already a conf file found at `etc/nginx/conf.d/olive.com.sa.conf` that listents also to any request coming to the server but will only be activated if the user enter the a domain of name `olive.com.sa` or `www.olive.com.sa` and the file we just created at `/etc/nginx/sites-available/lawyer.olive.com.sa` will be activated if the user enters `lawyer.olive.com.sa` at the search bar ! so as you can both see that two nginx conf file listen to any request coming to `153.92.223.86` but only will be activated depeding on `server_name`

**Create symbolic link of the conf file you just created at `/etc/nginx/sites-available/`**    
sudo ln -s /etc/nginx/sites-available/lawyer.olive.com.sa /etc/nginx/sites-enabled/lawyer.olive.com.sa

**Reload nginx**  
`nginx -t && nginx -s reload`

