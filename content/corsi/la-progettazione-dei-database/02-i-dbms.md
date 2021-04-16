---
title: "I DBMS"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:54:18+0000
description: "DBMS: DataBase Management System"
url: /corsi/la-progettazione-dei-database/i-dbms/
weight: 12
categories:
  - La progettazione dei database
  
---
 Seguendo la definizione enunciata nella precedente lezione, chiunque può costruirsi la propria base di dati, annotando con un certo ordine tutte le informazioni di cui ha bisogno su un file di testo o su un file formattato in un certo qual modo. Immaginate però di dover scrivere un software che implementi la lettura e la scrittura da una sì fatta base di dati, senz'altro dovrebbe essere scritto parecchio codice! L'utilizzo di un DBMS sicuramente ci semplificherebbe la vita.

 *Un DBMS (DataBase Management System = sistema di gestione di basi di dati) è un insieme di programmi che permettono agli utenti di creare e gestire una base di dati.*

 Un DBMS, quindi, è un sistema software che permette di **definire** (specificare i tipi di dati e tutti i vincoli su di essi), **costruire** (immagazzinare i dati), **manipolare** (recuperare e/o modificare i dati) e **condividere** (con altri utenti e/o programmi) la base di dati.

 Un'altra funzione importante fornita dai DBMS è la **protezione** dei dati sia da guasti che da tentativi di accessi non autorizzati. L'utilizzo di un DBMS quindi ci consente di non preoccuparci di moltissime cose noiose come i dettagli di memorizzazione sui file e la gestione della multiutenza.

 Se il DBMS utilizza il Modello Relazionale (che vedremo in seguito) per strutturare logicamente i dati al suo interno allora si parla di RDBMS (Relational DBMS).

 Sul mercato esistono diversi DBMS, spesso molto diversi tra loro, ma che condividono lo stesso linguaggio di definizione e manipolazione di basi di dati: il linguaggio **SQL**. Tale linguaggio è divenuto un vero e proprio standard anche se tutti i DBMS implementano l'SQL con molte variazioni.

 Esistono DBMS per tutti i gusti, alcuni gratuiti, come MySQL o PostgreSQL, altri invece possono essere molto costosi, come il famosissimo Oracle. MySQL è un RDBMS che ha tra i suoi pregi quello di essere considerato tra i più veloci e semplici, tanto da essere il più utilizzato nelle applicazioni web, in particolare con PHP.