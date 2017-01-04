# Установка SSL серфтификата на Nginx

## Мастерхост

1. Скачать все файлы сертификатов из личного кабинета https://cp.masterhost.ru/services/client

1. Переименовать по схеме:

        mv certificate.txt your_domain_name.crt
        mv key.txt your_domain_name.key

1. Скачать root.crt с сайта сертификационного центра - https://knowledge.rapidssl.com/support/ssl-certificate-support/index?page=content&id=INFO1548

1. Перенести сертификаты на сервер

        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/your_domain_name.crt user@ip:/etc/ssl/certs/your_domain_name_tld
        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/your_domain_name.key user@ip:/etc/ssl/certs/your_domain_name_tld
        scp /Users/leonidlukin/Sites/your_domain_name_tld/ssl/root.crt user@ip:/etc/ssl/certs/your_domain_name_tld
