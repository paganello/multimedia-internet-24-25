# Le vCDN e NFV
## 9.1 - Network Functions Virtualization e Virtual CDN

La Network Functions Virtualization (NFV) e' un paradigma in cui le funzioni di rete vengono eseguite in un ambiente virtualizzato su un'infrastruttura fisica condivisa.

Uno dei casi d'uso principali per NFV è rappresentato dalle Virtual Content Delivery Network (vCDN). Il focus della ricerca è proprio sulla pianificazione ottimale della rete per un sistema vCDN, che presenta sfide particolari legate alla natura variabile e incerta della domanda di traffico.

1. Se la domanda effettiva è inferiore alle previsioni, la rete risulta sovradimensionata con conseguente spreco di risorse.
2. Se la domanda è superiore alle previsioni, la rete è sottodimensionata e non può gestire adeguatamente le richieste.
   
## 9.2 - Modello di sistema proposto

Il modello di sistema che si puo quindi utilizzare include diversi componenti chiave dell'infrastruttura di rete:

1. Router di rete che costituiscono l'infrastruttura di base
2. Potenziali surrogati fisici CDN che possono essere attivati
3. Surrogati virtuali CDN implementati tramite NFV
4. Consumatori di contenuti che generano domanda

Il modello considera esplicitamente la natura stocastica e variabile nel tempo delle domande di traffico. L'obiettivo è determinare l'attivazione ottimale dei nodi CDN fisici (decisioni a lungo termine) e successivamente servire le richieste in modo ottimale utilizzando sia i CDN virtuali che quelli fisici attivati.

## 9.3 - Pianificazione stocastica per vCDN

Lo scopo del fornitore di servizi, secondo questo modello, è effettuare due scelte fondamentali:

1. Decidere se e dove installare nodi CDN fisici nella topologia della rete
2. Determinare il routing ottimale delle richieste, data l'infrastruttura installata

Decisione di posizionamento della rete fisuca di trasporto e installazione delle CDN fisiche candidate:
<div align="center">
  <img src="./images/09-1.png" width="250">
  <img src="./images/09-2.png" width="300">
</div>

Installazione delle vCDN:
<div align="center">
  <img src="./images/09-3.png" width="450">
</div>

Connessione dei consumatori:
<div align="center">
  <img src="./images/09-4.png" width="450">
</div>

Funzionamento del sistema finale:
<div align="center">
  <img src="./images/09-5.png" width="450">
</div>
Questo approccio di pianificazione stocastica rappresenta un avanzamento rispetto ai metodi tradizionali basati su previsioni deterministiche, poiché incorpora l'incertezza nella domanda direttamente nel processo di pianificazione.

Il documento si propone di affrontare e risolvere questo problema di ottimizzazione a due stadi, fornendo un modello matematico e metodi di soluzione appropriati, seguiti da risultati numerici che ne dimostrano l'efficacia.