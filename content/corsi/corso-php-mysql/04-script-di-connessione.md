---
title: "Script di connessione"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:01:14+0000
description: "Script PHP di connessione a MySQL"
url: /corsi/corso-php-mysql/script-di-connessione/
categories:
  - Corso PHP MySQL
  
---
 Le operazioni di connessione e selezione del database sono così comuni all'interno degli script PHP che spesso si preferisce inserirle in un file indipendente, da richiamare negli script che ne hanno bisogno. Il codice è sempre lo stesso, pertanto conviene scriverlo una volta sola, salvarlo in un file ed includerlo dove necessario.

 **connect.php**

 ```php
<?php
$link = mysql_connect('localhost', 'nome_utente', 'password');
if (!$link) {
	die ('Non riesco a connettermi: ' . mysql_error());
}

$db_selected = mysql_select_db('prova', $link);
if (!$db_selected) {
	die ("Errore nella selezione del database: " . mysql_error());
}
?>
```

 Ecco come potrebbe presentarsi il generico script che fa uso del precedente codice di connessione:

 ```php
<?php
// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

// il resto dello script: posso effettuare query
// senza preoccuparmi della connessione a MySQL
?>
```

 Si noti però che il riutilizzo dello scipt connect.php in altri contesti (altri programmi PHP/MySQL) non è ancora immediato, poiché al suo interno sono presenti diversi dati generalmente soggetti a variazione (host, username, password e nome del DB). Per questo motivo risulta comodo separare tali informazioni dal codice ed inserirle in un apposito file di configurazione config.php, come mostrato di seguito:

 **config.php**

 ```php
<?php
$DB_host     = 'localhost';
$DB_user     = 'nome_utente';
$DB_password = 'password';
$DB_name     = 'prova';
?>
```

 **connect.php**

 ```php
<?php
$link = mysql_connect($DB_host, $DB_user, $DB_password);
if (!$link) {
	die ('Non riesco a connettermi: ' . mysql_error());
}

$db_selected = mysql_select_db($DB_name, $link);
if (!$db_selected) {
	die ("Errore nella selezione del database: " . mysql_error());
}
?>
```

 Il generico script pertanto diventa:

 ```php
<?php
// richiamo il file di configurazione
require 'config.php';

// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

// il resto dello script: posso effettuare query
// senza preoccuparmi della connessione a MySQL
?>
```

 Al di là degli esempi mostrati in questa lezione ciò che conta è rendersi conto che bisogna fin da subito organizzare lo script affinché risulti snello, di facile manutenzione e, almeno in parte, riutilizzabile. Come vedremo, le applicazioni PHP/MySQL tendono con estrema facilità a "complicarsi", sopratutto quando si deve far interagire l'utente con i dati presenti nel database. Gli script che realizzeremo nelle prossime lezioni faranno uso dei file config.php e connect.php qui presentati.