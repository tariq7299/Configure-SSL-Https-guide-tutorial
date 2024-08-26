# Some common testing commands

## certbot commands
sudo certbot --nginx --staging -d olive.com.sa -d www.olive.com.sa
sudo certbot --nginx --staging -v -d olive.com.sa -d www.olive.com.sa
certbot certonly --webroot -w /var/www/certbot -d olive.com.sa -d www.olive.com.sa

## nginx stuff
sudo vim /etc/nginx/sites-available/olive.com.sa
sudo nginx -t;sudo systemctl reload nginx
sudo systemctl restart nginx
nginx -t && nginx -s reload
ls -la /etc/nginx/conf.d

## Acme challange configuration commands
mkdir -p /var/www/certbot/.well-known/acme-challenge/
sudo chmod -R 777 /var/www/certbot/.well-known/acme-challenge/
sudo chown -R www-data:www-data /var/www/certbot/.well-known/acme-challenge/
ls -la /var/www/olive.com.sa/.well-known/acme-challenge/
mkdir -p /var/www/html/.well-known/acme-challenge/
sudo chmod -R 777 /var/www/html/.well-known/acme-challenge/
sudo chown -R www-data:www-data /var/www/html/.well-known/acme-challenge/

## Testing Web app commnds
**To test try sending get requests to:**  
https://lawyer.olive.com.sa/api/admin/get_users/1
https://lawyer.olive.com.sa/api/superadmin/get_users
https://153.92.223.86/api/admin/get_users/1
https://153.92.223.86/api/superadmin/get_users
