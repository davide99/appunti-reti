# Livello applicazione

I protocolli a livello applicazione definiscono:
* **Tipi dei messaggi**:
	* Richieste
	* Risposte
* **Sintassi dei messaggi**:
	* Delimitazione dei campi
* **Semantica dei messaggi**:
	* Significato dei vari campi

## HTTP
* Su TCP
* Porta 80 del server
* Porta random del client
* È **stateless**:
	* Non mantiene uno delle richieste passate dal client
* Due tipi di connessioni:
	1. **HTTP persistente**: più oggetti su singola connessione TCP
	1. **HTTP non persistente**: un oggetto per connessione TCP

### Formato richiesta
Esempio:
```
GET URL HTTP/1.1
field_name1:value1
           :
field_name2:value2

Body (se POST)
```

#### Metodi
* **GET**
* **POST**
* **HEAD**:
	* Come *GET* ma solo header

### Formato risposta
Esempio:
```
HTTP/1.1 200 OK
field_name1:value1
           :
field_name2:value2

Body
```

#### Codici di risposta
* **1xx**: Informazioni
* **2xx**: Successo
* **3xx**: Redirezione
* **4xx**: Errore Client
* **5xx**: Errore Server

### Cookies
* *Problema*: HTTP stateless, come posso mantenere uno stato?
* *Soluzione*: Cookies

**Funzionamento**
* Server invia `set-cookie: x` in una risposta
* Client memorizza il cookie `x`
* Client invierà `cookie: x` per ogni richiesta al server

### Caching (Conditional GET)
* Evitano di richiedere oggetto al web server
* Interne al browser o con proxy HTTP
* Proxy richiede oggetto al server solo se diverso dalla sua cache locale:
	* Proxy aggiunge in header `If-modified-since:<data>`
	* Se non modificata:
		* Server web risponde `304 Not Modified`
		* Uso copia in cache
	* Se modificata:
		* Proxy ricevo intero oggetto

## Email
* Tre attori:
	1. **User Agent**: client mail
	1. **Mail Server**
	1. **SMTP**:
		* Scambio mail tra mail server
		* Invio mail da user agent a mail server
		* Basato su TCP
		* Porta 25
		* Comandi su ASCII 7-bit
		* Diviso in tre fasi:
			1. Handshaking
			1. Trasferimento
			1. Chiusura

### Multipurpose Internet Mail Extensions (MIME)
* Protocollo per inclusione contenuti multimediali in email
* All'interno dell'header dell'email:
	* **Versione di MIME**
	* **Content-Type**
	* **Content-Transfer-Encoding** (es. base64)
		
### Consultazione email
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
