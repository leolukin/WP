# Установка SSL серфтификата на Nginx

## Мастерхост

1. Скачать все файлы сертификатов из личного кабинета https://cp.masterhost.ru/services/client

1. Переименовать по схеме:

        mv certificate.txt your_domain_name.crt
        mv key.txt your_domain_name.key

1. Скачать root.crt с сайта сертификационного центра - https://knowledge.rapidssl.com/support/ssl-certificate-support/index?page=content&id=INFO1548 (нужно взять содержимое всех сертификатов и объединить в один текстовый файл root.crt)

1. Перенести сертификаты на сервер (из под root)

        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/your_domain_name.crt root@ip:/etc/ssl/certs/your_domain_name_tld
        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/your_domain_name.key root@ip:/etc/ssl/certs/your_domain_name_tld
        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/root.crt root@ip:/etc/ssl/certs/your_domain_name_tld

1. На сервере добавить промежуточный (корневой) сертификат к основному сертификату (под root)

        $ cat root.crt >> your_domain_name.crt
  
1. Открыть конфигурационный файл Nginx для сайта, сделать копию блока для HTTP соединения, вставить ниже и добавить в него:

        server { 
           listen 443; 

           ssl on;
           ssl_certificate /etc/ssl/certs/your_domain_name_tld/your_domain_name.crt;
           ssl_certificate_key /etc/ssl/certs/your_domain_name_tld/your_domain_name.key; 
 
        }
    
1. Перезагрузить Nginx (под root) и потом проверить работу сайта по HTTPS

        /etc/init.d/nginx restart
    
1. Проверить правильность установки сертификата - https://cryptoreport.rapidssl.com/checker/views/certCheck.jsp

1. Сделать принудительный 301-й редирект со всех документов домена без HTTP на HTTPS (просто добавить в первый блок HTTP сервера после server_name):

        return 301 https://your_domain_name.tld$request_uri;


