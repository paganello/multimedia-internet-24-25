# Access Technologies

## 3.1 - LAN Ethernet - IEEE 802.3

### IEEE 802 Working Group

| Data Link Layer | Physical Layer | Medium Access Control |
|---------------|---------------|------------------------|
| 802.3 (Ethernet) | 802.5 (Token Ring) | 802.11 (WiFi) |
| 802.2 (LLC) | 802.1 (Interconnessione LAN) | |

### Indirizzamento in un mezzo broadcast

L'indirizzamento in una rete broadcast consente a più dispositivi di ricevere lo stesso segnale simultaneamente. Questo tipo di topologia può essere gestito tramite differenti configurazioni.

- **Topologia a stella**: Utilizza uno switch per collegare tutte le stazioni di rete.
- **Topologia a bus (storica)**: Tutti i dispositivi sono connessi a un unico cavo condiviso.

## Frame IEEE 802.3 (Ethernet Frame)

![](./images/03-1.png)

- **Lunghezza minima del frame**: 512 bit (1 slot), pari a 51,2 µs
- **Velocità di propagazione**: 2 × 10⁸ m/s (~5 µs/km)
- **Diametro massimo della LAN**: 2,5 km

### Differenze tra IEEE 802.3 ed Ethernet

- **IEEE 802.3** utilizza il livello LLC (802.2) sopra il MAC
- **Ethernet** è direttamente collegato al livello di rete (IP)
- **Identificazione protocolli**:
  - In 802.3: lunghezza del frame (≤1500)
  - In Ethernet: codice protocollo (>1536 o 0x0600 in hex)

## Protocolli di accesso al mezzo

Problema: Se più stazioni trasmettono contemporaneamente, si verificano collisioni.

Soluzione: Definire protocolli per controllare l'accesso al mezzo broadcast e gestire le collisioni.

### Modelli concettuali di accesso multiplo

1. **Accesso ordinato**
   - **TDMA (Time Division Multiple Access)**
   - **Round Robin**
   - **Polling (Roll Call, Hub Polling)**
2. **Accesso casuale**
   - **CSMA/CD (Ethernet)**
   - **CSMA/CA (WiFi)**

### Esempio di TDMA

- Inefficiente per le LAN a traffico variabile
- Introduce alti ritardi e riduce la capacità di throughput

### Esempio di Round Robin

- Ogni stazione ha la possibilità di trasmettere in un turno
- Se non ha dati, passa il turno alla successiva
- Se ha dati, trasmette fino a un massimo definito (K)

### Accesso casuale: Slotted Aloha

- Tempo diviso in slot
- Le stazioni trasmettono nel primo slot disponibile
- In caso di collisione, ritentano dopo un tempo casuale

### CSMA/CD (Carrier Sense Multiple Access with Collision Detection)

1. Ascolto del canale prima di trasmettere
2. Se il canale è occupato, si attende
3. Se avviene una collisione, la trasmissione viene interrotta
4. Ritrasmissione dopo un tempo casuale (Backoff esponenziale)
5. Dopo 16 tentativi falliti, il frame viene scartato

## Interconnessione delle LAN

### Dispositivi di interconnessione

- **Repeater**: Estende il segnale fisico, trasmette bit
- **Bridge**: Filtra e inoltra i frame in base agli indirizzi MAC

### Bridge e forwarding

- Utilizza un database di inoltro (FDB)
- **Apprendimento all'indietro (Backward Learning)**:
  - Registra la porta di provenienza dei frame
  - Se la destinazione è nota, inoltra solo su quella porta
  - Se sconosciuta, inoltra su tutte le porte eccetto quella di ingresso

## Broadcast Storm e Spanning Tree Protocol (STP)

### Problema delle tempeste di broadcast

- Le topologie mesh introducono cicli
- I frame di broadcast si propagano indefinitamente tra i bridge

### Algoritmo Spanning Tree

- Seleziona un **Root Bridge** con l'ID più basso
- Ogni bridge seleziona una **Root Port** con la distanza minore
- Ogni segmento LAN ha un **Designated Bridge** che inoltra i frame
- Blocca alcune porte per eliminare cicli e ottenere una topologia ad albero

### Selezione del Root Bridge

- Basata sull'**ID Bridge** (Priorità + MAC Address)
- Il bridge con ID minore diventa il Root Bridge

### Selezione della Root Port

- La porta con il costo minore verso il Root Bridge diventa la Root Port

### Selezione della Designated Port

- Il bridge con la distanza minore al Root Bridge è il Designated Bridge
- La porta connessa al segmento LAN diventa Designated Port

## Conclusioni

- **Ethernet e IEEE 802.3** sono simili ma hanno differenze strutturali
- **CSMA/CD** gestisce le collisioni con backoff esponenziale
- **Bridging** migliora la separazione dei domini di collisione
- **STP** evita i cicli e previene le tempeste di broadcast

Con queste tecnologie, le reti LAN sono diventate più affidabili, scalabili ed efficienti.