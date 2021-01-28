# Livello trasporto

Servizi offerti dal livello trasporto: 
1. Multiplexing e demultiplexing
1. Comunicazione end-to-end
1. Controllo di congestione (traffico rete)
1. Controllo di flusso (traffico nodo destinatario)
1. Nessuna garanzia su banda / ritardi:
	* È una rete a pacchetto, non a circuito

## Protocolli di livello 4
* **TCP**
* **UDP**

## Multiplexing e demultiplexing
```
0              16           31
+-------------+------------+
| Source port | Dest. port |  <=  Comuni a TCP e UDP
+-------------+------------+
:                          :
.    Altri campi header    .
:                          :
+--------------------------+
|                          |
|         Payload          |
|                          |
+--------------------------+
```

* Livello trasporto aggiunge porta sorgente e destinazione
* I flussi vengono multiplati/demultiplati grazie al numero di porta
* **Socket**:
	* Interfaccia tra due processi in rete
	* Coppia (IP, # porta)
* **UDP**
	* Bisogna specificare solamente socket di destinazione
	* Flusso unidirezionale
	* In ricezione UDP inoltra payload verso processo associato a quella porta
- **TCP**
	* Bisogna specificare socket destinazione e socket sorgente
	* Flusso bidirezionale
	* In ricezione TCP inoltra payload verso processo associato a quel socket

## UDP
* Servizio best effort:
	* Trasferimento inaffidabile
		* I pacchetti possono andare persi
	* Trasferimento non ordinato
		* I pacchetti possono arrivare fuori sequenza
	* Affidabile quanto IP
	* Nessun controllo di flusso / congestione
* Connectionless

### Segmento UDP
```
0              16           31
+-------------+------------+
| Source port | Dest. port |
+-------------+------------+
|   Length    |  Checksum  |
+-------------+------------+
|                          |
|         Payload          |
|                          |
+--------------------------+
```

**NB**: Il multicast/broadcast è possibile solo con UDP. Con TCP non posso mantenere informazioni di stato per ogni host per poter effettuare controllo di flusso/sequenza.

## TCP
* Point-to-point
* Trasferimento affidabile e in ordine
* Controllo di flusso / congestione
* Comunicazione full-duplex
* Connection-oriented (handshake)

### Segmento TCP
![](img/TCP.png)
* **Sequence number**:
	* Offset corrente in byte
	* Rispetto all'inizio dello stream
	* Inizializzato a numero casuale
* **ACK number**:
	* Prossimo *sequence number* che il ricevitore si aspetta
	* Cumulativo
	* Possibilità di *piggyback*
* **Flag**:
	* **ACK**: *ACK number* valido?
	* **SYN**: Apertura connessione
	* **FIN**: Chiusura connessione
* **Receive window**: Per controllo flusso
* **Opzioni**:
	* **MSS** (Maximum Segment Size)

**NB**: I segmenti ricevuti fuori sequenza possono essere scartati o meno, implementazione libera.
		
## Funzionamento lato trasmettitore
1. Crea segmento con `seq #` adeguato
1. Invia segmento
1. Fa partire timer se non c'è già altro timer
	* Timer sull'ultimo segmento unacked
1. Se timeout:
	1. Ritrasmette segmento che l'ha causato
	1. Riavvia timer
1. Se riceve ACK:
	1. Effettua acknowledge
	1. Fa partire timer su segmento unacked più vecchio

## Funzionamento lato ricevitore (generazione ACK)
**Evento** => **Azione**

- Arriva segmento in ordine, segmenti precedenti tutti ACKed => 
	* Ack ritardato: aspetta altri segmenti per 500ms
	* Se non arriva niente manda ACK

- Arriva segmento in ordine, solo segmento precedente non ACKed =>
	* Manda subito ACK cumulativo per entrambi

- Arriva segmento fuori sequenza: `seq #` maggiore di quello che mi aspettavo, c'è gap =>
	* Invio ACK con `seq #` che mi aspettavo prima (*ACK duplicato*)
	* Es: aspettavo `#3`, arriva `#5` => invio di nuovo ACK `#3`

- Arriva segmento che completa gap parzialmente o totalmente (segmento che fa spostare la finestra) =>
	* Mando ACK

## Fast retransmit
* Tempi di timout relativamente lunghi
* Passa del tempo prima di ritrasmettere segmento perso
* Rilevo segmenti persi con i **tripli ACK duplicati**

**Funzionamento**
* Ricevo per 3 volte ACK duplicati (1 ACK "normale" + 3 duplicati)
* Probabilmente segmento perso
* Trasmetto subito segmento con `seq #` non ancora ACKed

## Scelta del timeout
* Il timeout dipende dall'RTT
* L'RTT varia =>
* Si stima l'RTT con una media mobile esponenziale:
	* L'influenza del passato decresce esponenzialmente

## Controllo di flusso
* Evita di sovraccaricare il nodo ricevitore
* Ogni ricevitore:
	* Ha un buffer
	* Spazio libero nel buffer di dimensione `rwnd`
	* Comunica al trasmettire `rwnd` nel campo *receive window* dell'header TCP
* Trasmettitore adatta la sua finestra di trasmissione: `twnd<=rwnd`

## Gestione della connessione
La connessione viene instaurata con il cosiddetto "Handshake" in cui ci si mette d'accordo sullo stabilire la connessione e sui parametri di quest'ultima. Spesso ci si riferisce a questa procedura con SYN-SYNACK-ACK per i flag settati.
1) host sceglie randomicamente il seq# e manda un pacchetto (SYN) vuoto con il flag SYN=1;
2) server sceglie un seq#, manda un pacchetto (SYNACK) con ACK=1, SYN=1 e con ACK# settato al seq# che ci si aspetta (fa piggybacking);
3) dopo aver ricevuto il messaggio SYNACK il client sa che il server è live e manda un pacchetto (che potrebbe già contenere dati) con flag ACK=1 e ACK# settato al seq# che ci si aspetta.
Dopo aver ricevuto ACK il server sa che il client è live.

Per chiudere la connessione viene mandato un messaggio con FIN=1. Da quel momento chi ha mandato FIN=1 non può più mandare ma può ancora ricevere; l'altro può ancora fare entrambe.
L'altro manda FIN=1 e non può più mandare dati. Il primo conferma la ricezione con ACK e la connessione viene chiusa. 

		PRINCIPI DI CONTROLLO DELLA CONGESTIONE
Ci potrebbero essere due soluzioni:
1) Coinvolgere gli apparecchi di livello 3
2) controllo di congestione solo a livello di end-system.
Scelta la seconda soluzione altrimenti si andrebbe a gravare troppo sui router.
TCP su ogni end-system stima una finestra di congestione fittizia dedotta da ritardi o perdite.

		CONTROLLO DI CONGESTIONE IN TCP
Un algoritmo è congestion avoidance (additive increase - multiplicative decrease). Il trasmettitore continua ad incrementare la cwnd (congestion window - la finestra di congestione fittizia) di 1 MSS(Max Segment Size, definito sopra nell'intestazione TCP) per ogni RTT fino a quando non rileva una perdita, in quel caso la finestra viene dimezzata. 
Il rate è circa cwnd/RTT byte/sec.

Un secondo algoritmo è slow start, usato perchè con congestion avoidance la rete può essere molto lontana dalla congestione e quindi crescendo linearmente si arriva lentamente.
Una volta che inizia la connessione, si aumenta cwnd esponenzialmente fino alla prima perdita. In questo caso dipende cosa ha fatto rilevare la perdita:
- 3 ACK duplicati: Segmenti persi ma comunque ricevo ancora gli ACK, il traffico ancora viaggia sulla rete, si va verso la congestione ma ancora la situazione non è grave, ha senso dimezzare cwnd, cresco linearmente. ==> vado in congestion avoidance.
- Timeout, i segmenti potrebbero essere persi o non arrivare proprio e non ricevo neanche più gli ACK.
la situazione è più grave e la congestione è elevata. Cwnd viene settata a 1 per ricominciare da capo con slow start. Slow start procede fino ad un certo treshold (soglia) che è stato settato quando è scattato il timeout al valore cwnd/2. Dopo continuo con congestion avoidance.

	FAIRNESS
TCP si definisce come un protocollo fair, ovvero se K connessioni TCP usano lo stesso algoritmo condividendo lo stesso canale che ha un collo di bottiglia a un rate R, tutte le connessioni vanno a R/K (mediamente), perchè man mano che mandano, con il multiplicative decrease la finestra inizia a stabilizzarsi attorno a un valore medio.
Se invece ci fosse una connessione UDP, che non effettua congestion avoidance, quando TCP decrementa la sua finestra di congestione, UDP usa ancora più banda essendocene più libera dopo il dimezzamento di TCP.  
