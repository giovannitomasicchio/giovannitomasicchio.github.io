---
title: "Chiave primaria surrogata"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:57:12+0000
description: "La chiave primaria surrogata"
url: /corsi/la-progettazione-dei-database/chiave-primaria-surrogata/
weight: 3
categories:
  - La progettazione dei database
  
---
 La scelta di una chiave primaria per una relazione è molto importante. Poiché il Modello Relazionale viene implementato direttamente all'interno del database, bisogna tenere presente che gli RDBMS indicizzano le chiavi primarie in modo da velocizzare notevolmente la ricerca di una specifica riga all'interno delle tabelle.

 Esistono quindi dei casi in cui è opportuno inserire delle chiavi primarie "surrogate", realizzate cioè con campi che non contengono un particolare attributo della tupla (ad esempio nome e cognome per una tabella "studenti"), ma che vengono creati ad-hoc per far fronte a due comuni scenari: la difficoltà di reperire un insieme ci campi che garantisca l'univocità della tupla o la complessità di gestione per la macchina di chiavi complesse.

- Immaginate che esistano due attori diversi, ma con lo stesso nome e cognome (gli omonimi non sono poi così rari!). Se usassimo la coppia nome e cognome come chiave primaria potremmo inserire nella nostra base di dati uno solo di essi. Nella realtà spesso si utilizza il codice fiscale (è già una chiave surrogata!) ma spesso non basta poiché anche questi sono a rischio duplicati.
- Supponiamo di avere una chiave formata da parecchi campi e ci sono altre tabelle che referenziano la tabella in questione attraverso chiavi esterne, ovviamente con lo stesso numero di campi. Poiché alla fine di tutto questo lavoro toccherà al computer memorizzare i dati ed effettuare le ricerche su di essi, è importante non complicargli troppo la vita altrimenti le prestazioni del sistema subiranno un inevitabile degrado.
 
 Per ovviare a questi problema è sufficiente inserire un nuovo campo, comunemente costituito da un numero intero crescente e chiamato ID ovvero "identificativo", che diventa la chiave primaria della tabella. Gli elementi della tabella verranno identificati quindi da un semplice numero intero.

 In questo modo la chiave primaria sarà costituita da un unico campo, contenente un numero facile da gestire per la macchina. Inoltre non ci saranno rischi di chiavi duplicate poiché basta incrementare tale numero ad ogni inserimento di una nuova riga nella tabella.