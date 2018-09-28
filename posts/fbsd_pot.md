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
