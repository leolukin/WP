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

1. Установка пакетов

        $ sudo apt-get install mysql-server 
