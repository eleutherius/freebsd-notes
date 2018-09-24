Сборка ядра
----
Если вы хотите самостоятельно создать собственное ядро, вам сначала нужно установить исходный код. Затем введите каталог конфигурации вашего аппарата:

	# cd /usr/src/sys/`uname -m`/conf

Скопируйте новый файл конфигурации ядра:

	# cp GENERIC NAN_FIRST_BUILD

Вы можете изменить новый файл конфигурации (`NAN_FIRST_BUILD`) в соответствии с вашими потребностями.

Соберите ядро:

	# cd /usr/src
	# make buildkernel KERNCONF="NAN_FIRST_BUILD"

Как только процес будет завершен, установите новый двоичный файл ядра и перезагрузите его:

	# make installkernel KERNCONF="NAN_FIRST_BUILD"
	# reboot

Проверьте версию ядра, теперь вы обнаружите, что ОС теперь использует ваше недавно собанное ядро:

	# uname -a
	FreeBSD FreeBSD 10.3-RELEASE-p5 FreeBSD 10.3-RELEASE-p5 #0: Tue Jul 19 17:52:57 CST 2016     root@FreeBSD:/usr/obj/usr/src/sys/NAN_FIRST_BUILD  amd64

Справка:
[How to build a custom kernel in FreeBSD](https://www.youtube.com/watch?v=KVNxaKu11F0).
