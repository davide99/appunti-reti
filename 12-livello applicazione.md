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
Tre alternative:
1. **POP3**:
	* Download in locale delle email
	* Due fasi:
		1. Autenticazione
		1. Transazione
	* Stateless tra sessioni diverse
1. **IMAP**:
	* Mantiene email sul server remoto
	* Client manipola direttamente email sul server
	* Possibile organizzazione in cartelle
	* Stato mantenuto tra sessioni diverse
1. **HTTP** (interfacce web)

## BitTorrent
* File diviso in blocchi (*chunk*) da 256kB
* I peer scambiano chunk tra di loro
* **Torrent**: Gruppo di peer che scambiano chunk di un certo file
* **Nodo tracker**:
	* Conserva lista dei peer di un torrent
* **"Nuovo arrivato"**:
	* Si registra sul tracker
	* Richiede lista peers di un torrent al tracker
	* Si connette a un sottoinsieme della lista peers (*neighbours*)
	* Inizia a scambiare chunk

### Tecnica di ricezione
* Periodicamente peer chiede ai neighbours lista dei chunk che possiedono
* Della lista richiede chunk secondo criterio **rarest first**

### Tecnica di trasmissione
* **Tit-for-tat**
* Ogni peer manda chunk solo ai 4 peer che gli stanno mandando più chunk
* Top 4 vengono rivalutati ogni 10 secondi
* Ogni 30 secondi si sceglie peer a caso a cui mandare chunk
	* Si sperando ottimisticamente che entri nella top 4 (**optimistically unchoke**)

### Distributed Hash Table (DHT)
* Database distribuito tra peer
* Contiene coppie (chiave, valore):
	* **chiave**: file da ottenere
	* **valore**: IP o lista di IP che fanno parte del torrent
* **Meccanismo di distribuzione**:
	1. Chiave convertita in intero tramite funzione di hash
	1. Ad ogni peer è assegnato un ID
	1. (chiave, valore) assegnata a peer con id più "vicino" all'hash

#### DHT circolare
* *Problema*: Trovare il peer con (chiave, valore) che mi interessa
* *Soluzione*: Peer organizzati in struttura circolare:
	* In ordine crescente di ID
	* Tipo token ring
	* Ogni peer conosce successore e predecessore
	* Noto hash della chiave:
		1. Peer manda al successore "Chi è responsabile di \<hash>?"
		1. Messaggio scambiato da successore in successore
		1. Arriva al responsabile
		1. Risponde mandando lista peer del torrent
	* Ricerca è O(N):
		* Introdotti shortcut: ogni peer conosce anche peer a distanza potenza del 2
		* Ricerca di un peer diventa logaritmica
	* Quando un peer abbandona rete DHT:
		* Disagio minimo
		* Coppie (chiave, valore) di cui era responsabile vengono redistribuite (al successore o predecessore)
