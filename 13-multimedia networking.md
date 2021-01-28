# Multimedia networking

Con Multimedia intendiamo contenuti di tipo diverso, come audio o video.
L'audio è un segnale analogico che deve essere campionato e quantizzato prima di poter essere trasmesso in rete, formati famosi sono CD o MP3.
Il video è una sequenza di immagini statiche rappresentate ad una velocità costante. Può essere presente una ridondanza spaziale o temporale per ridurre il numero di bit e mandare solo quelli che sono cambiati (temporale) o mandare un'informazione sul valore di un pixel e quante volte si ripete (spaziale). Il video può avere bitrate costante (CBR) o variabile (VBR).
Ci sono 3 tipi di applicazioni multimediali:
1) Applicazioni di streaming di contenuti memorizzato (su un server) es. Netflix;
2) Applicazioni di streaming di contenuti live es. eventi sportivi.
3) Applicazioni interattive voce/video over IP es. Skype;
Ordine crescente di difficoltà.


	1) Streaming di contenuti memorizzati
Se lo streaming avvenisse su una rete in cui non ci sono ritardi dovuti a congestioni, il ritardo sarebbe fisso, cioè quello di propagazione, non ci sarebbe nessun problema. In realtà i pacchetti si fermano nei buffer dei router e si accumulano ritardi variabili, allora deve essere introdotto un "playout buffer" che introduce un "playout delay". I pacchetti vengono bufferizzati man mano che arrivano e la riproduzione del contenuto inizia dopo un certo tempo ma a un rate costante.
Meglio usare UDP o TCP?
UDP ha difficoltà con i firewall e non fa error recovery, oggi la maggior parte quindi usa TCP. Dato che TCP fa controllo di congestione e il throughput si potrebbe abbassare, è stato introdotto DASH, Dynamic Adaptive Streaming over Http. 
Il server divide i contenuti in chunk (come BitTorrent) e per ogni chunk crea diverse versioni codificate a rate diversi (ad esempio 360p, 720p, 1080p). Quando l'applicazione si accorge che potrebbero esserci problemi di banda, interviene prima che TCP abbassi il throughput inviando chunk di dimensione inferiore e se la banda torna disponibile ricomincia a mandare chunk a dimensione maggiore, ad esempio su YouTube si abbassa la qualità mentre guardiamo un video e poi si rialza.

	2) Streaming di contenuti live
Caratteristica che li distingue dai contenuti memorizzati sono i ritardi, che devono essere bassi. Oggi è spesso comunque usato TCP grazie al DASH, nonostante UDP sia migliore a livello di ritardi.
I content provider usano i CDN, Content Delivery Network, per permettere agli utenti di fruire del contenuto non da un unico server, ma da più server dislocati sul territorio geografico e l'utente utilizza il server più vicino. È vantaggioso rispetto all'approccio centralizzato sotto molti punti di vista, infatti un solo server potrebbe essere congestionato dalle molte richieste e i ritardi potrebbero variare di molto in base alla locazione fisica dell'utente relativamente al server. 
Esistono due approcci: enter deep e bring home.
- enter deep: I server del cdn vengono dislocati sulla rete d'accesso per permettere all'utente di raggiungerli in pochi hop. 
- bring home: un numero minore di server di maggiori dimensioni vicino ma non dentro le reti di accesso, via di mezzo tra enter deep e approccio centralizzato.

	3) Applicazioni interattive
	VOIP - VOICE OVER IP
Utilizzata perchè fornisce funzioni in più (come la presenza) o la possibilità di chiamare gratuitamente. Il ritardo deve essere basso, <150ms ma non >400ms, altrimenti viene persa la parte conversazionale.
VOIP usa UDP perchè è preferibile perdere qualche pacchetto piuttosto che avere ritardi, anche se a volte ci possono essere problemi sul firewall; se ci sono problemi l'applicazione automaticamente usa TCP (Skype "trucca" TCP permettendo la perdita dei pacchetti per renderlo simile a UDP ma facendolo passare dai firewall). Vengono anche ridotti i costi per l'ISP con TOIP (Telephone), in cui si collega il telefono alla rete IP e la voce viene mandata su Internet, non sulla rete telefonica.
Un problema di VOIP potrebbe essere quello della segnalazione, ovvero lo squillo del telefono nel caso del telefono tradizionale per iniziare la comunicazione, per comunicare l'IP, mettersi d'accordo sui codec ecc. 
 
		SKYPE
Skype usa un protocollo proprietario ma si può intuire cosa fa. Usa una rete P2P in cui è presente un login server, in cui sono memorizzate le credenziali degli utenti.
I nodi non sono uguali, ma sono presenti client e supernodi. I supernodi creano una rete tra di loro (overlay) e i client si collegano ai supernodi (l'IP dei supernodi viene ottenuto dai client dopo aver fatto l'accesso ai login server). 
Quando un utente vuole chiamare lo fa attraverso i supernodi, che effettuano il routing delle chiamate sull'overlay.

		RTP - Real Time Protocol
Layer sopra UDP per migliorare il suo servizio e aggiungere numero di sequenza, tipo di payload (audio o video) e timestamp. I pacchetti RTP sono incapsulati dentro UDP. RTP non migliora la qualità del servizio della rete perchè comunque poggia su IP.

		SIP - Session Initiation Protocol
Protocollo usato per creare, modificare e terminare sessioni. Comprende la segnalazione. Vengono offerti servizi aggiuntivi come la possibilità di modificare IP e porta durante la chiamata, l'aggiunta di partecipanti o di media.
Un utente in un servizio VOIP che usa SIP viene identificato tramite username o indirizzo mail.
	Messaggio SIP
Molto simile ad HTTP.
Per far partire la chiamata viene usato il comando INVITE seguito dal nome e sulle successive righe l'header. Finito di specificare l'header viene saltata una riga e inizia il corpo. All'interno del corpo viene descritta la sessione in funzione di parametri su tipo di chiamata (uso un altro protocollo, "sdp" Session Description Protocol), versione IP, IP, attributo m, che sta per media e indica se si tratta di audio o video, porta, RTP e codec, rappresentato da un numero, se ne possono specificare diversi e poi il ricevitore può scegliere.
Come si fa a sapere l'indirizzo IP del nome specificato nel comando INVITE?
Viene usato un server intermediario (SIP Server) che cerca l'utente finale. Questi  SIP server fungono da registrar o da proxy.
- Registrar, ovvero ogni utente quando accede al client SIP usa un messaggio REGISTER per dire al suo SIP server dove (quale IP) si trova. 
- Proxy, cioè fa da intermediario tra l'utente e il destinatario. Ogni utente usa il suo SIP server connettendosi al SIP server del destinatario per saperne l'IP.

	PERCORSO SIP:
1) A manda INVITE al suo SIP Server (proxy).
2) SIP Server di A trova IP di SIP server B e inoltra INVITE. L'IP viene trovato usando un server DNS (servono record di tipo SRV e NA-PTR).
3) Il SIP Server di A da un feedback positivo 100 TRYING (chiamato provisional) con cui conferma di aver fatto quello che è stato chiesto (inoltrare INVITE in questo caso).

A questo punto la chiamata non è stata ancora instaurata 

4) SIP server B inoltra INVITE a B e manda indietro (verso SIP Server di A) 100 TRYING.
6-8) Viene fatto squillare il telefono B e viene mandato da B ad A attraverso i SIP server un messaggio 180 RINGING (Quindi B->SIP B, SIP B->SIP A, SIP A->A).
9-11) Quando viene accettata la chiamata B manda 200 OK ad A attraverso i SIP Server.
12) Visto che potrebbe funzionare su UDP che non ha acknowledge, A manda ACK a B per la ricezione di 200 OK.
- La chiamata è ATTIVA, data flow usando RTP.
13) Per chiudere la chiamata si manda messaggio BYE. 14)Si risponde con 200 OK.
Data flow e passi 13 e 14 possono avvenire direttamente da A a B senza passare per i SIP server. Fino ad 11 è sicuro il coinvolgimento dei server.

Ci può essere un header "record route" messo dai server per far capire all'user agent che si deve sempre passare dai server (per motivi di fatturazione per esempio).
Questo schema di comunicazione host - server - server - host è detto trapezoide per la forma, perchè prima passo dai server per l'instaurazione e poi uso la base del trapezio per le successive comunicazioni.

H.323 vecchio e non usato, complesso, proviene dalla telefonia e i messaggi sono più difficili.

	Network support for multimedia
IP non è in grado di fornire supporto alla qualità del servizio, allora si sono usati principalmente due approcci, Differentiated e Integrated Service, quest'ultimo anche detto Per-connection QOS. 
- Differentiated Service cerca di dare priorità ad un certo tipo di traffico piuttosto che un altro con il campo DS in sostituzione a TOS nell'intestazione di un pacchetto IP, es. voce piuttosto che HTTP
- Integrated Service, ogni router tiene statistiche su ogni singolo flusso, non scalabile perchè usa algoritmi complessi.







