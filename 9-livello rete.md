		TRAMA IP
20 byte di header comuni a tutte le trame IP, contenenti i campi:
https://static.packt-cdn.com/products/9781904811718/graphics/1718_05_07.jpg

Versione (4), header length (4), type of service (8), lunghezza totale (16);
identifier(16), flags (3), fragment offset(13);
Time to live (8), protocol (8), checksum(16);
Source IP address (32);
Destination IP address (32);
A volte possono essere presenti delle opzioni;
Dati, cioè il payload della trama.

Header length indica solo la lunghezza dell'header. È necessaria a causa dell'eventuale presenza delle opzioni, quindi per separare header, opzioni e payload. Es. con un Header Lenght di 7 si hanno due word da 32 bit nel campo opzioni, perché 5 word sono relative all'header (20 byte, ogni parola da 4 byte => 5 parole).

Nel campo lunghezza totale invece è specificata la lunghezza dell'intera trama, incluso l'header. Essendo un campo da 16 bit la lunghezza totale della trama è massimo di 2^16=65536 bit => 8192 byte (circa 8 KB) (Credo). Utile se viene usato padding a livello 2 e per gestire la frammentazione. 

A causa dell'MTU (Maximum Transmission Unit, tipicamente su internet uguale a 1500 byte), i pacchetti IP possono essere frammentati dal router che crea più datagram da un solo datagram in ingresso e li contrassegna opportunamente per permettere il loro riassemblaggio a destinazione. Gli gli unici campi modificati dal router sono il time to live (decrementato di 1) e il checksum che viene ricalcolato. I flag e l'offset per la frammentazione sono ovviamente settati in fase di frammentazione. L'offset rappresenta la posizione, in multipli di 8 bytes, del frammento all'interno del datagram originale.
Pag 4-20 slide Network laser esempio.


		ADDRESSING
L'addressing viene effettuato utilizzando gli indirizzi IP, che sono indirizzi da 32 bit associati ad un'interfaccia di host o router. L'interfaccia rappresenta la connessione tra un host o router verso il collegamento fisico.

L'IP addressing si è evoluto in tre step:
- Classful addressing, divisione della parte network e della parte host di un indirizzo di rete in base alla classe di appartenenza. Statico e poco flessibile, solo 3 possibili dimensioni di una rete;
- Subnetting, introdotto il concetto delle sottoreti e di subnet mask partendo da un indirizzo di una certa classe e creando al suo interno sottoreti di dimensione fissa o variabile (VLSM, Variabile Length Subnet Masking).
- Classless addressing, rimosso il concetto delle classi, si lavora solo su un IP e una netmask che separa parte network e host (CIDR, Classless Inter Domain Routing).

	Classi di Indirizzi IP
Classe A, 8 bit per la parte network e 24 per la parte host. Primo bit a 0, allora è possibile creare 128 reti. Il primo byte ha range 0-127 (00000000-01111111). 

Classe B, 16 bit per la parte network e 16 per la parte host. Primi bit 10, allora è possibile creare 2^14 reti. Il primo byte ha range 128-191 (10000000-10111111). 

Classe C, 24 bit per la parte network e 8 per la parte host. Primi bit 110, allora è possibile creare 2^21 reti. Il primo byte ha range 192-223.
Classe D (Multicast) inizia con 1110 e classe E (riservata a scopi futuri) con 1111.

	Subnetting
Dato un indirizzo di una certa classe, vengono definite sottoreti più piccole di una determinata dimensione. L'indirizzo IP ha il seguente formato: IP + subnet mask oppure più comodamente IP/prefix length (indica il numero di bit a 1 della maschera di sottorete). 
	CIDR
La stessa cosa vale per il CIDR ma non viene considerata la classe dell'indirizzo, allora la parte network può avere una lunghezza arbitraria. La netmask deve essere composta da 1 seguito da tutti 0, non ci possono essere 0 nel mezzo. I bit della parte host rappresentano gli indirizzi disponibili, non gli host che posso indirizzare, perchè devo riservare due indirizzi (broadcast e gateway), allora gli host saranno 2^n - 2.

	INDIRIZZAMENTO GERARCHICO
Ad esempio quando le organizzazioni devono richiedere un IP all'ISP.
L'isp possiede un certo Address Range, ovvero un range di indirizzi molto esteso.
Le organizzazioni possono richiedere pezzi dell'address range, ad esempio per richiedere 512 indirizzi richiedono un /23 (32-23=9 => 2^9 = 512). Quando viene assegnata diventa una rete IP. Al suo interno ogni organizzazione può creare sottoreti => indirizzamento gerarchico. Tutte queste sottoreti sono viste dall'esterno come appartenenti ad un'unica grande rete es. quella dell'ISP e nelle tabelle di routing non devo memorizzare ogni singola rete ma solo quella grande.

		FUNZIONAMENTO ROUTING IP
Il routing viene effettuato con l'AND bit a bit tra IP e netmask per riconoscere se un indirizzo IP di destinazione è nella stessa sottorote dell'indirizzo di partenza. Se dopo l'and tra ip sorgente e destinazione gli indirizzi coincidono allora sono nella stessa sottorete, altrimenti devo controllare nella tabella di routing verso quale porta inoltrare. Per scegliere la porta corretta effettuo un AND bit a bit tra l'indirizzo destinazione e la netmask di ogni entry della tabella. Posso avere un match e allora invio su quell'interfaccia, posso avere più match e allora uso il "Longest Prefix Matching", ovvero si utilizza la entry che ha un match con il prefisso di rete più lungo.
Es. data la tabella
200.23.16.0/20 -- 0
200.23.18.0/23 -- 1

E dato l'indirizzo 200.23.18.170, dove devo mandarlo?
Facendo l'AND bit a bit con la prima e con la seconda entry ottengo un match in entrambi i casi, ma scelgo l'interfaccia 1, ovvero la seconda entry, in quanto ho un prefix length maggiore.

		TABELLE DI ROUTING
Ci possono essere tre diversi tipi di entry:
- Statiche, configurate manualmente verso network remoti;
- Dinamiche, configurate automaticamente verso network remoti;
- Dirette, reti collegate direttamente al router.

		DHCP - Dynamic Host Configuration Protocol - Protocollo UDP
Per permettere ad un host di ottenere un indirizzo IP, si può configurare manualmente oppure dinamicamente attraverso il DHCP, che implica vantaggi come, spreco di indirizzi IP nella rete se gli host non sono connessi, auto-rinnovo dell'IP e supporto per dispositivi mobili. 
Ci sono 4 messaggi che vengono scambiati tra host che vuole un IP e DHCP server:
DHCP discover: pacchetto inviato dall'host che vuole connettersi alla rete.
L'IP di destinazione è broadcast (tutti 1), e porta 67.
L'IP sorgente è 0.0.0.0, 68.

DHCP offer: pacchetto inviato dal server DHCP all'host in cui propone un IP (campo yiaddress).
L'IP di destinazione è broadcast (tutti 1), e porta 68.
L'IP sorgente è quello del server con porta 67.

DHCP request: pacchetto inviato dall'host in broadcast per accettare l'IP. Viene mandato in broadcast per avvertire gli altri server che non è stata accettata la loro offerta.
L'IP di destinazione è broadcast (tutti 1), e porta 67.
L'IP sorgente è 0.0.0.0, 68.
In yiaddr è presente l'indirizzo IP accettato.

DHCP ack: pacchetto inviato dal server all'host, in cui si conferma la ricezione della request dell'IP.
L'IP di destinazione è broadcast (tutti 1), e porta 68.
L'IP sorgente è quello del server con porta 67.
In yiaddr è presente l'indirizzo IP assegnato all'host.

Con DHCP si può ottenere oltre all'IP anche la configurazione di rete rispetto alla netmask assegnata, l'indirizzo del next-hop (gateway) e nome e indirizzo del server DNS.




		ICMP - Internet Control Message Protocol
ICMP viene usato come sistema di scambio di informazioni a livello rete, come ecco request/reply o errori. ICMP è impacchettato dentro IP.
Un messaggio ICMP è composto da tipo, codice e i primi 8 byte del datagram IP che causano l'errore.







