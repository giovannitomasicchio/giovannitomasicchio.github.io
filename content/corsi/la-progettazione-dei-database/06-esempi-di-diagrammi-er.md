---
title: "Esempi di diagrammi ER"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:55:35+0000
description: "Diagrammi ER Entità Relazione"
url: /corsi/la-progettazione-dei-database/esempi-di-diagrammi-er/
categories:
  - La progettazione dei database
  
---
 Riprendiamo l'esempio della lezione precedente: avevamo individuato le entità film e persona. L'associazione tra le due entità la chiamiamo ad esempio "cast", e indica che una persona ha partecipato alla realizzazione di uno o più film ovvero che un film è stato realizzato da una o più persone. Il diagramma ER corrispondente è:

 ![ER1.gif](images/stories/Corsi/progettazione_database/ER1.gif)

 Nel diagramma ci sono alcuni elementi che devono ancora essere spiegati. Iniziamo a vedere cosa significano quella M e quella N vicino all'entità film e all'entità persona. Ogni volta che si introduce un'associazione bisogna chiedersi quale sia la sua **cardinalità**. Nel nostro esempio lo si fa facendosi due semplici domande:

- una persona a quanti film può partecipare? La risposta è M (ovvero più di uno);
- un film quante persone può annoverare nel suo cast? La risposta è N (ovvero più di una).
 
 Nel nostro caso si dice che la cardinalità è M:N (si legge "M a N"). Si noti che tale soluzione va bene anche nel caso in cui una persona svolga più ruoli all'interno dello stesso film: semplicemente vi saranno più istanze dell'associazione "cast". Facciamo un esempio: supponiamo che venga registrato nella base di dati il film "Compagni di scuola". Verranno inseriti i dati relativi al film (nome, genere, data, lingue, ?) e i dati relativi ad ogni singola persona che vi ha partecipato. In questo film Carlo Verdone è sia regista che attore, ma verrà inserito una sola volta tra le persone; invece ci saranno due associazioni cast: una col ruolo di attore e una col ruolo di regista.

 Oltre alla cardinalità M:N, le associazioni possono avere cardinalità 1:N o 1:1. Vediamo alcuni semplici esempi.

 ![ER_N1.gif](images/stories/Corsi/progettazione_database/ER_N1.gif)

 Ogni film può essere N volte oggetto di una proiezione in sala, mentre ogni proiezione ha come oggetto (di norma) un solo film.

 ![ER_11.gif](images/stories/Corsi/progettazione_database/ER_11.gif)

 Ogni studente ha una sola carriera universitaria e ogni carriera appartiene ad un solo studente.

 Ritornando all'esempio iniziale, notiamo che l'attributo lingue di film è cerchiato due volte. Questo sta ad indicare che tale **attributo** è **multivalore**, cioè al suo interno vi sono memorizzati più valori (le lingue in cui è stato tradotto il film possono essere più di una).

 Un altro tipo di attributo, non mostrato nell'esempio, è l'**attributo composto**, eccone un esempio:

 ![ER_composto.gif](images/stories/Corsi/progettazione_database/ER_composto.gif)

 L'indirizzo è un attributo composto perché costituito da ulteriori attributi semplici: via, numero, città, provincia e cap.