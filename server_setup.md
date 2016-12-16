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

        
        
