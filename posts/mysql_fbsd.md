Установка Mysql-server на FreeBSD
----

```
pkg install mysql57-server mysql57-client
```

```
sysrc mysql_enable="yes"
```

```
service mysql-server start
```

```
cat $HOME/.mysql_secret
mysql_secure_installation
```

```
mysql> UPDATE mysql.user SET Password=PASSWORD('your_new_password')

       WHERE User='root'; 
```

```
mysql> SET PASSWORD = PASSWORD('your_new_password');
```



https://www.digitalocean.com/community/tutorials/how-to-install-an-nginx-mysql-and-php-femp-stack-on-freebsd-10-1
