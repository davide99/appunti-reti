	I MEZZI TRASMISSIVI
I mezzi trasmissivi si possono identificare in base ai segnali utilizzati, ovvero elettrici, ottici o radio.
Un buon mezzo è caratterizzato da basse impedenza o resistenza, flessibilità, resistenza alla trazione.

I mezzi trasmissivi elettrici soddisfano le richieste in modo adeguato. Parametri importanti dei mezzi elettrici sono l'attenuazione che cresce con la distanza e la diafonia, ovvero i disturbi elettromagnetici generati da cavi vicini. I mezzi elettrici fondamentali sono:
Doppino: composto da una coppia di fili ritorti per ridurre le interferenze tramite tecniche trasmissive, possono essere UTP (Unshielded Twisted Pair) o S/FTP (Shielded/Foiled Twisted Pair) da dopo cat 6a. Facile da installare ed economico.
Cavo coassiale: composto da un filo avvolto in un dielettrico e una o più schermature. Alte velocità trasmissive e maggiore schermatura ai disturbi esterni, ma più costosi e difficili da installare.

I mezzi trasmissivi ottici sfruttano la fibra ottica, composta da un filo di vetro, costituito da due parti (core e cladding) con indici di rifrazione diverse, avvolto in rivestimenti atti a proteggerlo. Per la legge di Snell i raggi luminosi entranti con un certo angolo minore di un angolo limite restano intrappolati nel filo. Caratterizzata da velocità trasmissive molto elevate e immunità da disturbi elettromagnetici, sono però difficili da installare e abbastanza costose. Le fibre possono lavorare in una "finestra" di lavoro intorno a 1300nm in cui le attenuazioni al chilometro sono basse. Volendo si potrebbero anche usare i laser ma sarebbero più costosi a favore dell'offerta di un segnale più pulito e a maggiore frequenza. Spesso posate sul fondo dell'oceano.

I mezzi trasmissivi radio sfruttano le onde radio per trasmettere segnali tramite delle antenne. Se almeno uno tra RX e TX è in movimento si parla di canale radiomobile. La qualità della trasmissione dipende dal rapporto tra potenza ricevuta e potenza trasmessa e dipende direttamente dal quadrato della lunghezza d'onda e inversamente dal quadrato della distanza (Eq. di Friis). Nella realtà i segnali radio sono soggetti a:
Attenuazioni(Fading) dovute ad ostacoli, fenomeni atmosferici o altre sorgenti sulla stessa frequenza; 
Rumore(Shadowing): dovuto alla presenza di ostacoli che determinano rifrazione o riflessione del segnale creandone copie.


	LA TRASMISSIONE SUL MEZZO FISICO
La trasmissione avviene sfruttando diverse tecniche che servono ad associare un particolare segnale ad una certa informazione; le tecniche maggiormente usate sono codifiche di linea e modulazioni digitali.

Codifiche di Linea: Rappresentazione di informazioni numeriche mediante l'uso di segnali numerici (solo elettrici e ottici), ottima per segnali impulsivi. Esempi possono essere:
Unipolari: Una sola polarità, ovvero viene usata tensione positiva per "1" e nulla per "0". Componente continua non nulla, perdita di sincronismo se vengono mandati molti valori uguali e si può avere surriscaldamento (ottica) con sequenze di "1".
Polari: Usano due polarità, riducendo la componente continua, si distinguono in: 
	- NRZ (Non Return to Zero): sequenze lunghe dello stesso simbolo possono portare perdite di sincronismo.
	- RZ (Return to Zero): Ritorno a zero prima di ogni bit, potenzialmente frequenza doppia.
	- Bifase: es. Manchester, ogni bit è rappresentato da una transizione di tensione da basso ad alto o viceversa. Il sincronismo è automatico perchè non ci sono mai periodi privi di transizioni.
Bipolari: "0" rappresentato da tensione nulla, "1" da tensione con polarità opposta.
nBmB: rappresentazione di simboli a n bit con simboli a m bit (popolare 8B10B). Utili perchè posso evitare parole con molti 0 o 1 consecutivi, limitano la componente continua e i simboli che "avanzano" possono essere usati come descrittori di sequenze speciali.


Modulazioni digitali: Rappresentazione di informazioni numeriche mediante l'uso di segnali analogici (tutti i mezzi, radio, ottici ed elettrici). Usano un segnale sinusoidale detto portante variando ampiezza (ASK, Amplitude Shift Keying), fase (PSK), frequenza(FSK).
Un altro tipo di modulazione è la QAM ( Quadrature Amplitude Modulation), che è così denominata in quanto il segnale può essere visto come la somma di due segnali portanti alla stessa frequenza modulati in ampiezza e in quadratura (ovvero che differiscono di 90°). Ciascun tipo di modulazione QAM è caratterizzato da un diagramma sul piano complesso (costellazione), su cui sono rappresentati tutti i possibili stati della portante (le legittime posizioni del vettore) (QAM => WIKIPEDIA).
Spesso precedute da un numero che indica il numero di simboli del segnale modulante, ovvero il segnale che specifica le variazioni (il segnale che dobbiamo "codificare/modulare"). Se non è specificato nulla di default 2 per P/F/ASK e 4 per QAM. Maggiore il numero maggiore il bitrate.

	RETI DI ACCESSO E DI TRASPORTO
La rete di accesso consiste negli apparati e mezzi che collegano l'utente col nodo di accesso del gestore. Consiste nell'ultima tratta di rete necessaria ad arrivare dalla prima centrale di commutazione (es. cabina) agli utenti finali ("Ultimo miglio").
La rete di accesso può essere realizzata con diverse tecnologie come DSL, PON, HFC o la rete cellulare.
DSL: Digital Subscriber Line poi evoluta in Asymmetric DSL. Si accede tramite un MODEM che modula il segnale per renderlo adatto alla trasmissione analogica e lo demodula in ricezione. Oltre al modem un altro apparato utente è il filtro splitter che separa voce e dati (https://www.sostariffe.it/news/wp-content/uploads/2017/04/Splitter-ADSL.jpg).
Per quanto riguarda gli apparati del gestore, sono presenti filtri/modem POTS che hanno una funzione analoga al filtro splitter dell'utente e DSLAM (DSL Access Multiplexer) che riceve segnali dati e li convoglia su un unico canale. 
Un'evoluzione di ADSL è VDSL in cui V sta per Very-High-Rate che porta bitrate più alti. A/VDSL è soggetta ad attenuazioni di velocità al crescere della distanza.

PON: Passive Optical Network, utilizza fibre ottiche per la connettività di ultimo miglio senza componenti attivi, composta da OLT (Optical Line Terminator) in centrale, ONU (Optical Netwok Units) in cabina, ONT (Terminals) a casa dell'utente e ODN (Optical Distribution Network) che rappresentano mezzi e dispositivi ottici che permettono agli utenti la distribuzione dei segnali.
Sostituisce o integra VDSL, costi maggiori per posa fibra ma alte velocità.

HFC: Hybrid Fiber Coax, vengono usate le infrastrutture presenti per la rete televisiva e include fibra e cavi coassiale nell'ultimo miglio. HFC viene condiviso tra utenti nella stessa zona e include problemi di sicurezza, ma non risentono della distanza; ADSL è punto punto, risente della distanza e usa la rete telefonica standard.

Rete Cellulare: Offre servizi voce/dati agli utenti, consiste in (oggi) 5 generazioni in base a velocità e sicurezza offerta o a tecniche di accesso e tecnologia per realizzarle. Attuata grazie ad antenne di portata limitata sparse sul territorio. Supportano il roaming e l'handover (passare da una antenna ad un'altra senza perdere connessione).

Un ultimo tipo di rete oggi in disuso è la rete satellitare che comprende tre tipologie di orbite:
GEO (35000 km, 3 sat, 270ms), broadcast e dati.
MEO (23000 km, 10 sat, 100ms), GPS
LEO (<1000 km, 50 sat, 5ms), telefono satellitare.

La rete di trasporto consiste negli apparati e mezzi destinati al transito dei dati.
Crea interconnessione tra reti d'accesso con nodi (router) connessi da linee ad alta velocità (fibra). La rete può essere proprietaria di un ISP o affittata.
Sulle reti di trasporto la trasmissione si è evoluta dalla rete telefonica tradizionale ed è digitale con multiplazione gerarchica a divisione di tempo.
Protocolli di trasporto sono PDH (Plesiochronos Digital Hierarchy) evoluto in SDH (synchronous)

PDA: Plesiocrono=quasi sincrono. Pensata per canali vocali, non usa Store and Forward ed è necessaria una stretta sincronizzazione ottenuta con il meccanismo di bit stuffing (inserzione/rimozione di bit quando necessario). (Es T-1 24 canali per trama, 1 trama ogni 125 microsecondi => 1/125us = 8000 trame/s, 1 bit di segnalazione => (24*8 +1 ) * 8000 = 1.544 Mb/s).

SONET/SDH: Synchronous Optical NETwork in America, in Europa SDH. La topologia è ad anello bidirezionale. Viene inviato un flusso continuo di bit alla stessa velocità per mantenere il sincronismo. La multiplazione è a divisione di tempo. È ottenuto multiplando più tributari (apparati utente) ma non accostandoli come accadeva in PDH, ma interlacciandoli (alternando un byte di ogni tributario). Si può accedere ai sottoflussi accedendo un byte ogni n senza demultiplare. La sincronizzazione è ottenuta grazie all'uso di orologi atomici (WIKIPEDIA)  









