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

## Установка и настройка Nginx

1. Установка и настройка Nginx

        $ sudo apt-get update
        $ sudo apt-get install nginx
