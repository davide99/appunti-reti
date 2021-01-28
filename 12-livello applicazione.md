Quando si scrive un'applicazione di rete bisogna progettarla perchè funzioni sugli end System, non bisogna preoccuparsi degli apparati di rete.
Le principali tipologie di architetture sono client-server e peer-to-peer.
- Client Server: Un server è sempre acceso e i client possono essere connessi a intermittenza al server.Tra client non avvengono comunicazioni.
- P2P: Host comunicano direttamente tra di loro senza bisogno di un server. C'è la self scalabilità, ovvero man mano che si aggiungono nuovi peer alla rete, aumenta capacità e domanda di servizi.
Ricordiamo che con client e server indichiamo non gli host fisici ma i processi.

I protocolli a livello app definiscono tipi, semantica e sintassi dei messaggi scambiati e le regole per scambiarli. Servizi di trasporto richiesti potrebbero essere integrità dei dati, throughput (non garantito dal livello 4), ritardi (non garantiti da livello 4) e sicurezza. Quest'ultima si implementa oggi su TCP interponendo tra livello 4 e applicazione (si usa effettivamente nelle applicazioni) un "livello" detto SSL (Secure Socket Layer) che fornisce una connessione sicura e criptata tramite TCP.

	HTTP - HyperText Transfer Protocol
Protocollo di livello applicazione utilizzato sul web che si basa sull'architettura client/server; il client è il browser e il server è un web server che ritorna oggetti in risposta a richieste HTTP.
Sfrutta TCP sulla porta 80 (del server, client ricordiamo random). HTTP è stateless, ovvero il server non mantiene uno stato rispetto alle richieste effettuate in passato dal client, di ciò può occuparsi eventualmente l'applicazione.
(HTTP persistente - manda più oggetti su TCP; HTTP non persistente un oggetto alla volta)

I messaggi HTTP sono testuali, ovvero scritti in caratteri ASCII facilmente leggibili. 

		RICHIESTA HTTP
Possono usare diversi metodi, come  GET, POST e HEAD.
L'header si apre con una request line in cui si specifica il metodo, la risorsa richiesta(URL) e la versione di HTTP (1.0 o 1.1, che aggiunge i metodi PUT e DELETE). 
Dopo sono presenti le header lines, in cui in ogni riga vengono specificati dei parametri della richiesta come header field name: value. Ogni riga termina con \r\n e l'header viene chiuso con una riga contenente solo \r\n.
Poi c'è il body.
Quando si utilizza GET i parametri dell'input (ad esempio un form) vengono concatenati all'url, quando si usa POST vengono inseriti nel body.

		RISPOSTA HTTP
Status Line, contenente versione di HTTP, codice risposta e messaggio.
Il codice risposta può avere diversi valori tra cui 1xx=Informazioni, 2xx=Successo, 3xx=Redirezione, 4xx=Errore Client, 5xx=Errore Server.
La status line è seguita dalle header lines, dello stesso formato del messaggio di richiesta.
Poi ci sono i dati. Ogni riga e la parte di header sono terminate come nella richiesta.

		COOKIES
Essendo HTTP un protocollo stateless, come si può fare per mantenere uno stato?
Vengono utilizzati i cookies (letteralmente biscotti). Per ogni client che effettua una richiesta al server, viene creato un identificativo e una entry nel database del web server associato all'identificativo. Quando il server web manda una response include il cookie nell'header, che viene identificato dall'header field "Set-Cookie". Il cookie viene poi memorizzato dal client e inviato, senza modifiche, ogni volta che fa una richiesta allo stesso web server, includendolo nell'header con leader field "Cookie". In questo modo il web server riesce ad associare al cookie l'utente che sta facendo una richiesta e può identificarlo ad esempio mostrando il carrello in un sito di e-commerce. Senza cookie ad esempio la gestione del carrello solo tramite HTTP sarebbe impossibile (a meno che non venga implementato con un login e un database). 
I cookies sono quindi formalmente una stringa di testo memorizzata in un'area apposita (spesso si usano directory in cui ogni cookie è un file). Ad un cookie è spesso associata una data di scadenza.

		CACHING
Viene utilizzato per evitare che l'utente utilizzi delle risorse in rete andando ad interrogare il server finale per richiedere l'oggetto. Le cache possono essere locali (interne al browser) o con proxy HTTP. Se la pagina non è presente neanche nel proxy si va al server finale. Un problema sorge quando il contenuto della cache non è aggiornato, in quel caso andrei a richiedere risorse web che non sono più valide. Soluzione più utilizzata: quando un server risponde con una risorsa include l'header "Last-Modified" includendo la data di ultima modifica.
la GET HTTP che arriva al server finale contiene l'header line "If-modified-since" specificando la data che era stata memorizzata precedentemente. 
Se la data di ultima modifica non è più recente rispetto a quella che invio, ricevo una risposta 304 Not Modified, allora uso la versione in cache.
Se la data di ultima modifica è anteriore rispetto alla data di ultima modifica sul server ricevo l'intera risorsa. Questa soluzione si indica con GET condizionale.

	EMAIL
Tre attori principali: User Agent, Mail Server, SMTP (Simple Mail Transfer Protocol).
User Agent: L'applicazione utilizzata comunemente per leggere e scrivere email.
Mail server: divisa in mailbox (messaggi in arrivo), message queue (messaggi in uscita), SMTP.
SMTP si basa su TCP, porta 25 per scambiare messaggi tra mail server. Diviso in tre fasi:
handshaking, trasferimento, chiusura. Per interagire si usano comandi (7-bit ASCII) e response composte da codice + frase.
Comandi per inviare una mail sono HELO, MAIL FROM, RCPT TO, DATA, QUIT. Dopo DATA specifico il messaggio che va chiuso con un "." su una singola riga.

		FORMATO DEL MESSAGGIO
Il messaggio è diviso in un header e un body separati da una riga vuota.
Nell'header sono contenute informazioni come mittente, oggetto e destinatario.
Nel body è contenuto (in ASCII) il messaggio.
			MIME - Multipurpose Internet Mail Extensions
Per includere contenuti multimediali si usa MIME e si deve dichiarare nell'header la versione di MIME, il tipo di contenuto ("Content-Type) es. image/jpeg e il metodo di codifica dello stesso ("Content-Transfer-Encoding) es. base64. Dopo di che è presente una riga vuota e poi il contenuto codificato nella codifica specificata nell'header. Tipi di contenuti possono essere testo (come html), immagini, audio, video o applicazioni. Possono essere specificati più MIME content per messaggio.
		
		CONSULTAZIONE DEL MESSAGGIO
SMTP viene usato tra mail server per scambiare i messaggi, ma come fa un utente a recuperare i messaggi dal server? Ci sono due (tre) protocolli principali, ovvero POP e IMAP ( possiamo anche considerare HTTP in quanto ci sono le interfacce web dei provider come gmail alle quali si può accedere come fosse una pagina web qualsiasi).

Il protocollo POP, Post Office Protocol (oggi POP3) è usato per effettuare un download in locale delle mail contenute nel mail server. POP3 si basa su due fasi, autenticazione e transazione, ovvero prima ci si autentica al server e poi si possono ottenere i messaggi. Si può operare in due modalità: modalità "download and delete", ovvero per scaricare la posta e cancellarla dal server e "download and keep" per mantenerla sul server, in questo modo cambiando client posso comunque scaricare nuovamente le mail. POP3 è stateless tra le sessioni.

Il protocollo IMAP, Internet Mail Access Protocol, mantiene i messaggi sul server e ne permette una manipolazione da parte del client direttamente sul server stesso, ad esempio per organizzare i messaggi in cartelle. Lo stato viene salvato tra le sessioni, ad esempio le cartelle vengono mantenute anche se cambio client, o lo stato di mail aperta.

	APPLICAZIONI PEER TO PEER
Assenza di server, solo client che comunicano tra di loro ad intermittenza.
BitTorrent è un protocollo di file sharing che sfrutta p2p (TCP sotto).
Ogni file è diviso in blocchi (chunk) da 256kB che i peer si scambiano tra di loro. Si definisce con torrent un gruppo di peer che scambiano blocchi di un file. Inoltre c'è la presenza di un tracker che conserva una lista di peer che partecipano al torrent. Quando un peer si unisce al torrent ottiene dal tracker la lista dei peer e, connettendosi a un sottoinsieme di essi (neighbours, vicini) inizia a scambiare blocchi. All'inizio non ne ha ma man mano che li ottiene inizia a condividerli. Quando un peer ottiene tutti i blocchi può decidere di lasciare il torrent o rimanere per condividere con altri peer i suoi blocchi. Per ricevere o condividere blocchi si usano due tecniche differenti.
Ricezione: periodicamente ogni peer chiede agli altri nel vicinato quali chunk possiedono. Ottenute le liste il peer richiede il local rarest first, ovvero il chunk di cui sono presenti meno copie nel vicinato.

Condivisione: tit-for-tat. Ogni peer manda chunk soltanto ai 4 che stanno mandando ad esso i chunk alla velocità massima. I top 4 vengono rivalutati ogni 10 secondi, mentre ogni 30 secondi si sceglie un peer a caso e si inizia a mandare, sperando ottimisticamente (optimistically unchoke) che esso entri nella nuova top 4.


		DHT - Distributed Hash Table
Si tratta di un database distribuito in cui è presente una lista di coppie chiave valore, la chiave rappresenta il contenuto da ottenere, il valore l'IP o la lista di IP che fanno parte del torrent. 
È distribuito perchè ogni peer diventa responsabile di una porzione delle chiavi e dei corrispondenti valori. La chiave di un certo contenuto (ad esempio il titolo di un film), è in un intero ottenuto tramite una funzione di hash. Ad ogni peer viene assegnato un identificativo nello stesso spazio dei numeri delle chiavi e per assegnare ad un peer la chiave la confronto con l'identificativo del peer; assegno la chiave all'identificativo più vicino (uguale o quello subito maggiore/minore), allora ogni peer è una specie di tracker. 
Per permettere ai peer di muoversi tra gli altri per cercare il peer giusto è stata creata una struttura di peer, allora la rete p2p con DHT è detta rete strutturata, in contrapposizione alle reti senza DHT ma con i tracker, che erano non strutturate.
La struttura più semplice è quella circolare in cui i peer sono concatenati in ordine crescente e ogni peer conosce il successore e il predecessore. È necessario conoscere un punto di ingresso nella struttura, quindi o si usano peer speciali usati solo per l'ingresso o in alternativa si può provare ad entrare nella struttura con peer usati in passato. Conoscendo l'identificativo da raggiungere si cerca il peer responsabile della chiave. 
Un nuovo peer che entra nella struttura si muove fino a trovare il punto dove fermarsi e comunica al nuovo predecessore e successore di aggiornare rispettivamente il successore e il predecessore con se stesso. 
Nella struttura circolare la ricerca è O(N), allora sono state introdotte le shortcut: ogni peer ha link oltre che verso predecessore e successore, anche verso peer che distano da quest'ultimo potenze di 2. La ricerca di un peer diventa allora logaritmica. Un'altro tipo di struttura che offre ricerca logaritmica è quella ad albero. 
Dato che la responsabilità nel mantenere il mapping chiave/valore è lasciata ai peer, quando un peer decide di lasciare il torrent il disagio è minimo in quanto le chiavi di cui era responsabile vengono subito redistribuite, avendo ogni peer contatti solo con pochi peer, rendendo DHT molto scalabile.





