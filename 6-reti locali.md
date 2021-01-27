# Protocolli di accesso per reti locali (LAN)

* Canale condiviso da più utilizzatori (accesso multiplo)
* Necessario protocollo di accesso:
    * Ad **accesso casuale / con contesa**:
        * Aloha
        * CSMA
    * Ad **accesso ordinato / senza contesa**:
        * Token-Ring

## Aloha
* Un nodo trasmettere senza preoccuparsi degli altri nodi
* Nessuna coordinazione tra i nodi
    * Probabilità di collisione (anche parziale) elevata
* Un canale broadcast per trasmettere
* Un canale broadcast per ricevere
* Utilizza stop and wait
* In caso di collisione si esegue **backoff**:
    1. I due nodi in collisione si fermano per periodo di tempo casuale
    1. Inviano di nuovo
    1. Se collidono di nuovo si raddoppia tempo di backoff

## Aloha slotted
* Evoluzione di Aloha con introduzione di time slot
* Ogni nodo trasmette all'inizio del time slot
    * Due trasmissioni o collidono nell'intero slot, o non collidono affatto
    * Se collidono, si ritrasmette in un altro slot con probabilità `p` fino al successo

## Carrier Sense Multiple Access (CSMA)
* Si ascolta il canale prima di trasmettere
    * Se libero trasmetto
    * Se occupato aspetto

### Strategie di attesa
**CSMA p-persistente**: 
1. Aspetto che si liberi il canale
1. Trasmetto con probabilità `p`, ovvero ritardo la trasmissione con probabilità `(1-p)` di un tempo casuale

* Ethernet usa **CSMA 1-persistente**: appena libero trasmette
* WIFI usa **CSMA non-persistente**: aspetta tempo casuale e ricontrolla, se libero trasmette

### Collisioni nel CSMA
* Le collisioni si possono verificare a causa dei tempi di propagazione
* Dipendono da:
    * Distanza tra i trasmettitori
    * Tempo propagazione trame = `d`
* Due trasmettitori A e B abbastanza distanti:
    1. A vede il canale libero => trasmette
    1. B vede il canale libero (tempo di propagazione) => trasmette
    1. A rileva la collisione solo se sta ancora trasmettendo, cioè solo se ![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20d%3ERTT_%7BAB%7D):

## CSMA/CD (Collision Detection)
* Stazione continua ad ascoltare il canale anche durante la trasmissione
* Se avviene collisione ferma la trasmissione
    * Non diminuisce il numero di collisioni
    * Diminuisce la durata delle collisioni
* Non è necessario ACK se ![](https://latex.codecogs.com/gif.latex?%5Cbg_white%20d%3ERTT_%7BAB%7D)

## CSMA/CA (Collision Avoidance) - WiFi
Mezzi radio di solito half duplex => non posso ascoltare mentre trasmetto => no CSMA/CD

**Funzionamento trasmettitore**:
* Ascolta canale per un tempo DIFS (**Distributed Interface Space**)
* Se libero inizia la trasmissione
* Se occupato (o lo diventa durante DIFS):
    * Aspetta un tempo di backoff
    * Il backoff viene decremento mentre il canale rimane libero
    * Ritenta trasmissione

Se si genera collisione nuovamente, le stazioni raddoppiano il backoff.

**Funzionamento ricevitore**:
* Verifica correttezza
* Aspetta un tempo SIFS (**Short**) < DIFS
* Invia ACK

**Funzionamento trasmettitore in attesa di ACK**:
* Se non riceve ACK dopo timeout:
    * Genera tempo di backoff
    * Decrementa backoff
    * backoff=0 ritrasmette
