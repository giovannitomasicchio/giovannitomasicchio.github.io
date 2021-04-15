---
title: "Preparazione del database"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:17:30+0000
description: "Preparazione del database necessario alla nostra applicazione MVC con lo Zend Framework"
url: /corsi/corso-zend-framework/preparazione-del-database/
categories:
  - Introduzione allo Zend Framework
  
---
Ora che abbiamo lo scheletro dell'applicazione con le action del controller e i file view pronti, è il momento di dare uno sguardo alla sezione model dell'applicazione. Ricordiamo che i model sono la parte che si occupa delle funzionalità centrali dell'applicazione (le così dette "business rules") e, nel nostro caso, interagiscono con il database. Useremo la classe Zend\_Db\_Table di Zend Framework per trovare, inserire, aggiornare e cancellare righe dalla tabella del database.

###  Configurazione del database

Per usare Zend\_Db\_Table, abbiamo bisogno di indicare quale database useremo ed il nome utente e la password. Dato che preferiamo salvare queste informazioni in modo semplice nella nostra applicazione, useremo il file di configurazione. Il componente Zend\_Application dispone di una risorsa per la configurazione del database, così dobbiamo solo impostare la configurazione appropriata in configs/application.ini e l'oggetto farà il resto.

Apriamo il file application/configs/application.ini e aggiungiamo le seguenti rige alla fine della sezione \[production\] (ovvero sopra alla sezione \[staging\]):

 ```
<pre class="brush: ini">
resources.db.adapter = PDO_MYSQL
resources.db.params.host = localhost
resources.db.params.username = rob
resources.db.params.password = 123456
resources.db.params.dbname = zf-tutorial
```

Ovviamente si deve usare il nome utente, la password ed il nome del proprio database! La connessione al database avverrà in modo automatico e di default l'adapter per Zend\_Db\_Table farà il resto. Si può sapere di più sui plugin delle risorse disponibili a questo indirizzo: <http://framework.zend.com/manual/en/zend.application.available-resources.html>.

###  Creazione della tabella del database

Come detto nella pianificazione del progetto, utilizzeremo un database per immagazzinare i dati degli album. In questo corso utilizzeremo MySQL e così l'SQL per creare la tabella è:

 ```
<pre class="brush: mysql">
CREATE TABLE albums (
    id int(11) NOT NULL auto_increment,
    artist varchar(100) NOT NULL,
    title varchar(100) NOT NULL,
    PRIMARY KEY (id)
);
```

Eseguiamo questa query in un client MySQL come ad esempio phpMyAdmin o nel client da riga di comando standard di MySQL.

###  Inserimento dei dati di prova

Inseriamo ora qualche riga nella tabella, così potremo vedere qualche risultato in home page. Prenderemo alcuni CD. Lanciamo la seguente query in un client MySQL:

 ```
<pre class="brush: mysql">
INSERT INTO albums (artist, title) VALUES
    ('Paolo Nutine', 'Sunny Side Up'),
    ('Florence + The Machine', 'Lungs'),
    ('Massive Attack', 'Heligoland'),
    ('Andre Rieu', 'Forever Vienna'),
    ('Sade', 'Soldier of Love');
```

Ora abbiamo qualche dato nel database e possiamo scrivere dei semplici model.