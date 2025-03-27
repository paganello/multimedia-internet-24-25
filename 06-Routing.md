# Protocolli di Routing Internet

## 6.0 - Concetti Fondamentali del Routing

L'infrastruttura di routing di Internet è composta da Sistemi Autonomi (AS) interconnessi, che sono porzioni di rete gestite da una singola organizzazione. I protocolli di routing si suddividono in due tipi principali:

Un sistema autonomo è una parte della rete gestita da una singola organizzazione, che può contenere più domini di routing. Questi sistemi sono interconnessi e utilizzano protocolli specifici per lo scambio di informazioni di routing.

I protocolli di routing principali si dividono in:
- Protocolli di Gateway Interni (IGP): utilizzati per il routing all'interno di un sistema autonomo
- Protocolli di Gateway Esterni (EGP): utilizzati per il routing tra sistemi autonomi

Un dominio di routing rappresenta una porzione di un sistema autonomo che esegue un singolo protocollo di routing. Alcuni router possono appartenere a più domini di routing e implementare protocolli diversi, facilitando interazioni di rete complesse.

## 6.1 - Routing Information Protocol (RIP)

RIP è un protocollo di routing a vettore di distanza progettato a Berkeley nel 1982 e standardizzato in RFC 1058. Le sue caratteristiche principali includono:

Meccanismo di Funzionamento:
- Utilizza l'algoritmo di Bellman-Ford per calcolare i percorsi più brevi
- Metrica basata sul numero di hop (salti) tra router
- Limitato a un massimo di 16 hop
- Messaggi RIP incapsulati in segmenti UDP sulla porta 520

Versioni Principali:
- RIP versione 1 (originale)
- RIP versione 2 (migliorata)

Caratteristiche RIPv1:
- Formato messaggi semplice
- Comandi: richieste e risposte
- Indirizzi di rete e distanze come elementi principali
- Aggiornamenti periodici (default 30 secondi)

Miglioramenti RIPv2:
- Supporto per subnet mask (routing senza classi)
- Autenticazione
- Indicazione esplicita dell'indirizzo del next hop
- Multicast invece di broadcast
- Tag per identificare l'origine delle rotte

Limitazioni di RIP:
- Metrica molto semplice (solo conteggio hop)
- Inadatto per reti grandi
- Tempo di convergenza lento
- Incapace di utilizzare metriche più complesse come lunghezza coda, ritardi, tasso errori

## 6.2 - Open Shortest Path First (OSPF)

OSPF è un protocollo di routing a stato di link, molto più avanzato rispetto a RIP. Caratteristiche principali:

Meccanismo di Funzionamento:
- Esegue l'algoritmo di Dijkstra in ogni nodo
- Supporta routing gerarchico con aree e area backbone
- Trasportato direttamente su IP (protocollo 89)
- Utilizza il protocollo Hello per monitorare lo stato dei vicini

Tipi di Router OSPF:
- Router interni (all'interno di un'area)
- Router di confine area
- Router backbone
- Router di confine del sistema autonomo

Tipi di Messaggi:
- Hello: gestione stato dei vicini
- Descrizione database: scambio database di rete
- Richiesta stato link
- Aggiornamento stato link
- Acknowledgement stato link

Tipi di Link State Advertisement (LSA):
- Type 1: annunci link router (all'interno di un'area)
- Type 2: annunci link di rete
- Type 3: sommario link di rete
- Type 4: sommario router di confine
- Type 5: link esterni del sistema autonomo

Vantaggi:
- Metriche configurabili dall'amministratore
- Supporto autenticazione
- Bilanciamento carico su rotte con stesso costo
- Rapida convergenza

I router vedono una topologia di rete basata solo sulla distanza, non vedono la topologia reale e quindi nemmeno i dipositivi intermedi nella rete.

## 6.3 - Border Gateway Protocol (BGP)

BGP è il protocollo esterno de facto per l'instradamento su Internet.

Caratteristiche Principali:
- Protocollo vettore di percorso
- "Colla" che tiene insieme Internet
- Consente agli AS di annunciarsi e essere raggiunti

Meccanismo di Funzionamento:
- Scambio di vettori di percorso tra router
- Utilizzato per routing inter-AS
- Decisioni basate su policy invece che su sole metriche
- Utilizza TCP sulla porta 179

Tipi di Messaggi BGP:
- OPEN: apertura connessione TCP
- UPDATE: annuncio/rimozione rotte
- KEEPALIVE: mantenimento connessione
- NOTIFICATION: segnalazione errori

Attributi Obbligatori:
- ORIGIN: protocollo di origine
- AS_PATH: sequenza AS attraversati
- NEXT_HOP: router successivo

Routing Policy:
- Ogni router può decidere se:
  - Aggiungere una destinazione alla tabella di routing
  - Inoltrare il vettore di percorso ai vicini
- Basato su politiche locali del sistema autonomo
- Ogni AS ha un numero identificativo globale (ASN)

## 6.4 - Confronto tra Protocolli

Ogni protocollo presenta vantaggi e limitazioni specifiche:
- RIP: Semplice, limitato a reti piccole
- OSPF: Efficiente, scalabile, adatto a reti complesse
- BGP: Fondamentale per routing inter-dominio, basato su policy

La scelta del protocollo dipende dalle specifiche esigenze di routing, dimensione della rete e complessità dei requisiti.