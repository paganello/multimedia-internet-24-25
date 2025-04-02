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

<div align="center">
  <img src="./images/07-14.png" width="450">
</div>

#### QUIC
QUIC (Quick UDP Internet Connections) è un protocollo di trasporto moderno sviluppato per superare i limiti di TCP, migliorando la velocità, l'affidabilità e la sicurezza delle connessioni su Internet. A differenza di TCP, che richiede più passaggi per stabilire una connessione, QUIC utilizza UDP e integra meccanismi avanzati per ridurre la latenza e ottimizzare il trasferimento dei dati.

Uno dei principali vantaggi di QUIC è la riduzione del tempo di handshake, che combina l'inizializzazione della connessione con la negoziazione della crittografia (basata su TLS 1.3) in un unico passaggio. 

Un altro aspetto chiave di QUIC è la sua capacità di multiplexare più flussi di dati sulla stessa connessione senza il rischio di blocchi. Con TCP, se un pacchetto viene perso, l’intero flusso si ferma fino alla sua ritrasmissione (Head-of-Line Blocking). QUIC elimina questo problema consentendo ai pacchetti di flussi diversi di viaggiare indipendentemente, garantendo così una trasmissione più fluida ed efficiente.

Inoltre, QUIC è progettato per essere più resiliente ai cambiamenti di rete. Se un dispositivo cambia indirizzo IP, ad esempio passando da Wi-Fi a rete mobile, la connessione QUIC rimane attiva senza dover essere ristabilita, cosa che invece TCP non può fare senza interrompere la sessione.

Infine, QUIC è alla base di HTTP/3, la nuova versione del protocollo web, che sfrutta le sue caratteristiche per offrire prestazioni migliori nelle applicazioni web. Sempre più servizi online, tra cui Google, Facebook e Cloudflare, stanno adottando QUIC per garantire un’esperienza utente più veloce e stabile, rendendolo uno degli standard del futuro per le comunicazioni su Internet.

## 7.6 - File Transfer Protocol (FTP)

FTP permette il trasferimento di file:
- Utilizza **due connessioni TCP**:
  - Una per il **controllo** (porta 21).
  - Una per il **trasferimento dati** (porta 20).
- Modalità attiva e passiva per la connessione dati.

<div align="center">
  <img src="./images/07-15.png" width="450">
</div>

Il client, di conseguenza, apre una porta (>1024) che dedica alla comunicazione con il server.

<div align="center">
  <img src="./images/07-16.png" width="450">
</div>

FTP (File Transfer Protocol) utilizza due tipi di connessioni: una connessione di controllo, persistente, che opera sulla porta 21 e viene utilizzata per lo scambio di comandi e risposte tra client e server, e una connessione dati, non persistente (dati), che viene aperta solo quando è necessario trasferire un file o un elenco di directory e chiusa al termine dell'operazione.

L'apertura di una connessione dati può avvenire in due modi.
Nel modo **attivo (Active Mode)**, il client sceglie una porta dinamica e la apre in modalità passiva, comunicandone il numero al server attraverso il comando `PORT`. Il server, utilizzando la porta 20 come origine, stabilisce una connessione verso la porta indicata dal client. Tuttavia, questo approccio può essere problematico in presenza di firewall o NAT, poiché il server deve avviare una connessione verso il client. 
Nel modo **passivo (Passive Mode)**, invece, il client invia il comando `PASV` al server, il quale sceglie una porta dinamica, la apre in modalità passiva e comunica il numero al client. A questo punto, il client avvia una connessione verso la porta specificata del server. Questa modalità è generalmente preferibile, poiché riduce i problemi di compatibilità con firewall e NAT.

Il trasferimento dei file può avvenire in due formati: 
- ASCII, adatto ai file di testo, in quanto permette la conversione dei caratteri tra diversi sistemi operativi.
- Binary, utilizzato per file binari come immagini, eseguibili o archivi compressi, garantendo il trasferimento senza modifiche. 

Inoltre, FTP supporta due modalità di trasmissione: Stream Mode, in cui il file viene inviato come un flusso continuo di byte e la segmentazione è gestita direttamente dal protocollo TCP, e Block Mode, dove il file è suddiviso in blocchi, ciascuno con un'intestazione, offrendo una struttura più definita.

Comandi FTP:
- `USER`, `PASS`: autenticazione.
- `RETR`, `STOR`: scaricare e caricare file.
- `LIST`: visualizzare i file disponibili.

<div align="center">
  <img src="./images/07-17.png" width="450">
</div>


## 7.7 - Simple Mail Transfer Protocol (SMTP) e Posta Elettronica

Le comunicazioni Email globali sono principalemente basate su 3 protocolli distinti:
- **SMTP**
- **POP3**: scarica e rimuove email dal server.
- **IMAP**: permette la gestione della posta direttamente sul server.

In particolare, la comunicazione con e tra Mail servers avviene tramite protocllo SMTP.
<div align="center">
  <img src="./images/07-18.png" width="450">
</div>

### SMTP
Gestisce l'invio di email:
- Basato su TCP (porta 25).
- Solo testo ASCII; MIME è usato per allegati binari.

Quando un server riceve un messaggio proveniente da un client utente:
1. Immagazzina il messaggio in una coda.
2. Apre una comunicazione TCP su porta 25 con il server del destinatario.
3. Invia il messagio.

<div align="center">
  <img src="./images/07-19.png" width="550">
</div>


#### Funzionamento di MIME
SMTP (Simple Mail Transfer Protocol) è il protocollo utilizzato per l'invio di email, ma di base supporta solo messaggi di testo semplice (ASCII). **MIME (Multipurpose Internet Mail Extensions)** è un'estensione di SMTP che permette di inviare contenuti più complessi come allegati, immagini, video e testo formattato (HTML).

MIME introduce degli **header aggiuntivi** nelle email per indicare il tipo di contenuto e la sua codifica. Alcuni degli header più importanti sono:

- **`MIME-Version: 1.0`** → Indica che il messaggio utilizza MIME.
- **`Content-Type`** → Specifica il tipo di contenuto (es. testo, immagine, video, ecc.).
- **`Content-Transfer-Encoding`** → Definisce la codifica del contenuto (es. Base64 per file binari).
- **`Content-Disposition`** → Indica se un allegato deve essere visualizzato inline o come file separato.

Il tipo di encordi puo essere diverso da comunicazione a comunicazione:

**Base64 Encoding** è un metodo di codifica utilizzato per rappresentare dati binari in un formato testuale ASCII. Viene spesso usato in email MIME e in altre applicazioni dove è necessario trasmettere dati binari su canali di comunicazione che supportano solo testo. 
1. Il flusso di bit viene diviso in **blocchi da 24 bit**.
2. Ogni blocco da 24 bit viene suddiviso in **4 gruppi da 6 bit** ciascuno.
3. Ogni gruppo da 6 bit viene convertito in un carattere della **tabella Base64**, che utilizza 64 caratteri diversi (`A-Z`, `a-z`, `0-9`, `+`, `/`).
3. Se il numero di bit non è multiplo di 24, vengono aggiunti byte di padding (`=`) per completare il blocco.

<div align="center">
  <img src="./images/07-20.png" width="450">
</div>
<br>

Il **Quoted-Printable Encoding** è un metodo di codifica usato per rappresentare dati testuali in modo che possano essere trasmessi in modo sicuro su reti che supportano solo caratteri ASCII stampabili.

1. Il flusso di bit viene diviso in **blocchi da 8 bit** (1 byte).
2. Se il byte corrisponde a un carattere ASCII stampabile (33-126, escluso `=`), viene inviato direttamente.
3. Se il byte **non** corrisponde a un carattere ASCII stampabile, viene codificato come **tre caratteri:**
   - Il simbolo `=`
   - La rappresentazione esadecimale del byte (due caratteri).

<div align="center">
  <img src="./images/07-21.png" width="450">
</div>

Esempio di codifica Quoted-Printable:
- La lettera **"A"** (ASCII 65) viene trasmessa direttamente come `A`.
- Il carattere **"è"** (non ASCII, byte `0xE8`) viene codificato come `=E8`.

Quoted-Printable è utile quando si vogliono trasmettere email testuali con caratteri speciali, senza dover trasformare l'intero messaggio in Base64.

## 7.8 - TELNET (TErminal NETwork)

Telnet è un protocollo di rete utilizzato per stabilire connessioni remote tra due dispositivi. È uno dei protocolli più vecchi ed è stato progettato per permettere a un utente di connettersi a un altro computer (o server) e interagire con esso come se fosse localmente presente. Utlizza TCP per il trasferimento dati e connessione.

<div align="center">
  <img src="./images/07-23.png" width="450">
</div>

In Telnet, i caratteri di dati vengono trasferiti in due modalità principali:

1. **Caratteri ASCII con il primo byte '0'**
I caratteri ASCII vengono inviati come sequenze di byte, dove il primo byte è '0', rappresentando valori di caratteri standard come lettere e numeri.

1. **Caratteri ASCII con il primo byte '1'**
Quando i caratteri ASCII hanno il primo byte impostato su '1', significa che sono preceduti da un **byte di controllo speciale**. Questi caratteri sono gestiti in modo diverso rispetto ai normali caratteri ASCII.

#### Caratteri di Controllo
In Telnet, i **caratteri di controllo** sono utilizzati per inviare comandi speciali tra il client e il server. Sono rappresentati da **comandi a 8 bit**, dove il primo bit è impostato su '1'. Questi comandi sono usati per gestire la sessione Telnet e altre funzioni.

Esempi di Comandi di Controllo:
- **IAC (255):** Indica che il prossimo byte è un **carattere di controllo**. In altre parole, il byte successivo dopo IAC deve essere interpretato come un comando speciale di controllo, non come un carattere normale.
- **EC (247):** Questo comando viene utilizzato per **cancellare un carattere** dalla schermata, eliminando l'ultimo carattere visualizzato.

<div align="center">
  <img src="./images/07-24.png" width="450">
</div>


## 7.9 - Domain Name System (DNS)

Il Sistema dei Nomi a Dominio (DNS) è strutturato in modo gerarchico, con ogni livello della gerarchia contiene informazioni di profondità diversa.

Esempio: Risoluzione di www.google.com
Un utente vuole trovare l'indirizzo IP di `www.google.com`. Il processo funziona nel seguente modo:

1. **Server dei Nomi Root:**
   - I server dei nomi root sanno come localizzare i server dei nomi che gestiscono i domini di primo livello (TLD), come `.com`.

2. **Server dei Nomi .com:**
   - I server `.com` sanno come trovare i server dei nomi che gestiscono il dominio `google.com`.

3. **Server dei Nomi google.com:**
   - I server dei nomi `google.com` sanno come risolvere il nome simbolico `www.google.com` nel suo corrispondente indirizzo IP.

Questa struttura gerarchica permette una risoluzione dei nomi efficiente attraverso Internet.

<div align="center">
  <img src="./images/07-25.png" width="450">
</div>

Ecco come viaggia la richiesta attraverso la gerarchia:
<div align="center">
  <img src="./images/07-26.png" width="450">
</div>

#### Tipi di Record DNS Comuni

| Tipo di Record | Nome Completo  | Descrizione                                                                                                                                                                                                                                                      |
| -------------- | -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **A**          | Address Record | Associa un nome di dominio a un indirizzo IPv4. È il record più comune e fondamentale usato per la risoluzione dei nomi in indirizzi IP. Esempio: `example.com. IN A 93.184.216.34`                                                                              |
| **CNAME**      | Canonical Name | Crea un alias per un nome di dominio. Utilizzato per mappare un nome di dominio a un altro nome di dominio anziché direttamente a un indirizzo IP. Utile per servizi che cambiano indirizzo IP frequentemente. Esempio: `www.example.com. IN CNAME example.com.` |
| **MX**         | Mail Exchange  | Specifica i server di posta responsabili per l'accettazione delle email destinate a un dominio. Include anche un valore di priorità per determinare quale server contattare per primo. Esempio: `example.com. IN MX 10 mailserver.example.com.`                  |
| **NS**         | Name Server    | Identifica i server DNS autoritativi per una zona DNS. Questi server contengono informazioni autorevoli su un dominio. Esempio: `example.com. IN NS ns1.example.com.`                                                                                            |

Due modalità di interrogazione:
- **Iterativa**: il client contatta un server che si occupa di gestile la risoluzione nella gerarchia.
- **Ricorsiva**: il client contatta i singoli server che compongolo la gerarchia fino alla risoluzione dell'indirizzo.