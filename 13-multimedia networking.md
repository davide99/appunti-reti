# Multimedia networking

Tre tipi di applicazioni multimediali:
1. Streaming di contenuti memorizzati (es. Netflix)
1. Streaming di contenuti live (es. eventi sportivi)
1. Applicazioni interattive voce/video over IP (es. Skype)

## Streaming di contenuti memorizzati
* Ritardi variabili dovuti a congestioni (**jitter**)
* Introduzione del **playout buffer**:
	* Pacchetti bufferizzati nel *playout buffer*
	* **Playout delay**:
		* Ritardo tra ricezione pacchetto e playout
		* Utilizzato per "tamponare" il jitter successivo

### Dynamic Adaptive Streaming over HTTP (DASH)
* Server divide contenuti in chunk
* Per ogni chunk diverse risoluzioni (es. 360p, 720p, 1080p)
* Applicazione rileva problemi di banda:
	* Invia chunk di dimensione inferiore
	* Se la banda torna disponibile utilizza chunk a qualità maggiore

## Streaming di contenuti live
* Spesso usano DASH
* Content provider usano **CDN** (**Content Delivery Network**),:
	* Più server dislocati geograficamente

## Applicazioni interattive (VOIP)
* Ritardo deve essere basso
* Usa UDP:
	* Preferibile perdere qualche pacchetto che avere ritardi
* Utilizzato da alcuni ISP evitando rete telefonica "standard"
 
###	Skype
* Rete P2P, composta da:
	* **Login Server**
	* **Supernodi**:
		* Creano rete tra di loro (overlay)
		* Client si collegano ai supernodi
		* Effettuano routing delle chiamate sull'overlay
	* **Client**

### Real Time Protocol (RTP)
* Layer sopra UDP
* Campi principali:
	* **Numero di sequenza** (tipo TCP)
	* **Tipo di payload**: codifica audio o video
	* **Timestamp**
	* **SSRC**: ID dello stream
* Nessun QoS (IP non ha QoS)

### Session Initiation Protocol (SIP)
* Usato per creare, modificare e terminare sessioni
* Prevede:
	* Segnalazione
	* Possibilità di modifica IP/porta durante la chiamata
	* Aggiunta di partecipanti
	* Aggiunta di uno stream
* Utente identificato tramite username/indirizzo mail

#### Messaggi SIP
* Simile ad HTTP
* Diversi messaggi:
	* **INVITE**:
		* Fa partire la chiamata
		* Contiene informazioni su:
			1. "Mail" mittente
			1. "Mail" destinatario
			1. Codec utilizzati
			1. Stream disponibili
	* **REGISTER**:
		* Client invia a server SIP il proprio IP
* **SIP Server**: gestisce mapping *"mail" destinatario* <=> *IP destinatario*
	* Due modalità di funzionamento:
		1. **Registrar**: 
			1. Utente accede al client SIP
			1. Client invia messaggio REGISTER
		1. **Proxy**:
			1. A manda attraverso il suo proxy server *INVITE* a B
			1. Proxy server di A instrada *INVITE* a proxy server di B
			1. B risponde
			1. Risposta segue percorso inverso

#### Percorso SIP
A chiama B, diverse "fasi":
1. A->B: invio di *INVITE*
1. A<-B: invio di *RINGING*
1. A<-B: Invio di *OK* quando B "alza la cornetta"
1. A->B: Invio di *ACK*
1. Flow dei dati (es. con RTP)
1. A->B (A<-B): Invio di *BYE*
1. A<-B (A->B): Invio di *OK*

* Schema di comunicazione A - Server - Server - B
* Detto a **trapezoide**:
	* Prima passo dai server (per l'instaurazione)
	* Poi uso la base del trapezio (per dialogo A<->B)

### H.323
* Vecchio, complesso, non usato
* Proviene dalla telefonia
* Svolge funzioni sia di SIP sia di RTP

## Supporto multimediale a livello rete
* IP non fornire QoS
* Due approcci per fornire QoS:
	1. **Differentiated service**:
		* Cerca di dare priorità alla classe di traffico
		* Utilizzato campo DS (prende il posto di TOS nell'header IP)
		* Es. voce piuttosto che HTTP
	1. **Integrated Service**, anche detto **Per-connection QoS**:
		* Ogni router mantiene statistiche su ogni singolo flusso
		* Algoritmi complessi => Non scalabile
