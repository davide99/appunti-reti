Funzioni dello strato rete:
Instradamento, consultando tabelle di routing, Indirizzamento, Tariffazione e Controllo di congestione.

	INSTRADAMENTO
Per effettuare l'instradamento devono essere definiti:
- Protocollo di instradamento, per definire la modalità di scambio di informazioni necessarie alla costruzione di tabelle di routing;
- Algoritmo di instradamento, cioè le istruzioni da eseguire per scegliere il percorso verso il nodo destinazione e per creare le tabelle di routing;
- Procedura di forwarding, cioè come eseguire l'inoltro dei pacchetti verso la porta corretta leggendo le tabelle di routing.

Gli algoritmi di instradamento servono a scegliere il percorso migliore da un nodo di partenza ad uno di arrivo. La topologia di rete viene trasformata in un grafo in cui i nodi sono i vertici e i canali/mezzi sono gli archi. A questi ultimi viene assegnato un peso o costo che può essere il livello di congestione, la distanza, il ritardo o il costo (euro); inoltre il peso può essere statico o dinamico.

Esistono algoritmi molto semplici come il flooding o "hot potato" e complessi che sono classificati in:

Centralizzati: Un solo nodo della rete possiede tutte le informazioni sugli altri nodi e si occupa di calcolare il percorso. Può essere utile per mantenere un piano di instradamento coerente tra tutti i nodi ma si è molto sensibili al guasto del nodo centrale e inoltre le richieste potrebbero creare congestione. => si usano oggi distribuiti.

Distribuiti: I nodi si scambiano informazioni tra di loro per far si che ognuno possa calcolare in autonomia i percorsi. I nodi devono avere una certa "intelligenza" ma la rete è robusta ai guasti.
Gli algoritmi distribuiti si dividono ulteriormente in base se l'informazione sia globale o parziale.
- Globale: Algoritmi Link State, i nodi conoscono la topologia della rete, le informazioni sono scambiate con tutti.
- Parziale: Algoritmi Distance Vector, i nodi sanno solo con chi sono collegati direttamente e scambiano informazioni solo con questi ultimi.

		ALGORITMI LINK STATE
I nodi si scambiano informazioni sul costo dei propri canali in broadcast. Tutti conoscono la topologia e i costi, allora calcolano i propri percorsi ottimi. Viene usato l'algoritmo di Dijkstra.
Con M nodi presenta una complessità O(M^2), ma può essere migliorato a O(M log(M)).

		ALGORITMI DISTANCE VECTOR
Periodicamente ogni nodo scambia con i propri vicini un vettore contenente le informazioni sui nodi raggiungibili e la distanza (costo) da essi. Ogni nodo che riceve informazioni aggiorna la propria tabella di routing se sono presenti modifiche da effettuare come aggiornamento di un percorso ottimo o aggiunta di destinazioni. 
È una famiglia di algoritmi iterativi che continuano a scambiare informazioni fin quando non ce ne sono più di nuove da scambiare => non sono necessari segnali di terminazione, sono facili da implementare ma sono poco scalabili, lenti a convergere ed errori di routing si propagano su tutta la rete. Ogni nodo esegue un loop infinito in cui aspetta la modifica del costo di un canale o un messaggio da un altro nodo, allora si ricalcola la tabella delle distanze e se trova percorsi migliori avvisa i vicini.

Ogni nodo possiede una tabella delle distanze riempita calcolando i costi verso ogni destinazione tramite ogni vicino sommando il costo per arrivare al vicino più il costo minimo dal vicino alla destinazione.
Se il costo di un canale si abbassa il cambiamento viene recepito velocemente dagli altri nodi, se aumenta (idealmente a infinito), ci possono essere scenari in cui i nodi creano loop e impiegano tanto tempo a convergere. 

	CONFRONTO
- Complessità dei messaggi: O(E M) e canali, m nodi.
- Velocità di convergenza: LS convergenza immediata, DV tempo di convergenza variabile. (Dipende dal diametro della rete).
Affidabilità: LS i guasti si recuperano velocemente, DV gli errori si propagano.
DV è molto semplice e spesso per reti molto piccole è preferito.








