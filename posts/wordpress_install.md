Установка Wordpress on FreeBSD. Script
----
```shell
#!/bin/sh
# Скрипт по установке Wordpress on Freebsd 11.X 
#  Запускаем скрипт только от root
if [ $USERNAME != root ]; then
    echo "This script must be run as root"
    exit 1
fi
echo "Install components"

if [ -f !=/usr/local/bin/bash ] ; then
echo "Installing BASH"
pkg install -y bash
fi

/usr/local/bin/bash
# Настраиваем MYSQL, сбрасываем дефолтный пароль, сенерируем новые, записывем в log_install.txt

pkg install -y  mysql57-server mysql57-client
sysrc mysql_enable="yes"
service mysql-server start
TEMP_MYSQL_PASS="$(grep -v -E "#|^$" ~/.mysql_secret)"
MYSQL_PASS="$(openssl rand -base64 10)"

echo "mysql_password: $MYSQL_PASS" >> ./log_install.txt
mysql -u root  -p$TEMP_MYSQL_PASS -Bse "SET PASSWORD = PASSWORD( '$MYSQL_PASS');"
WP_PASS="$(openssl rand -base64 10)" && echo "wordpress_password: $WP_PASS" >> ./log_install.txt
mysql -u root  -p$MYSQL_PASS -Bse "CREATE USER 'wordpress'@'localhost' IDENTIFIED BY '$WP_PASS';"
mysql -u root  -p$MYSQL_PASS -Bse "CREATE DATABASE wordpress_db;"
mysql -u root  -p$MYSQL_PASS -Bse "GRANT ALL PRIVILEGES ON wordpress_db. * TO 'wordpress'@'localhost';"
mysql -u root  -p$MYSQL_PASS -Bse "FLUSH PRIVILEGES;"

#устанавлиаем php и все составляющие и сервер nginx  

pkg install -y php72-xml libxml2 php72 pcre php72-hash php72-gd libXpm xproto libXext xextproto libXau libX11 libxcb libXdmcp libpthread-stubs kbproto libXt libSM libICE gettext-runtime indexinfo freetype2 png jpeg-turbo t1lib libXaw printproto libXp libXmu php72-ftp php72-curl php72-tokenizer php72-mysqli php72-zlib php72-zip libzip nginx ca_root_nss 
sysrc nginx_enable="yes"
sysrc php_fpm_enable="yes"

mkdir /usr/local/www/wordpress
cd /usr/local/www/wordpress
fetch https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
mv wordpress/* ./
rm -rf /usr/local/www/wordpress/latest.tar.gz  /usr/local/www/wordpress/wordpress/
chown -R www:www /usr/local/www/wordpress
cp /usr/local/www/wordpress/wp-config-sample.php /usr/local/www/wordpress/wp-config.php


# Тут нужно поменять пароли и все остальное!! 
 
sed  -i '' -e 's#database_name_here#$DB#g' /usr/local/www/wordpress/wp-config.php
chown -R www:www ./


define('DB_NAME', 'database_name_here');

/** MySQL database username */
define('DB_USER', 'username_here');

/** MySQL database password */
define('DB_PASSWORD', 'password_here');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


sed  -i '' -e 's#listen=127.0.0.1:9000#listen=/var/run/php-fpm.sock#g' /usr/local/etc/php-fpm.conf

sed  -i '' -e 's#; listen.owner = www#listen.owner = www#g' /usr/local/etc/php-fpm.conf
sed  -i '' -e 's#; listen.group = www#listen.group = www#g' /usr/local/etc/php-fpm.conf
sed  -i '' -e 's#; listen.mode = 0660#listen.mode = 0660#g' /usr/local/etc/php-fpm.conf

cp /usr/local/etc/php.ini-production /usr/local/etc/php.ini

sed  -i '' -e 's#;cgi.fix_pathinfo=1#cgi.fix_pathinfo=0#g' /usr/local/etc/php.ini 

```
