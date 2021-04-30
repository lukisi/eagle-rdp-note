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

Gli *eventi* permettono a un *publisher* (di norma una classe o un oggetto) di
notificare che è accaduto qualcosa di interessante (si dice *sollevare/raise*
un evento).  
Questa notifica arriva ad un set di *subscribers* (di norma classi/oggetti) che
hanno espresso il loro interesse a ricevere tale notifica (si dice *gestire/handle*
l'evento).

Solo il publisher determina quando un evento è sollevato. I subscribers determinano
quali azioni intraprendere quando l'evento è loro notificato.

Un evento può avere molti subscribers. Un subscriber può essere interessato a
gestire diversi eventi da diversi publishers.

Quando un publisher solleva un evento, se questo ha diversi subscribers, questi
vengono notificati in modo sincrono.

### Libreria .NET - sintassi in C\#

Guardare cosa offre la libreria .NET e capire la sintassi del linguaggio C\#
sarà molto utile per approfondire la libreria `libwinpr`, la quale si basa
sulla stessa logica e offre molte analogie.

La libreria di .NET fornisce
una classe `EventArgs` praticamente vuota (eredita `Object`) che può essere usata
come classe base per un generico contenitore di dati relativi ad un evento.  
Inoltre fornisce il *delegato* (puntatore a funzione) `EventHandler` che
rappresenta la più semplice firma per un gestore di eventi:

```c#
public delegate void EventHandler(object? sender, EventArgs e);
```

### Declare an event

Assumiamo di definire una classe. Questa rappresenta i bottoni della UI. Ogni
istanza è un bottone di una app. Ogni bottone è un publisher nel senso che
notifica a chiunque sia interessato (subscribers) l'evento che l'utente della
app ha fatto click sul bottone.

Per prima cosa definisco le caratteristiche dell'evento. Ad esempio definisco
una classe con i dati dell'evento che mi aiuti a descrivere tutta la firma
dell'evento in modo semplice e leggibile.  
Nel caso in esame diciamo che l'evento è molto semplice. Potrei fare a meno
di definire una classe diversa dalla base, ma solo per completezza diciamo
che definisco la classe `ButtonClickedEventArgs` che estende `EventArgs` senza
in effetti aggiungere altro.  
In conclusione la firma sarà molto simile al `EventHandler` ma, sempre
per completezza, dichiaro un nuovo delegato `ButtonClickedEventHandler`:

```c#
public class ButtonClickedEventArgs : EventArgs
{
    ...
}

public delegate void ButtonClickedEventHandler(Button sender,
        ButtonClickedEventArgs e);
```

Poi nella classe dichiaro che faccio le veci di publisher notificando
l'evento `Clicked` secondo la firma prima definita.  
In qualche parte del codice della mia classe sollevo l'evento.

```c#
public class Button : Object
{
    ...

    public event ButtonClickedEventHandler Clicked;

    blabla miafunc() {
        ...
        // Event will be null if there are no subscribers
        if (Clicked != null) {
            Clicked(this, new ButtonClickedEventArgs());
        }
    }
}
```

**Attenzione:** il fatto che l'evento sia dichiarato *public* implica che da qualsiasi
punto del codice si può registrare un handler. Ma questo non toglie che
**solo la classe publisher può sollevare il suo proprio evento**
(in questo caso `Button`). Se si vuole fare in modo che la classe publisher
possa essere usata come classe base e che le classi
che derivano possano avere del codice specializzato che solleva questo evento
occorre aggirare il meccanismo.  
La soluzione suggerita consiste nel creare un
metodo *protetto* che al suo interno solleva l'evento. Il
nome della funzione di norma è `On<eventname>`, ad esempio `OnClicked`.

Il codice di cui sopra diventa:

```c#
public class Button : Object
{
    ...

    public event ButtonClickedEventHandler Clicked;

    blabla miafunc() {
        ...
        OnClicked(new ButtonClickedEventArgs());
        }
    }

    protected virtual void OnClicked(ButtonClickedEventArgs e) {
        // Event will be null if there are no subscribers
        if (Clicked != null) {
            Clicked(this, e);
        }
    }
}
```

### Subscribe to an event

Assumiamo che sia stato definito un evento che può essere notificato da un
publisher. Diciamo che nel mio codice ho accesso a questo publisher (ad esempio
una istanza di un UI button, `Button launch;`) e voglio registrare un
gestore ad uno specifico evento che conosco (ad esempio `Clicked` è un evento
della classe `Button` come definita sopra).

Definisco una funzione (o metodo) la cui firma combacia con la firma dell'evento
definito. Ad esempio:

```c#
void handleLaunchClicked(Button sender, ButtonClickedEventArgs e)  
{  
   // Do something useful here.  
}
```

Con la sintassi a seconda del linguaggio (in C# si usa l'operatore `+=`) si
registra il gestore all'evento.

```c#
// explicit:
launch.Clicked += new ButtonClickedEventHandler(handleLaunchClicked);
// sugar syntax in C# 2.0:
launch.Clicked += handleLaunchClicked;
```

#### Unsubscribe from an event

Se voglio annullare la registrazione del mio codice alla gestione dell'evento
(si dice *unsubscribe* dall'evento) uso questa sintassi.  
*Nota:* Necessario per evitare *resource leaks*, particolarmente se il subscriber è
un oggetto (istanza di classe).

```c#
// explicit:
launch.Clicked -= new ButtonClickedEventHandler(handleLaunchClicked);
// sugar syntax in C# 2.0:
launch.Clicked -= handleLaunchClicked;
```

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

