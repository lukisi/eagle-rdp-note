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


Nella sezione di esempi (sezione 3.1 "`Example 1: Connecting from an RDP Client to an RD Session Host`") viene detto che come primo passo il
RDP client inizia una connessione al RD Session Host inviando un
`X.224` Connection Request protocol data unit (PDU), come descritto nel
"`[MS-RDPBCGR]`" sezione 1.3.1.1.  
[link al doc](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/023f1e69-cfe8-4ee6-9ee0-7e759fb4e4ee)

In questo documento viene descritta la *RDP Connection Sequence*.

**Connection Initiation** - Il primo
passo è la trasmissione dal *client* al *Session Host* di un
*X.224 Connection Request PDU*
([2.2.1.1](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/18a27ef9-6f9a-4501-b000-94b1fe3c2c10)).
Il *Session Host* risponde con un *X.224 Connection Confirm PDU*
([2.2.1.2](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/13757f8f-66db-4273-9d2c-385c33b1e483)).  
*Nota:* Da questo punto in avanti, tutti i dati scambiati tra client e
server sono incapsulati in *X.224 Data PDU*.

**Basic Settings Exchange** - Di seguito il client
invia un *Multipoint Communication Service (MCS)*
*Connect Initial PDU* con un *GCC Conference Create Request*
([2.2.1.3](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/db6713ee-1c0e-4064-a3b3-0fac30b4037b)).  
Il Session Host risponde con un *MCS Connect Response PDU* con
un *GCC Conference Create Response*
([2.2.1.4](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/927de44c-7fe8-4206-a14f-e5517dc24b1c)).  
Questi pacchetti contengono principalmente delle impostazioni (tra le quali
core data, security data, e network data) che sono comunicate dal client
al server e anche dal server al client.

**Channel Connection** - Di seguito...

...

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
