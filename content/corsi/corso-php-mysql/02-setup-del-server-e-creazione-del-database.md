---
title: "Setup del server e creazione del database"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:00:24+0000
description: "Corso PHP MySQL: creazione del database di prova"
url: /corsi/corso-php-mysql/setup-del-server-e-creazione-del-database/
categories:
  - Corso PHP MySQL
  
---
Per far girare script PHP che si interfacciano ad un database MySQL è necessario avere a disposizione un server web con il supporto al PHP ed un server MySQL, presenti generalmente, ma non necessariamente, sullo stesso PC. Il PHP deve inoltre caricare l'**estensione mysql**, ovvero la libreria di funzioni che permettono di interagire con MySQL. Questa estensione è attiva di default solo nelle versioni Windows di PHP 4. Per una descrizione dettagliata della procedura di installazione e configurazione di Apache 2, PHP 5 e MySQL 4.1 su Window XP è possibile consultare [questo articolo](http://www.phpnews.it/articoli/installare-apache-2-php-5-mysql-4.1-su-windows/) che mostra anche come verificare il corretto funzionamento dell'intero sistema.

Vivamente consigliata anche l'installazione di un **client MySQL**. Installando MySQL avremo a disposizione il MySQL Command Line Client, un semplice programma a riga di comando (in stile DOS) per inviare comandi ed eseguire query. Se si desidera qualcosa di meno spartano e con più funzionalità è possibile utilizzare un client MySQL con interfaccia grafica, come ad esempio [MySQL Query browser](http://www.mysql.com/products/tools/query-browser/) o [phpMyAdmin](http://www.phpmyadmin.net/).

Prima di iniziare a realizzare i nostri script PHP/MySQL dobbiamo preparare un database nel quale salvare i nostri dati. Gli esempi proposti nelle prossime lezioni fanno riferimento ad un database di nome *prova* contenente un'unica tabella di nome *utenti*. Per creare il database e la tabella in questione basta lanciare le seguenti query:

 ```
<pre class="brush: mysql">
create database prova;

use prova;

CREATE TABLE utenti (
id int(11) NOT NULL auto_increment,
nome varchar(128) NOT NULL default '',
email varchar(128) NOT NULL default '',
sesso tinyint(4) NOT NULL default '0',
newsletter tinyint(4) NOT NULL default '0',
attivita tinyint(4) NOT NULL default '0',
messaggio text NOT NULL,
PRIMARY KEY (id));
```

Nell'immagine che segue è mostrato l'esito delle precedenti query eseguite all'interno del MySQL Command Line client.

![mqb.png](http://www.phpnews.it/images/stories/Corsi/corso_php_mysql/mqb.png)

La tabella *utenti* memorizza un ipotetico profilo di un utente che fa richiesta di un servizio offerto da un sito web. Essa contiene il **nome** dell'utente, il suo indirizzo email, il **sesso** (1= maschio, 2=femmina), se accetta di registrarsi alla **newsletter** (0=no, 1=si), l'**attività** svolta (0=non dichiarata,1=studente, 2=impiegato, 3=disoccupato) ed infine un **messaggio** inviato alla redazione del sito. Si noti anche che per la chiave primaria viene impiegato un campo **id** di tipo intero auto\_increment. Questa tabella non ha alcun valore pratico ma la sua struttura ci permetterà di esplorare le tecniche necessarie a gestire le differenti tipologie di informazione.

Effettuato il setup del nostro server e creato il database di prova possiamo procedere con la prossima lezione nella quale impareremo a connetterci a MySQL con PHP.