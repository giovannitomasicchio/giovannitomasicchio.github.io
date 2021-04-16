---
title: "Cancellazione dei dati"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:03:06+0000
description: "Cancellare un record di una tabella MySQL con PHP. Eseguire una DELETE con PHP e MySQL."
url: /corsi/corso-php-mysql/cancellazione-dei-dati/
weight: 3
categories:
  - Corso PHP MySQL
  
---
La cancellazione di record presenti in una tabella è un'operazione che richiede l'esecuzione di una semplice query. Ad esempio per cancellare dalla tabella utenti il record con id pari a 5 basta lanciare la seguente query.

 ```sql
DELETE FROM utenti WHERE id = 5
```

Per eseguire questa cancellazione in PHP è sufficiente quindi il seguente script:

 ```php
<?php
// richiamo il file di configurazione
require 'config.php';

// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

// preparo la query
$query = "DELETE FROM utenti WHERE id = 5";

// invio la query
$result = mysql_query($query);

// controllo l'esito
if (!$result) {
	die("Errore nella query $query: " . mysql_error());
}

// chiudo la connessione a MySQL
mysql_close();

echo 'Query eseguita correttamente';
?>
```

Di solito però lo script che deve cancellare dei dati non sa a priori quale record eliminare. In un contesto interattivo è l'utente ad indicare cosa cancellare, e lo fa cliccando su di un link o selezionando in altro modo i record da eliminare. Lo script quindi dovrebbe occuparsi di mostrare i dati all'utente, permettergli di selezionare quelli da cancellare, ed infine effettuare l'operazione.

Vediamo quindi come realizzare uno script che mostra l'elenco dei record presenti nella tabella "utenti", associando a ciascuno di essi un link e una checkbox attraverso cui selezionare quelli da eliminare. Lo script verrà suddiviso in due pari, la prima adibita alla creazione dell'elenco di record disponibili, con relativi link e checkbox, la seconda per l'eliminazione del record identificato dall'id passato via $\_GET o via $\_POST.

 ```php
<?php
// richiamo il file di configurazione
require 'config.php';

// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

if($_POST)
{
	$ids = isset($_POST['id']) ? $_POST['id'] : array();
	elimina_record($ids);
}
elseif(isset($_GET['id']))
{
	elimina_record(array($_GET['id']));
}
else
	mostra_lista();

function mostra_lista()
{
	// mostro un eventuale messaggio
	if(isset($_GET['msg']))
		echo '<b>'.htmlentities($_GET['msg']).'</b><br /><br />';

	// preparo la query
	$query = "SELECT id,nome FROM utenti";

	// invio la query
	$result = mysql_query($query);

	// controllo l'esito
	if (!$result) {
		die("Errore nella query $query: " . mysql_error());
	}

	echo '
	<form name="form1" method="post" action="">
	<table border="1">
		<tr>
			<th>&nbsp;</th>
			<th>Nome</th>
			<th>&nbsp;</th>
		</tr>';

	while ($row = mysql_fetch_assoc($result))
	{
		$nome = htmlentities($row['nome']);

		// preparo il link per la modifica dei dati del record
		$link = $_SERVER['PHP_SELF'].'?id=' . $row['id'];

		echo "<tr>
				<td><input name=\"id[]\" type=\"checkbox\" value=\"$row[id]\" /></td>
				<td>$nome</td>
				<td><a href=\"$link\">elimina</a></td>
			</tr>";
	}

	echo '</table>
		<br />
		<input type="submit" name="Submit" value="Elimina record selezionati" />
		</form>';

	// libero la memoria di PHP occupata dai record estratti con la SELECT
	mysql_free_result($result);

	// chiudo la connessione a MySQL
	mysql_close();
}

function elimina_record($ids)
{
	// verifico che almeno un id sia stato selezionato
	if(count($ids) < 1)
	{
		$messaggio = urlencode("Nessun record selezionato!");
		header('location: '.$_SERVER['PHP_SELF'].'?msg='.$messaggio);
		exit;
	}

	// per precauzione converto gli ID in interi
	$ids = array_map('intval',$ids);

	// creo una lista di ID per la query
	$ids = implode(',',$ids);

	// preparo la query
	$query = "DELETE FROM utenti WHERE id IN ($ids)";

	// invio la query
	$result = mysql_query($query);

	// controllo l'esito
	if (!$result) {
		die("Errore nella query $query: " . mysql_error());
	}

	// conto il numero di record cancellati
	$num_record = mysql_affected_rows();

	// chiudo la connessione a MySQL
	mysql_close();

	$messaggio = urlencode("Numero record cancellati: $num_record");
	header('location: '.$_SERVER['PHP_SELF'].'?msg='.$messaggio);
}
?>
```

La funzione mostra\_lista() si occupa di creare l'elenco dei record all'interno di un form. Per ciascuno di essi viene mostrato il nome, a cui viene applicata la funzione htmlentities() per la conversione dei caratteri nelle relative entità HTML, il link per la cancellazione diretta e la checkbox di selezione.

La funzione elimina\_record() invece provvede alla cancellazione dei record selezionati. Gli id dei record vengono passati all'interno dell'array $ids (riga 10,11,15). Questi vengono prima convertiti in numeri interi per precauzione (riga 83), trasformati in una stringa di id separai da virgola (riga 86) ed infine inseriti nella query (riga 89).

Eseguita la query vengono contati i record effettivamente interessati da questa operazione attraverso la funzione **mysql\_affected\_rows()** (riga 100) e viene effettuato un redirect all'URL corrente per evitare possibili problemi legati al refresh della pagina, come visto nella Lezione 5. Anche in questo caso un messaggio viene accodato all'URL che verrà visualizzato all'inizio della funzione mostra\_lista().

Un punto debole dello script presentato risiede nel passaggio degli id dei record da cancellare attraverso un link ($\_GET) o una checkbox ($\_POST), elementi comunque accessibili all'utente e pertanto soggetti a manomissione. Supponiamo ad esempio che un utente abbia i permessi sufficienti per poter cancellare solo i record con id compreso tra 1 e 10. La funzione mostra\_lista() deve quindi mostrare solo questi record ma nulla vieta all'utente di manomettere un link per la cancellazione inserendo un id superiore a 10. Senza nessun controllo quindi l'utente potrebbe eliminare record su cui non ha i permessi. Diverse le possibili soluzioni al problema. Prima di effettuare la DELETE si potrebbe verificare che l'id passato sia tra quelli accessibili all'utente impiegando la stessa logica usata da mostra\_lista() per la loro individuazione. Se tale procedimento risulta oneroso mostra\_lista() potrebbe salvare in sessione gli id dei record accessibili all'utente. Contro la manomissione dei link esiste anche una particolare tecnica denominata HMAC implementata nel package [PEAR::Crypt\_HMAC](http://pear.php.net/package/Crypt_HMAC).