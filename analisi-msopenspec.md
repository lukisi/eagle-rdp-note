# Studio specifiche MS RDS in relazione al progetto FreeRDP

Ho iniziato a leggere la documentazione di Microsoft per le specifiche del
protocollo "`[MS-RDSOD]`: Remote Desktop Services", in particolare la versione
7.0 del protocollo reperibile a questo link:

*   [MS-RDSOD](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdsod/072543f9-4bd4-4dc6-ab97-9a04bf9d2c6a).

La definizione di questo protocollo fa riferimenti (e si appoggia) a una serie
di altri protocolli definiti da Microsoft e a loro volta estendono o
complementano altri standard definiti anche da enti di standardizzazione
come ITU e altri. Qui ne elenco alcuni a cui farò riferimento nel resto
di questo documento.

*   [MS-RDPBCGR](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/5073f4ed-1e93-45e1-b039-6e30c385867c)
    Remote Desktop Protocol: Basic Connectivity and Graphics Remoting (versione 54.0)
*   [ITU-T X.224](https://www.itu.int/rec/T-REC-X.224-199511-I/en)
*   [ITU-T T.123](https://www.itu.int/rec/T-REC-T.123/en)
*   [MS-RDPELE](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpele/3d3f160a-3ab3-4dfb-ba4e-47c27cd79409)
    Remote Desktop Protocol: Licensing Extension
*   [MS-RDPEMT](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpemt/d22b606c-32c4-4647-b356-86f75e23a22c)
    Remote Desktop Protocol: Multitransport Extension

Nella sezione di esempi (sezione 3.1
"`Example 1: Connecting from an RDP Client to an RD Session Host`")
viene detto che come primo passo il
RDP client inizia una connessione al RD Session Host inviando un
`X.224` Connection Request protocol data unit (PDU), come descritto nel
"`[MS-RDPBCGR]`" sezione 1.3.1.1.  
[link al doc](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/023f1e69-cfe8-4ee6-9ee0-7e759fb4e4ee)

In questo documento viene descritta la *RDP Connection Sequence*.

## RDP Connection Sequence

**1. Connection Initiation** -
Il primo
passo è la trasmissione dal *client* al *Session Host* di un
*X.224 Connection Request PDU*
([2.2.1.1](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/18a27ef9-6f9a-4501-b000-94b1fe3c2c10)).
Il *Session Host* risponde con un *X.224 Connection Confirm PDU*
([2.2.1.2](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/13757f8f-66db-4273-9d2c-385c33b1e483)).

Da questo punto in avanti, tutti i dati scambiati tra client e
server sono incapsulati in *X.224 Data PDU*.

**2. Basic Settings Exchange** -
Di seguito il client
invia un *Multipoint Communication Service (MCS)*
*Connect Initial PDU* con un *GCC Conference Create Request*
([2.2.1.3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/db6713ee-1c0e-4064-a3b3-0fac30b4037b)).  
Il Session Host risponde con un *MCS Connect Response PDU* con
un *GCC Conference Create Response*
([2.2.1.4](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/927de44c-7fe8-4206-a14f-e5517dc24b1c)).  
Questi pacchetti contengono principalmente delle impostazioni (tra le quali
core data, security data, e network data) che sono comunicate dal client
al server e anche dal server al client.

**3. Channel Connection** -
Di seguito il client invia un
*MCS Erect Domain Request PDU*
([2.2.1.5](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/04c60697-0d9a-4afd-a0cd-2cc133151a9c))
seguito da un *MCS Attach User Request PDU*
([2.2.1.6](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/f5d6a541-9b36-4100-b78f-18710f39f247))
per connettere (*attach*) l'utente al dominio.  
Il server risponde con un *MCS Attach User Confirm PDU*
([2.2.1.7](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/3b3d850b-99b1-4a9a-852b-1eb2da5024e5))
che contiene il *User Channel ID*. Altri channel ID (I/O channel,
static virtual channels) il client li può
reperire dai pacchetti GCC.  
Dopo il client si unisce (*join*) al user channel, I/O channel e i vari
static virtual channels tramite diversi
*MCS Channel Join Request PDUs*
([2.2.1.8](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/64564639-3b2d-4d2c-ae77-1105b4cc011b)).  
Il server conferma ogni channel con un
*MCS Channel Join Confirm PDU*
([2.2.1.9](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/cfc938b5-041d-4c15-9909-81dd035b914e)).  
*Nota:* Alcune versioni di MS RDP Client aspettano la Channel Join Confirm PDU
prima di inviare la successiva Channel Join Request PDU. Altre versioni
inviano tutte le richieste in blocco.

Da questo punto in avanti, tutti i dati trasmessi dal client sono
incapsulati in *MCS Send Data Request PDU*. Mentre i dati trasmessi
dal server sono incapsulati in *MCS Send Data Indication PDU*. Questo
in aggiunta al fatto che i dati sono incapsulati in *X.224 Data PDU*.

**4. RDP Security Commencement** -
A questo punto inizia la
comunicazione crittografata, se è in forza la encryption con
il meccanismo di sicurezza standard di RDP
(sezione [5.3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/8e8b2cca-c1fa-456c-8ecb-a82fc60b2322)).

Il client capisce dal pacchetto GCC Conference Create Response se questa
crittografia è in forza e riceve in esso anche la chiave pubblica del server
e un numero random di 4 byte generato dal server.  
Ora il client genera un numero random di 4 byte e lo comunica al server
in modo crittografato. In seguito i due numeri sono usati (sia dal client
che dal server) per generare chiavi di sessione con le quali il traffico
RDP d'ora in avanti sarà crittografato.

Il suddetto numero random generato dal client è trasmesso al server
all'interno di un *Security Exchange PDU*
([2.2.1.10](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/9cde84cd-5055-475a-ac8b-704db419b66f)).

Se la encryption con questo meccanismo è in forza,
tutti i dati trasmessi in seguito avranno un *security header*.  
Fanno eccezione alcuni PDU che hanno sempre un *security header*, anche se
la encryption con questo meccanismo non è in forza.  
Inoltre alcune comunicazioni da server a client non possono venire
criptate.  
Tutte le comunicazioni da client a server possono (e devono) essere
criptate, ma per le PDU di licenza questo è opzionale.

**5. Secure Settings Exchange** -
I dati sensibili del client (come username, password, e
cookie di auto-riconnessione) sono trasmessi al server
dentro un *Client Info PDU*
([2.2.1.11](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/772d618e-b7d6-4cd0-b735-fa08af558f9d)).

**6. Optional Connect-Time Auto-Detection** -
Un rilevamento automatico delle caratteristiche della rete (latenza,
larghezza di banda, ...) può essere fatto in questo momento (durante la
sequenza di connessione).  
Vengono scambiati tra client e server una collezione di PDU
([2.2.14](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/925b8e3a-fe60-43fe-aaff-29f07dc18993))
entro un determinato periodo di tempo con sufficiente volume di dati
per avere risultati statisticamente rilevanti.

**7. Licensing** -
Obiettivo di questa fase è che il server trasferisce al client una licenza
perché esso la memorizzi e la possa presentare nelle prossime connessioni.  
La sequenza di operazioni e di pacchetti trasmessi dipende da come il server
intende gestire il meccanismo di licenza.  
Questo documento assume che il client non riceve una licenza da
memorizzare.  
Per info dettagliate si veda
[1.3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpele/74d92d24-56cd-44ef-9c34-06a9346fa874)
di "`[MS-RDPELE]`"

**8. Optional Multitransport Bootstrapping** -
A questo punto il server può decidere di iniziare connessioni
multitransport come definisce l'estensione Multitransport `[MS-RDPEMT]`
(overview [1.3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpemt/5aa61c5c-e864-4ec6-bd80-e1df51555a48)).

L'estensione Multitransport `[MS-RDPEMT]` permette la realizzazione di
connessioni multiple su diversi canali side-band. Queste connessioni
possono essere di diverso tipo, poggiare su diversi protocolli per
sfruttarne al meglio le caratteristiche a seconda del tipo di messaggi
che si scambiano il server e il client e del loro uso.  
Nella fase iniziale (RDP Connection Sequence) tramite un messaggio dal
server al client (*Initiate Multitransport Request PDU*,
[2.2.15.1](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/a6abf1a2-f0ea-427e-bf02-fae7beb09b6a))
il client viene istruito su quali connessioni multitransport sicure può
cercare di realizzare e le necessarie credenziali.

In alcuni casi il server si accorge delle avvenute connessioni nei
canali side-band e prosegue. In altri casi il client invia un
*Multitransport Response PDU*
(section [2.2.15.2](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/fbf85772-51e9-4458-931d-c05b1d561e08))
al server per farlo proseguire.

**9. Capabilities Exchange** -
The server sends the set of capabilities it supports to the client in a Demand Active PDU (section 2.2.1.13.1). The optional Monitor Layout PDU (section 2.2.12.1) is sent by the server after the Demand Active PDU. The client responds to the Demand Active PDU with its capabilities by sending a Confirm Active PDU (section 2.2.1.13.2). 

### Dettagli delle strutture

**X.224 Connection Request PDU**

La sezione 2.2.1.1 delle specifiche "`[MS-RDPBCGR]`" definisce la struttura
del X.224 Connection Request PDU.

La prima parte di questo PDU è un *TPKT Header*, come specificato
dallo standard "`[T123]`" section 8. È costituito da 4 byte: il primo è
la versione dello standard T.123, vale a dire 3. Il secondo è
riservato. Gli ultimi 2 indicano la lunghezza totale del PDU.

...

**MCS Connect Initial PDU**

La sezione 2.2.1.3 delle specifiche "`[MS-RDPBCGR]`" definisce la struttura
del MCS Connect Initial PDU.

Questo PDU incapusla un *GCC Conference Create Request*, che poi incapsula
dei blocchi concatenati di dati di *settings* (impostazioni).

La prima parte di questo PDU è di nuovo un *TPKT Header*.

...

## Riferimenti trovati nel codice di FreeRDP

Nella sezione 1.3.1.1 delle specifiche del protocollo "`[MS-RDPBCGR]`"
viene descritta la *RDP Connection Sequence*.  
[link al doc](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/023f1e69-cfe8-4ee6-9ee0-7e759fb4e4ee)

Vedo una corrispondenza nel file `libfreerdp/core/connection.c` nel progetto
FreeRDP.  
Nel commento all'inizio del file è riportata la medesima sequenza di operazioni.
