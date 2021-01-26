LAN = local area network, ovvero rete che ha un'estensione geografica molto limitata. 
Il canale trasmissivo viene condiviso, perchè averne uno dedicato sarebbe uno spreco in quanto le trasmissioni sono per lo più impulsive o broadcast.
Per la condivisione del canale multiplazione (in codice, frequenza o tempo) non è una soluzione accettabile, allora si usano protocolli per accesso multiplo, la cui famiglia principale è quella a contesa o ad accesso casuale.	
	PROTOCOLLI AD ACCESSO CASUALE
Quando un nodo deve trasmettere invia senza preoccuparsi degli altri nodi, se trova una collisione se due o più nodi trasmettono contemporaneamente. I protocolli ad accesso casuale si preoccupano proprio di gestire questi eventi e in particolare: come renderli meno probabili, come riconoscerli o come recuperare in seguito ad una collisione.

		ALOHA
Protocollo ad accesso casuale inventato nel '70 basato su commutazione di pacchetti su onde radio.
Non è richiesta sincronizzazione e la conferma della ricezione avviene su un canale separato. Probabilità di collisione elevata, in particolare in caso di collisione si esegue un backoff, ovvero i due nodi in collisione si fermano per un periodo di tempo casuale e poi inviano di nuovo. Se si collide di nuovo si raddoppia il tempo di attesa casuale.
		ALOHA SLOTTED
Evoluzione di Aloha con l'introduzione di time slot. Ogni nodo trasmette all'inizio del time slot e in caso di collisione si  ritrasmissione in un altro slot con probabilità p fino al successo.

Protocolli caratterizzati da throughput limitati => efficienza bassa, infatti 0.18 ALOHA e 0.37 ALOHA SLOTTED (efficienza massima, dopo decresce).
Ritardi di accesso nullo o limitato.

		CSMA
Carrier Sense Multiple Access
Si ascolta il canale prima di trasmettere e se lo si trova libero si inizia la trasmissione, altrimenti si pospone. 
Se trovo il canale occupato mi comporto in modo diverso a seconda della persistenza impostata: CSMA p-persistente, aspetto che si liberi il canale e trasmetto con probabilità p, ovvero ritardo la trasmissione con probabilità (1-p). Ethernet usa  CSMA 1-persistente, WIFI usa CSMA non-persistente, ovvero un tempo casuale e ricontrolla, se libero trasmette.

Le collisioni si possono comunque verificare a causa dei tempi di propagazione, in particolare è fondamentale la distanza e la dimensione delle trame, infatti con trame di dimensione maggiore ho meno collisioni perchè sono più esposto alle collisioni inviando più trame più piccole. Disegno: https://www.icloud.com/iclouddrive/0M6tMndhgW4dKfiof8qbUKo5g#06_-_Reti_locali_-_collisioni_su_csma

	VARIANTI DI CSMA
	CSMA/CD (Collision Detection) - Ethernet
CSMA con rilevazione delle collisioni (mezzi cablati). 
Se una stazione trova il canale libero trasmette e continua ad ascoltare anche durante la trasmissione. Se occorre una collisione ferma la trasmissione; non è necessaria conferma di trasferimento se la lunghezza della trama è superiore al doppio del tempo di propagazione. Si introduce la definizione di dominio di collisione, ovvero la porzione di rete nella quale se due stazioni trasmettono insieme o quasi è sicuro che collidano. Se due stazioni sono nello stesso dominio di collisione e viene rispettata la condizione che lunghezza trama > 2*RTT, anche se la seconda stazione inizia a trasmettere un attimo prima che la trama della prima stazione arrivi, la trama della seconda stazione arriva alla prima mentre la trasmissione della prima è ancora in corso, allora rilevo la collisione, altrimenti non ho modo di farlo.
È vantaggioso perchè posso bloccare la trasmissione ed evitare lo spreco di una trasmissione inutile, va usato nelle reti cablate di dimensioni ridotte o di dimensioni ridotte rispetto alla dimensione della trama, o in reti con velocità di trasmissione basse. Si preferisce 1-persistente per ritardi di accesso ridotti e per ridurre costo di collisione.

	CSMA/CA (Collision Avoidance) - WiFi
CSMA con prevenzione delle collisioni (mezzi radio).
Versione non persistente di CSMA in cui è necessaria la conferma di ricezione.
La stazione che vuole trasmettere ascolta il canale per un tempo DIFS (Distributed Interfrace Space) e inizia la trasmissione. Se il canale è occupato o lo diventa durante DIFS aspetta un tempo di backoff, che viene decremento mentre il canale rimane libero, poi si riprova la trasmissione. 
La stazione che riceve la trama, verifica che sia corretta e aspetta un tempo SIFS (Short) prima di inviare ACK, che ha priorità sulle altre trame. Se il trasmettitore non riceve ACK, inizia a decrementare backoff e quando arriva a 0 riprova la trasmissione. Le collisioni si possono comunque verificare e le stazioni raddoppiano il backoff. Prestazioni migliori su reti ridotte.







