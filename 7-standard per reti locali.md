Nelle reti locali lo strato 2 è suddiviso in due sottolivelli:
LLC: Logical Link Control e MAC: Medium Access Control.
Funzioni principali del livello 2 sono delimitazione trame, multiplazione, rilevazione errore, controllo di flusso sull'interfaccia verso livelli superiori e indirizzamento.
Con LLC si possono multiplare più protocolli di livello superiore;
Con MAC si identifica la scheda tra i nodi della LAN tramite indirizzi MAC sorgente e destinatario. Indirizzo composto da 6 byte in esadecimale e salvato nella ROM della scheda. I 3 byte più significativi identificano il costruttore, gli ultimi 3 identificano la scheda internamente al produttore. Gli indirizzi MAC possono essere broadcast(tutti F), multicast o unicast. In multicast ci sono due modalità, solicitation per richiedere servizi e advertisement per diffondere informazioni.

	ETHERNET E IEEE 802.3
		LIVELLO MAC
Ethernet fu sviluppato nel '76. IEEE 802.3 è basato su Ethernet, sono simili ma differiscono per caratteristiche relative al livello MAC e fisico. 
Sfrutta protocollo CSMA/CD 1-persistente, se viene rilevata una collisione viene inviata una sequenza di jamming dopo aver interrotto la trasmissione. Le trame tra 802.3 ed Ethernet differiscono in due aspetti:
Il campo EtherType in Ethernet diventa la lunghezza della trama, i dati in Ethernet hanno una dimensione da 46 a 1500 byte, mentre in 802.3 vanno da 0 a 1500 e viene introdotto un campo di padding di 0-46 byte. La lunghezza della trama dipende dalla velocità del mezzo, dalle dimensioni della rete e dall'IPG (InterPacket Gap), che ne segnala la fine.

		LIVELLO FISICO
Originariamente venivano usati cavi coassiali, UTP o fibra, oggi solo gli ultimi 2. Prima dimensioni della rete di massimo 2800m con trame da 64B, oggi max 100m (su fibra diversi km) e trame da 1500 byte (??Verificare??). Prima max 10Mb/s, oggi 100Gb/s (su fibra forse di più).

	RETI ETHERNET OGGI
Reti semplici, economiche e con topologia a stella gerarchica (interconnessione di reti Ethernet per aumentare l'estensione, gli utenti e la sicurezza). Si usano Hub (in disuso) e Switch. 
		HUB
Lavora a livello 1, centro stella passivo che non riconosce le trame e non separa i domini di collisione.
		SWITCH
Lavora a livello 2, centro stella attivo che riconosce ma non modifica le trame e separa i domini di collisione. Utilizza Store And Forward.
Una LAN interconnessa tramite switch è detta estesa, ciò permette di partizionare in reti indipendenti, annulla la probabilità di collisione e la rete si trasforma in una rete a commutazione di pacchetto. In questo caso non serve un protocollo d'accesso ma si introducono ritardi dovuti a store and forward. Migliorate gestibilità e sicurezza.
La presenza dello switch non modifica il comportamento dei terminali, il cui unico requisito è possedere un indirizzo di livello 2, vi è il cosiddetto transparent switching. Consiste in:
- Address learning, acquisizione di indirizzi e creazione in tabella (dinamica) di una tupla con una corrispondenza indirizzo MAC <-> port_id dello switch. Ad ogni tupla viene associato un timer per aggiornare periodicamente la corrispondenza. Utilizza algoritmo backward learning, che funziona solo in assenza di anelli;

- frame forwarding, grazie alla tabella i frame ricevuti vengono inoltrati con filtraggio degli indirizzi. Se viene ricevuto un indirizzo non presente in tabella si inoltra su tutte le porte per poterlo imparare e aggiungere;

- esecuzione dell'algoritmo spanning tree per eliminare eventuali anelli spegnendo determinate intefacce. Requisiti sono uno switch_id unico per ogni switch nella rete, indirizzo multicast che raggiunga tutta la rete e dei costi associati ad ogni porta identificata univocamente in uno switch.


		VLAN
LAN virtuali logicamente partizionate, deve essere supportato dallo switch.


	WIFI
IEEE 802.11 è la famiglia di standard che regolano le tecnologie per le reti locali wireless. WiFi indica solo la certificazione di compatibilità e aderenza allo standard.
L'architettura di IEEE 802.11 può essere:
Con infrastruttura, ovvero con la presenza di AP (Access Point) a cui i terminali si collegano per comunicare nella rete;
Ad hoc, in cui la comunicazione avviene direttamente tra terminali (WiFi Direct).

	IEEE 802.11
		STRATO FISICO
Lavora su bande di frequenza a 2.4 GHz con 14 canali(3 non sovrapposti) e 5 GHz(23 canali disgiunti). La velocità di trasmissione dipende dalla versione, dalla qualità del canale e distanza dall'AP.
		STRATO MAC
Si usa IEEE 802.11 DCF (Distributed Coordination Function) che è basato su CSMA/CA; le stazioni sono half-duplex.
DCF: In trasmissione e ricezione uguale a CSMA/CA. È presente un NAV (Network Access Vector), con cui gli altri dispositivi sanno che c'è in corso una trasmissione e evitano di inviare dati. Il tempo della trasmissione si conosce grazie ad un campo nell'intestazione.

Problema del terminale nascosto: Due stazioni sono a portata dell'AP ma non a portata reciproca, quindi se una di esse trasmette, l'altra sente comunque il canale libero.
Soluzione -> DFC con handshaking: si articola in 2 fasi:
- Trasmissione di una microtrama detta RTS (Ready to send) al ricevitore. Contiene parametri della connessione;
- Ricevitore risponde dopo SIFS con microtrama CTS (Clear to send);
Qualsiasi terminale nascosto sente il canale occupato e attende tempo di backoff. Le collisioni non sono totalmente evitate perchè le due stazioni nascoste l'un l'altra possono mandare RTS insieme.

Se sono presenti più connessioni allo stesso AP che inviano a velocità diverse, il throughput di tutti si uniforma a quello della connessione più lenta. (Cioè anche se stazione A invia dati molto velocemente, stazione B più lenta impiega più tempo a inviare e allora si rallenta tutta la rete).