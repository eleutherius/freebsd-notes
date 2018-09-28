Установка Wordpress on FreeBSD. Script
----
```shell
#!/usr/local/bin/bash
#  Скрипт по установке Wordpress on Freebsd

echo "install components"

pkg install mysql57-server mysql57-client
sysrc mysql_enable="yes"
service mysql-server start
cat $HOME/.mysql_secret
mysql_secure_installation

pkg install php72-xml libxml2 php72 pcre php72-hash php72-gd libXpm xproto libXext xextproto libXau libX11 libxcb libXdmcp libpthread-stubs kbproto libXt libSM libICE gettext-runtime indexinfo freetype2 png jpeg-turbo t1lib libXaw printproto libXp libXmu php72-ftp php72-curl php72-tokenizer php72-mysqli php72-zlib php72-zip libzip nginx
sysrc nginx_enable="yes"
sysrc php_fpm_enable="yes"

```
