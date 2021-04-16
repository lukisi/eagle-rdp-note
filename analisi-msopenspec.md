# Studio specifiche MS RDS in relazione al progetto FreeRDP

Ho iniziato a leggere la documentazione di Microsoft per le specifiche del
protocollo "`[MS-RDSOD]`: Remote Desktop Services", in particolare la versione
7.0 del protocollo reperibile a questo
[link](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdsod/072543f9-4bd4-4dc6-ab97-9a04bf9d2c6a).

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

In essa viene descritta la *RDP Connection Sequence*. Il primo passo è
la trasmissione dal *client* al *Session Host* di un
*X.224 Connection Request PDU*. Il *Session Host* risponde con un
*X.224 Connection Confirm PDU*.

La sezione 2.2.1.1 dello stesso "`[MS-RDPBCGR]`" definisce la struttura del
X.224 Connection Request PDU.  
La prima parte di questo PDU è un *TPKT Header*, come specificato
dallo standard "`[T123]`" section 8.

test

## Riferimenti trovati nel codice di FreeRDP

Nella sezione 1.3.1.1 delle specifiche del protocollo "`[MS-RDPBCGR]`"
viene descritta la *RDP Connection Sequence*.

Vedo una corrispondenza nel file `libfreerdp/core/connection.c` nel progetto
FreeRDP.  
Nel commento all'inizio del file è descritta tale sequenza:

*   Connection Initiation: The client initiates the connection by sending the server a Class 0 X.224 Connection Request PDU (section 2.2.1.1).
*   The server responds with a Class 0 X.224 Connection Confirm PDU (section 2.2.1.2).
*   From this point, all subsequent data sent between client and server is wrapped in an X.224 Data Protocol Data Unit (PDU).

