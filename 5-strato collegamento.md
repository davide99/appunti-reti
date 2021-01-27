# Strato collegamento

Le funzioni dello strato collegamento sono:
* Rilevazione di errori
* Controllo di flusso (traffico sul nodo)
* Controllo sequenza (protocolli a finestra)
* Delimitazione trama
* Multiplazione (dei vari flussi dal livello superiore)
* Indirizzamento locale

## Apparati di livello 2
* **DTE**:
	* *Data Terminal Equipment*
	* Apparato terminale (dell'utente)
* **DCE**:
	* *Data Circuit-terminating Equipment*
	* Del gestore
	* Interfaccia tra DTE e rete

## Formato della PDU
```
+----------+-----------+-----------+------+-----+----------+
| 01111110 | indirizzo | controllo | dati | CRC | 01111110 |
+----------+-----------+-----------+------+-----+----------+
     8           8          8/16     >=0    16       8
```

* **Flag di delimitazione**
* **Indirizzo**: solo se la comunicazione non è punto-punto
* **Controllo**: tipo

*Problema*: Il flag di delimitazione non può comparire nella SDU\
*Soluzione*: Bit/byte stuffing

### Bit Stuffing
* Per protocolli orientati al bit
* Il trasmettitore inserisce un bit `0` dopo 5 `1` consecutivi
* Il ricevitore elimina ogni `0` che segue 5 `1` consecutivi

### Byte stuffing
* Per protocolli orientati al byte
* Il trasmettitore inserisce un byte di escape `01111101` prima di ogni flag o escape
* Il ricevitore scarta l'escape o il primo di due escape consecutivi

## Protocolli di livello 2 per reti pubbliche

### Point to Point Protocol (PPP)
* Utilizzi:
	* DSL: connessione tra utenze finali e gestore
	* Connessione tra reti di trasporto
* Diviso in tre sotto-protocolli:
	* Incapsulamento
	* Link Connection Protocol: gestione connessione e autenticazione
	* Network Connection Protocol: negoziazione IP

### Asynchronous Transfer Mode (ATM)
* Rete a pacchetto a circuito virtuale
* PDU dette **celle** di dimensione fissa
* **AAL** (ATM Adaptation Layer):
	* Integra ATM per offrire servizi a livelli superiori
	* Gestione di segmentazione e riassemblaggio
		* Principio di generazione di più (N-1) SDU da una (N) PDU
		* Ovvero segmentazione a carico del livello più basso

## Protocolli di livello 2 per reti private
```
   .            .
   :            :
   +------------+
   |            |
3° |    Rete    |
   |            |
   +------------+\
   |    LLC     | \
   +------------+  | 2°
   |    MAC     | /
   +------------+/
   |            |
1° |   Fisico   |
   |            |
   +------------+
```
###	Logical Link Control - IEEE 802.2

```
+-----------+-----------+-----------+------+
| indirizzo | indirizzo | Controllo | Dati |
|   DSAP    |   SSAP    |           |      |
+-----------+-----------+-----------+------+
     8           8          8/16       8*n
```

* Orientato al byte
* La trama LLC, rispetto alla trama "generica":
	* Non possiede delimitatori => compito del MAC
	* Campo indirizzo sorgente e destinazione:
		* 2 bit riservati per controllo
		* Un indirizzo (tutti 0) riservato
		* Si possono avere solo 63 indirizzi (*)
		* Utilizzato per specificare il protocollo di livello superiore
* (*) Essendo 63 protocolli pochi, due alternative:
	* Se si usa Ethernet si usa il campo "EtherType"
	* Se si usa 802.3 "EtherType" => "Length":
		* Dev'essere aggiunta a LLC una PDU (campi) detta **SNAP** per identificare ulteriori protocolli
