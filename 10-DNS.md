# DNS (Domain Name System)

* Traduzione da nomi logici a indirizzi IP e viceversa
* Basato su database distribuiti su gerarchia di server
* A livello applicazione

## Gearchia dei server
![DNS](img/DNS.webp)
1. **Root name server**
1. **TLD** (**Top Level Domain**) server:
	* Gestiscono domini di primo livello (.com, .org, ...)
1. **Authoritative DNS server**:
	* Relativo ad un'organizzazione / ad un provider

### Local DNS server
* Non fa parte realmente della gerarchia
* Dell'ISP (o nel router stesso)
* Mantiene cache senza contattare *Root name server*

**Funzionamento**:
* Host chiede risoluzione di un nome al local DNS server
* Se il mapping non viene trovato si procede gerarchicamente:
	* Richiesta inoltrata al root server -> TLD server -> authoritative server

## Metodi per risposta a query DNS
* **Query iterativa**:
	* Ogni server risponde al local DNS con il server successivo da interrogare
	* Il local DNS interroga il server
* **Query ricorsiva**:
	* I server effettuano le richieste "in avanti"
	* La risposta ritorna indietro seguendo il percorso inverso

## Caching DNS
* Ogni server DNS effettua **caching**
* Timeout dell'entry dopo un certo TTL
* Di solito caching dei TLD server nel local server:
	* Evito di contattare il Root name server

## Tipi di record DNS
Entry:
```
+------+-------+------+-----+
| Name | Value | Type | TTL |
+------+-------+------+-----+
```
* **Type**: tipo di mapping
* **Name**: chiave
* **Value**: valore

Tipi più importanti:

* **type**=A:
	* **name**=hostname (www.google.com)
	* **value**=IP (120.40.50.150)

* **type**=CNAME:
	* **name**=alias (www.ibm.com)
	* **value**=nome canonico (servereast.backup2.ibm.com)

* **type**=NS:
	* **name**=dominio (polito.it)
	* **value**=hostname del DNS autoritativo per questo dominio (leonardo.polito.it)
	* Sarà necessaria entry di tipo A per risolvere hostname (value)

* **type**=MX:
	* usato per i mail server
	* simile a CNAME
	* **name**=dominio (alias) (gmail.com)
	* **value**=hostname "reale" (gmail1.com, gmail2.com)

* **type**=PTR:
	* risoluzione inversa

## Messaggi DNS
* Due tipi:
	* **Query**
	* **Reply**
* Entrambi con stesso formato
* Contengono identificativo trasnazione
