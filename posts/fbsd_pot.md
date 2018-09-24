# Тут будет материал по FreBSD pot 

[github](https://github.com/pizzamig/pot)
[intro](https://archive.fosdem.org/2018/schedule/event/pot_container_framework/attachments/slides/2128/export/events/attachments/pot_container_framework/slides/2128/pot_slides.pdf)

### Create ZFS zroot

```
#zpool create zroot /dev/ada1  
```

### Create new POT 

```
#pot init
#pkg install freebsd-release-manifests
#pot create-base -r 11.1
#pot create -p A -b 11.1
#pot run A
```
