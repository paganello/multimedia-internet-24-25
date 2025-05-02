# Tecnologie di Rete Wireless: WLAN, WPAN, Reti Ad Hoc

## 15.1 - Introduzione alle Reti Wireless

La scelta tra reti wireless e cablate dipende dalla situazione specifica. La differenza fondamentale non risiede solo nel mezzo trasmissivo. Le reti wireless presentano caratteristiche peculiari del mezzo (canale radio) che hanno un impatto significativo sulle prestazioni del sistema e gestiscono naturalmente la mobilità degli utenti.

Generalmente, le reti si dividono in reti di accesso e reti di backbone. Le tecnologie wireless sono impiegate principalmente come **reti di accesso**, mentre le reti di backbone basate su collegamenti radio punto-punto non sono solitamente considerate "reti wireless" in questo contesto. Le reti di accesso wireless sono più complesse e presentano differenze fondamentali rispetto a quelle cablate, prima fra tutte l'uso di un **mezzo trasmissivo broadcast**, condiviso tra più utenti.

Il canale broadcast può essere **centralizzato**, come nei sistemi cellulari, WLAN o WMAN, dove un punto di accesso fisso (Base Station - BS o Access Point - AP) gestisce la comunicazione con le stazioni mobili (MS) all'interno di un'area di servizio definita (cella). 

<div align="center">
  <img src="./images/15-1.png" width="550">
</div>

Oppure può essere **distribuito**, tipico delle reti ad-hoc (come mesh o reti di sensori), dove le connessioni avvengono direttamente tra dispositivi mobili, potenzialmente attraverso multipli salti (multi-hop), dove le stazioni mobili agiscono anche da ripetitori (relay).

<div align="center">
  <img src="./images/15-2.png" width="450">
</div>

Le principali differenze tra reti cablate e wireless riguardano:

1.  **Canale Radio:** È intrinsecamente variabile a causa di interferenze, ostacoli e multi-percorso (multipath). Richiede tecniche avanzate di modulazione e codifica per garantire affidabilità.
2.  **Mobilità Utente:** Le reti wireless devono gestire la mobilità sia quando l'utente è in stand-by sia durante una sessione attiva (conversazione), garantendo continuità del servizio (handover/handoff).

### 15.2 - Il Canale Wireless: Caratteristiche e Modelli

Il canale wireless presenta generalmente caratteristiche "peggiori" rispetto a quello cablato, soffrendo di maggiore attenuazione, comportamento variabile nel tempo e distorsioni. I segnali sono influenzati principalmente da: 
- Attenuazione dovuta alla distanza trasmettitore-ricevitore (TX-RX)
- Attenuazione causata da ostacoli
- Propagazione attraverso percorsi multipli.

La comunicazione avviene tramite **onde radio**. Lo **spettro radio** è diviso in bande, ciascuna con proprietà e usi diversi. Ad esempio, le bande VHF/UHF (usate per reti mobili) permettono buona penetrazione degli edifici con potenze relativamente basse, mentre le bande SHF (satelliti, link punto-punto) offrono alta larghezza di banda ma sono sensibili a effetti meteorologici. Le WLAN e WPAN operano tipicamente nelle bande ISM (2.4 GHz e 5 GHz), soggette a interferenze da altri dispositivi (forni a microonde, ecc.) e, a 5 GHz, anche ad attenuazione da pioggia/nebbia. In generale, le alte frequenze offrono più banda e sono meno affollate, ma la propagazione è ostacolata dagli ostacoli; le basse frequenze penetrano meglio ma hanno meno banda, richiedono antenne più grandi e soffrono di più interferenze.

<div align="center">
  <img src="./images/15-3.png" width="450">
</div>

#### Antenne 

Un'antenna isotropica ideale irradia potenza uniformemente in tutte le direzioni. La densità di potenza F(d) a distanza d da una sorgente isotropica con potenza PT e'
$$
F(d) = \frac{PT}{4\pi d^2}
$$
Le antenne reali sono **direttive**, concentrando potenza in alcune direzioni, effetto modellato tramite il **guadagno** (gain) g(θ). La densità di potenza nella direzione di massimo guadagno (gT) diventa
$$
F(d) = \frac{PT \cdot g_T}{4\pi d^2}
$$
Il prodotto **PTgT** è detto EIRP (Effective Isotropically Radiated Power).

<div align="center">
  <img src="./images/15-4.png" width="450">
</div>

La potenza ricevuta (PR) dipende dalla densità di potenza e dall'area equivalente dell'antenna ricevente (Ae). Per un'antenna ricevente con guadagno gR, la potenza ricevuta è data dalla **formula di Friis** (modello di propagazione in spazio libero): 
$$
PR = PT \cdot g_T \cdot g_R \left( \frac{\lambda}{4\pi d} \right)^2
$$
Questa formula mostra che la potenza ricevuta decresce con il quadrato della distanza (PR µ d⁻²) ed è valida in condizioni ideali (linea di vista libera).


Nella realtà, la propagazione è influenzata da **riflessioni**, **diffrazione** (segnale che si piega attorno agli ostacoli), **scattering** (segnale disperso da piccoli oggetti) e **shadowing** (attenuazione dovuta a grandi ostacoli). Questi fenomeni causano la **propagazione multi-percorso (multipath)**: multiple copie dello stesso segnale arrivano al ricevitore con ritardi e fasi diverse, combinandosi vettorialmente.

Un modello semplificato è il **Two-ray model**, che considera il percorso diretto e un percorso riflesso dal suolo. In questo caso, l'attenuazione è più severa, con Pr µ d⁻⁴. Modelli più generali usano un esponente di propagazione η (Pr µ d⁻η), con η tipicamente tra 2 (spazio libero) e 5 (ambiente urbano denso). Esistono anche **modelli empirici** (es. Okumura-Hata) che forniscono formule basate su misurazioni reali per stimare l'attenuazione in scenari specifici (urbano, suburbano, rurale).

<div align="center">
  <img src="./images/15-5.png" width="550">
</div>

Il **Multi-path Fading** è la rapida fluttuazione della potenza del segnale ricevuto causata dalla combinazione delle repliche multi-percorso. A seconda delle fasi relative, il segnale può essere fortemente attenuato o addirittura amplificato. Se il terminale si muove, queste condizioni cambiano rapidamente nel tempo. Sotto certe ipotesi (molti percorsi, nessuno dominante), l'ampiezza del segnale segue una distribuzione di **Rayleigh**, e la potenza una distribuzione **esponenziale**. Questo permette di calcolare la **probabilità di outage**, cioè la probabilità che la potenza ricevuta scenda sotto una soglia minima necessaria per la corretta decodifica. 

<div align="center">
  <img src="./images/15-6.png" width="550">
</div>

Lo **Shadowing** è invece un fading più lento: nella propagazione il segnale puo essere parzialmente rilesso e rifratto dagli ostacoli, cio genera una ulteriore attenuazione. Questo effetto viene modellato spesso come una variabile casuale log-normale.

## 15.3 - WLAN (Wireless Local Area Network)

Le reti WLAN sono standardizzate dall'IEEE sotto il comitato **802.11**. Questi standard coprono i livelli Fisico (PHY) e MAC (Medium Access Control).

L'evoluzione degli standard 802.11 include: 
- il **legacy** (1997, 1-2 Mbps), **802.11b** (1999, 11 Mbps a 2.4 GHz)
- **802.11a** (1999, 54 Mbps a 5 GHz), **802.11g** (2003, 54 Mbps a 2.4 GHz)
- **802.11n** (2009, velocità più elevate)
- **802.11ac/ax** (velocità Gigabit) e vari emendamenti per sicurezza (11i)
- QoS (11e)
- mesh (11s)
- comunicazioni veicolari (11p)
- ecc.

La **Wi-Fi Alliance** è un consorzio industriale che certifica l'interoperabilità dei prodotti 802.11.

L'architettura del protocollo 802.11 prevede due modalità operative principali a livello MAC:
1.  **DCF (Distributed Coordination Function):** Obbligatoria, basata su contesa, utilizza CSMA/CA (Carrier Sense Multiple Access with Collision Avoidance) per l'accesso al mezzo best-effort.
2.  **PCF (Point Coordination Function):** Opzionale, centralizzata e senza contesa, basata su polling gestito dall'Access Point. Tuttavia, a causa di problemi nello standard, non è stata implementata commercialmente.

#### Network architetture
I componenti di una rete WLAN sono: 
- la **Stazione (STA)** (un dispositivo utente)
- l'**Access Point (AP)** (che collega la rete wireless a quella cablata)
- il **Basic Service Set (BSS)** (un gruppo di STA controllate dalla stessa funzione di coordinamento, simile a una cella)
- l'**Extended Service Set (ESS)** (un insieme di BSS interconnessi) 
- **Distribution System (DS)** (l'infrastruttura, cablata o wireless, che collega gli AP di un ESS). 

<div align="center">
  <img src="./images/15-7.png" width="450">
</div>

Un BSS può essere di tipo **Infrastructure** (con AP) o **Independent (IBSS)** (senza AP, detta rete ad hoc). 

<div align="center">
  <img src="./images/15-9.png" width="450">
</div>

Un ESS permette la mobilità tra AP diversi, apparendo come un'unica rete di livello 2. Gli AP agiscono come bridge, inoltrando frame tra DS e STA associate.

<div align="center">
  <img src="./images/15-8.png" width="350">
  <img src="./images/15-10.png" width="350">
</div>
<br>

#### Medium Access Control
Il livello MAC 802.11 fornisce servizi di accesso al canale, recupero errori, frammentazione/riassemblaggio, risparmio energetico, indirizzamento e framing. L'accesso al canale nel DCF è regolato da **CSMA/CA**: una stazione ascolta il canale (carrier sense); se è libero per un tempo **DIFS (DCF Interframe Spacing)**, trasmette; se è occupato, attende che si liberi e poi inizia una procedura di **backoff** (attesa casuale) prima di ritentare. Il backoff è un numero casuale di slot temporali estratto uniformemente da una **Contention Window (CW)**. La CW viene raddoppiata esponenzialmente (fino a CWmax) dopo una collisione/errore e resettata a CWmin dopo una trasmissione riuscita.

Il recupero errori per trasmissioni unicast si basa su **ACK (acknowledgement)** positivi inviati dal ricevitore. Se l'ACK non arriva entro un timeout, il trasmettitore assume che il frame sia perso e lo ritrasmette (fino a un numero massimo di tentativi). Gli ACK hanno priorità alta perché vengono inviati dopo un intervallo **SIFS (Short IFS)**, che è più breve del DIFS. Esistono anche PIFS (per PCF) ed EIFS (usato dopo errori di ricezione).

<div align="center">
  <img src="./images/15-11.png" width="450">
</div>

Per mitigare il problema del **terminale nascosto** (due stazioni che non si sentono tra loro ma collidono presso un ricevitore comune), 802.11 introduce il **Virtual Carrier Sense** tramite lo scambio di frame di controllo **RTS (Request To Send)** e **CTS (Clear To Send)**. Le stazioni che sentono RTS o CTS impostano il loro **NAV (Network Allocation Vector)** e si astengono dal trasmettere per la durata indicata, prevenendo collisioni. Questo meccanismo introduce però overhead e può causare il problema del **terminale esposto** (una stazione che si astiene inutilmente dal trasmettere). L'uso di RTS/CTS è solitamente opzionale e attivato solo per frame di dati di dimensioni superiori a una soglia (RTSThreshold).

<div align="center">
  <img src="./images/15-12.png" width="450">
</div>

Lo standard **802.11e** introduce il supporto per la **Quality of Service (QoS)** definendo la **Hybrid Coordination Function (HCF)**, che include due meccanismi: **EDCA (Enhanced Distributed Channel Access)** e HCCA (HCF Controlled Channel Access). EDCA è un'estensione del DCF che differenzia il traffico in 4 **Access Categories (AC)** (Voce, Video, Best Effort, Background). Ad ogni AC sono associati parametri di backoff diversi (AIFS, CWMin, CWMax, TXOPlimit), permettendo di assegnare priorità diverse. Stazioni con traffico a priorità più alta usano valori AIFS/CWMin più bassi, accedendo al canale più velocemente. 

## 15.4 - Bluetooth

Bluetooth è nato come standard industriale (promosso dal Bluetooth SIG - Special Interest Group) ed è stato poi adottato come base per lo standard **IEEE 802.15.1** (livelli PHY e MAC). Prende il nome dal re danese Harald Blåtand (Bluetooth).

Le sue caratteristiche principali sono: 
- Tecnologia radio a basso costo, corto raggio (tipicamente 10-20 metri, ma esistono classi di potenza diverse)
- Bassa complessità
- Piccole dimensioni
- Uso della banda ISM a 2.4 GHz

Il **livello fisico** (PHY) utilizza la banda ISM a 2.4 GHz con 79 canali da 1 MHz. La modulazione è GFSK (Gaussian Frequency Shift Keying) con un data rate di 1 Mb/s. Per la multiplazione e la resistenza alle interferenze, impiega la tecnica **Frequency Hopping Spread Spectrum (FHSS)**, saltando tra i 79 canali 1600 volte al secondo (ogni hop dura 625 µs). La sequenza di hopping è pseudo-casuale ed è determinata dall'indirizzo e dal clock del dispositivo **Master** della rete; i dispositivi **Slave** seguono la sequenza del Master. I pacchetti possono occupare 1, 3 o 5 slot temporali consecutivi.

<div align="center">
  <img src="./images/15-13.png" width="350">
  <img src="./images/15-14.png" width="350">
</div>

L'architettura di rete base di Bluetooth è la **Piconet**: una rete ad hoc composta da un Master e fino a 7 Slave attivi. La comunicazione avviene **esclusivamente** tra il Master e gli Slave; gli Slave non possono comunicare direttamente tra loro. Ogni dispositivo ha un indirizzo MAC a 48 bit. All'interno di una piconet, agli slave attivi viene assegnato un indirizzo più breve (AMA, 3 bit). Possono esserci fino a 256 dispositivi "parcheggiati" (Parked), membri della piconet ma inattivi, identificati da un PMA (8 bit).

<div align="center">
  <img src="./images/15-15.png" width="550">
</div><br>

Bluetooth supporta due tipi di connessioni logiche:
1.  **SCO (Synchronous Connection Oriented):** Connessione simmetrica, bidirezionale, a capacità fissa (64 kbit/s), simile a un circuito, usata principalmente per l'audio (voce).
2.  **ACL (Asynchronous ConnectionLess):** Connessione a pacchetto, asimmetrica o simmetrica, basata su polling da parte del Master, usata per i dati. Raggiunge velocità fino a circa 723 kbps in modo asimmetrico.

L'accesso multiplo si basa su **Time Division Duplex (TDD)**: il Master trasmette negli slot pari, gli Slave rispondono (se interrogati) negli slot dispari.

<div align="center">
  <img src="./images/15-16.png" width="550">
</div><br>

Il **Link Controller** gestisce diversi **stati** del dispositivo: 
1. Stand-by (radio spento)
2. Inquiry/Inquiry Scan (scoperta di altri dispositivi)
3. Page/Page Scan (creazione di una connessione)

La **procedura di Page** permette a un dispositivo (futuro Master) di connettersi a uno specifico dispositivo (futuro Slave) conoscendone l'indirizzo. Se un device vuole connettersi ad un altro dispositivo deve conoscere il suo indirizzo e iniziare la procedura di Page; dall'indirizzo si puo derivare il Device Access Code, codice che verra' scambiato con il dispositivi al quale ci si vuole connettere quando questo uscira' dallo stato di sleep avviando la procedura di Page scan. Per limitare il consumo energetico, la procedura di page scan dura al massimo 10ms, a fronte di alcuni secondi di sleep (1.28s - 3.85s).
Inizialmente la procedura di Page start viene eseguita su 16 dei 32 canali, ma se alla prima botta non si ha risposta, al secondo tentativo la richiesta viene estesa anche agli altri 16; una volta che arriva il DAC da dispositivo al quale ci si vuole connettere, allora si inviano ad esso alcune info e la connessione puo definirsi terminata.
<div align="center">
  <img src="./images/15-18.png" width="550">
</div><br>


**procedura di Inquiry** permette di scoprire i dispositivi presenti nel raggio d'azione. Entrambe usano sequenze di hopping specifiche e meccanismi per ridurre il consumo energetico (scan periodici brevi seguiti da periodi di sleep). 

Sono previste anche modalità a **basso consumo** durante la connessione (Hold, Sniff, Park) per permettere agli slave di "dormire" pur mantenendo la connessione.

<div align="center">
  <img src="./images/15-17.png" width="550">
</div><br>

Infine, più piconet possono essere interconnesse formando una **Scatternet**. Un dispositivo può partecipare a più piconet (come slave) o essere Master in una e Slave in altre. Questo permette topologie più complesse, ma la formazione e il routing nelle scatternet non sono standardizzati da Bluetooth.

