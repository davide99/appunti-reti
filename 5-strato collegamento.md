Le funzioni dello strato collegamento sono: 
Controllo di errore, flusso, sequenza con protocolli a finestra;
Delimitazione trama, Multiplazione, Indirizzamento locale e rilevazione errore.

I protocolli derivano da SDLC (Synchronous Data Link Control), poi standardizzato dall'ISO come HDLC(High-Level) e sono:
LAPB e LAPD (obsoleti) LAPF, LLC, PPP.
I protocolli di livello 2 sono usati in:
Reti pubbliche per collegare le utenze finali alla rete del gestore(rete di accesso, derivati da SDLC). Sono presenti due diversi apparati, DTE (Data Terminal Equipment) dell'utente e DCE (Data Circuit-terminating Equipment) del gestore;
Reti private per collegare apparati in ambienti limitati, in questo caso le funzioni di strato 2 sono realizzate da due sottostrati (LLC da SDLC e MAC);
Reti pubbliche di trasporto (ATM).

	FORMATO DELLA PDU
Le PDU hanno un formato generico, sia che si tratti di reti pubbliche sia che si tratti di reti private.
- Flag di delimitazione (8), solitamente 01111110;
- Indirizzo(8), per le configurazioni multi-punto, altrimenti tutto 1;
- Controllo(8/16), per differenziare le PDU di diverso tipo;
- Dati (>0) e CRC(16) per il controllo dell'errore.

Per quanto riguarda la lunghezza, dipende se il protocollo è orientato al bit, ovvero viene mandato un bit alla volta, allora sarà un suo multiplo, altrimenti un multiplo del byte se il protocollo è orientato al byte.
Il flag di delimitazione ovviamente non può comparire all'interno della PDU per evitare errori, allora per garantire la trasparenza, ovvero per evitare che chi trasmette debba preoccuparsi di controllare che nella PDU non ci sia il flag, vengono implementati meccanismi di bit/byte stuffing, in base se il protocollo è orientato al bit con mezzi sincroni o al byte con mezzi asincroni.
Bit Stuffing: Il trasmettitore inserisce un bit "0" dopo 5 "1" consecutivi, tranne nel flag, in ricezione ogni "0" che segue 5 "1" viene eliminato;
Byte Stuffing: viene inserito un byte di escape "01111101" prima di ogni flag o escape, in ricezione viene scartato il primo escape della coppia.

	PROTOCOLLI
		RETI PUBBLICHE
			PPP
Point to Point Protocol
Viene utilizzato nelle reti DSL per connettere le utenze finali al gestore, inoltre è usato su connessioni SONET/SDH. Gli obiettivi di PPP sono di delimitare le trame, multiplare più protocolli di livello superiore, trasparenza, riconoscimento degli errori e negoziazione dell'indirizzo del livello superiore. È semplice da implementare.

Diviso in tre sotto-protocolli: Incapsulamento, Link Connection Protocol e Network Connection Protocol:
Incapsulamento: La trama PPP conserva la struttura della trama "generica", ma il campo address e control non hanno significato, mentre è presente un campo Protocol per identificare il protocollo di livello superiore da cui provengono o a cui destinare i dati;

Link Connection Protocol: serve a creare e abbattere il collegamento PPP negoziando opzioni come lunghezza della trama o protocolli di autenticazione. Inizia nello stato di DEAD->Instaurazione->Autenticazione->Configurazione Rete (NCP)->Aperto->Rilascio->DEAD;

NCP, Network Connection Protocol: negozia l'indirizzo e definisce le modalità di trasferimento. Per ogni protocollo di livello superiore una versione diversa di NCP, es per IP -> IP Control Protocol, per negoziare indirizzo IP e decidere compressione dei datagram IP.

			ATM
Asynchronous Transfer Mode
Rete a pacchetto con servizio a circuito virtuale su scala geografica. (All'inizio avevamo detto infatti che è utilizzato su rete pubblica di trasporto). Caratterizzato da velocità elevate e PDU dette celle di dimensione fissa (53 byte di cui 48 dati) che servono ad abbassare la latenza.
L'intestazione, ovvero i primi 5 byte, sono così suddivisi:
VPI (12): Virtual Path Identifier, id percorso tra più commutatori ATM.
VCI (16): Virtual Circuit Identifier, identifica singolo circuito virtuale nel Virtual Path, 2^16 VC per ogni VP.
PLT (3) : Payload Type, identificazione del tipo di informazioni nel payload. Contiene il Payload Type Identifier PTI, che permette di identificare 4 funzioni di utente e 4 di rete.
CLP (1) : Cell Loss Priority
HEC (8) : Header Error Code

AAL, cioè ATM Adaptation Layer, integra ATM per offrire servizi a livelli superiori. Oggi si usa AAL5. Si interpone tra ATM e il livello 3(rete) soprattutto per gestirne la segmentazione e riassemblaggio delle PDU secondo il principio di generazione di più (N-1) SDU da una (N) PDU.
Le funzioni principali sono di segmentazione e riassemblaggio e riconoscimento errori, controllo di flusso, numero di sequenza ecc. 
AAL riceve una PDU di livello 3, ne aggiunge una coda con lunghezza, pudding (usato per arrivare a un multiplo di 48) e CRC, la divide in segmenti da 48 byte e le inserisce in celle ATM, mettendo a 1 il LSB del Payload Type nell'intestazione della stessa cella ATM.

		RETI PRIVATE
			LLC - IEEE 802.2
Logical Link Control
È il livello superiore nei due sottolivelli dello strato 2, LLC e MAC (Medium Access Control). Orientato al byte.
La trama LLC, rispetto alla trama "generica":
Non possiede delimitatori => compito lasciato allo strato MAC;
Campo indirizzo sorgente e destinazione, con due bit riservati per controllo. Un indirizzo (tutti 0), è riservato, quindi si possono avere 63 indirizzi. Con il campo indirizzo viene inoltre specificato il protocollo di livello superiore;
Assenza del campo CRC per il controllo degli errori.
Campo di controllo da 8 nelle PDU non numerate, da 16 in quelle numerate.
La dimensione è variabile ma dipende dai vincoli imposti dal sottostrato MAC.

Per multiplare i protocolli di livello superiore deve essere aggiunta a LLC una PDU detta SNAP che identifica questi protocolli. Nel caso di Ethernet si può specificare nel campo EtherType, ma in 802.3 esso è usato per indicare la lunghezza della trama, allora è questo un caso in cui è necessaria la SNAP PDU.








