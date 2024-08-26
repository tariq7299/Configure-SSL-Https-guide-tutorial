**Configure DNS**  
This will make 
|  Type | Name | Priority  | Content| TTL  |
|---|---|---|---|---|
| A | olive.com.sa | 0  | 153.92.223.86 *your VPS server ip*  | 14400  |



**Isuuing a free SSL certificate using `zerossl.com`**

https://zerossl.com/

login: admin@olive.com.sa  
password: *Ask Admin*

Follow steps and choose free certificate

Choose a CSR automatic way and don't create it by yourself

Choose 90 days (the only free options)

Create `admin@olive.com.sa` from `https://hpanel.hostinger.com/` if not already created

Choose the verification method of sending email to hostinger `admin@olive.com.sa` 

Open up the email and then verify using from `https://hpanel.hostinger.com/`

Download the certificate on your personal device

*Create the following dirs to store the certifiactes*
`sudo mkdir -p /etc/ssl/olive.com.sa/`

*From your personal device (the one you downloaded the certificate for) copy your certifiactes to ssl dir found in server*
`scp ca_bundle.crt certificate.crt private.key root@153.92.223.86:/etc/ssl/olive.com.sa/`

*Change some permissions*
sudo chown root:root /etc/ssl/olive.com.sa/*
sudo chmod 600 /etc/ssl/olive.com.sa/*

*Head to conf file dir*
`sudo vim /etc/nginx/conf.d/olive.com.sa.conf`

*An example of the `/etc/nginx/conf.d/olive.com.sa.conf` file*
```bash
server {
    # Listen to port 443 to accept https/ssl requests
    listen 443 ssl http2;
    # Your domain name
    server_name olive.com.sa www.olive.com.sa;

    # path of ssl/CA certificates
    ssl_certificate /etc/ssl/olive.com.sa/certificate.crt;
    ssl_certificate_key /etc/ssl/olive.com.sa/private.key;
    ssl_trusted_certificate /etc/ssl/olive.com.sa/ca_bundle.crt;

    # Some ssl bullshit
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    # This won't work because you already serving a wrodpress website 
    # root /var/www/html;
    # index index.html;

    # HSTS (HTTP Strict Transport Security) header
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name olive.com.sa www.olive.com.sa;
    return 301 https://$host$request_uri;
}
```
*Reload nginx*    
`nginx -t && nginx -s reload`