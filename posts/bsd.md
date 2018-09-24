### Настройка 

####Открытые порты



```
sockstat -4l
```

####Apache 24
```
service apache24 restart
service apache24 configtest
```

Ставим  все без зависимостей
```
root@ghostbsd:/basejail/usr/ports/editors/vim # make DISABLE_VULNERABILITIES=yes install clean
```



Настраиваем  apache сначала server name, потом document root 
```
 grep 'wgDB*' /usr/local/www/mediawiki/LocalSettings.php
```

Делаем 301 редирект Apache. 
```
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

Делаем локальную базу данных файлов, смотрим где находится файл 
```
/usr/libexec/locate.updatedb 
locate nginx.conf
```