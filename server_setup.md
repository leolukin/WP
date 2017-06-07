# Настройка удаленного сервера

1. Заходим под root

        $ ssh root@ip
	
## Обновления
    $ apt-get -y update
    $ apt-get upgrade
    $ apt-get install build-essential
    $ apt-get -y install curl git-core python-software-properties

## Установка временной зоны
    $ dpkg-reconfigure tzdata
    $ apt-get install ntp
    $ ntpdate ntp.ubuntu.com
        
## Установка русской локали

1. Загрузка пака

        $ apt-get install language-pack-ru-base
	
2. В файл /var/lib/locales/supported.d/ru (vi /var/lib/locales/supported.d/ru, потом для выхода ESC -> :wq) добавляем (или проверяем наличие):

        ru_RU.UTF-8 UTF-8
	
3. Генерируем локали

        $ locale-gen

4. В файл /etc/environment (vim /etc/environment, потом для выхода ESC -> :wq) добавляем:

        LANG=ru_RU.UTF-8
        LC_ALL=ru_RU.UTF-8
	
5. Устанавливаем локали без перезагрузки сервера:

        $ export LANG=ru_RU.UTF-8
        $ export LC_ALL=ru_RU.UTF-8

## Настройка дополнительного (выделенного) IP адреса - если нужно!

1. После добавления дополнительного IP-адреса в панели управления ресурсами хостинга, необходимо отредактировать файл /etc/network/interfaces (добавляем в конец строки):

        auto eth0:0
        iface eth0:0 inet static
        address <требуемый айпи-адрес>
        netmask 255.255.0.0

1. После редактирования файла конфигурации необходимо выполнить команду:

        /etc/init.d/networking restart

## Создание пользователя приложения
    
1. Создаем нового пользователя

        $ adduser username
       
1. Добавление пользователя в группу sudo и www-data

        $ gpasswd -a username sudo
	$ adduser username www-data
        
1. Выход и вход от нового пользователя

        $ ssh username@ip

## Установка Nginx

1.    Если на сервере был прежде запущен Apache2

        $ sudo service apache2 status 
        $ sudo service apache2 stop
        $ sudo service apache2 status  

1. Установка и запуск Nginx (после проверки - выход CTRL + C)

        $ sudo apt-get install nginx
        $ sudo service nginx status 

2. Проверить работоспособность Nginx в браузере по адресу http://ip

        
## Установка MySQL

1. Установка пакетов, задание пароля для root

        $ sudo apt-get install mysql-server 
        $ sudo mysql_secure_installation

## Установка PHP (поддержки PHP в Nginx)

1. Установка последней версии PHP

        $ sudo apt-get install software-properties-common
        $ sudo apt-add-repository ppa:ondrej/php
        $ sudo apt-get update
        $ sudo apt-get install php7.1 php7.1-fpm php7.1-mysql -y

1. Фикс безопасности: "What we are looking for in this file is the parameter that sets cgi.fix_pathinfo. This will be commented out with a semi-colon (;) and set to "1" by default." и установка максимального размера памяти memory_limit (256MB для 2G памяти)      
        
        $ sudo vim /etc/php/7.1/fpm/php.ini
        $ sudo service php7.1-fpm restart
        
1. Создание папки проекта

        $ mkdir /home/username/www
        $ mkdir /home/username/www/domain.tld
        $ mkdir /home/username/www/domain.tld/app
        $ mkdir /home/username/www/domain.tld/app/public

1. Задание параметров основного сайта в Nginx и связка его с PHP (новый файл)

        $ sudo vim /etc/nginx/sites-enabled/domain.tld

    Содержимое файла

        server {
            listen 80 default_server;
            listen [::]:80 default_server ipv6only=on;

            root /home/username/www/domain.tld/app/public;
            index index.php index.html index.htm;
            
            server_name www.domain.tld domain.tld;

            if ($host != 'domain.tld') {
                rewrite ^/(.*)$ http://domain.tld/$1 permanent;
            }

            location / {
                try_files $uri $uri/ /index.php?q=$uri&$args;
            }
        
            error_page 404 /404.html;
            error_page 500 502 503 504 /50x.html;
            location = /50x.html {
                root /home/username/www/domain.tld/html;
            }

            location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass unix:/var/run/php/php7.1-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
            }

        }

1. Удаление дефолтного сервера Nginx и его перезапуск:

        $ sudo rm /etc/nginx/sites-enabled/default
        $ sudo service nginx restart

1. Создание тестового файла PHP (проверка в браузере http://domain.tld/domain-411.php)

        $ vim /home/username/www/domain.tld/app/public/domain-411.php

    Содержимое файла:
    
        <?php
        phpinfo();
        
## Создание MySQL базы данных

1. Вход в MySQL

        $ mysql -u root -p
        
1. Создание базы данных для пользователя

        $ CREATE DATABASE username;
        
1. Создание пользователя

        $ CREATE USER username@localhost IDENTIFIED BY 'password';
        
1. Задание прав для пользователя для базы

        $ GRANT ALL PRIVILEGES ON username.* TO username@localhost;
        $ FLUSH PRIVILEGES;
        $ exit;
        
## Создание удаленного репозитория на VPS

1.  Создать репозиторий

        $ cd /home/leonidlukinru/
        $ mkdir repo && cd repo
        $ mkdir leonidlukinru.git && cd leonidlukinru.git
        $ git init --bare
	
1. Создать хук (автоматическое копирование файлов проекта в рабочую папку после выкладки в репозиторий и задание прав - после ввода текст - 'control-d' для сохранения)

        $ cd hooks
        $ cat > post-receive

        #!/bin/sh
        git --work-tree=/home/leonidlukinru/www/leonidlukin.ru/app --git-dir=/home/leonidlukinru/repo/leonidlukinru.git checkout -f
        find /home/leonidlukinru/www/leonidlukin.ru/app/public/wp-content -type d -exec chmod 775 {} \;
        find /home/leonidlukinru/www/leonidlukin.ru/app/public/wp-content -type f -exec chmod 664 {} \;

1. Сделать этот файл исполняемым

        $ chmod +x post-receive
	
	
## Создание локального репозитория

1. Создать репозиторий на ginhub

1. Создать локальный депозиторий (в папке app)

        $ git init
	
1. Создать .gitignore с содержимым

        *.log
        public/wp-content/backups/
        public/wp-content/cache/
        public/wp-content/upgrade/
        public/wp-content/uploads/

        ehthumbs.db
        Thumbs.db
        *.sublime-project
        *.sublime-workspace
        .DS_Store
        .idea

1. Добавить файлы проекта в локальный репозиторий 

        $ git add .
        $ git status
        $ git commit -m "first commit"
	
1. Добавить удаленный репозиторий на GitHub и запушить туда код

        $ git remote add origin git@github.com:githubusername/repositoryname.git
        $ git push origin master	

1. Добавить удаленный репозиторий на production сервере

        $ git remote add production ssh://leonidlukinru@62.76.178.217/home/leonidlukinru/repo/leonidlukinru.git

1. Задеплоить на production сервер

        $ git push production master
	
	
	
	

## Вариант 2. Установка и настройка Wordpress (непосредственно на сервере)

1. Переход в папку пользователя и скачивание последней версии WordPress

        $ cd ~
        $ wget http://wordpress.org/latest.tar.gz
        
1. Разархивировать Wordpress

        $ tar xzvf latest.tar.gz
        
1. Установить зависимости

        $ sudo apt-get update
        $ sudo apt-get install php7.1-gd php-ssh2
        
1. Настройка конфигурации

        $ cd wordpress
        $ cp wp-config-sample.php wp-config.php
        $ vim wp-config.php
        
1. Задать параметры подключения к DB и разрешение к установке плагинов "на лету"

        define('DB_NAME', '...');
        define('DB_USER', '...');
        define('DB_PASSWORD', '...');
        
        define('FS_METHOD', 'direct');

1. Копирование сайта в корневую папку

        $ rsync -avP ~/wordpress/ /home/username/www/domain.tld/html
        
1. Создать папку uploads в wp-content

        $ mkdir /home/username/www/domain.tld/html/wp-contant/uploads
        
1. Дать права на папку сайта группе пользователя www-data (от этого пользователя работает Nginx)

        $ sudo chown -R elvidigitalru:www-data /home/username/www/domain.tld/html/*
	$ sudo chown -R www-data:www-data /home/username/www/domain.tld/html/wp-content
        
1. Задать разрешение на запись файлов и папкам

        $ find /home/elvidigitalru/www/elvidigital.ru/html -type d -exec chmod 755 {} \;
        $ find /home/elvidigitalru/www/elvidigital.ru/html -type f -exec chmod 644 {} \;

1. Задать разрешение на запись файлов и папкам в wp-content

        $ find /home/elvidigitalru/www/elvidigital.ru/html/wp-content -type d -exec chmod 775 {} \;
        $ find /home/elvidigitalru/www/elvidigital.ru/html/wp-content -type f -exec chmod 664 {} \;

1. Продолжить установку через web-интерфейс http://domain.tld/


### Перезагрузка Nginx и приложения после изменения конфигурации

        $ sudo service nginx restart
        $ sudo service php7.1-fpm restart
	
## Дополнительная настройка PHP-FPM для ВТРОГО+ сайтов на одном сервере

1. Создать файл sudo vim /etc/php/7.1/fpm/pool.d/<название_сайта>.conf с содержимым

        [<название_сайта>]
        user = www-data
        group = www-data
        listen = /var/run/php/php7.1-fpm_<название_сайта>.sock
        listen.owner = www-data
        listen.group = www-data
        php_admin_value[disable_functions] = exec,passthru,shell_exec,system
        php_admin_flag[allow_url_fopen] = off
        pm = dynamic
        pm.max_children = 5
        pm.start_servers = 2
        pm.min_spare_servers = 1
        pm.max_spare_servers = 3
        chdir = /

1. Затем перезапустить PHP-FPM

        sudo service php7.1-fpm restart	

## Опорный материал

https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04

https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-14-04

https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-nginx-on-ubuntu-14-04

https://www.digitalocean.com/community/tutorials/how-to-set-up-automatic-deployment-with-git-with-a-vps
