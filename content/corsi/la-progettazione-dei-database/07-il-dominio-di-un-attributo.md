---
title: "Il dominio di un attributo"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:55:48+0000
description: "Il dominio di un attributo di una entità del modello ER Entità Relazione"
url: /corsi/la-progettazione-dei-database/il-dominio-di-un-attributo/
categories:
  - La progettazione dei database
  
---
 Ogni attributo di una entità o di una associazione possiede un dominio, ovvero ad esso è possibile assegnare solo un ben preciso insieme di valori. Il dominio di un attributo corrisponde ai tipi di dati elementari:

- Stringhe di caratteri
- Valori numerici
- Valori temporali (date, ore, ...)
- Valori booleani (vero o falso)
- Enumerazioni (si definisce una lista di valori validi)
 
 Tornando al primo esempio della lezione precedente, gli attributi nome e cognome dell'entità persona hanno come dominio le stringhe di caratteri mentre data di nascita ovviamente apparterrà al dominio dei valori temporali.

 Una volta assegnato un certo dominio ad un attributo, esso potrà assumere solo i valori consentiti dal dominio ed eventualmente il valore speciale **NULL**. Il valore NULL sta a significare che per un particolare elemento dell'entità il valore dell'attributo in questione **non è noto** oppure **non è applicabile**. Se ad esempio non si conosce la data di realizzazione di un particolare film, il valore dell'attributo "data" sarà NULL.

 Come già accennato in precedenza, un attributo può essere a valore singolo o multiplo. Un esempio di attributo a valore multiplo è dato dalle lingue in cui può essere stato tradotto un film: se un certo film viene tradotto in italiano, inglese e spagnolo, allora l'attributo "lingue" dell'entità film avrà il valore multiplo (o meglio 3 valori): "italiano", "inglese" e "spagnolo". Tutti i dati memorizzati in un attributo a valore multiplo devono avere lo stesso dominio di valori; infatti "italiano", "inglese" e "spagnolo" sono tutte stringhe.