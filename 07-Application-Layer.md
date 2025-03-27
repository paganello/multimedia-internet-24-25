# Livello Applicativo

Il livello applicativo dei protocolli di rete gestisce la comunicazione tra processi applicativi sui nodi della rete. Questo livello comprende numerosi protocolli e servizi fondamentali, tra cui HTTP, FTP, SMTP, DNS e le architetture client-server e peer-to-peer.

Le applicazioni di rete devono:
- Essere eseguite su diversi sistemi terminali.
- Comunicare su una rete senza coinvolgere dispositivi del core della rete (router, switch, etc.).

## 7.1 - Comunicazione tra Processi

Un processo è un programma in esecuzione su un host. La comunicazione tra processi avviene tramite:

- **Inter-Process Communication (IPC)** per processi sullo stesso host.
- **Scambio di messaggi** per processi su host diversi.

In questo secondo caso le informazioni che costituiscono i messaggi transitano nella rete sotto forma di PDU incapsulate in header di piu basso livello.

<div align="center">
  <img src="./images/07-1.png" width="450">
</div>

Per questo motivo ogni processo viene associato ad un SAP (Service access point), che idendifica il punto di arrivo delle PDU dai livelli inferiori che hanno come payload il contenuto con destinatario il processo in questione.

Tutto il meccanismo intermedio tra livello fisico e il processo viene gestito dall'OS.

<div align="center">
  <img src="./images/07-2.png" width="450">
</div>

Nasce cosi il concetto di socket, ovvero quell'identificativo univoco (idealmente una porta) che viene assegnato ad un determento processo all'interno di un OS e che viene sfuttato per ricevere e inviare i pacchetti.

Allontandoci ulteriormente, ovvero dal punto di vista di internet, ci rendiamo conto che ogni processo, per essere raggiungibile, deve avere un identificativo univoco; ovvero qualcosa che identifichi quel determinato processo in tutto internet. Questo fantomatico identificativo e' l'assocciazione congiunta di **IP Address + Port Number**, dove il port number identifica il processo.

E.g: per raggiungere il processo Nginx (webserver) che rende diponibile il sito www.unibg.it abbiamo bisogno `dell'ip di unibg.it` + `porta associata al socket di Nginx sul server (80 di default)`, in altre parole, per raggiungere il sito, il nostro computer sta *chiamando* `unibg.it:80`

## 7.2 - Servizi di Trasporto per le Applicazioni

Le applicazioni hanno requisiti di trasporto differentim in base alle loro caratteristiche:
- **Affidabilità**: alcune (file transfer) richiedono zero perdita di dati, altre (streaming) possono tollerare qualche perdita.
- **Tempi di risposta**: VoIP e giochi interattivi necessitano di bassa latenza.
- **Banda minima garantita**: richiesto per streaming video.

Per questo motivo esistono diversi protocolli a diversi livelli dello stack TCP/IP (in questo caso *Transport*) che vengono incontro alle piu eterogenee necessita':

TCP offre i seguenti servizi:
1. Orientamento alla connessione
2. Sicurezza della ricezione
3. Controllo di flusso
4. Gestione della congestione


UDP d'altra parte non offre nessuno dei servizi che offre TCP, cio rende UDP molto piu unsafe e non adatto a quegli ambiti che necessitano un orientamento alla connessione e affidabilità nella ricezione. D'altra parte UDP è particolarmente indicato per la realizzazione di scambi di dati leggeri e con bassa latenza, proprio per la sua caratteristica non avere una gestione dei pacchetti.

<div align="center">
  <img src="./images/07-3.png" width="450">
</div>

## 7.3 - Architettura Client-Server
Il principale obbiettivo nella comunicazione tra processi remoti è lo scambio di servizi, questo si traduce in dispositivi che offrono servizi (Servers) e dispositivi che ne fanno uso (Clients).

Nel modello client-server:
- Il **client** invia richieste e interpreta risposte.
- Il **server** riceve richieste ed elabora risposte.

<div align="center">
  <img src="./images/07-4.png" width="450">
</div>

Come abbiamo visto essiste una profonda differenza tra programma e processo, il primo rappresenta il codice mentre il secondo un istanza di esecuzione dello stesso. un processo (nel caso Client-Server) puo essere un deamon, ovvero un entità del programma in continua esecuzione che attende di servire un client. il processo diventa cosi in questo caso quell'entita' alla quale facevamo riferimento sopra, ovvero colui che ha associato un socket che lo renda unico nell'OS e a sua volta in internet.

Tipicamente se un processo utilizza TCP allora inplemententerà probabilmente un meccanismo di parallelismo per le risponte ai client, mentre se opera UDP probabilmente risponderà ai client in maniera seriale.

## 7.4 - Architettura Peer-to-Peer (P2P)

Nel modello P2P un client funge anche da server (e quindi posside le caratteristiche descritte sopra) in quanto:
- Non esiste un server centrale, ma tutti sono server distribuiti.
- I peer comunicano direttamente, ogni Client e' a sua volta server per qualcun'altro.
- Il modello è altamente scalabile ma difficile da gestire.

<div align="center">
  <img src="./images/07-5.png" width="250">
</div>

Esempi:
- **Gnutella**: rete distribuita senza server centrale.
- **BitTorrent**: condivisione di file basata su "tracker".

## 7.5 - Protocollo HTTP e Navigazione Web

HTTP è un protocollo client-server:
- Il client richiede oggetti identificati da un URL.
- Il server restituisce gli oggetti richiesti.

<div align="center">
  <img src="./images/07-6.png" width="450">
</div>

L'operazione è **stateless** (il server non mantiene memoria delle richieste passate) se non nei log di sistema.


#### Basi della connessione HTTP

Nelle sue versioni base (quindi precedenti a HTTP/3) HTTP utilizza protcollo TCP per il trasporto, instaurando connessioni nelle seguenti modalita':
- *Non persistente*: una nuova connessione TCP per ogni richiesta (HTTP/1), si possono avere piu connessioni TCP parallele.
  <div align="center">
    <img src="./images/07-7.png" width="450">
  </div>
- *Persistente*: connessione mantenuta per più richieste (HTTP/1.1, HTTP/2), ogni connessione puo essere chiusa dal server in base al timeout su esso configurato.
  - *Senza pipelining*: il client intraprende una nuova richiesta solo quando la richiesta precedente è stata terminata.
  - *Con pipelining*: vengono create piu comunicazioni parallele con il servers.
  <div align="center">
    <img src="./images/07-8.png" width="450">
  </div>

Come si puo immaginare, sfruttare un'unica connessione per piu pachetti permette un notevole risparmio in termini di banda e tempo, proviamo a quantificare:

<div align="center">
  <img src="./images/07-10.png" width="250">
</div>

Cio e' dovuto dal fatto che la richiesta che regola lo stabilimento della comunicazione TCP (che precede la comunicazione HTTP) non viene inutilmente ripetuta ad ogni comunicazione, ma viene riciclato il suo primordiale operato per tutte le comunicazioni future. Cio si traduce in migliormaneti prestazionali.

#### Richieste e Risposte
In HTTP si hanno richieste e risposte, ad ogni richiesta da parte del client esiste una risposta da parte del server. Vediamo alcune richieste:

- `GET`: recupera un documento.
- `POST`: invia dati al server.
- `HEAD`: ottiene solo l'intestazione (informazioni) di un documento.
- `PUT`: Per inviare un documento al servers, l'URL specifica la posizione di store del documento.

<div align="center">
  <img src="./images/07-9.png" width="550">
</div>

Un ruolo altrettanto importante in termini informativi lo hanno gli headers, i quali permettono il trasporto di ulteriori informazioni:

<div align="center">
  <img src="./images/07-11.png" width="450">
</div>

#### Proxy
Un proxy HTTP è un server che funge da intermediario tra un client (come un browser web) e un server di destinazione (come un sito web). Quando un client invia una richiesta HTTP per accedere a un sito, questa richiesta passa prima attraverso il proxy. Il proxy inoltra la richiesta al server di destinazione, ottiene la risposta e la restituisce al client.

Funzioni principali del proxy HTTP:

1. Nascondere l'identità del client: Il server di destinazione vede solo l'indirizzo IP del proxy e non quello del client. Questo offre una certa privacy al client.

2. Cache dei contenuti: Il proxy può memorizzare localmente le risposte per ridurre il tempo di risposta e il carico sui server di destinazione. Se il client richiede lo stesso contenuto, il proxy può restituirlo direttamente senza contattare il server di destinazione.

3. Filtraggio e controllo: Un proxy può essere utilizzato per filtrare i contenuti (es. bloccare l'accesso a determinati siti web), applicare politiche di sicurezza, monitorare il traffico o ottimizzare l'accesso alle risorse.

<div align="center">
  <img src="./images/07-12.png" width="450">
</div>

#### Autenticazione e Cookie

l'autenticazione è il processo con cui un server verifica l'identità di un utente, generalmente tramite credenziali come nome utente e password.

I cookie in HTTP sono piccoli file di testo inviati dal server al browser dell'utente per memorizzare informazioni come sessioni o preferenze. Sono essenziali per gestire l'autenticazione, poiché dopo un login, il server invia un cookie che permette al browser di mantenere l'utente autenticato durante la navigazione. Esistono cookie di sessione (temporanei) e persistenti (che durano oltre la sessione del browser), che possono essere anche di terze parti per il tracciamento delle attività utente. I cookie sono fondamentali per garantire esperienze personalizzate e sicure.

<div align="center">
  <img src="./images/07-13.png" width="450">
</div>

#### Differenze HTTP/1.1 e HTTP/2

| Caratteristica             | HTTP/1.1                              | HTTP/2                                               |
| -------------------------- | ------------------------------------- | ---------------------------------------------------- |
| **Formato**                | Testuale                              | Binario (frame-based)                                |
| **Multiplexing**           | No, una richiesta per connessione     | Sì, una connessione per più stream                   |
| **Compressione Header**    | No, header ripetuti in ogni richiesta | Sì, utilizza HPACK per ridurre overhead              |
| **Server Push**            | No                                    | Sì, il server può inviare dati prima della richiesta |
| **Controllo di flusso**    | Gestito a livello TCP                 | Implementato a livello applicativo                   |
| **Numero di connessioni**  | Più connessioni TCP aperte            | Una sola connessione TCP persistente                 |
| **Unità di comunicazione** | Messaggi HTTP standard                | Frame, messaggi e stream                             |
| **Frame**                  | Non supportati                        | Sì, il frame è l’unità base di comunicazione         |
| **TLS**                    | Facoltativo                           | Richiesto per la maggior parte delle implementazioni |
| **Priorità richieste**     | No, FIFO (First In First Out)         | Sì, con prioritizzazione degli stream                |


Negli **HTTP request header**, possono essere presenti molte **informazioni ridondanti** e di grandi dimensioni, come:
- *Cookie multipli*: inviati con ogni richiesta
- *Diverse intestazioni di autenticazione*: ripetute ogni volta
- *Dati specifici della transazione*: che non cambiano frequentemente

In **HTTP/1.1**, ogni richiesta contiene l’intero header, anche se molte informazioni rimangono invariate. Questo introduce un **overhead** significativo e rallenta le comunicazioni.

Per ridurre l'overhead, **HTTP/2** utilizza **HPACK**, una tecnica di compressione basata su tre strategie principali:

1. *Codifica di Huffman*:
   - Assegna stringhe binarie più corte ai simboli più comuni.
   - **Esempio**: la parola *accept* (che in ASCII occupa 6 byte) viene compressa:
     - *a = 101*, *c = 0*, *e = 111*, *p = 110*, *t = 100*
     - Il risultato finale è **101 0 0 111 110 100**, occupando solo **2 byte** invece di 6.

2. *Indicizzazione*:
   - Gli header più comuni ricevono un **indice numerico**.
   - Il client invia solo il numero associato all'header, invece di ripetere la stringa.
   - **Esempio**: invece di inviare `"User-Agent: Mozilla/5.0"`, il client invia solo un numero.

3. *Codifica Differenziale*:
   - Ogni richiesta successiva invia **solo le differenze** rispetto alla precedente.
   - Se alcuni header rimangono invariati, vengono omessi o abbreviati.
   - **Effetto**: riduzione del **traffico di rete** e miglioramento delle prestazioni.


#### QUIC
QUIC (Quick UDP Internet Connections) è un protocollo di trasporto moderno sviluppato per superare i limiti di TCP, migliorando la velocità, l'affidabilità e la sicurezza delle connessioni su Internet. A differenza di TCP, che richiede più passaggi per stabilire una connessione, QUIC utilizza UDP e integra meccanismi avanzati per ridurre la latenza e ottimizzare il trasferimento dei dati.

Uno dei principali vantaggi di QUIC è la riduzione del tempo di handshake, che combina l'inizializzazione della connessione con la negoziazione della crittografia (basata su TLS 1.3) in un unico passaggio. 

Un altro aspetto chiave di QUIC è la sua capacità di multiplexare più flussi di dati sulla stessa connessione senza il rischio di blocchi. Con TCP, se un pacchetto viene perso, l’intero flusso si ferma fino alla sua ritrasmissione (Head-of-Line Blocking). QUIC elimina questo problema consentendo ai pacchetti di flussi diversi di viaggiare indipendentemente, garantendo così una trasmissione più fluida ed efficiente.

Inoltre, QUIC è progettato per essere più resiliente ai cambiamenti di rete. Se un dispositivo cambia indirizzo IP, ad esempio passando da Wi-Fi a rete mobile, la connessione QUIC rimane attiva senza dover essere ristabilita, cosa che invece TCP non può fare senza interrompere la sessione.

Infine, QUIC è alla base di HTTP/3, la nuova versione del protocollo web, che sfrutta le sue caratteristiche per offrire prestazioni migliori nelle applicazioni web. Sempre più servizi online, tra cui Google, Facebook e Cloudflare, stanno adottando QUIC per garantire un’esperienza utente più veloce e stabile, rendendolo uno degli standard del futuro per le comunicazioni su Internet.

## 7.12 - File Transfer Protocol (FTP)

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