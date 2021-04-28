# Windows Portable Runtime - Publisher/Subscriber Pattern

Come si può usare la libreria `libwinpr` (e come di fatto viene usata dal
progetto FreeRDP) per realizzare una programmazione event-driven.

Fra le API fornite dalla libreria `libwinpr` c'è il tipo (typedef) `wPubSub`.  
Esso è dichiarato nel file `winpr/include/winpr/collections.h`.  
Si tratta di una struct che viene definita nel file `winpr/libwinpr/utils/collections/PubSub.c`.

Il commento in testa al file indica che viene implementato il pattern
Publisher/Subscriber e rimanda al documento
[Events](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)
sul sito Microsoft della guida alla programmazione in C\#.

## Caratteristiche salienti

Alcune delle caratteristiche salienti della gestione di eventi realizzata con questa
libreria sono di seguito riportate. In particolare annoto quelle che possono
interessarmi nello sviluppo/debug di un client basato su `libfreerdp`.

Events enable a class or object to notify other classes or objects when something of interest occurs. The class that sends (or raises) the event is called the publisher and the classes that receive (or handle) the event are called subscribers.


## Definizione di eventi

Le funzioni `PubSub_OnActivated` e `PubSub_OnConnectionStateChange`
sono definite tramite dei blocchi `DEFINE_EVENT_BEGIN` e `DEFINE_EVENT_END`;
queste *keyword* sono realizzate con delle istruzioni `#define`
(istruzione al preprocessore C/C++) che sono incluse nel
file `winpr/include/winpr/collections.h`.

Ad esempio la `PubSub_OnActivated` è definita
con il seguente blocco nel file `include/freerdp/event.h`:

```c
	DEFINE_EVENT_BEGIN(Activated)
	BOOL firstActivation;
	DEFINE_EVENT_END(Activated)
```

## Raise di eventi

In conclusione, la sintassi `PubSub_OnActivated(context->pubSub, context, &activatedEvent);` significa

