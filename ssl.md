# Установка SSL серфтификата на Nginx

## Мастерхост

1. Скачать все файлы сертификатов из личного кабинета https://cp.masterhost.ru/services/client

1. Переименовать по схеме:

        mv certificate.txt your_domain_name.crt
        mv key.txt your_domain_name.key

1. Скачать root.crt с сайта сертификационного центра - https://knowledge.rapidssl.com/support/ssl-certificate-support/index?page=content&id=INFO1548

1. Перенести сертификаты на сервер (из под root)

        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/your_domain_name.crt root@ip:/etc/ssl/certs/your_domain_name_tld
        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/your_domain_name.key root@ip:/etc/ssl/certs/your_domain_name_tld
        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/root.crt root@ip:/etc/ssl/certs/your_domain_name_tld

1. На сервере соединить сертификат с корневым сертификатом

        $ cat root.crt >> your_domain_name.crt
