Установка Wordpress on FreeBSD
----

Разобьем наш проект на малeнькие подпункты:

1. Подготовка среды

2. Установка MYSQL

3. Установка NGINX + PHP

4. Равертывание WORDPRESS со стандартними настроками.



### 1. Подготовка среды

Для начала я создам одельный JAIL на  отдельном диске.
Это очень удобно, так как я смогу размножить свой контейнер
при необходимости.
Недавно я у знал про POT, средство удобного управления JAIL-лами
Подробнее можно ознакомится с этим инструментом [тут](https://fosdem.org/2018/schedule/eve
nt/pot_container_framework/attachments/slides/2128/export/events/attachments/pot_container_framework/slides/2128/pot_slides.pdf)   
Создаем  новый ZPOOL. Я выделил для этого отдельный диск.

```
#zpool create zroot /dev/ada1  
```

У меня стоит TRUEOS 18.06, и ZFS по дефолту.

```
#pkg install pot
```

Создаем новый POT

```
#pot init
#pkg install freebsd-release-manifests
#pot create-base -r 11.1
#pot create -p A -b 11.1
#pot run A

```

### 2. Установка MYSQL



Устанавливаем MYSQL.

```
#pkg install mysql57-server mysql57-client
#sysrc mysql_enable="yes"
#service mysql-server start
#cat $HOME/.mysql_secret
#mysql_secure_installation
```

####  ссоздать юзера MySQL и базу данных

```
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'my_password';
CREATE DATABASE wordpress_db;
GRANT ALL PRIVILEGES ON wordpress_db. * TO 'wordpress'@'localhost';
FLUSH PRIVILEGES;
```

Если просит поменять пароль root, меняем его.

```
SET PASSWORD = PASSWORD('my_root_password');
```



### 3. Установка NGINX + PHP

Я все ставлю одной командой.

```
#pkg install php72-xml libxml2 php72 pcre php72-hash php72-gd libXpm xproto libXext xextproto libXau libX11 libxcb libXdmcp libpthread-stubs kbproto libXt libSM libICE gettext-runtime indexinfo freetype2 png jpeg-turbo t1lib libXaw printproto libXp libXmu php72-ftp php72-curl php72-tokenizer php72-mysqli php72-zlib php72-zip libzip nginx
#sysrc nginx_enable="yes"
#sysrc php_fpm_enable="yes"
```

Моя рабочая конфигурация nginx

```
user  www;
worker_processes  1;

events {
    worker_connections  1024;
}

http {

    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    #gzip  on;

    server {
        listen       80;
        server_name  _;
        #charset koi8-r;
        #access_log  logs/host.access.log  main;
         root   /usr/local/www/wordpress;
         index index.php;
          location ~* \.(jpg|jpeg|gif|png|bmp|ico|pdf|flv|swf|exe|html|htm|txt|css|js)$ {
         root /usr/local/www/wordpress;
         }


        location / {
                 try_files $uri $uri/ /index.php?q=$request_uri;
        }

        #error_page  404              /404.html;
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/local/www/nginx-dist;
        }

        location ~ \.php$ {
            root /usr/local/www/wordpress;
            fastcgi_pass   unix:/var/run/php-fpm.sock;
            fastcgi_index  index.php;
            #fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            include        fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        }
        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        location ~ /\.ht {

            deny  all;
        }
    }


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;
    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```



### 4. Равертывание WORDPRESS со стандартними настроками.



```
<?php
phpinfo();
phpinfo(INFO_MODULES);
?>
```



```
cd /usr/local/www/wordpress/
fetch https://ru.wordpress.org/wordpress-4.9.7-ru_RU.zip
pkg install unzip
unzip wordpress-4.9.7-ru_RU.zip
mv wordpress/* ./
rm -rf wordpress-4.9.7-ru_RU.zip info.php wordpress/
chown -R www:www *
```

```
cp wp-config-sample.php wp-config.php
vim wp-config.php
chown -R www:www ./
```

### Конфигурируем  PHP

Next, we will configure our PHP-FPM service, which will be responsible for processing PHP requests sent from our web server.
To start, change to the /usr/local/etc directory, where configuration files for our optional programs are stored:
```
cd /usr/local/etc
```
There are a number of PHP configuration files in this directory that we will want to modify. We will start with the PHP-FPM configuration file itself. Open this with sudo privileges:
```
sudo vi php-fpm.conf
```
Inside, we want to adjust a few different options. First, we want to configure PHP-FPM to use a Unix socket instead of a network port for communication. This is more secure for services communicating within a single server.
Find the line that looks like this:

```
listen = 127.0.0.1:9000
```
Change this to use a socket within the /var/run directory:
```
listen = /var/run/php-fpm.sock
```
Next, we will configure the owner, group, and permissions set of the socket that will be created. There is a commented-out group of options that handle this configuration that looks like this:

```
;listen.owner = www
;listen.group = www
;listen.mode = 0660
```
Enable these by removing the comment marker at the beginning:
```
listen.owner = www
listen.group = www
listen.mode = 0660
```
Save and close the file when you are finished.
Next, we need to create a php.ini file that will configure the general behavior of PHP. Two sample files were included that we can choose to copy to the php.ini file that PHP reads.
The php.ini-production file will be closer to what we need, so we will use that one. Copy the production version over to the file PHP checks for:

```
sudo cp php.ini-production php.ini
```
Open the file for editing with sudo privileges:
```
sudo vi php.ini
```
Inside, we need to find a section that configures the cgi.fix_pathinfo behavior. It will be commented out and set to "1" by default. We need to uncomment this and set it to "0". This will prevent PHP from trying to execute parts of the path if the file that was passed in to process is not found. This could be used by malicious users to execute arbitrary code if we do not prevent this behavior.
Uncomment the cig.fix_pathinfo line and set it to "0":
```
cgi.fix_pathinfo=0
```
Save and close the file when you are finished.
Now that we have PHP-FPM completely configured, we can start the service by typing:
sudo service php-fpm start
We can now move on to configuring our MySQL instance.

===========================
===========================
Затем мы настроим нашу службу PHP-FPM, которая будет отвечать за обработку запросов PHP, отправленных с нашего веб-сервера.
Чтобы начать, перейдите в каталог /usr/local/etc, где хранятся файлы конфигурации:
`` `
cd /usr/local/etc
`` `
В этом каталоге есть несколько файлов конфигурации PHP, которые мы хотим изменить. Мы начнем с самого файла конфигурации PHP-FPM. Откройте это с помощью привилегий sudo:
```
sudo vi php-fpm.conf
```
Внутри мы хотим настроить несколько различных параметров. Во-первых, мы хотим настроить PHP-FPM на использование сокета Unix вместо сетевого порта для связи. Это более безопасный и быстрый способ обменивающихся данными на одном сервере.
Найдите строку, которая выглядит так:

```
listen = 127.0.0.1:9000
```
Измените на это, чтобы использовать сокет в каталоге /var/run:
```
listen = /var/run/php-fpm.sock
```
Затем мы настроим права владельца, группы и разрешений сокета, который будет создан.
Эти настроки уже есть:

```
; listen.owner = www
; listen.group = www
; listen.mode = 0660
```
Включите их, удалив комментарии в начале:
```
listen.owner = www
listen.group = www
listen.mode = 0660
```
Сохраните и закройте файл по завершении.
Затем нам нужно создать файл php.ini, который будет настраивать общее поведение PHP. Были включены два файла примеров, которые мы можем выбрать для копирования в файл php.ini, который читает PHP.
Файл php.ini-production скопируем в php.ini:

```
sudo cp php.ini-production php.ini
```
Откройте файл для редактирования с правами sudo:
```
sudo vi php.ini
```
Внутри нам нужно найти раздел, который настраивает поведение cgi.fix_pathinfo. Он будет закоментирован и установлен по умолчанию «1».
Нам нужно раскомментировать это и установить его в «0». Это предотвратит попытки PHP выполнять части пути, если файл, который был передан процессу, не найден.
Это может быть использовано злоумышленниками для выполнения произвольного кода, если мы не предотвратим это поведение.
Раскомментируйте строку cig.fix_pathinfo и установите ее в «0»:
```
cgi.fix_pathinfo = 0
```
Сохраните и закройте файл по завершении.
Теперь, когда мы полностью настроили PHP-FPM, мы можем запустить службу, набрав:
```
sudo service php-fpm start
```
