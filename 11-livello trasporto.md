Servizi offerti dal livello trasporto: 
1) Multiplexing e demultiplexing;
2) Controllo di congestione, per evitare di sovraccaricare la rete;
3) Controllo di flusso, per evitare di sovraccaricare il destinatario;
4) Trasferimento affidabile, perchè IP non lo fornisce ma è un protocollo best effort.
NON vengono offerte garanzie sul ritardo o sulla banda.

I principale protocolli usati a livello 4 sono UDP (User Datagram Protocol) che è connectionless e TCP (Transmission Control Protocol) che è connection-oriented.
Entrambi forniscono 1, ma solo TCP fornisce 2,3,4.
A livello 4 non sono presenti apparati di rete perchè i protocolli lavorano nei terminali finali, che eseguono la segmentazione e passano i segmenti a livello rete. Qualsiasi protocollo venga implementato (TCP o UDP), a livello rete non fa differenza perchè sono tutti trattati come datagram IP.
La principale differenza con il livello rete è che il livello rete lavora su una comunicazione logica tra host, il livello trasporto su una comunicazione tra processi in esecuzione sugli host.

			MULTIPLEXING / DEMULTIPLEXING

Ai dati ricevuti dalle applicazioni viene aggiunta un'intestazione a livello di trasporto e passata ai livelli inferiori. In particolare l'intestazione TCP/UDP viene incapsulata nei datagram IP e mandata in rete. In ricezione, grazie alle informazioni contenute nell'intestazione aggiunta dal livello di trasporto si può indirizzare il traffico verso l'applicazione corretta. Come viene ottenuto ciò? Grazie alle porte, ovvero degli identificativi legati al processo in esecuzione (ad esempio 80=HTTP, 22=SSH).
C'è una differenza nel modo in cui viene fatto mux/demux in base al protocollo TCP o UDP.
Introduciamo la definizione di socket, ovvero un'interfaccia di comunicazione tra due processi in rete, identificata da (IP, #porta).
- Caso UDP: Quando un pacchetto viene mandato bisogna specificare solamente la socket di destinazione. In fase di ricezione UDP controlla il numero di porta e inoltra il pacchetto verso il processo.
- Caso TCP: Non basta specificare solo la socket di destinazione ma serve anche quella sorgente. 
Questo perchè TCP in quanto effettua controllo di flusso e congestione deve mantenere diversi parametri per ogni connessione nel caso dovesse effettuare modifiche su una specifica comunicazione. Inoltre su un host ci potrebbero essere più socket TCP, ma con 4 parametri sono identificate univocamente nella rete, es. i web server hanno una socket per ogni client connesso, in questo caso ci sono più socket per un'applicazione.

Ci sono delle porte che sono riservate a diversi scopi e processi ben definiti, ad esempio 53=DNS(UDP), 67=DHCP(UDP), 25=SMTP(TCP), queste porte sono dette well-known ports e hanno un range 0-1024.
Le porte di un client in genere sono selezionate random.

			UDP
UDP offre un servizio best effort in cui i pacchetti possono andare persi o essere consegnati fuori sequenza.
			Intestazione UDP
src port# (16), dst port#(16)
length (16), checksum (16)
payload

Nel campo length è specificata la lunghezza in byte del segmento incluso l'header.
Il checksum è effettuato su tutto il segmento. (In IP solo sull'header). Serve a riconoscere eventuali errori durante il trasferimento.
Rende possibile Multicast IP. Su TCP non è possibile (spiegato dopo).

		TCP
Protocollo point-to-point, multicast non è possibile perchè dovrei mandare lo stesso pacchetto allo stesso modo verso il gruppo di host multicast. Visto che devo mantenere informazioni sulle connessioni per poter effettuare controllo di flusso e di sequenza, se un host dovesse dare problemi dovrei decidere come adattare i parametri della connessione e sarebbe un problema.

I pacchetti sono mandati in ordine, c'è controllo di flusso e di congestione e la comunicazione è full-duplex. Protocollo orientato alla connessione grazie all'handshaking.

			INTESTAZIONE TCP
src port# (16), dst port#(16)
sequence number
ack number
header length, flag come ACK(validità di ack),SYN,FIN per instaurare o chiudere la connessione, receive window (per controllo flusso)
checksum (come udp) 
opzioni, come MSS (Maximum Segment Size)
payload

				SEQUENCE NUMBER E ACK NUMBER
- Il sequence number indica la posizione, all'interno dello stream, del primo byte nel segmento. È inizializzato ad un numero casuale.
- L'acknowledgement number indica il prossimo numero di sequenza che ci si aspetta (usa ack cumulativo).
I segmenti ricevuti fuori sequenza non sono gestiti di default, nel senso che TCP lascia l'implementazione libera; Go-Back-N ad esempio li scartava.
Mandando ACK insieme ai dati sto implementando il cosiddetto piggybacking.
		
			TRASPORTO SICURO
TCP crea un servizio di trasporto affidabile sopra l'inaffidabile IP. Eventuali ritrasmissioni scattano dopo ACK duplicati o fine timeout.
Il timeout è unico come Go-Back-N, se scade viene mandato il segmento che ha causato il timeout. Se si riceve un ACK e ci sono segmenti non ancora confermati riparte il timer.
Al ricevitore possono succedere diversi eventi con gli ACK: 
EVENTO -> AZIONE
- Segmento con seq# che mi aspettavo, dati precedenti ricevuti in modo corretto e confermati ==> Ack ritardato. Aspetta altri dati per 500ms e se non mi arriva niente manda Ack.

- Tutto in ordine, un segmento aveva ACK pendente (situazione precedente) ==> Mando subito ACK e confermo questo e quello prima perchè ACK cumulativo (Risparmio 1 Ack).
ACK ritardato != Cumulativo; Ritardato ha senso solo se uso cumulativi.

- Segmento fuori sequenza. seq# maggiore di quello che mi aspettavo, c'è un gap ==> Invio subito ACK con seq# che mi aspettavo prima.
Es: mi aspettavo#3, arriva#5 ==> invio di nuovo ACK #3

- Segmento che completa il gap parzialmente o totalmente ==> mando subito ACK. Riprende tutto a funzionare normalmente

Un altro modo per attuare il controllo sicuro è quello del fast retransmit, ovvero nel momento in cui mi accorgo di segmenti persi trasmetto subito senza aspettare il timeout. Dopo aver mandato diversi pacchetti, se arrivano i cosiddetti "tripli duplicati ACK", ovvero se il trasmettitore riceve 4 ACK (1 originale + 3 duplicati) per gli stessi dati è molto probabile che il segmento sia stato perso, allora trasmette subito il segmento con il seq# non ancora confermato.
Il timeout non deve essere troppo breve o troppo corto, il problema è che il RTT varia, allora si fa una stima con una media mobile esponenziale, in cui l'influenza del passato decresce esponenzialmente -> stima=(1-a)*stima + a*campione di rtt, a tipicamente 0.125, se =0 considero solo passato, =1 solo presente. Alla stima si aggiunge un margine di sicurezza = (1-b)*margine + b*|campione-stima|, tipicamente b=0.25

			CONTROLLO DI FLUSSO
Viene evitato di sovraccaricare il ricevitore. Ogni ricevitore ha un buffer e fa sapere la quantità di buffer libero attraverso un parametro incluso nell'header TCP (rwnd = receive window). Il trasmettitore adatta la sua finestra di trasmissione ad essere <= di rwnd.

			GESTIONE DELLA CONNESSIONE
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
