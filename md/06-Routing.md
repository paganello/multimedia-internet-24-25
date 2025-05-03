# Protocolli di Routing Internet

L'infrastruttura di routing di Internet è composta da Sistemi Autonomi (AS) interconnessi, che sono porzioni di rete gestite da una singola organizzazione.

Un sistema autonomo è una parte della rete gestita da una singola organizzazione, che può contenere più domini di routing. Questi sistemi sono interconnessi e utilizzano protocolli specifici per lo scambio di informazioni di routing.

I protocolli di routing principali si dividono in:
- Protocolli di Gateway Interni (IGP): utilizzati per il routing all'interno di un sistema autonomo
- Protocolli di Gateway Esterni (EGP): utilizzati per il routing tra sistemi autonomi

<div align="center">
  <img src="./images/06-1.png" width="450">
</div>

Un dominio di routing rappresenta una porzione di un sistema autonomo che esegue un singolo protocollo di routing. Alcuni router possono appartenere a più domini di routing e implementare protocolli diversi, facilitando interazioni di rete complesse.

<div align="center">
  <img src="./images/06-2.png" width="450">
</div>

## 6.1 - Routing Information Protocol (RIP)

RIP è un protocollo di routing a vettore di distanza progettato a Berkeley nel 1982 e standardizzato in RFC 1058. Le sue caratteristiche principali includono:

Utilizza l'algoritmo di Bellman-Ford per calcolare i percorsi più brevi con metrica basata sul numero di hop (salti) tra router. Il numero di salti e' limitato a un massimo di 15 hop (16 = distanza infinita).

<div align="center">
  <img src="./images/06-3.png" width="450">
</div>

#### RIPv1

Nel protocollo RIP v1, la gestione dei tempi di aggiornamento e validità delle rotte è regolata da diversi timer. Il Routing Update Timer, con un valore predefinito di 30 secondi, determina l’intervallo tra due aggiornamenti consecutivi della tabella di routing inviati dai router. Questo meccanismo consente ai dispositivi di rete di condividere periodicamente le informazioni sulle rotte disponibili.

Se un router non riceve aggiornamenti su una rotta per un periodo di 180 secondi, il Route Invalid Timer entra in azione, dichiarando quella rotta non valida. Tuttavia, essa continua a essere annunciata agli altri router con una metrica di 16 hop, indicandone l’irraggiungibilità. Successivamente, interviene il Route Flush Timer (o Garbage Collection Timer), il quale stabilisce che una rotta non valida venga eliminata definitivamente dopo un intervallo compreso tra 60 e 120 secondi, a meno che non vengano ricevuti nuovi aggiornamenti validi. Questo processo consente di notificare i router vicini prima di rimuovere definitivamente la rotta dalla tabella.

Infine, il protocollo prevede anche gli aggiornamenti immediati o Triggered Updates. Se una metrica cambia improvvisamente, come nel caso di una rotta che diventa irraggiungibile, il router invia un aggiornamento immediato senza attendere il normale ciclo di 30 secondi, garantendo così una convergenza più rapida della rete.

#### RIPv2
Rispetto a RIP v1, questa versione introduce diverse migliorie. Tra queste, la possibilità di fornire informazioni esplicite sulla connettività, come l'indicazione esplicita dell'indirizzo del prossimo hop, che aiuta a ottimizzare il percorso dei pacchetti. Inoltre, introduce il router tag, che consente di marcare le rotte in base alla loro origine, facilitando l'integrazione con altri protocolli di routing come IGP ed EGP.

Un'altra importante novità è il supporto per l'autenticazione, che aggiunge un livello di sicurezza proteggendo gli aggiornamenti di routing tramite password in chiaro o hash MD5.

RIPv2 è anche classless, il che significa che supporta l'uso delle subnet mask (CIDR), permettendo una gestione più flessibile degli indirizzi IP e migliorando l'efficienza nell'assegnazione degli indirizzi di rete.

Infine, invece di trasmettere gli aggiornamenti via broadcast come RIPv1, RIPv2 utilizza multicast, inviando gli aggiornamenti solo agli host interessati tramite l'indirizzo 224.0.0.9, riducendo così il traffico di rete inutile.

<div align="center">
  <img src="./images/06-4.png" width="450">
</div>

Riassumento le principali differenze:

| **Caratteristica**            | **RIP v1**                          | **RIP v2**                                        |
| ----------------------------- | ----------------------------------- | ------------------------------------------------- |
| **Tipo di routing**           | Classful (non supporta subnet mask) | Classless (supporta subnet mask - CIDR)           |
| **Inoltro della subnet mask** | ❌ No                                | ✅ Sì                                              |
| **Autenticazione**            | ❌ No                                | ✅ Sì (password MD5 o in chiaro)                   |
| **Tipo di aggiornamenti**     | Broadcast (255.255.255.255)         | Multicast (224.0.0.9)                             |
| **Efficienza della banda**    | Minore (trasmette più dati inutili) | Maggiore (trasmette solo aggiornamenti necessari) |

Limitazioni di RIP:
- Metrica molto semplice (solo conteggio hop)
- Inadatto per reti grandi
- Tempo di convergenza lento
- Incapace di utilizzare metriche più complesse come lunghezza coda, ritardi, tasso errori

## 6.2 - Open Shortest Path First (OSPF)

OSPF (Open Shortest Path First) è un protocollo di routing dinamico basato sullo stato del collegamento (Link State), standardizzato negli RFC 1247, 1583 e 2328. A differenza di RIP, che utilizza la distanza in hop per determinare il miglior percorso, OSPF calcola il percorso più breve utilizzando l'algoritmo di Dijkstra, eseguito su ogni router per costruire una mappa completa della rete.

Una delle caratteristiche fondamentali di OSPF è il supporto per il routing gerarchico, che suddivide la rete in aree con una backbone centrale (Area 0), migliorando così la scalabilità e l’efficienza del protocollo.

OSPF utilizza metriche personalizzabili per determinare il costo di attraversamento di un'interfaccia, valore che può essere impostato dall'amministratore di rete per ottimizzare il traffico. Inoltre, implementa il Hello Protocol, che serve per monitorare lo stato dei router vicini e stabilire le relazioni di adiacenza tra i dispositivi di rete.

Le informazioni di stato della rete vengono scambiate tra i router tramite i Link State Advertisements (LSA), che contengono dettagli sulla topologia e consentono una rapida convergenza in caso di modifiche; tuttavia esistono altri tipi di pacchetti utilizzati da OSPF per le sue operazioni, tutti trasportati direttamente su protocollo IP (number 89), senza utilizzare TCP o UDP.

| **Tipo di Pacchetto**                 | **Descrizione**                                                                                                                                                                                             |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Hello**                             | Utilizzato per stabilire e mantenere la connessione tra router OSPF. Contiene informazioni di base sui router, come l'ID, i parametri di rete e i timer di Hello.                                           |
| **DBD (Database Description)**        | Utilizzato per scambiare informazioni sulla topologia della rete. Ogni router invia un riepilogo dei suoi LSAs (Link-State Advertisements) per consentire agli altri router di confrontare le informazioni. |
| **LSR (Link-State Request)**          | Utilizzato per richiedere informazioni specifiche a un router vicini. Viene inviato dopo che il router ha notato che alcune informazioni della topologia mancano o sono obsolete.                           |
| **LSU (Link-State Update)**           | Contiene le informazioni di stato dei collegamenti (LSAs) aggiornate. Viene inviato in risposta a una richiesta LSR o come parte della sincronizzazione dei database tra router.                            |
| **LSAck (Link-State Acknowledgment)** | Viene utilizzato per confermare la ricezione di LSUs. Ogni router invia un LSAck per garantire che gli aggiornamenti siano stati correttamente ricevuti.                                                    |

<div align="center">
  <img src="./images/06-5.png" width="450">
</div>

OSPF produce quindi una topografia della rete molto diversa da quella fisica, lo fa basandola sulle distanze pittosto che sui reali collegamenti tra router.

<div align="center">
  <img src="./images/06-6.png" width="450">
</div>

Basandoci per esempio sulla rete fisica rappresentata in basso a destra della seguente figura (la quale riporta anche i pesi di ogni percorso), OSPF sul router 1 avrebbe prodotto la topologia di rete raffigurata a sinistra.

<div align="center">
  <img src="./images/06-7.png" width="450">
</div>

Dal punto di vista dei router 7 e 10:

<div align="center">
  <img src="./images/06-8.png" width="450">
  <img src="./images/06-9.png" width="450">
</div>


## 6.3 - Border Gateway Protocol (BGP)

BGP è un protocollo di routing esterno utilizzato per scambiare informazioni di routing tra diversi sistemi autonomi (AS), ossia gruppi di reti sotto il controllo di un'unica amministrazione. È un protocollo di routing inter-dominio, che significa che viene utilizzato per comunicare tra diverse reti o ISP (Internet Service Providers). BGP è considerato un protocollo di tipo "path vector", e la sua funzione principale è determinare il miglior percorso per il traffico attraverso Internet.

BGP è fondamentale per il funzionamento di Internet e si basa su un modello di scambio di rotte. Ogni router BGP conserva una tabella di routing che indica i percorsi per raggiungere le destinazioni attraverso vari AS. Le rotte vengono propagate tra router BGP in modo che ciascun AS possa sapere come raggiungere un altro AS.

Per la comunicazione tra routers, GBP utilizza TCP sulla porta 179.

BGP (Border Gateway Protocol) utilizza vari tipi di messaggi per garantire il corretto funzionamento della comunicazione tra i router. Questi messaggi sono:

- OPEN: Apre la connessione TCP tra i router e gestisce l'autenticazione reciproca.

- UPDATE: Annuncia una nuova rotta o elimina una rotta obsoleta.

- KEEPALIVE: Mantiene attiva la connessione in assenza di messaggi UPDATE. Viene utilizzato anche come conferma (ACK) per i messaggi OPEN.

- NOTIFICATION: Notifica gli errori nei messaggi precedenti e viene utilizzato anche per chiudere la connessione.

<div align="center">
  <img src="./images/06-11.png" width="450">
</div>

#### Policy based routing
BGP è un protocollo di tipo Path Vector, il che significa che permette la distribuzione dei percorsi verso destinazioni specifiche. Tuttavia, la scelta del percorso viene lasciata alla gestione della rete, che può applicare policy-based routing per determinare quale percorso utilizzare in base a criteri amministrativi specifici. In altre parole, BGP non impone un percorso specifico, ma consente agli amministratori di rete di configurare il routing in base alle esigenze della rete.

<div align="center">
  <img src="./images/06-10.png" width="450">
</div>

Un router BGP, quando riceve un path vector (PV) da un peer, può decidere se:

1. Aggiungere la destinazione alla sua tabella di routing: Il router può aggiungere la destinazione specificata nel path vector alla propria tabella di routing, se ritiene che il percorso sia valido secondo le proprie politiche di routing locali.

2. Inoltrare il path vector ai suoi vicini: Il router può anche decidere di propagare il path vector ai suoi vicini, annunciando così il percorso ad altri router, se il percorso viene considerato valido.

Tutte queste decisioni dipendono dalla politica di routing locale del router. Inoltre, ogni AS (Sistema Autonomo) ha un Numero di Sistema Autonomo (ASN) che viene assegnato dall'IANA (Internet Assigned Numbers Authority), e questo numero ha un significato globale, simile agli indirizzi IP.

