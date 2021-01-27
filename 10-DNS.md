Domain Name System, utilizzato per la traduzione da nomi logici in indirizzi IP e viceversa, basato su gerarchia di database distribuiti. Si trova a livello applicazione.

Gearchia dei server:
- Root name server: Sono contattati dal local name server se non si riesce a risolvere un nome;
- TLD (Top Level Domain) server: server che gestiscono domini di primo livello come .com, .org e tutti i domini nazionali come .it o .uk;
- Authoritative DNS server: server relativo ad un'organizzazione o ad un provider (Aruba) che gestisce il dominio e fornisce informazioni sul mapping.
- Local DNS server, posseduto da ogni ISP, spesso nel router stesso. È il primo step effettuato da un host nella risoluzione di un nome e se non viene trovato un riscontro si procede gerarchicamente inoltrando la richiesta al root server -> TLD server -> authoritative.
Queste richieste possono essere effettuate iterativamente, ovvero ogni server risponde al local DNS ed è quest'ultimo che effettua man mano le query, oppure ricorsivamente, in cui sono i server ad effettuare le richieste "in avanti" e infine ritornano indietro attraversando tutti i server fino a ritornare al local DNS e infine all'host.
Ogni server DNS effettua un processo di caching, ovvero memorizza temporaneamente le risoluzioni per un tempo detto TTL (time to live). Ad esempio le risoluzioni per i TLD potrebbero essere memorizzate nei local server per evitare di rivolgersi ai root server.

		TIPI DI RECORD DNS
Un DNS è a tutti gli effetti un database che memorizza dei record con formato: (name, value, type, ttl)

I tipi possono essere:

- type=A: name=hostname, value=IP es. www.google.com 120.40.50.150

- type=CNAME: name=alias value=nome canonico es. 
A  workHP.polito.it 	130.192.85.1
CN www.polito.it 		workHP.polito.it
CN didattica.polito.it 	workHP.polito.it

- type=NS name=dominio value=hostname del DNS autoritativo per questo dominio es. 
A  leonardo.polito.it 	130.192.3.21
NS polito.it 		leonardo.polito.it

- type=MX usato per I mail server, value=nome del mail server associato con il valore del campo name.

I messaggi DNS scambiati quando si vuole risolvere un nome sono messaggi query  e reply, entrambi con lo stesso formato e che contengono nell'header identificativo (16 bit) uguale per query e reply, flags che indicano se si tratta di query o reply, se la reply è autoritativa, se è preferita o è disponibile la ricorsione. Altri campi sono questions, answers o altre info.


 

