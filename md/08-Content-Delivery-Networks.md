# Reti di Distribuzione dei Contenuti Multimediali su Internet

L'architettura Web ha permesso uno sviluppo notevole nella diffusione di contenuti grazie al suo paradigma semplice ed efficace. Questa architettura consente il trasferimento di contenuti eterogenei come testo, audio, video e immagini. Storicamente, i contenuti web sono stati distribuiti attraverso server centralizzati (server di origine) nella rete backbone. Questa centralizzazione genera un carico significativo sia sul server che sulla rete quando molti client accedono alle stesse informazioni, un problema che può essere evitato o ridimensionato.

## 8.1 - Utilizzo dei proxy

L'introduzione di sistemi che replicano i contenuti (sistemi di caching) e si interpongono tra client e server di origine, chiamati Proxy o Application Proxy, è una pratica che tende a migliorare il sistema di distribuzione dei contenuti. Una possibilità è utilizzare i reverse proxy come front-end del server da parte dei fornitori di contenuti per ridurre il carico del server nella diffusione di contenuti statici.

I forward proxy sono invece sistemi intermedi con funzioni di replica dei contenuti, posizionati vicino ai client e distribuiti nella rete aziendale o dall'ISP nella propria rete. Questa collocazione aiuta a ridurre la latenza di accesso e il carico di rete per i contenuti popolari. Entrambi i tipi di proxy vengono chiamati genericamente "cache".

<div align="center">
  <img src="./images/08-1.png" width="450">
</div>

## 8.2 - Strategie di Replica e Gestione dei Contenuti

La replica dei contenuti rappresenta una strategia fondamentale per migliorare l'efficienza e la rapidità di accesso alle informazioni. Questo processo risulta particolarmente efficace quando i contenuti presentano coerenza, validità e sono richiesti da diversi utenti in varie posizioni geografiche e temporali specifiche.

### Modalità di Caching e Tipologie di Accesso

Esistono due approcci principali alla replica dei contenuti:
- **Sistema Pull**: i contenuti vengono replicati nella cache in modo opportunistico quando richiesti da un utente
- **Sistema Push**: le repliche vengono programmate e distribuite sistematicamente

Parallelamente, l'accesso alla cache può avvenire secondo due paradigmi distinti:
- **Transparent Caching**: le cache operano in modo invisibile ai client, intercettando il traffico in transito
- **Explicit Caching**: i client sono configurati specificamente per indirizzare le richieste verso sistemi intermedi che gestiscono il caching

### Il Principio del Caching e la Località

Data la limitata capacità di archiviazione delle cache posizionate in prossimità degli utenti, è cruciale replicare solo i contenuti più frequentemente acceduti. La popolarità dei contenuti diventa quindi un criterio determinante nella strategia di caching.

Gli accessi ai contenuti seguono spesso modelli di correlazione, caratterizzati da proprietà di località:
- **Località Temporale**: un'informazione consultata in un determinato momento ha alta probabilità di essere riacceduta in tempi brevi
- **Località Spaziale**: un'informazione richiesta da una specifica posizione geografica tende ad essere consultata anche da punti adiacenti

### Meccanismi di Controllo e Coerenza delle Repliche

Per garantire l'allineamento e l'affidabilità delle repliche, vengono implementati tre meccanismi principali:
- **Invalidazione**: basata sul tempo di scadenza predefinito dal server di origine, determina quando una replica non è più considerata valida
- **Freschezza**: assicura che una replica nella cache sia ancora attuale e non obsoleta
- **Validazione**: consente di verificare la validità di una replica anche dopo la scadenza del suo tempo di vita prestabilito

### Architetture di Caching Cooperativo

In caso di "cache miss" (contenuto non disponibile localmente), anziché rivolgersi direttamente al server di origine, una cache può richiedere il contenuto ad altre cache. Questo approccio cooperativo si articola in due configurazioni:
- **Flat**: tutte le cache si trovano sullo stesso livello gerarchico
- **Gerarchico**: le cache sono organizzate in livelli, con una distribuzione dei contenuti strutturata ad albero

Questa impostazione integrata permette di ottimizzare la distribuzione dei contenuti, riducendo il carico sui server di origine e migliorando i tempi di risposta per gli utenti finali.

## 8.3 - Direttive HTML e HTTP

Le direttive HTML e HTTP consentono ai client e ai server di impostare la modalità di caching dei contenuti. Le direttive HTML, come `<meta http-equiv="pragma" content="no-cache">`, sono facili da controllare per gli autori di pagine web ma sono limitate agli oggetti HTML. Le direttive HTTP invece, come i campi dell'header HTTP (Cache-control:, Expires:, If-Modified-Since:), sono più facili da gestire dalle cache e sono più comuni (non ristretti a HTML ma a tutto HTTP).

Esistono direttive HTTP imperative che hanno la precedenza su altri controlli di cache. Nelle richieste e nelle risposte troviamo:
- **cache-control: no-store**: impedisce il caching degli oggetti
- **cache-control: no-transform**: impedisce alla cache di eseguire trasformazioni sui dati

Solo nelle richieste:
- **cache-control: only-if-cached**: richiede l'uso esclusivo del contenuto memorizzato nella cache

## 8.4 - Ciclo di vita dei contenuti e validazione

Nel ciclo di vita dei contenuti, diverse direttive HTTP svolgono ruoli importanti:
- **Last-modified**: istante in cui il contenuto è stato modificato dal server di origine
- **Date**: indica l'istante in cui l'oggetto è stato inviato dal server di origine alla cache
- **Expires**: stima del server del tempo in cui le copie devono essere sostituite
- **Age**: tempo trascorso dall'oggetto nella cache

<div align="center">
  <img src="./images/08-2.png" width="450">
</div>

I client possono richiedere contenuti con specifiche esigenze in termini di freschezza/età:
- **Max-age**: il client potrebbe non volere informazioni dopo un determinato tempo di obsolescenza
- **Min-fresh**: il client si assicura che il contenuto sia sufficientemente lontano dallo stato di scadenza
- **Max-stale**: il client potrebbe accettare un contenuto leggermente obsoleto

<div align="center">
  <img src="./images/08-3.png" width="450">
</div>

La validazione è la verifica effettuata per capire se, una volta scaduto il tempo di scadenza, una copia è ancora utilizzabile. Se il tempo di scadenza è stato raggiunto per il contenuto richiesto:
1. La cache invia una richiesta GET con if-modified-since (data della copia memorizzata nella cache) e if-none-match (Etag della copia memorizzata nella cache)
2. Il server risponde con il contenuto (se è stato modificato) o con il codice di risposta HTTP/1.0 304, "Not Modified"

<div align="center">
  <img src="./images/08-4.png" width="250">
  <img src="./images/08-5.png" width="600">
</div>

## 8.5 - Eterogeneità dei contenuti

Esistono tipi eterogenei di contenuti:
- **Contenuti statici**: relativamente stabili nel tempo (HTML, immagini, archivi)
- **Contenuti volatili**: modificati frequentemente, periodicamente o da eventi correnti (notizie, eventi sportivi, borse)
- **Contenuti dinamici**: creati dinamicamente in base alla richiesta del client (motori di ricerca, e-commerce)

I contenuti multimediali, cioè contenuti audio/video tipicamente più grandi di altri tipi di contenuti (videoclip, mp3, animazioni Flash), sono solitamente statici.

Una parte significativa dei contenuti (>50%) è "non memorizzabile nella cache". Le principali fonti di non memorizzabilità sono:
- **Volatilità**: cambiano frequentemente nel tempo e sarebbe costoso allineare le copie memorizzate nella cache
- **Dinamismo**: le cache non sono generalmente progettate per generare contenuti dinamicamente
- **SSL**: i dati crittografati non possono essere elaborati nella cache
- **Pubblicità/Analisi**: ad esempio, il proprietario di un banner pubblicitario vuole misurare il numero di clic

Tuttavia, la maggior parte dei contenuti volatili o dinamici ha dimensioni modeste, mentre i contenuti statici e multimediali sono voluminosi.

## 8.6 - Content Delivery Network (CDN)

Una CDN è un'infrastruttura creata per distribuire efficacemente i contenuti dei server web più popolari agli utenti di Internet. Le CDN si basano sulla distribuzione programmata e intelligente di repliche dei contenuti del server principale del Content Provider (server di origine) a una molteplicità di server disposti sulla rete da un CDN Provider.

<div align="center">
  <img src="./images/08-6.png" width="450">
</div>

Il servizio CDN è offerto dai CDN Provider ai Content Provider che hanno contenuti popolari che richiedono una diffusione ampia e capillare. Il servizio CDN mira a migliorare le prestazioni:
- Ridurre la latenza di accesso ai contenuti
- Riduzione della banda occupata sulla rete


### Componenti dell'architettura
L'architettura CDN comprende diversi componenti:

1. Componenti di distribuzione dei contenuti:
   - Server di origine e set di server replica (cache)

2. Componente di distribuzione dei contenuti:
   - Replica il contenuto del server originale sui server Replica e mantiene la coerenza

3. Componente di routing delle richieste:
   - Dirige le richieste degli utenti a un server (origine o replica)
   - Interagisce con il componente di distribuzione per mantenere una copia aggiornata del contenuto

4. Componente di accounting:
   - Mantiene i log di accesso degli utenti
   - Esegue l'analisi del traffico e consente al Content Provider di addebitare

### Posizionamento dei server replica e routing delle richieste

Il posizionamento dei server replica è un problema di ottimizzazione, una variante del problema k-medians (che è NP-hard, ma esistono buone euristiche e algoritmi approssimativi). Dato un insieme di punti (utenti) e k (k = numero di server replica), bisogna trovare k centri tali che i cluster determinati (distanza dal centro/server) siano minimizzati (ad es.: distanza media, latenza ecc.).

Per quanto riguarda il routing delle richieste, il sistema Domain Name Server (DNS) viene utilizzato per dirigere una richiesta client a uno specifico server cache CDN. Si utilizzano due meccanismi:
- Reindirizzamento DNS
- Riscrittura URL

Nel reindirizzamento DNS, il DNS autorevole del sito web può delegare la risoluzione dell'hostname a un indirizzo IP a un name server controllato dalla CDN, o direttamente risolvere un indirizzo a un server cache CDN. In entrambi i casi, la scelta di uno specifico server cache CDN viene effettuata con la traduzione da hostname a indirizzo IP, e la scelta è fatta dal sistema DNS interno della CDN.

Nella riscrittura URL, il Content Provider riscrive gli URL presenti nella pagina HTML per far apparire che gli oggetti incorporati si trovino su un server cache. Così ci sarà bisogno di una risoluzione specifica dell'hostname dei vari oggetti incorporati, il cui name server autorevole è sotto il controllo della CDN. È possibile utilizzare più di un server cache per gli oggetti incorporati di una pagina.

## 8.11 - Akamai e architetture CDN

Akamai è un'azienda americana che si occupa di servizi Internet. Nata nel 1998 in Massachusetts, è il più importante CDN Provider al mondo. Ha la più grande CDN al mondo, con numerosi Content Provider che utilizzano la CDN Akamai per la diffusione dei loro contenuti. Tra le varie aziende che hanno una partnership commerciale con Akamai si possono citare Apple, Facebook e Twitter.

L'architettura Akamai utilizza il routing indiretto e la riscrittura URL. Un sito web che vuole avere parte del suo contenuto distribuito da Akamai deve rinominare gli URL relativi ad essi con un prefisso specifico. La risoluzione dell'hostname a un indirizzo IP di un server cache Akamai viene eseguita dal DNS di Akamai. Il server cache scelto è "vicino" al client e non dovrebbe essere sovraccarico. Akamai esegue misurazioni e test per ottenere una mappa di Internet, in questo modo, il DNS può stabilire il server cache ottimale da cui ottenere un contenuto specifico per un utente specifico.

L'ARL (Akamai Resource Locator) è lo strumento che trasforma l'URL in un formato specifico per Akamai. In questo modo, il client accede ai server cache di Akamai e non al server di origine. Se il server Akamai non ha il contenuto memorizzato nella cache, questo viene richiesto al server di origine.

<div align="center">
  <img src="./images/08-7.png" width="450">
</div>

Akamai fornisce due livelli di reindirizzamento DNS:
1. Akamai Top-Level Name Server (TLNS): localizzati con 4 negli Stati Uniti, 3 nell'UE e 1 in Asia. I TLNS rispondono con un LLNS, scelto da un set di 8 LLNS vicini all'utente.
2. Akamai Low-Level Name Server (LLNS): puntano agli Akamai Edge Server che consegnano il contenuto ed eseguono il bilanciamento del traffico.

<div align="center">
  <img src="./images/08-8.png" width="350">
</div>

## 8.12 - Architetture di YouTube e Netflix

L'architettura di YouTube è organizzata con una struttura di cache a tre livelli: primario, secondario e terziario. Il bilanciamento del carico dei server è basato su informazioni di localizzazione. Ci sono circa 50 cache, di cui 40 primarie (circa 10 presso gli ISP), 8 secondarie e 5 terziarie.

<div align="center">
  <img src="./images/08-9.png" width="450">
</div>

Netflix, d'altra parte, ha il proprio Datacenter per funzioni di gestione (ad esempio registrazione e prezzi), mentre per il resto si basa sul Cloud AWS (Amazon Web Service) di Amazon per i server di streaming video. Utilizza tre CDN per la distribuzione di contenuti video con codifica scalabile.

<div align="center">
  <img src="./images/08-10.png" width="450">
</div>
