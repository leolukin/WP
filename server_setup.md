https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04

https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-14-04

https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-nginx-on-ubuntu-14-04


# Настройка удаленного сервера

## Создание пользователя приложения

1. Заходим под root

        $ ssh root@ip
    
1. Создаем нового пользователя

        $ adduser username
       
1. Добавление пользователя в группу sudo

        $ gpasswd -a username sudo
        
1. Выход и вход от нового пользователя

        $ ssh username@ip

## Установка Nginx

1. Установка и запуск Nginx

        $ sudo apt-get update
        $ sudo apt-get install nginx

2. Проверить работоспособность Nginx в браузере по адресу http://ip

   Если на сервере был прежде запущен Apache2

        $ service apache2 status 
        $ service nginx status 
        $ sudo service apache2 stop
        $ service apache2 status 
        $ sudo service nginx start
        $ service nginx status 
        
## Установка MySQL

1. Установка пакетов, задание пароля для root

        $ sudo apt-get install mysql-server 
        $ sudo mysql_install_db
        $ sudo mysql_secure_installation

## Установка PHP (поддержки PHP в Nginx)

1. Установка последней версии PHP

        $ sudo apt-add-repository ppa:ondrej/php
        $ sudo apt-get update
        $ sudo apt-get install php7.1 php7.1-fpm php7.1-mysql -y

1. Фикс безопасности: "What we are looking for in this file is the parameter that sets cgi.fix_pathinfo. This will be commented out with a semi-colon (;) and set to "1" by default."        
        
        $ sudo nano /etc/php/7.1/fpm/php.ini
        $ sudo service php7.1-fpm restart
        
1. Создание папки проекта

        $ mkdir /home/username/www
        $ mkdir /home/username/www/domain.tld
        $ mkdir /home/username/www/domain.tld/html

1. Редактирование параметров основного сайта в Nginx и связка его с PHP

        $ sudo vim /etc/nginx/sites-available/default

    Содержимое файла

        server {
                listen 80 default_server;
                listen [::]:80 default_server ipv6only=on;

                root /usr/share/nginx/html;
                index index.php index.html index.htm;

                server_name server_domain_name_or_IP;
    
                if ($host != 'elvidigital.ru') {
                        rewrite ^/(.*)$ http://elvidigital.ru/$1 permanent;
                }


                location / {
                        try_files $uri $uri/ =404;
                }

                error_page 404 /404.html;
                error_page 500 502 503 504 /50x.html;
                location = /50x.html {
                        root /usr/share/nginx/html;
                }

                location ~ \.php$ {
                        try_files $uri =404;
                        fastcgi_split_path_info ^(.+\.php)(/.+)$;
                        fastcgi_pass unix:/var/run/php5-fpm.sock;
                        fastcgi_index index.php;
                        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                        include fastcgi_params;
                }
           }


1. Перезапуск Nginx

        $ sudo service nginx restart

        
