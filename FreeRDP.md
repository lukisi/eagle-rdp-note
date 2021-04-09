# FreeRDP

## Progetto

Il sito ufficiale è `https://www.freerdp.com/`.

Il repository Git del progetto FreeRDP è `https://github.com/FreeRDP/FreeRDP.git`.

```
luca@dell:~/eagle$ git clone https://github.com/FreeRDP/FreeRDP.git
Cloning into 'FreeRDP'...
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 126810 (delta 0), reused 0 (delta 0), pack-reused 126809
Receiving objects: 100% (126810/126810), 42.83 MiB | 7.29 MiB/s, done.
Resolving deltas: 100% (97051/97051), done.
luca@dell:~/eagle$ cd FreeRDP/
luca@dell:~/eagle/FreeRDP$ ls -lA
total 176
-rw-rw-r--  1 luca luca   356 feb  1 09:13 buildflags.h.in
-rw-rw-r--  1 luca luca 14479 feb  1 09:13 ChangeLog
drwxrwxr-x 27 luca luca  4096 feb  1 09:13 channels
drwxrwxr-x  3 luca luca  4096 feb  1 09:13 ci
-rw-rw-r--  1 luca luca  3396 feb  1 09:13 .clang-format
drwxrwxr-x 10 luca luca  4096 feb  1 09:13 client
drwxrwxr-x  7 luca luca  4096 feb  1 09:13 cmake
-rw-rw-r--  1 luca luca  3884 feb  1 09:13 CMakeCPack.cmake
-rw-rw-r--  1 luca luca   345 feb  1 09:13 CMakeCPackOptions.cmake.in
-rw-rw-r--  1 luca luca 37033 feb  1 09:13 CMakeLists.txt
-rw-rw-r--  1 luca luca  4894 feb  1 09:13 config.h.in
drwxrwxr-x  2 luca luca  4096 feb  1 09:13 docs
drwxrwxr-x  2 luca luca  4096 feb  1 09:13 external
drwxrwxr-x  8 luca luca  4096 feb  1 09:13 .git
drwxrwxr-x  4 luca luca  4096 feb  1 09:13 .github
-rw-rw-r--  1 luca luca  1782 feb  1 09:13 .gitignore
drwxrwxr-x  3 luca luca  4096 feb  1 09:13 include
drwxrwxr-x 11 luca luca  4096 feb  1 09:13 libfreerdp
-rw-rw-r--  1 luca luca 11358 feb  1 09:13 LICENSE
drwxrwxr-x  6 luca luca  4096 feb  1 09:13 packaging
drwxrwxr-x  5 luca luca  4096 feb  1 09:13 rdtk
-rw-rw-r--  1 luca luca  1169 feb  1 09:13 README.md
drwxrwxr-x  2 luca luca  4096 feb  1 09:13 resources
drwxrwxr-x  2 luca luca  4096 feb  1 09:13 scripts
drwxrwxr-x  8 luca luca  4096 feb  1 09:13 server
drwxrwxr-x  2 luca luca  4096 feb  1 09:13 third-party
-rw-rw-r--  1 luca luca  1001 feb  1 09:13 .travis.yml
drwxrwxr-x  5 luca luca  4096 feb  1 09:13 uwac
drwxrwxr-x  6 luca luca  4096 feb  1 09:13 winpr
```

Nel relativo wiki c'è anche una documentazione di riferimento con i link
a tutti i documenti delle specifiche del protocollo MS RDP:
[Reference-Documentation](https://github.com/FreeRDP/FreeRDP/wiki/Reference-Documentation)

## Compilazione

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

Inoltre richiede `libavutil-dev libavcodec-dev libavresample-dev`
e (opzionale) `libcunit1-dev libdirectfb-dev xmlto doxygen libxtst-dev`

Anche questi installati.

Ora dice di dare questo comando:

```
cmake -GNinja -DCHANNEL_URBDRC=ON -DWITH_DSP_FFMPEG=ON -DWITH_CUPS=ON -DWITH_PULSE=ON -DWITH_FAAC=ON -DWITH_FAAD2=ON -DWITH_GSM=ON .
```

e di leggere attentamente l'output (un po' come farei con ./configure)

```
luca@dell:~/eagle/FreeRDP$ cmake -GNinja -DCHANNEL_URBDRC=ON -DWITH_DSP_FFMPEG=ON -DWITH_CUPS=ON -DWITH_PULSE=ON -DWITH_FAAC=ON -DWITH_FAAD2=ON -DWITH_GSM=ON .
-- The C compiler identification is GNU 9.3.0
-- The CXX compiler identification is GNU 9.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PkgConfig: /usr/bin/pkg-config (found version "0.29.1") 
FREERDP_VERSION=3.0.0-dev
-- Git Revision 83658d212
-- Looking for __x86_64__
-- Looking for __x86_64__ - found
-- Performing Test Wno-unused-result
-- Performing Test Wno-unused-result - Success
-- Performing Test Wno-unused-but-set-variable
-- Performing Test Wno-unused-but-set-variable - Success
-- Performing Test Wno-deprecated-declarations
-- Performing Test Wno-deprecated-declarations - Success
-- Performing Test Wno-deprecated-declarationsCXX
-- Performing Test Wno-deprecated-declarationsCXX - Success
-- GCC default symbol visibility: hidden
-- Performing Test Wimplicit-function-declaration
-- Performing Test Wimplicit-function-declaration - Success
-- Performing Test Wredundant-decls
-- Performing Test Wredundant-decls - Success
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE  
-- Looking for pthread_mutex_timedlock
-- Looking for pthread_mutex_timedlock - not found
-- Looking for pthread_mutex_timedlock in pthread
-- Looking for pthread_mutex_timedlock in pthread - found
-- Performing Test fno-omit-frame-pointer
-- Performing Test fno-omit-frame-pointer - Success
-- Looking for include file fcntl.h
-- Looking for include file fcntl.h - found
-- Looking for include file unistd.h
-- Looking for include file unistd.h - found
-- Looking for include file execinfo.h
-- Looking for include file execinfo.h - found
-- Looking for include file inttypes.h
-- Looking for include file inttypes.h - found
-- Looking for include file sys/modem.h
-- Looking for include file sys/modem.h - not found
-- Looking for include file sys/filio.h
-- Looking for include file sys/filio.h - not found
-- Looking for include file sys/sockio.h
-- Looking for include file sys/sockio.h - not found
-- Looking for include file sys/strtio.h
-- Looking for include file sys/strtio.h - not found
-- Looking for include file sys/select.h
-- Looking for include file sys/select.h - found
-- Looking for include file syslog.h
-- Looking for include file syslog.h - found
-- Performing Test HAVE_TM_GMTOFF
-- Performing Test HAVE_TM_GMTOFF - Success
-- Looking for include file aio.h
-- Looking for include file aio.h - found
-- Looking for include file sys/eventfd.h
-- Looking for include file sys/eventfd.h - found
-- Looking for eventfd_read
-- Looking for eventfd_read - found
-- Looking for include file sys/timerfd.h
-- Looking for include file sys/timerfd.h - found
-- Looking for include file poll.h
-- Looking for include file poll.h - found
-- Looking for ceill
-- Looking for ceill - found
-- Looking for getlogin_r
-- Looking for getlogin_r - found
-- Finding recommended feature libsystemd for systemd journal appender (allows to export wLog to systemd journal)
--     Disable feature libsystemd using "-DWITH_LIBSYSTEMD=OFF"
-- Could NOT find libsystemd (missing: LIBSYSTEMD_LIBRARY LIBSYSTEMD_INCLUDE_DIR) 
CMake Warning at cmake/FindFeature.cmake:46 (message):
      feature libsystemd was requested but could not be found! ON / FALSE
Call Stack (most recent call first):
  CMakeLists.txt:790 (find_feature)


-- Finding recommended feature X11 for X11 (X11 client and server)
--     Disable feature X11 using "-DWITH_X11=OFF"
-- Found X11: /usr/lib/x86_64-linux-gnu/libX11.so  
-- Finding recommended feature Wayland for Wayland (Wayland client)
--     Disable feature Wayland using "-DWITH_WAYLAND=OFF"
-- Checking for module 'wayland-scanner'
--   Found wayland-scanner, version 1.18.0
-- Checking for module 'wayland-client'
--   Found wayland-client, version 1.18.0
-- Checking for module 'wayland-cursor'
--   Found wayland-cursor, version 1.18.0
-- Checking for module 'xkbcommon'
--   No package 'xkbcommon' found
-- Could NOT find WAYLAND (missing: XKBCOMMON_INCLUDE_DIR XKBCOMMON_LIBS) 
CMake Warning at cmake/FindFeature.cmake:46 (message):
      feature Wayland was requested but could not be found! ON / FALSE
Call Stack (most recent call first):
  CMakeLists.txt:813 (find_feature)


-- Finding required feature ZLIB for compression (data compression)
-- Found ZLIB: /usr/lib/x86_64-linux-gnu/libz.so (found version "1.2.11") 
-- Finding required feature OpenSSL for cryptography (encryption, certificate validation, hashing functions)
-- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libssl.so;/usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1f") 
-- Skipping optional feature MbedTLS for cryptography (encryption, certificate validation, hashing functions)
--     Enable feature MbedTLS using "-DWITH_MBEDTLS=ON"
-- Skipping optional feature OpenSLES for multimedia (OpenSLES audio / video)
--     Enable feature OpenSLES using "-DWITH_OPENSLES=ON"
-- Finding recommended feature OSS for sound (audio input, audio output and multimedia redirection)
--     Disable feature OSS using "-DWITH_OSS=OFF"
-- Found OSS Audio
-- Finding recommended feature ALSA for sound (audio input, audio output and multimedia redirection)
--     Disable feature ALSA using "-DWITH_ALSA=OFF"
-- Found ALSA: /usr/lib/x86_64-linux-gnu/libasound.so (found version "1.2.2") 
-- Finding recommended feature Pulse for sound (audio input, audio output and multimedia redirection)
--     Disable feature Pulse using "-DWITH_PULSE=OFF"
-- Checking for module 'libpulse'
--   Found libpulse, version 13.99.1
-- Found Pulse: /usr/include  
-- Finding recommended feature Cups for printing (printer device redirection)
--     Disable feature Cups using "-DWITH_CUPS=OFF"
-- Found Cups: /usr/lib/x86_64-linux-gnu/libcups.so (found version "2.3.1") 
-- Finding recommended feature PCSC for smart card (smart card device redirection)
--     Disable feature PCSC using "-DWITH_PCSC=OFF"
-- Found PCSC: /usr/lib/x86_64-linux-gnu/libpcsclite.so  
-- Finding recommended feature FFmpeg for multimedia (multimedia redirection, audio and video playback)
--     Disable feature FFmpeg using "-DWITH_FFMPEG=OFF"
-- Checking for module 'libavcodec'
--   Found libavcodec, version 58.54.100
-- Checking for module 'libavutil'
--   Found libavutil, version 56.31.100
-- Checking for module 'libavresample'
--   Found libavresample, version 4.0.0
-- Checking for module 'libswresample'
--   Found libswresample, version 3.5.100
-- Found FFmpeg: TRUE  
-- Skipping optional feature JPEG for codec (use JPEG library)
--     Enable feature JPEG using "-DWITH_JPEG=ON"
-- Skipping optional feature x264 for codec (use x264 library)
--     Enable feature x264 using "-DWITH_X264=ON"
-- Skipping optional feature OpenH264 for codec (use OpenH264 library)
--     Enable feature OpenH264 using "-DWITH_OPENH264=ON"
-- Skipping optional feature OpenCL for codec (use OpenCL library)
--     Enable feature OpenCL using "-DWITH_OPENCL=ON"
-- Finding optional feature GSM for codec (GSM audio codec library)
-- Found GSM: /usr/include  
-- Skipping optional feature LAME for codec (lame MP3 audio codec library)
--     Enable feature LAME using "-DWITH_LAME=ON"
-- Finding optional feature FAAD2 for codec (FAAD2 AAC audio codec library)
-- Found FAAD2: /usr/include  
-- Finding optional feature FAAC for codec (FAAC AAC audio codec library)
-- Found FAAC: /usr/include  
-- Skipping optional feature soxr for codec (SOX audio resample library)
--     Enable feature soxr using "-DWITH_SOXR=ON"
-- Skipping optional feature GSSAPI for auth (add kerberos support)
--     Enable feature GSSAPI using "-DWITH_GSSAPI=ON"
-- Skipping optional feature IPP for performance (Intel Integrated Performance Primitives library)
--     Enable feature IPP using "-DWITH_IPP=ON"
-- Using OpenSSL Version: 1.1.1f
using default plugins location
-- Looking for include file stdbool.h
-- Looking for include file stdbool.h - found
-- Looking for include file stdint.h
-- Looking for include file stdint.h - found
-- Looking for include file inttypes.h
-- Looking for include file inttypes.h - found
-- Looking for timer_create
-- Looking for timer_create - found
-- Looking for timer_delete
-- Looking for timer_delete - found
-- Looking for timer_settime
-- Looking for timer_settime - found
-- Looking for timer_gettime
-- Looking for timer_gettime - found
-- Looking for backtrace
-- Looking for backtrace - found
-- Checking for module 'cairo'
--   No package 'cairo' found
-- Could NOT find Cairo (missing: CAIRO_LIBRARIES CAIRO_INCLUDE_DIR) 
CMake Warning at libfreerdp/CMakeLists.txt:102 (message):
  -DWITH_SWSCALE=OFF and -DWITH_CAIRO=OFF, compiling without image scaling
  support!


-- Finding recommended feature XKBFile for X11 keyboard (X11 keyboard file extension)
--     Disable feature XKBFile using "-DWITH_XKBFILE=OFF"
-- Found XKBFile: /usr/lib/x86_64-linux-gnu/libxkbfile.so  
-- Adding STATIC channel client server "drdynvc": Dynamic Virtual Channel Extension
-- Adding DYNAMIC channel client "video": Video optimized remoting Virtual Channel Extension
-- Adding DYNAMIC channel client "urbdrc": USB Devices Virtual Channel Extension
-- Found libusb-1.0:
--  - Includes: /usr/include/libusb-1.0
--  - Libraries: /usr/lib/x86_64-linux-gnu/libusb-1.0.so
-- Adding DEVICE channel client "smartcard": Smart Card Virtual Channel Extension
-- Adding DEVICE channel client "serial": Serial Port Virtual Channel Extension
-- Adding STATIC channel client server "remdesk": Remote Assistance Virtual Channel Extension
-- Adding STATIC channel client server "rdpsnd": Audio Output Virtual Channel Extension
-- Adding DYNAMIC channel client "rdpgfx": Graphics Pipeline Extension
-- Adding DYNAMIC channel client "rdpei": Input Virtual Channel Extension
-- Adding STATIC channel client server "rdpdr": Device Redirection Virtual Channel Extension
-- Adding STATIC channel client "rdp2tcp": Tunneling TCP over RDP
-- Adding STATIC channel client "rail": Remote Programs Virtual Channel Extension
-- Adding DEVICE channel client "printer": Print Virtual Channel Extension
-- Adding DEVICE channel client "parallel": Parallel Port Virtual Channel Extension
-- Adding DYNAMIC channel client "geometry": Geometry tracking Virtual Channel Extension
-- Adding STATIC channel client server "encomsp": Multiparty Virtual Channel Extension
-- Adding DYNAMIC channel client server "echo": Echo Virtual Channel Extension
-- Adding DEVICE channel client "drive": Drive Redirection Virtual Channel Extension
-- Adding DYNAMIC channel client "disp": Display Update Virtual Channel Extension
-- Adding STATIC channel client server "cliprdr": Clipboard Virtual Channel Extension
-- Adding DYNAMIC channel client server "audin": Audio Input Redirection Virtual Channel Extension
-- Found DocBookXSL: /usr/share/xml/docbook/stylesheet/docbook-xsl  
-- Finding required feature XShm for X11 shared memory (X11 shared memory extension)
-- Found XShm: /usr/lib/x86_64-linux-gnu/libXext.so  
-- Finding recommended feature Xinerama for multi-monitor (X11 multi-monitor extension)
--     Disable feature Xinerama using "-DWITH_XINERAMA=OFF"
-- Found Xinerama: /usr/lib/x86_64-linux-gnu/libXinerama.so  
-- Finding recommended feature Xext for X11 extension (X11 core extensions)
--     Disable feature Xext using "-DWITH_XEXT=OFF"
-- Found Xext: /usr/lib/x86_64-linux-gnu/libXext.so  
-- Finding recommended feature Xcursor for cursor (X11 cursor extension)
--     Disable feature Xcursor using "-DWITH_XCURSOR=OFF"
-- Found Xcursor: /usr/lib/x86_64-linux-gnu/libXcursor.so  
-- Finding recommended feature Xv for video (X11 video extension)
--     Disable feature Xv using "-DWITH_XV=OFF"
-- Found Xv: /usr/lib/x86_64-linux-gnu/libXv.so  
-- Finding recommended feature Xi for input (X11 input extension)
--     Disable feature Xi using "-DWITH_XI=OFF"
-- Found Xi: /usr/lib/x86_64-linux-gnu/libXi.so  
-- Looking for XITouchClass
-- Looking for XITouchClass - found
-- Finding recommended feature Xrender for rendering (X11 render extension)
--     Disable feature Xrender using "-DWITH_XRENDER=OFF"
-- Found Xrender: /usr/lib/x86_64-linux-gnu/libXrender.so  
-- Finding recommended feature XRandR for tracking output configuration (X11 randr extension)
--     Disable feature XRandR using "-DWITH_XRANDR=OFF"
-- Found XRANDR: /usr/lib/x86_64-linux-gnu/libXrandr.so  
-- Finding recommended feature Xfixes for X11 xfixes extension (Useful additions to the X11 core protocol)
--     Disable feature Xfixes using "-DWITH_XFIXES=OFF"
-- Found Xfixes: /usr/lib/x86_64-linux-gnu/libXfixes.so  
-- Finding recommended feature FUSE for Remote to local file copy (use -DWITH_FUSE2 or -DWITH_FUSE3 to specify fuse version)
--     Disable feature FUSE using "-DWITH_FUSE=OFF"
-- Could NOT find FUSE (missing: FUSE_LIBRARIES FUSE_INCLUDE_DIR FUSE_API_VERSION) 
CMake Warning at cmake/FindFeature.cmake:46 (message):
      feature FUSE was requested but could not be found! ON / FALSE
Call Stack (most recent call first):
  client/X11/CMakeLists.txt:184 (find_feature)


-- Configuring done
-- Generating done
-- Build files have been written to: /home/luca/eagle/FreeRDP
luca@dell:~/eagle/FreeRDP$ 
```

Non trova libsystemd, si può evitare il test con "-DWITH_LIBSYSTEMD=OFF".

Non trova wayland, si può evitare il test con "-DWITH_X11=OFF".

Non trova CAIRO_LIBRARIES, limiterebbe la possibilità di scaling.  
Ho provato a installare `sudo apt install libcairo2-dev` e lo trova
ma non cambia che non supporta lo scaling. **Indagare**.

Non trova FUSE_LIBRARIES, limiterebbe il file transfer.  
Ho provato a installare `sudo apt install libfuse-dev` e lo trova e
ottiene il supporto del file transfer.

A questo punto dice di dare il comando `cmake --build .` e questo
emette alcuni warning (unused function) e poi compila.

La installazione sarebbe fatta con `cmake --build . --target install`.

La produzione di un pacchetto installabile con `cmake --build . --target package`.  
Questa usa `CPack` che al momento sembra configurato per produrre
alcuni tipi: STGZ, TGZ, TZ, ZIP. **Indagare** per DEB.

```
luca@dell:~/eagle/FreeRDP$ cmake --build . --target package
[0/1] Run CPack packaging tool...
CPack: Create package using STGZ
CPack: Install projects
CPack: - Install project: FreeRDP []
CPack: Create package
CPack: - package: /home/luca/eagle/FreeRDP/freerdp-3.0.0-dev-Linux-x86_64.sh generated.
CPack: Create package using TGZ
CPack: Install projects
CPack: - Install project: FreeRDP []
CPack: Create package
CPack: - package: /home/luca/eagle/FreeRDP/freerdp-3.0.0-dev-Linux-x86_64.tar.gz generated.
CPack: Create package using TZ
CPack: Install projects
CPack: - Install project: FreeRDP []
CPack: Create package
CPack: - package: /home/luca/eagle/FreeRDP/freerdp-3.0.0-dev-Linux-x86_64.tar.Z generated.
CPack: Create package using ZIP
CPack: Install projects
CPack: - Install project: FreeRDP []
CPack: Create package
CPack: - package: /home/luca/eagle/FreeRDP/freerdp-3.0.0-dev-Linux-x86_64.zip generated.
luca@dell:~/eagle/FreeRDP$
```



