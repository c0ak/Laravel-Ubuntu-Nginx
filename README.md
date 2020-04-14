# Laravel-Ubuntu-Nginx

How to setup Laravel on Ubuntu using Nginx engine with a MYSQL database.

## Getting Started 
```
sudo apt-get update && sudo apt-get upgrade && sudo apt-get install nginx php7.2-fpm php7.2-mbstring php7.2-xmlrpc php7.2-soap php7.2-gd php7.2-xml php7.2-cli php7.2-zip php7.2-mysql
```

## Edit PHP Config
```
sudo nano /etc/php/7.2/fpm/php.ini
```
```
memory_limit = 256M
upload_max_filesize = 64M
cgi.fix_pathinfo=0
```

## Install Composer
```
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

## Permissions
```
... Move your project to /var/www/html/{project_name}
```
```
sudo chown -R www-data:www-data /var/www/html/{project_name}/
sudo chmod -R 755 /var/www/html/{project_name}/
```

## Nginx Config
```
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/laravel
sudo nano /etc/nginx/sites-available/laravel
```
```
server {
    listen 80;
    listen [::]:80;
    root /var/www/html/{project_name}/public;
    index  index.php index.html index.htm;
    server_name  example.com;

    location / {
        try_files $uri $uri/ /index.php?$query_string;        
    }

  
    location ~ \.php$ {
       include snippets/fastcgi-php.conf;
       fastcgi_pass             unix:/var/run/php/php7.2-fpm.sock;
       fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

}
```
```
sudo ln -s /etc/nginx/sites-available/laravel /etc/nginx/sites-enabled/ 
sudo rm /etc/nginx/sites-enabled/default
sudo systemctl restart nginx.service
```
## Setting up the database
```
sudo apt install mysql-server
sudo mysql_secure_installation
```
```
Password Plugin: Y
Password Validation Policy: 1 (can be 2 if you want)
(Enter Password and save somewhere)
Continue with Password: Y
Remove Anonymous Users: Y
Disallow Root Login Remotely: N
Remote Test DB: Y
Reload Privileges: Y
```
```
sudo mysql -u root -p
CREATE DATABASE db_name;
GRANT ALL ON db_name.* to 'db_user'@'localhost' IDENTIFIED BY 'db_password';
FLUSH PRIVILEGES;
quit
```

## Project
```
cd /var/www/html/{project_name}
sudo composer install
sudo composer update
mv .env.example .env
nano .env
// ^ Enter Databse Credentials
php artisan config:cache
php artisan key:generate
php artisan migrate:fresh --seed
```
