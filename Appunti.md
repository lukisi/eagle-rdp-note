# libfreerdp

Analiziamo come viene usata la libreria `libfreerdp` da un client. In
particolare guardiamo cosa fa il client `xfreerdp` su linux.

## Preparazione server RDP

Ho realizzato una VM con "Virtual Machine Manager" nel mio
laptop. Su questa ho installato - seguendo le istruzioni in
questo [articolo](https://www.ryadel.com/rdp-windows-linux-ubuntu-server-xrdp-accesso-remoto-desktop) -
un server Xrdp.

Quando la macchina virtuale è accesa e nessuno è loggato nella
sua console, si può accedere con un client RDP al suo IP
che è `192.168.122.246`.

## Creazione container

Per sfruttare la comodità dei container LXD, la possibilità di creare snapshot
e clonare macchine virtuali con estrema rapidità; e al contempo avere
accesso alla GUI della macchina virtuale (container) ho seguito i
suggerimenti di questo
[articolo](https://blog.simos.info/running-x11-software-in-lxd-containers/)
creando un profilo `x11`. Per via di difficoltà a far funzionare anche
PulseAudio, ho leggermente modificato il profilo suddetto:

```
luca@dell:~$ lxc profile show x11 
config:
  environment.DISPLAY: :0
  nvidia.driver.capabilities: all
  nvidia.runtime: "true"
  user.user-data: |
    #cloud-config
    packages:
      - x11-apps
      - mesa-utils
description: GUI LXD profile
devices:
  X0:
    bind: container
    connect: unix:@/tmp/.X11-unix/X1
    listen: unix:@/tmp/.X11-unix/X0
    security.gid: "1000"
    security.uid: "1000"
    type: proxy
  mygpu:
    type: gpu
name: x11
used_by: []
luca@dell:~$ 

```

Creato un container di nome `official-x11` con:

```
lxc launch ubuntu:20.04 --profile default --profile x11 official-x11
```

posso entrare con il comando `lxc exec test -- sudo --user ubuntu --login`
e da qui dare comandi che usano il server X11 del mio laptop. Ad esempio
`glxgears`.

## Scaricamento e lavoro sul progetto FreeRDP

Dentro il container ho scaricato il progetto:

```
git clone https://github.com/FreeRDP/FreeRDP.git
```

### Tentativo flatpak

Ho provato a seguire le istruzioni
[qui](https://github.com/FreeRDP/FreeRDP/wiki/Compilation)
relative alla compilazione con `flatpak-builder` dopo aver
installato le dipendenze con `flatpak`, ma probabilmente a
causa del fatto che sono in un container questo procedimento
non funziona. Comunque sarei lo stesso passato alla compilazione
con cmake, quindi procedo.

### Compilazione cmake

Avendo fatto uno snapshot del container `official-x11` prima di
iniziare il tentativo flatpak, lo riprendo con il comando `lxc copy`
per clonare il container in uno nuovo (`gabri`).

Seguendo le istruzioni per la compilazione con cmake, installo con
`apt` i pacchetti 
`build-essential`,
`zlib1g-dev`,
`pkg-config`,
`libssl-dev`,
`libcairo2-dev`,
`libusb-1.0-0-dev`,
`cmake`.

Poi sulla repo eseguo `cmake .` e poi `cmake --build .`
per compilare il progetto.  
In seguito ne verifico il funzionamento con l'esecuzione
di `./client/X11/xfreerdp 192.168.122.246`.

### Compilazione cmake tramite vscode

Avendo fatto uno snapshot del container `gabri` prima di
iniziare la compilazione con cmake, lo riprendo con il comando `lxc copy`
per clonare il container in uno nuovo (`severo`).

Dopo l'installazione di cmake, sempre con `apt` installo il
pacchetto `gdb` e in seguito con `snap` installo Visual Studio Code:

```
sudo apt install cmake
snap install code --classic
```

Apro il progetto con VS Code (`code .`) e installo le apposite
estensioni (cmake, c/c++, gdb, ...).  
Vado sulla estensione (prospettiva?) `cmake` di vscode e vedo che
ci sono vari target fra cui
`FreeRDP/client/X11/xfreerdp-client (xfreerdp)`. Ne faccio il build.  
Eseguo in debug il progetto con questa configurazione nel file `launch.json`:

```
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/client/X11/xfreerdp",
            "args": ["192.168.122.246"],
            "stopAtEntry": true,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
```

Arrivo alla chiamata alla funzione `freerdp_connect`
nel file `libfreerdp/core/freerdp.c`.

La funzione `main` crea un thread e ne attende il completamento:

```c
	thread = freerdp_client_get_thread(context);

	WaitForSingleObject(thread, INFINITE);
```

Un thread (realizzato in linux con `libpthread` del pacchetto `libc6-dev`
incapsulato in `libwinpr3` che è un target di cmake di questo progetto)
è stato avviato; esso esegue la funzione `xf_client_thread` che subito
esegue la funzione `freerdp_connect`.
