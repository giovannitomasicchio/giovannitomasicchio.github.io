---
title: "Dal Modello Relazionale al DDL dell'RDBMS"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:57:44+0000
description: "Dal Modello Relazionale al DDL dell'RDBMS"
url: /corsi/la-progettazione-dei-database/dal-modello-relazionale-al-ddl-dellrdbms/
weight: 1
categories:
  - La progettazione dei database
  
---
Abbiamo preparato la descrizione del mini-mondo, abbiamo realizzato il relativo Modello ER, convertendolo poi in Modello Relazionale. Abbiamo scelto con cura le chiavi primarie da utilizzare, optando per la creazione di alcune chiavi surrogate per ottimizzare le prestazioni del sistema. Abbiamo verificato la normalizzazione delle relazioni e magari abbiamo deciso di denormalizzare qualche attributo "strategico". Siamo quindi pronti per implementare la nostra base di dati nell'RDBMS scelto. Per farlo dobbiamo descriverlo utilizzando il **DDL**, ovvero il **linguaggio di definizione dei dati** compreso dall'RDMBS.

Il DDL, essendo un sottoinsieme dell'SQL, è un linguaggio solo in parte standardizzato, costituito da diversi comandi, ciascuno con una propria sintassi. Non potendo in questa sede presentare il DDL dei diversi RDBMS attualmente disponibili ci limitiamo a vedere solo un piccolo esempio, relativo a MySQL.

Quelle che seguono sono le query necessarie a creare il database della rubrica personale presentato nella prima lezione.

 ```sql
CREATE TABLE `lavoro` (
   `lavoro` varchar(255),
   `indirizzo` varchar(255),
   `telefono` varchar(64),
   PRIMARY KEY  (`lavoro`)
) ;

CREATE TABLE `persona` (
   `nome` varchar(128),
   `cognome` varchar(128),
   `indirizzo` varchar(255),
   `telefono` varchar(64),
   `lavoro` varchar(255),
   PRIMARY KEY  (`nome`,`cognome`),
   FOREIGN KEY (`lavoro`) REFERENCES `lavoro` (`lavoro`)
);
```

In ciascuna query viene specificato il nome della tabella e i nomi dei campi che la costituiscono. Per ciascun campo viene indicato il dominio (in questo caso sono tutti varchar, ovvero stringhe a lunghezza variabile). Le query specificano anche i campi che partecipano alla chiave primaria e alla chiave esterna. Per quest'ultima inoltre viene indicata la tabella ed il campo alla quale la FK fa riferimento.