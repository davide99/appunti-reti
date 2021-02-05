# Architettura e protocolli di rete

## Architettura a strati

* **Protocolli**: permettono la comunicazione tra oggetti (entità) allo stesso livello gerarchico

* **Sistema**: composto da sottosistemi
* **Sottosistemi**: realizzano le funzioni di un certo strato tramite delle entità

```
                  Sistema
            +-----------------+
            |                 |
            |                 |
            |                 |
            +-----------------+
            |                 |
 strato N+1 |                 |
            |                 |
            +-----------------+
            | +-----+ +-----+ |<---- sottosistema
 strato N   | | eN1 | | eN2 | | eNX = entità X di strato N
            | +-----+ +-----+ |
            +-[N-SAP]-[N-SAP]-+
            |                 |
 strato N-1 |                 |
            |                 |
            +-----------------+
            |                 |
            |                 |
            |                 |
            +-----------------+
```

* **Servizio**: Uno strato (N+1) usa un servizio (N) fornito da uno strato (N)

* **SAP** (Service Access Point): punto di incontro tra fornitore e utilizzatore dell'(N) servizio

    * Un (N)-SAP può essere fornito da una stessa (N)-entità
    * Una (N+1)-entità può utilizzare più (N)-SAP

```
                    +--------------+
                    | (N+1)-entità |
                    +--------------+
                     |      |     |                Strato (N+1)
              +------+      |     +-----+
             /              |            \
            /               |             \
======[(N)-SAP 1]=====[(N)-SAP 2]=====[(N)-SAP 3]======
        |                  |            /
        |                  |           /
  +--------------+       +--------------+          Strato (N)
  | (N)-entità 1 |       | (N)-entità 2 |
  +--------------+       +--------------+
```

### Creazione di PDU
Possibilità di: 
1. **Segmentazione**: in due modi:
    * spezzo la N-SDU e creo più N-PDU, N-1 riceve N-PDU della dimensione giusta
    * spezzo la N-PDU e creo più (N-1)-SDU allo strato N-1
    * ovvero la segmentazione può essere fatta sia da chi sà di fornire una PDU troppo grossa, sia da chi riceve una PDU troppo grossa
1. **Concatenazione**: operazione inversa alla segmentazione


## Modello OSI
Composto da 7 livelli:

1. **Fisico**:
    * Mezzi meccanici e fisici per gestire la connessione
    * Definisce caratteristiche elettriche
    * *BIT*

1. **Data-link**:
    * Trasferimento di unità dati (trame)
    * Gestire malfunzionamenti strato fisico:
        * Rilevare e recuperare gli errori
    * Controllo di flusso
    * Delimitazione delle trame
    * *TRAME*

1. **Network**:
    * Instaura, mantiene e abbatte una connessione
    * Routing
    * Controllo di flusso (traffico tra nodi) e congestione (traffico sulla rete)
    * *PACCHETTI*

1. **Trasporto**:
    * Connessione end-to-end
    * Colma carenze strato 3:
        * Rileva errori
    * Segmentazione/concatenazione
    * Controllo di flusso, sequenza
    * Mux/demux

1. **Sessione**:
    * Sincronizza lo scambio di dati per poterlo sospendere e riprendere

1. **Presentazione**
    * Risolvere problemi di compatibilità/codifica dati
    * Servizi di cifratura

1. **Applicazione**
    * Fornisce alle applicazioni mezzi per accedere alla rete
