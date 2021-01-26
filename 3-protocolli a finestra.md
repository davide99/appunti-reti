# Protocolli a finestra

## Correzione o recupero
* **FEC** (Forward Error Correction): correggo gli errori in ricezione senza richiedere la ritrasmissione
* **ARQ** (Automatic Retransmission/Repeat reQuest): rilevo gli errori e richiedo la ritrasmissione

## Semantica degli ACK
Gli ACK possono essere: 
- **Cumulativi**: `ACK N`
	* Confermano tutte le PDU con `N'<N`
	* Indicano al trasmettitore che `N` è la PDU attesa
- **Selettivi**: `ACK N`
	* Confermano la PDU `N`
- **Negativi**: `NACK N`
	* Specificano la mancata ricezione della PDU `N`
	* Si chiede di ritrasmettere la PDU `N`.

**Piggybacking**:
* Il ricevitore manda ACK + dati che deve trasmettere
* ACK in ritardo (se il ricevitore non deve trasmettere)

## Protocolli a finestra
Tre tecniche con ARQ:
* **Stop and wait**
* **Go back N**
* **Selective Repeat**

###	Stop and wait
Il trasmettitore quando vuole trasmettere:
1. Invia una PDU
1. Attiva un timer
1. Attende ACK
1. Se il timeout scade torna al punto 1

Il ricevitore quando riceve la PDU:
1. Controlla che sia corretta (checksum)
1. Se:
	* È in sequenza:
		* Invia ACK
		* Invia PDU al livello superiore
	* Non è in sequenza:
		* Viene scartata
		* Invia ACK relativo alla PDU che vuole

Il trasmettitore quando riceve l'ACK:
1. Controlla che sia corretto
1. Se:
	* È in sequenza:
		* Arresta il timer
		* Invia la PDU successiva
	* Non è in sequenza:
		* L'ACK viene ignorato

L'efficienza di stop and wait è molto bassa in quanto per ogni pachetto spedito devo aspettare l'ACK.


### Scelta del tempo di timeout
* Se timeout < RTT si ritrasmette un pacchetto due volte (non aspetto abbastanza)
* Se timeout è molto lungo rischio di non reagire subito ad una perdita
* Il valore ottimale sarebbe RTT.

## Go back N
* Miglioramento di stop and wait: invio più PDU prima di attendere ACK
* Concetto di **finestra di trasmissione** ![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20W_T): quante PDU possono essere inviate prima di ricevere conferma

Il trasmettitore quando vuole trasmettere:
1. Invia ![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20W_T) PDU
1. Attiva un timer all'ultima PDU inviata
1. Attende ACK
1. Per ogni ACK ricevuto in sequenza:
	* Scorrere in avanti la finestra di tx di tanti posti quanti sono i pacchetti confermati dall'ACK
	* Ovvero invia nuovi pacchetti
	* Torna al punto 2
1. Se scade il timeout, ovvero niente ACK dell'ultima PDU trasmessa, si ritrasmettono tutte le PDU non ancora confermate (quelle in finestra) => go back N

Il ricevitore (a finestra unitaria) funziona come nello stop and wait.

## Selective repeat

* Miglioramento di go back N: anche il ricevitore ha una finestra non unitaria
* Concetto di **finestra di ricezione** ![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20W_R): quante PDU (non in sequenza) possono essere accettate e memorizzate dal ricevitore

Il trasmettitore funziona cone nel go back N.

Il ricevitore quando riceve la PDU:
1. Controlla correttezza
1. Se la PDU:
	* È in sequenza (non ci sono "buchi" prima):
		* La invia al livello superiore
		* Fa avanzare finestra
	* Non è in sequenza:
		* Se in finestra la memorizza
		* Se non in finestra la scarta
1. Invia comunque un ACK relativo al primo "buco" (a sinistra)

Vale la relazione ![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20W_T%20&plus;%20W_R%20%5Cleq%202%5Ek), con `k`=numero bit per numerazione. 


## Efficienza dei protocolli a finestra

### Stop and wait

![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20%5Ceta%20%3D%20%5Cfrac%7BT_%7BTX%7D%7D%7BT_%7BTX%7D%20&plus;%20RTT%7D)

Dividendo per Ttx:

![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20%5Ceta%20%3D%20%5Cfrac%7B1%7D%7B1&plus;2a%7D%5C%2C%2C%20a%3D%5Cfrac%7BRTT%7D%7B2%5Ccdot%20T_%7BTX%7D%7D)

### Go back N / Selective Repeat

![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20%5Cbegin%7Bcases%7D%201%20%26%20W_T%5Cge%201&plus;2a%20%5C%5C%20%5Ccfrac%7BW_T%7D%7B1&plus;2a%7D%20%26%20W_T%3C%201&plus;2a%20%5Cend%7Bcases%7D)

### Throughput

![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20%5Ctheta%3D%20%5Ceta%20C)

Con `C`=capacità del canale
