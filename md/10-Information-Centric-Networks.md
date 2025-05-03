# Modelli per la distribuzione dei contenuti: Valutazione delle prestazioni delle reti incentrate sui contenuti

## 10.1 - Evoluzione del traffico Internet

Il traffico Internet ha subito una trasformazione radicale negli ultimi decenni. Originariamente concepito come mezzo di comunicazione tra host, Internet è diventato principalmente un'infrastruttura per la distribuzione di contenuti. I dati statistici mostrano una crescita esponenziale del traffico legato ai contenuti multimediali, con servizi di streaming video e piattaforme di condivisione che rappresentano una percentuale sempre maggiore del traffico globale.

Questa crescita ha portato alla necessità di ripensare le architetture di rete per adattarle alle nuove esigenze degli utenti, che sono sempre più interessati al contenuto stesso piuttosto che alla sua origine.

<div align="center">
  <img src="./images/10-1.png" width="450">
</div>

## 10.2 - Architettura CDN

L'architettura di una CDN come Akamai si basa su un sistema complesso che include:

- Server di origine (Origin server) che ospitano i contenuti originali
- Server replica distribuiti geograficamente
- DNS autoritativo per gestire i domini (come akamaihd.net)
- Integrazione con DNS locali (LDNS) degli ISP

<div align="center">
  <img src="./images/10-2.png" width="450">
</div>

Il processo di funzionamento segue un flusso preciso:
1. Un utente richiede una pagina web da un fornitore di contenuti
<div align="center">
    <img src="./images/10-3.png" width="450">
</div>

2. Il server risponde con HTML che include riferimenti a risorse ospitate sulla CDN
<div align="center">
  <img src="./images/10-4.png" width="450">
</div>

3. Il browser dell'utente esegue query DNS per risolvere questi domini CDN
<div align="center">
  <img src="./images/10-5.png" width="400">
  <img src="./images/10-6.png" width="400">
</div>

4. Il sistema di routing delle richieste della CDN sceglie il server replica ottimale basandosi su:
   - Località (preferendo server più vicini)
   - Carico (per evitare congestione del server e della rete)
   - Disponibilità dei contenuti (per massimizzare il tasso di hit)
  
5. Il server replica fornisce il contenuto, sia dalla cache (hit) che recuperandolo dal server di origine (miss)
<div align="center">
  <img src="./images/10-7.png" width="400">
  <img src="./images/10-8.png" width="400">
</div>

## 10.3 - Sfide delle CDN

Le CDN affrontano diverse sfide significative:

1. I meccanismi di routing delle richieste sono componenti critici:
   - Possono essere basati su DNS, riscrittura URL o reindirizzamento HTTP

2. La gestione di una CDN comporta costi elevati:
   - Ingenti spese per la distribuzione dei server replica
   - Costosa infrastruttura di "mapping" per dirigere le richieste

3. La sicurezza presenta complessità non banali:
   - Utilizzo di certificati condivisi
   - Necessità di trasferire in modo sicuro i contenuti sotto domini specifici

## 10.4 - Information-Centric Networks (ICN)

Le ICN rappresentano un insieme di proposte per il "Future Internet" che mirano a risolvere le limitazioni delle architetture attuali:

Come le CDN, le ICN propongono di:
- "Appiattire" Internet
- Replicare i contenuti al "bordo" della rete, più vicino agli utenti finali

A differenza delle CDN:
- Le ICN sono gestite da molti operatori in modo distribuito
- Sono generalmente progettate come protocolli di rete radicalmente nuovi (con alcune eccezioni)

Tutte le proposte ICN condividono principi comuni:
1. Un nuovo spazio dei nomi orientato ai contenuti, passando da indirizzi basati sulla localizzazione (indirizzi IP) a nomi di contenuti indipendenti dalla localizzazione
2. Funzionalità di caching aggiunte ai nodi di rete
3. Possibilità di recuperare lo stesso contenuto da molte fonti diverse

## 10.7 - Content-Centric Networking (CCN/NDN)

Il Content-Centric Networking (CCN) sviluppato da PARC e il progetto Named Data Networking (NDN) mirano a creare un nuovo "vita stretta" per la clessidra di Internet, spostando il focus dalla connessione tra host ai contenuti stessi.

<div align="center">
  <img src="./images/10-9.png" width="350">
</div>

Il sistema di naming in CCN si basa su strutture gerarchiche, opache alle applicazioni. Ogni nome è una lista di componenti di lunghezza variabile, ad esempio:
`/unibg.it/martignon/presentations/2018apr09.pdf/_v<timestamp>/_s2`

<div align="center">
  <img src="./images/10-10.png" width="450">
</div>

CCN prevede due tipi di pacchetti:
- Interest Packet: utilizzati per richiedere contenuti
- Data Packet: utilizzati per fornire i contenuti richiesti

<div align="center">
  <img src="./images/10-11.png" width="450">
</div>

## 10.8 - Il modello di nodo CCN

Il modello di nodo CCN è caratterizzato da tre tabelle principali:

1. FIB (Forwarding Information Base): indirizza gli Interest Packet verso potenziali fonti del contenuto
2. PIT (Pending Interest Table): tiene traccia degli Interest in attesa di risposta
3. CS (Content Store): memorizza in cache i contenuti per riutilizzi futuri

Le "Face" sono interfacce logiche che possono rappresentare connessioni fisiche o applicazioni in esecuzione su un nodo.

Il processo di elaborazione di un Interest e Data packet segue un flusso ben definito che permette l'inoltro efficiente delle richieste e la distribuzione dei contenuti, con funzionalità intrinseche come:
- Forwarding multipath
- Evitamento dei loop grazie alla PIT e al campo "nonce" negli Interest Packet
- Aggregazione delle richieste per lo stesso contenuto
- Caching distribuito che riduce il carico sui server originali

## 10.9 - Sicurezza in NDN/CCN

CCN implementa la sicurezza attraverso tre proprietà fondamentali:

1. Integrità dei dati: garantire che il contenuto sia completo e non corrotto
2. Provenienza: verificare che il produttore sia affidabile per fornire questo contenuto
3. Rilevanza: assicurare che il contenuto risponda alla domanda posta dal consumatore

A differenza dell'Internet TCP/IP tradizionale, dove si protegge il canale di comunicazione tra i due endpoint, in CCN la sicurezza è integrata nel contenuto stesso.

Solo i pacchetti Data sono firmati. La firma crea un collegamento tra:
- Il nome del contenuto
- I dati stessi
- La chiave privata del produttore

Questo approccio permette di verificare l'integrità e la provenienza del contenuto indipendentemente dal percorso attraverso cui è stato ricevuto.

## 10.10 - Problemi di sicurezza nelle ICN

Nonostante i vantaggi, le attuali proposte CCN presentano alcune limitazioni in termini di sicurezza:

- Mancanza di meccanismi per garantire comunicazioni confidenziali
- Difficoltà nel tracciare l'accesso ai contenuti
- Scarso supporto per l'evoluzione delle politiche di accesso

Per risolvere questi problemi è stato proposto ConfTrack-CCN, un sistema che implementa:
- Un primo livello di crittografia per garantire la confidenzialità
- Un secondo livello per garantire la tracciabilità
- Derivazione delle chiavi per supportare l'evoluzione delle politiche
- Compatibilità con i meccanismi di caching