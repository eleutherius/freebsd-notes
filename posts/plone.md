Plone installing on FreeBSD 11.2
----

```
pkg install ca_root_nss sudo  libxslt-1.1.32 py27-pillow-5.2.0

fetch https://launchpad.net/plone/5.1/5.1.4/+download/Plone-5.1.4-UnifiedInstaller.tgz

tar xvf Plone-5.1.4-UnifiedInstaller.tgz
cd Plone-5.1.4-UnifiedInstaller

setenv CFLAGS "-I/usr/local/include"
setenv CPPFLAGS "-I/usr/local/include"
setenv LDFLAGS "-L/usr/local/lib"
./install.sh standalone
```
