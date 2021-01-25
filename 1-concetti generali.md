# Concetti generali

## Funzioni di una rete
**Segnalazione**: informazioni di apertura, controllo, chiusura

## Topologia fisiche
`C` => Numero canali \
`N` => Numero nodi

Dove non inidicato, i canali sono considerati bidirezionali

* **Ad albero**: `C=N-1`, un solo percorso per ogni coppia di nodi.

* **A stella**: centro stella a cui sono collegati gli altri nodi.
	* **Stella attiva**: (switch) `C=N`
	* **Stella passiva**: (hub) `C=1`, un solo dominio di broadcast

* **A maglia non completa**: Instradamento complesso

* **Ad anello**:
	* **Bidirezionale**: `C=N`
	* **Unidirezionale**: `C=N/2`

* **A bus**:
	* **Bus attivo**: `C=N-1`
	* **Bus passivo**: `C=1`

## Servizi offerti da una rete

* **Portanti**: trasferimento di informazioni "grezze" utente-rete
* **Teleservizi**: comunicazione tra utenti sfruttando i servizi portanti
	* **Servizi di base**: offrono cervizio con funzionalità minima (es. telefono)
	* **Servizi supplementari**: integrano o modificano i servizi di base con funzionalità aggiuntive (es. segreteria telefonica)

## Condivisione di canale
* **FDM**: si trasmettere contemporaneamente su più bande

* **TDM**: si trasmettono segnali diversi in tempi diversi

* **CDM**: ad ogni flusso è associato un codice, le codifiche devono essere ortogonali

* **Multiplazione di spazio**: in zone diverse si possono ripetere le stesse frequenze

La multiplazione (una delle 4) può inoltre essere:
* **Predeterminata**: fissa
* **Statistica**: si adatta al traffico (storia passata)

## Condivisione di nodo
* **Commutazione di circuito**: collegamento fisico riservato tra due terminali d'utente (es. telefono)

* **Commutazione di pacchetto**:
	* informazioni organizzate in PDU
	* utilizzato *Store and Forward*

```
+-------+-----------+
|  PCI  |    SDU    |
+-------+-----------+
\--------PDU--------/
```
`PCI` = Protocol Control Information \
`SDU` = Service Data Unit

### Modalità di trasferimento su rete commutata a pacchetto
* **Datagram**:
	* non c'è segnalazione (connection-less)
	* i pacchetti possono seguire percorsi diversi
* **Circuito virtuale**:
	* c'è segnalazione (connection oriented)
	* i pacchetti seguono sempre lo stesso percorso prestabilito.
	* differenza con la commutazione a circuito:
		* non si allocano staticamente delle risorse (se ho `n` utenti, non ho `n(n-1)/2` canali)
	* durante la trasmissione occorre specificare il circuito virtuale (es. in ATM identificazione tramite coppia VCI e VPI, per VPI ci sono più VCI)

## Tecniche di segnalazione
* **Segnalazione associata al canale**: canale di segnalazione e di dati legati da relazione 1:1:
	* **in banda**: i due canali coincidono, sono utilizzati in tempi diversi
	* **fuori banda**: i due canali sono distinti
* **Segnalazione a canale comune**: unico canale di segnalazione <u>a pacchetti</u> per più canali dati
