---
title: "Login con PHP"
author: "Giovanni Tomasicchio"
type: article
date: 2009-05-04T20:44:51+0000
description: "Come creare uno script PHP per effettuare il login degli utenti"
url: /articoli/login-con-php/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "sicurezza"
  - "crittografia"
  - "SQL"

  
---
 Realizzare uno **script PHP per effettuare il login degli utenti** è un compito piuttosto semplice per un programmatore PHP con un po' di esperienza. Gli sviluppatori alle prime armi possono invece incontrare qualche difficoltà.

 Lo script che viene proposto di seguito vuole essere quindi un punto di partenza per i neofiti che li guidi alla realizzazione del proprio sistema di login con PHP. La complessità è stata ridotta al minimo per rendere lo script maggiormente comprensibile, ma non per questo a rischio sicurezza.

 Per provare lo script di login proposto bisogna creare una tabella degli utenti ed inserire almeno un record. Ecco i comandi SQL necessari, da eseguire ad esempio con phpMyAdmin.

 ```
<pre class="brush:sql">
CREATE TABLE `utenti` (
   `id` INT(11) NOT NULL AUTO_INCREMENT,
   `nome` VARCHAR(32) NOT NULL,
   `pswd` VARCHAR(32) NOT NULL,
   PRIMARY KEY  (`id`)
);

INSERT INTO utenti (nome, pswd) VALUES ('gianni', MD5('prova'));

```

 I più attenti avranno notato che la password non viene memorizzata in chiaro nel database, bensì sotto forma di **hash MD5**.

 Ed ecco lo script PHP di login:

 ```
<pre class="brush:php">
<?php
$DB_host     = 'localhost';
$DB_user     = 'root';
$DB_password = 'secret';
$DB_name     = 'test';

$link = mysql_connect($DB_host, $DB_user, $DB_password);
if (!$link) {
	die ('Non riesco a connettermi: ' . mysql_error());
}

$db_selected = mysql_select_db($DB_name, $link);
if (!$db_selected) {
	die ("Errore nella selezione del database: " . mysql_error());
}

if($_POST) {
	effettua_login();
} else {
	mostra_form();
}

function mostra_form()
{
	// mostro un eventuale messaggio
	if(isset($_GET['msg'])) {
		echo '<b>'.htmlentities($_GET['msg']).'</b><br /><br />';
	}
	?>
	<form name="form_login" method="post" action="">
		<label>nome: <input name="nome" type="text" value="" /></label><br />
		<label>password: <input name="password" type="password" value="" /></label><br />
	    <input name="invia" type="submit" value="Invia" />
	</form>
	<?
}

function effettua_login()
{
	// recupero il nome e la password inseriti dall'utente
	$nome      = trim($_POST['nome']);
	$password  = trim($_POST['password']);
	// verifico se devo eliminare gli slash inseriti automaticamente da PHP
	if(get_magic_quotes_gpc()) {
		$nome      = stripslashes($nome);
		$password  = stripslashes($password);
	}

	// verifico la presenza dei campi obbligatori
	if(!$nome || !$password) {
		$messaggio = urlencode("Non hai inserito il nome o la password");
		header("location: $_SERVER[PHP_SELF]?msg=$messaggio");
		exit;
	}
	// effettuo l'escape dei caratteri speciali per inserirli all'interno della query
	$nome     = mysql_real_escape_string($nome);
	$password = mysql_real_escape_string($password);	

	// preparo ed invio la query
	$query = "SELECT id FROM utenti WHERE nome = '$nome' AND pswd = MD5('$password')";
	$result = mysql_query($query);
	// controllo l'esito
	if (!$result) {
		die("Errore nella query $query: " . mysql_error());
	}

	$record = mysql_fetch_array($result);

	if(!$record) {
		$messaggio = urlencode('Nome utente o password errati');
		header("location: $_SERVER[PHP_SELF]?msg=$messaggio");
	} else {
		session_start();
		$_SESSION['user_id'] = $record['id'];
		$messaggio = urlencode('Login avvenuto con successo');
		header("location: $_SERVER[PHP_SELF]?msg=$messaggio");
	}
}
?>
```

 Analizziamo i punti principali dello script:

- La prima parte del codice è dedicata alla connessione con il server MySQL ed alla selezione del database.
- La funzione mostra\_form() provvede a mostrare il form HTML nel quale l'utente inserisce il nome e la password 
  - notare come vengono recuperati e visualizzati eventuali messaggi passati via $\_GET\['msg'\]
- La funzione effettua\_login() verifica le credenziali inserite: 
  - vengono eliminati gli spazi bianchi con la funzione trim()
  - vengono eliminati gli eventuali caratteri di slash inseriti automaticamente da PHP con stripslashes()
  - viene verificata la presenza del nome e della password
  - viene effettuo l'escape dei caratteri speciali eventualmente presenti nel nome o nella password affinché possano essere inseriti con sicurezza all'interno della query
  - viene eseguita la query di ricerca del record con nome e password coincidenti con quelli specificati dall'utente. Nella query la password viene convertita nel suo hash MD5
  - L'eventuale record individuato dalla query viene letto con mysql\_fetch\_array()
  - Se la query non ha restituito risultati viene effettuato il redirect con la funzione header() e viene mostrato un messaggio di errore
  - Se la query ha avuto successo 
    - viene avviata la sessione,
    - viene memorizzato l'id dell'utente loggato in sessione, utile nelle pagine successive per sapere se l'utente ha effettuato il login
    - viene effettuato il redirect con la funzione header() e viene mostrato un messaggio di sucesso
 
 Come detto all'inizio, questo script vuole essere solo uno spunto, un'indicazione di massima su come realizzare un **sistema id login con PHP e MySQL**.