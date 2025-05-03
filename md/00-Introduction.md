
# Reti Internet Multimediali
**Prof. Fabio Martignon**

## Informazioni sul Docente
Prof. Fabio Martignon è disponibile presso l'ufficio DIGIP (2° piano), contattabile al numero (035205) 2358 o via email a fabio.martignon@unibg.it. Il ricevimento è previsto il lunedì dalle 13:30 alle 15:30, previo appuntamento via mail.

---

## Materiale Didattico
Il testo di riferimento è "Internetworking with TCP/IP, volume 1" di Douglas E. Comer, pubblicato da Prentice Hall (preferibilmente 6ª edizione, ma anche la 5ª è utilizzabile). Il corso si avvale inoltre di slide disponibili sul sito web, documenti supplementari segnalati durante le lezioni e riferimenti a standard Internet (RFC). Tutto il materiale è accessibile tramite la pagina web del corso: http://cs.unibg.it/martignon/indexRIM.html (o in alternativa: http://193.204.253.55/martignon/indexRIM.html).

---

## Programma del Corso
Il corso parte da un ripasso dei concetti fondamentali studiati in FRT per poi approfondire le tecnologie di accesso, inclusi protocolli LAN, tecniche di multiplexing e un focus particolare sulle reti cellulari. Vengono analizzati il protocollo STP e il livello di rete con particolare attenzione al protocollo IP, agli algoritmi di routing (DV, Link State) e ai protocolli implementati nelle reti IP (RIP, OSPF, BGP).

La seconda parte del corso affronta il livello di trasporto, differenziando tra trasporto non affidabile (UDP) e affidabile (TCP), e il livello applicativo con i principali servizi: DNS, FTP, HTTP, SMTP e sistemi peer-to-peer.

Il corso si conclude con argomenti avanzati quali reti wireless, Internet of Things, Qualità del Servizio, Content Delivery Networks e Voice Over IP.

---

## 0.1 - Concetti Fondamentali di Internet

### Cos'è Internet?
Internet è un'infrastruttura di comunicazione globale composta da milioni di computer (host) connessi tramite collegamenti fisici (fibra, cavi, wireless, satellite) e dispositivi di rete (router). Questa rete permette alle applicazioni di comunicare a distanza seguendo protocolli standardizzati.

I protocolli sono regole che governano lo scambio di informazioni tra dispositivi in rete, similmente ai protocolli di comunicazione umana. Esempi includono TCP/IP, HTTP, SMTP, ecc.

Internet è strutturata in:
- **Terminali (host)**: eseguono applicazioni (web, email, ecc.)
- **Core di rete**: router interconnessi che instradano i pacchetti
- **ISP (Internet Service Provider)**: forniscono connettività a vari livelli (locale, regionale, nazionale, internazionale)
- **Autonomous System**: Un AS è un sistema IP gestito da una singola organizzazione. La maggior parte degli ISP gestitiscono un proprio AS. 
- **Interior Gateway**: Routers che si interpongono tra i diversi Autonomous Systems.

### Tecnologie di Commutazione
Internet utilizza principalmente la **commutazione di pacchetto**, dove le informazioni vengono divise in pacchetti inviati indipendentemente attraverso la rete. Questo differisce dalla **commutazione di circuito** (usata nelle reti telefoniche tradizionali) dove viene riservato un percorso dedicato per l'intera durata della comunicazione (la comunicazione era identificata dal percorso, non dall'associazione IP sorgente e IP destinatario).

**Vantaggi della Commutazione di Pacchetto:**
- Utilizzo più efficiente delle risorse
- Adattabilità al traffico a burst
- Condivisione delle risorse

**Svantaggi:**
- Possibili ritardi e perdite di pacchetti
- Necessità di protocolli per il controllo della congestione e il recupero delle perdite

Nelle reti a pacchetto i messaggi vengono splittati in pacchetti, i quali poi vengo uno a uno inoltrati sulla rete. Essi possono intraprendere percorsi differenti nel viaggio tra mittente e destinatario, ecco perchè nasce la necessistà di verificare che le rotte non siano congestionate e che tutti i pacchetti arrivino a destinazione.

### Ritardi e Perdite nella Rete
Ogni pacchetto subisce vari tipi di ritardi:
- **Elaborazione**: verifica errori e determinazione del percorso
- **Accodamento**: attesa in coda per la trasmissione
- **Trasmissione**: tempo necessario per inviare i bit sul collegamento
- **Propagazione**: tempo di percorrenza fisica del segnale

I pacchetti possono essere persi quando le code dei router si riempiono completamente.

### Accesso a Internet
Le principali tecnologie di accesso includono:
- **Dial-up**: accesso tramite PSTN
- **ADSL**: linea digitale asimmetrica su doppino telefonico
- **Reti locali (LAN)**: Ethernet (10/100/1000 Mbps)
- **Accesso wireless**: WLAN, sistemi cellulari (GSM, GPRS, EDGE, UMTS, LTE)
- **Reti mesh e ad hoc**: connessioni peer-to-peer wireless
- **Reti di sensori wireless**: dispositivi piccoli ed economici per IoT

Gli standard Internet sono documenti pubblici chiamati RFC (Request For Comments), coordinati dall'Internet Engineering Task Force (IETF). Internet ha avuto origine negli anni '60 con ARPANET, evolvendosi attraverso l'introduzione dei protocolli TCP/IP negli anni '80, la nascita del Web negli anni '90, fino all'attuale ecosistema con miliardi di utenti e numerose applicazioni (messaging, condivisione file, telefonia IP).
<br><br>


## 0.2 - Concetto di Store and Forward

Il **meccanismo di Store and Forward** (memorizza e inoltra) è un principio fondamentale nelle reti a commutazione di pacchetto, come Internet. Questo metodo definisce come i pacchetti vengono gestiti dai router e altri dispositivi di rete durante il loro percorso dalla sorgente alla destinazione.

### Funzionamento di Base

In una rete Store and Forward:

1. Un router riceve **completamente** un pacchetto su un'interfaccia di ingresso
2. Il pacchetto viene temporaneamente **memorizzato** in un buffer
3. Il router elabora l'intestazione del pacchetto e determina l'interfaccia di uscita
4. Il pacchetto viene **inoltrato** attraverso l'interfaccia di uscita appropriata

Questo processo si ripete ad ogni hop (salto) lungo il percorso del pacchetto attraverso la rete.

### Tempistiche di Trasmissione

Nel meccanismo Store and Forward intervengono due componenti di tempo principali (per piu segmenti di rete, vanno calcolati i seguenti indicatori per ogni segmento e poi sommati tra di loro):

#### 1. Tempo di Trasmissione

Il tempo di trasmissione è il tempo necessario per inserire tutti i bit di un pacchetto sul collegamento di uscita:

$$
T_{trasmissione} = \frac{L}{R}
$$

Dove:
- $L$ = lunghezza del pacchetto in bit
- $R$ = velocità di trasmissione del collegamento in bit/s

#### 2. Tempo di Propagazione

Il tempo di propagazione è il tempo necessario affinché un bit viaggi da un nodo al successivo:

$$
\tau_{propagazione} = \frac{l}{C}
$$

Dove:
- $l$ = lunghezza fisica del collegamento in metri
- $C$ = velocità di propagazione nel mezzo (circa 3×10⁸ m/s per la fibra ottica)


### Multiplexing Statistico
I pacchetti provenienti da diverse sorgenti condividono gli stessi collegamenti di uscita, generando quindi una competizione, gestita appunto dai router. Durante la trasmissione ogni pacchetto utilizza completamente il canale e le risorse di rete vengono utilizzate in base alle esigenze attuali.

Questo porta a:
1. **Contesa per le risorse**: più flussi competono per lo stesso collegamento
2. **Code (buffering)**: i pacchetti attendono il loro turno per la trasmissione
3. **Tempi di attesa variabili**: il ritardo di accodamento dipende dal carico della rete

Il multiplexing statistico è uno dei principali vantaggi delle reti a commutazione di pacchetto rispetto alle reti a commutazione di circuito, poiché consente un utilizzo più efficiente delle risorse di rete, specialmente con traffico intermittente o a burst.

