# FreeRDP

Primi passi per la compilazione su Ubuntu 20.04

La pagina [Compilation](https://github.com/FreeRDP/FreeRDP/wiki/Compilation)
del wiki suggerisce una lista di dipendenze.

Alcuni erano già presenti nel sistema. Alcuni non esistono nei repository
di APT (libgstreamer0.10-dev e libgstreamer-plugins-base0.10-dev)

```
luca@dell:~/eagle/FreeRDP$ apt list -qq ninja-build build-essential git-core debhelper cdbs dpkg-dev autotools-dev cmake pkg-config xmlto libssl-dev docbook-xsl xsltproc libxkbfile-dev libx11-dev libwayland-dev libxrandr-dev libxi-dev libxrender-dev libxext-dev libxinerama-dev libxfixes-dev libxcursor-dev libxv-dev libxdamage-dev libxtst-dev libcups2-dev libpcsclite-dev libasound2-dev libpulse-dev libjpeg-dev libgsm1-dev libusb-1.0-0-dev libudev-dev libdbus-glib-1-dev uuid-dev libxml2-dev libgstreamer1.0-dev libgstreamer0.10-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-base0.10-dev libfaad-dev libfaac-dev
autotools-dev/focal,focal,now 20180224.1 all [installed,automatic]
build-essential/focal-updates,now 12.8ubuntu1.1 amd64 [installed]
build-essential/focal-updates 12.8ubuntu1.1 i386
cdbs/focal,focal 0.4.159ubuntu2 all
cmake/focal 3.16.3-1ubuntu1 amd64
cmake/focal 3.16.3-1ubuntu1 i386
debhelper/focal,focal 12.10ubuntu1 all
docbook-xsl/focal,focal 1.79.1+dfsg-2 all
dpkg-dev/focal,focal,now 1.19.7ubuntu3 all [installed,automatic]
libasound2-dev/focal-updates 1.2.2-2.1ubuntu2.3 amd64
libasound2-dev/focal-updates 1.2.2-2.1ubuntu2.3 i386
libcups2-dev/focal-updates,focal-security 2.3.1-9ubuntu1.1 amd64
libcups2-dev/focal-updates,focal-security 2.3.1-9ubuntu1.1 i386
libdbus-glib-1-dev/focal 0.110-5fakssync1 amd64
libdbus-glib-1-dev/focal 0.110-5fakssync1 i386
libfaac-dev/focal 1.30-1 amd64
libfaad-dev/focal 2.9.1-1 amd64
libfaad-dev/focal 2.9.1-1 i386
libgsm1-dev/focal 1.0.18-2 amd64
libgsm1-dev/focal 1.0.18-2 i386
libgstreamer-plugins-base1.0-dev/focal 1.16.2-4 amd64
libgstreamer-plugins-base1.0-dev/focal 1.16.2-4 i386
libgstreamer1.0-dev/focal 1.16.2-2 amd64
libgstreamer1.0-dev/focal 1.16.2-2 i386
libjpeg-dev/focal 8c-2ubuntu8 amd64
libjpeg-dev/focal 8c-2ubuntu8 i386
libpcsclite-dev/focal 1.8.26-3 amd64
libpcsclite-dev/focal 1.8.26-3 i386
libpulse-dev/focal-updates 1:13.99.1-1ubuntu3.10 amd64
libpulse-dev/focal-updates 1:13.99.1-1ubuntu3.10 i386
libssl-dev/focal-updates,focal-security,now 1.1.1f-1ubuntu2.1 amd64 [installed,automatic]
libssl-dev/focal-updates,focal-security 1.1.1f-1ubuntu2.1 i386
libudev-dev/focal-updates 245.4-4ubuntu3.4 amd64
libudev-dev/focal-updates 245.4-4ubuntu3.4 i386
libusb-1.0-0-dev/focal 2:1.0.23-2build1 amd64
libusb-1.0-0-dev/focal 2:1.0.23-2build1 i386
libwayland-dev/focal 1.18.0-1 amd64
libwayland-dev/focal 1.18.0-1 i386
libx11-dev/focal-updates,focal-security,now 2:1.6.9-2ubuntu1.1 amd64 [installed,automatic]
libx11-dev/focal-updates,focal-security 2:1.6.9-2ubuntu1.1 i386
libxcursor-dev/focal 1:1.2.0-2 amd64
libxcursor-dev/focal 1:1.2.0-2 i386
libxdamage-dev/focal 1:1.1.5-2 amd64
libxdamage-dev/focal 1:1.1.5-2 i386
libxext-dev/focal 2:1.3.4-0ubuntu1 amd64
libxext-dev/focal 2:1.3.4-0ubuntu1 i386
libxfixes-dev/focal 1:5.0.3-2 amd64
libxfixes-dev/focal 1:5.0.3-2 i386
libxi-dev/focal 2:1.7.10-0ubuntu1 amd64
libxi-dev/focal 2:1.7.10-0ubuntu1 i386
libxinerama-dev/focal 2:1.1.4-2 amd64
libxinerama-dev/focal 2:1.1.4-2 i386
libxkbfile-dev/focal 1:1.1.0-1 amd64
libxkbfile-dev/focal 1:1.1.0-1 i386
libxml2-dev/focal 2.9.10+dfsg-5 amd64
libxml2-dev/focal 2.9.10+dfsg-5 i386
libxrandr-dev/focal 2:1.5.2-0ubuntu1 amd64
libxrandr-dev/focal 2:1.5.2-0ubuntu1 i386
libxrender-dev/focal 1:0.9.10-1 amd64
libxrender-dev/focal 1:0.9.10-1 i386
libxtst-dev/focal 2:1.2.3-1 amd64
libxtst-dev/focal 2:1.2.3-1 i386
libxv-dev/focal 2:1.0.11-1 amd64
libxv-dev/focal 2:1.0.11-1 i386
ninja-build/focal,now 1.10.0-1build1 amd64 [installed,automatic]
ninja-build/focal 1.10.0-1build1 i386
pkg-config/focal,now 0.29.1-0ubuntu4 amd64 [installed,automatic]
pkg-config/focal 0.29.1-0ubuntu4 i386
uuid-dev/focal-updates,now 2.34-0.1ubuntu9.1 amd64 [installed,automatic]
uuid-dev/focal-updates 2.34-0.1ubuntu9.1 i386
xmlto/focal 0.0.28-2.1 amd64
xmlto/focal 0.0.28-2.1 i386
xsltproc/focal 1.1.34-4 amd64
xsltproc/focal 1.1.34-4 i386
luca@dell:~/eagle/FreeRDP$ sudo apt install ninja-build build-essential git-core debhelper cdbs dpkg-dev autotools-dev cmake pkg-config xmlto libssl-dev docbook-xsl xsltproc libxkbfile-dev libx11-dev libwayland-dev libxrandr-dev libxi-dev libxrender-dev libxext-dev libxinerama-dev libxfixes-dev libxcursor-dev libxv-dev libxdamage-dev libxtst-dev libcups2-dev libpcsclite-dev libasound2-dev libpulse-dev libjpeg-dev libgsm1-dev libusb-1.0-0-dev libudev-dev libdbus-glib-1-dev uuid-dev libxml2-dev libgstreamer1.0-dev libgstreamer0.10-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-base0.10-dev libfaad-dev libfaac-dev
[sudo] password for luca: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Note, selecting 'git' instead of 'git-core'
E: Unable to locate package libgstreamer0.10-dev
E: Couldn't find any package by glob 'libgstreamer0.10-dev'
E: Unable to locate package libgstreamer-plugins-base0.10-dev
E: Couldn't find any package by glob 'libgstreamer-plugins-base0.10-dev'
luca@dell:~/eagle/FreeRDP$ sudo apt install ninja-build build-essential git-core debhelper cdbs dpkg-dev autotools-dev cmake pkg-config xmlto libssl-dev docbook-xsl xsltproc libxkbfile-dev libx11-dev libwayland-dev libxrandr-dev libxi-dev libxrender-dev libxext-dev libxinerama-dev libxfixes-dev libxcursor-dev libxv-dev libxdamage-dev libxtst-dev libcups2-dev libpcsclite-dev libasound2-dev libpulse-dev libjpeg-dev libgsm1-dev libusb-1.0-0-dev libudev-dev libdbus-glib-1-dev uuid-dev libxml2-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libfaad-dev libfaac-dev
Reading package lists... Done
...

luca@dell:~/eagle/FreeRDP$
```

Il resto è stato installato.


