# Livello Applicativo

Il livello applicativo dei protocolli di rete gestisce la comunicazione tra processi applicativi sui nodi della rete. Questo livello comprende numerosi protocolli e servizi fondamentali, tra cui HTTP, FTP, SMTP, DNS e le architetture client-server e peer-to-peer.

# 7.1 - Paradigmi di Comunicazione: Client-Server e Peer-to-Peer

Esistono due principali paradigmi di comunicazione tra applicazioni in rete:
- **Client-Server**: un client richiede un servizio e un server lo fornisce.
- **Peer-to-Peer (P2P)**: ogni nodo può fungere sia da client che da server, scambiando direttamente dati con altri nodi.

# 7.2 - Applicazioni più comuni su Internet

Le applicazioni più diffuse nel web includono:
- **Navigazione Web**: HTTP
- **Posta elettronica**: SMTP
- **Social network**: Facebook, Twitter, Instagram
- **File sharing P2P**: BitTorrent, eMule
- **Streaming video**: Netflix, YouTube
- **Telefonia VoIP**: Skype, Hangouts
- **Videoconferenza, giochi online, login remoto**: TELNET

# 7.3 - Progettazione di applicazioni di rete

Le applicazioni di rete devono:
- Essere eseguite su diversi sistemi terminali.
- Comunicare su una rete senza coinvolgere dispositivi del core della rete (router, switch, etc.).

# 7.4 - Comunicazione tra Processi

Un processo è un programma in esecuzione su un host. La comunicazione tra processi avviene tramite:
- **Inter-Process Communication (IPC)** per processi sullo stesso host.
- **Scambio di messaggi** per processi su host diversi.

# 7.5 - Protocolli di Applicazione e Interazione con i Livelli Inferiori

I protocolli applicativi definiscono:
- Tipologie di messaggi scambiati (richiesta, risposta).
- Sintassi e significato dei messaggi.
- Regole per l'invio e la ricezione di messaggi.

I protocolli applicativi utilizzano i servizi dei livelli inferiori tramite i **Service Access Points (SAPs)** e interagiscono direttamente con il livello di trasporto (TCP/UDP). In base al tipo di traffico che l'applicazione necessita si puo selezione il protocollo di trasporto piu adeguato.

# 7.6 - Indirizzamento dei Processi

Per ricevere messaggi, un processo deve avere un identificatore:
- L'**indirizzo IP** identifica l'host.
- Il **numero di porta** identifica il processo specifico.
- Esempi: HTTP usa la porta 80, SMTP la 25.

# 7.7 - Servizi di Trasporto per le Applicazioni

Le applicazioni hanno requisiti di trasporto differenti:
- **Affidabilità**: alcune (file transfer) richiedono zero perdita di dati, altre (streaming) possono tollerare qualche perdita.
- **Tempi di risposta**: VoIP e giochi interattivi necessitano di bassa latenza.
- **Banda minima garantita**: richiesto per streaming video.

TCP offre i seguenti servizi:
- Orientamento alla connessione
- Sicurezza della ricezione
- Controllo di flusso
- Gestione della congestione


UDP d'altra parte non offre nessuno dei servizi che offre TCP, cio rende UDP molto piu unsafe e non adatto a quegli ambiti che necessitano un orientamento alla connessione e affidabilità nella ricezione. D'altra parte UDP è particolarmente indicato per la realizzazione di scambi di dati leggeri e con bassa latenza, proprio per la sua caratteristica non avere una gestione dei pacchetti.

# 7.8 - Architettura Client-Server
Il principale obbiettivo nella comunicazione tra processi remoti è lo scambio di servizi, questo si traduce in dispositivi che offrono servizi (Servers) e dispositivi che ne fanno uso (Clients).

Nel modello client-server:
- Il **client** invia richieste e interpreta risposte.
- Il **server** riceve richieste ed elabora risposte.

Essiste una profonda differenza tra  programma e processo, il primo rappresenta il codice mentre il secondo un istanza di esecuzione dello stesso. un processo puo essere un deamon, ovvero un entità del programma in continua esecuzione che attende di servire un client.

Tipicamente se un processo utilizza TCP allora inplemententerà probabilmente un meccanismo di parallelismo per le risponte ai client, mentre se opera UDP probabilmente risponderà ai client in maniera seriale.

# 7.9 - Architettura Peer-to-Peer (P2P)

Nel modello P2P:
- Non esiste un server centrale.
- I peer comunicano direttamente.
- Il modello è altamente scalabile ma difficile da gestire.

Esempi:
- **Gnutella**: rete distribuita senza server centrale.
- **BitTorrent**: condivisione di file basata su "tracker".

# 7.10 - Protocollo HTTP e Navigazione Web

HTTP è un protocollo client-server:
- Il client richiede oggetti identificati da un URL.
- Il server restituisce gli oggetti richiesti.

L'operazione è **stateless** (il server non mantiene memoria delle richieste passate).

Modalità di connessione:
- **Non persistente**: una nuova connessione TCP per ogni richiesta.
- **Persistente**: connessione mantenuta per più richieste (HTTP/1.1, HTTP/2).

Possiamo inoltre dividere i metodi di comunicazione **persistente** con il servers in ulteriori 2 categorie:
- *Con pipelining*: vengono create piu comunicazioni parallele con il servers.
- *Senza pipelining*: il client intraprende una nuova richiesta solo quando la richiesta precedente è stata terminata.

In HTTP si hanno richieste e risposte, ad ogni richiesta da parte del client esiste una risposta da parte del server.

Richieste HTTP principali:
- `GET`: recupera un documento.
- `POST`: invia dati al server.
- `HEAD`: ottiene solo l'intestazione (informazioni) di un documento.
- `PUT`: Per inviare un documento al servers, l'URL specifica la posizione di store del documento.

Differenze tra HTTP/1.1 e HTTP/2:


# 7.12 - File Transfer Protocol (FTP)

FTP permette il trasferimento di file:
- Utilizza **due connessioni TCP**:
  - Una per il **controllo** (porta 21).
  - Una per il **trasferimento dati** (porta 20).
- Modalità attiva e passiva per la connessione dati.

Comandi FTP:
- `USER`, `PASS`: autenticazione.
- `RETR`, `STOR`: scaricare e caricare file.
- `LIST`: visualizzare i file disponibili.

# 7.13 - Simple Mail Transfer Protocol (SMTP) e Posta Elettronica

SMTP gestisce l'invio di email:
- Basato su TCP (porta 25).
- Solo testo ASCII; MIME è usato per allegati binari.

Protocolli di accesso alla posta:
- **POP3**: scarica e rimuove email dal server.
- **IMAP**: permette la gestione della posta direttamente sul server.

# 7.14 - Domain Name System (DNS)

DNS converte nomi simbolici in indirizzi IP:
- Struttura **gerarchica e distribuita**.
- Tipi di record DNS:
  - `A`: indirizzo IPv4.
  - `CNAME`: alias.
  - `MX`: mail server.
  - `NS`: name server.

Due modalità di interrogazione:
- **Iterativa**: il server DNS risponde con un riferimento.
- **Ricorsiva**: il server DNS ottiene la risposta per conto del client.

# 7.15 - Conclusione

Il livello applicativo gestisce le principali comunicazioni su Internet, fornendo i protocolli essenziali per la navigazione, lo scambio di file, la posta elettronica e la risoluzione dei nomi. Comprendere questi protocolli è fondamentale per sviluppare e ottimizzare applicazioni di rete.