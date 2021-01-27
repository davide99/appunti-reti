# Network Address Translation (NAT)

* IP privati escono su internet come unico IP pubblico
* Al livello trasporto
* Esiste **tabella di traduzione**:
    * Tupla con (porta "pubblica") <=> (IP sorgente, porta)

**Funzionamento**:\
Il NAT deve:
- **In uscita**: Rimpiazzare IP locale con quello pubblico
- Ricordare la traduzione
- **In ingresso**: Rimpiazzare IP pubblico con quello sorgente locale

*Problema*: Un server locale non è direttamente visibile su Internet\
*Due soluzioni*:
1. **Port forwaring**: Configurazione manuale del NAT
1. **Relaying**:
    * Sfrutta un terzo dispositivo (**relay**) come intermediario
    * Il server locale si connette al relay
    * Il client esterno si connette al relay
    * Il relay può:
        * Fare da "ponte"
        * Fornire al server e al client abbastanza informazioni da potersi collegare senza relay
