# Gestione degli Indirizzi nelle Reti IP

## 4.0 - Indirizzi IP e Indirizzi Fisici

Gli indirizzi IP e gli indirizzi fisici (MAC) sono fondamentali per il funzionamento delle reti. Le tabelle di inoltro che mettono in relazione gli indirizzi IP con gli indirizzi fisici vengono create e gestite dinamicamente dagli host attraverso il protocollo ARP (Address Resolution Protocol). Questa mappatura è essenziale poiché i pacchetti IP devono essere incapsulati in frame di livello data-link che utilizzano indirizzi MAC per l'instradamento locale.

Per esempio, in una rete locale dove abbiamo due host A e B:
- Host A: IP 193.17.31.45, MAC 00:9f:7a:89:90:7a
- Host B: IP 193.17.31.55, MAC 05:98:76:6c:4a:7b

Per permettere la comunicazione tra questi host, è necessario risolvere gli indirizzi IP nei corrispondenti indirizzi MAC, operazione che viene eseguita dinamicamente mediante Address Resolution Protocol (ARP).

---

## 4.1 - Protocollo di Risoluzione degli Indirizzi (ARP)

ARP, definito nell'RFC 826, è un protocollo che consente di risolvere un indirizzo IP in un indirizzo MAC all'interno di una rete locale. Il suo funzionamento si basa sulla capacità di indirizzamento broadcast della tecnologia sottostante.

Il processo di risoluzione ARP funziona come segue:
1. Quando un host deve comunicare con un altro host sulla stessa rete locale ma non conosce il MAC address di destinazione, genera un messaggio ARP-request.
2. L'ARP-request viene inviato in broadcast (fisicamente a tutti gli host della rete) contenendo l'indirizzo IP da risolvere.
3. L'host che riconosce il proprio indirizzo IP nel messaggio risponde con un ARP-reply unicast (direttamente all'host richiedente) fornendo il proprio indirizzo MAC.
4. L'host richiedente aggiorna la propria ARP-cache con la nuova associazione IP-MAC.

Nel formato del pacchetto ARP vengono specificati i campi necessari per l'operazione di risoluzione, tra cui gli indirizzi IP e MAC sia del mittente che del destinatario. La cache ARP viene utilizzata per memorizzare temporaneamente le associazioni già risolte, migliorando l'efficienza della rete riducendo il traffico broadcast.

---

## 4.2 - Protocollo di Risoluzione Inversa degli Indirizzi (RARP)

RARP (Reverse Address Resolution Protocol) svolge l'operazione inversa rispetto ad ARP: assegna un indirizzo IP a un indirizzo MAC noto. Questo protocollo è stato progettato principalmente per supportare le macchine diskless durante l'avvio da rete (network bootstrap), ma è ormai scarsamente utilizzato.

Il funzionamento del RARP prevede:
1. Un host che conosce solo il proprio indirizzo MAC invia una richiesta RARP.
2. Un server RARP risponde fornendo l'indirizzo IP associato a quell'indirizzo MAC.

Oggi RARP è stato largamente sostituito da protocolli più versatili come BOOTP e DHCP.

<div align="center">
  <img src="./images/04-1.png" width="450">
</div>


---

## 4.3 - Gestione Dinamica degli Indirizzi & DHCP

Le procedure statiche di assegnazione degli indirizzi IP presentano una flessibilità limitata. L'utilizzo di un server centrale per memorizzare la configurazione degli host offre una soluzione più efficiente.

In molti casi, un'associazione statica tra indirizzo IP e indirizzo MAC non è necessaria, specialmente quando ci sono più host che indirizzi IP disponibili. Questo scenario è comune in situazioni come:
- Cicli di attività degli host (es. connessioni dial-up)
- Host con performance limitate o utilizzo sporadico

La gestione dinamica degli indirizzi prevede l'utilizzo di un server per gestire l'assegnazione degli indirizzi IP su richiesta. Esistono diverse soluzioni possibili:

1. Binding statico: il server mantiene una tabella di corrispondenza statica tra indirizzi IP e MAC. Quando riceve una richiesta, verifica nella tabella l'indirizzo MAC del mittente e gli assegna l'indirizzo IP corrispondente.

2. Binding dinamico: l'insieme degli indirizzi IP può essere piccolo rispetto al numero di host da servire. Il binding cambia nel tempo, consentendo la condivisione degli indirizzi IP tra host con diversi cicli di attività.

#### Protocollo di Configurazione Dinamica degli Host (DHCP)

DHCP (Dynamic Host Configuration Protocol), definito nell'RFC 2131, rappresenta una versione evoluta del protocollo BOOTP. Si tratta di un protocollo di livello applicativo basato sul paradigma client-server che consente l'assegnazione automatica di indirizzi IP e altri parametri di configurazione agli host di una rete.

Il processo di configurazione DHCP si articola in diverse fasi:

1. Fase di scoperta (DHCP DISCOVER):
   - Il client invia un messaggio DHCP DISCOVER in broadcast contenente il proprio indirizzo MAC e un ID di sessione.
   - Il server risponde con un messaggio DHCP OFFER contenente l'indirizzo IP proposto con netmask, il tempo di lease e l'ID di sessione.

2. Fase di richiesta (DHCP REQUEST):
   - Il client può accettare la proposta inviando un messaggio DHCP REQUEST contenente l'ID di sessione e i parametri proposti (indirizzo IP, netmask, tempo di lease).
   - Il server associa i due indirizzi e risponde con un messaggio DHCPACK confermando la configurazione.

3. Configurazione completa:
   - I parametri di configurazione trasmessi possono includere: indirizzo IP, netmask, gateway, server DNS e altri parametri opzionali.
   - L'associazione viene interrotta attraverso un messaggio DHCPRELEASE inviato dal client.

Lo scambio completo di messaggi DHCP include quindi:
- DHCP DISCOVER: dal client in broadcast
- DHCP OFFER: dal server con proposta di configurazione
- DHCP REQUEST: dal client per accettare la configurazione
- DHCP ACK: dal server per confermare la configurazione
- DHCP RELEASE: dal client per rilasciare l'indirizzo quando non più necessario

#### Architettura DHCP Avanzata

DHCP supporta architetture multi-server per garantire ridondanza e scalabilità. Inoltre, per estendere il servizio oltre la rete locale, è possibile utilizzare i DHCP Relay, dispositivi che inoltrano i messaggi DHCP tra diverse reti IP.

I messaggi DHCP utilizzano UDP come protocollo di trasporto. Durante la fase di configurazione (fino alla creazione del binding), i messaggi del client hanno:
- Indirizzo IP sorgente: 0.0.0.0
- Indirizzo IP destinazione: 255.255.255.255 (broadcast)
- Porta sorgente: 68
- Porta destinazione: 67

Questa configurazione permette al client di comunicare prima di avere un indirizzo IP valido, utilizzando il broadcast per raggiungere il server DHCP.
