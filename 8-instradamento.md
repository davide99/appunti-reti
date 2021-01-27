# Instradamento (livello rete)

Funzioni:
* Instradamento
* Indirizzamento
* Risoluzione indirizzi (IP <=> MAC)
* Controllo di congestione.

## Instradamento
Per effettuare l'instradamento servono:
- **Protocollo di instradamento**:
	* Modalità di <u>scambio di informazioni</u> per costruzione tabelle di routing
- **Algoritmo di instradamento**:
	* <u>Scelta percorso</u>
	* <u>Creazione tabelle di routing</u>
- **Procedura di forwarding**:
	* <u>Instradare pacchetti</u> usando tabelle di routing

## Algoritmi di instradamento
* **Centralizzati**:
	* Compiti demandati ad un solo nodo
	* Possiede tutte le informazioni necessarie
	* Si occupa di calcolare il percorso migliore
	* *Vantaggi*:
		* Utile per avere piano di instradamento coerente su tutti i nodi
	* *Svantaggi*:
		* Guasto del nodo centrale
		* Traffico verso nodo centrale può creare congestione

* **Distribuiti**:
	* Nodi scambiano informazioni tra di loro
	* Ognuno calcola in autonomia i percorsi migliori
	* *Vantaggi*:
		* Rete robusta ai guasti
	* *Svantagi*:
		* Nodi "intelligenti"
	* Divisibili in base all'informazione:
		* **Globale**, algoritmi **Link State**:
			* Nodi conoscono l'intera topologia di rete
			* Informazioni scambiate con tutti
		* **Parziale**, algoritmi **Distance Vector**:
			* Nodi conoscono solo nodi direttamente collegati
			* Scambiano informazioni solo con questi

### Algoritmi Link State
* Nodi scambiano informazioni sul costo dei propri canali in broadcast
* Ognuno calcola i propri percorsi ottimi con Dijkstra:
	* `M` = numero nodi
	* Complessità O(M log(M))

### Algoritmi Distance Vector
* Periodicamente ogni nodo scambia con i vicini un vettore (**DV**), contiene:
	* Nodi raggiungibili
	* Costo per raggiungerli
* Eventualmente chi riceve il **DV** aggiorna la sua routing table
* Algoritmi iterativi:
	* Continuano a scambiare informazioni fin quando non convergono
	* Poco scalabili
	* Lenti a convergere
	* Propagazione di errori di routing
* Ogni nodo possiede **tabella delle distanze**:
	* Contiene costi verso ogni destinazione tramite ogni vicino
	* Costo = costo per arrivare al vicino + costo minimo dal vicino alla destinazione
* Se costo di un canale:
	* Si abbassa => cambiamento recepito velocemente
	* Si alza => tempi di convergenza potrebbero essere elevati

### Confronto
- **Complessità dei messaggi**:
	* E = n. canali, M = n. nodi.
	* Entrambi O(E M)
