---
title: " L'estensione mysqli - III"
author: "Giovanni Tomasicchio"
type: article
date: 2005-11-30T10:42:03+0000
description: "Gestire MySQL attraverso l'estensione mysqli di PHP 5"
url: /articoli/lestensione-mysqli-iii/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "sicurezza"
  - "BLOB"
  - "InnoDB"
  - "MyISAM"
  - "mysqli"
  - "SQL"
  - "upload"

  
---
 Concludiamo lo studio della nuova estensione *ext/mysqli* soffermandoci su altre interessanti novità. Inizieremo dai *prepared statement* con cui effettuare il binding dei parametri e dei risultati, vedremo poi come eseguire una transazione ed infine analizzeremo le novità introdotte nella gestione dei BLOB.

 I *prepared statement* ovvero le "dichiarazioni preparate" sono dei *modelli* di query da inviare e conservare nel server MySQL. Attraverso un sistema di collegamento tra le variabili PHP e i parametri delle query preparate è possibile sia modificare i dati contenuti nella query, sia recuperare direttamente i set di risultati.

 Si parla quindi di binding (collegamento) dei parametri quando ad una query precedentemente preparata vengono associati dei parametri legati ad altrettante variabili PHP.

 Si definisce invece binding dei risultati la tecnica che prevede il trasferimento diretto dei risultati di una query in determinate variabili PHP.

 Come risulterà chiaro dagli esempi, queste tecniche non solo hanno indubbi benefici legati alle prestazioni ed alla sicurezza ma rendono il codice molto più snello e leggibile.

- - - - - -

####  Binding dei parametri

 Nel primo esempio vedremo come implementare la tecnica del binding dei parametri. I passi da compiere sono i seguenti:

 **1) preparazione della query**

 La preparazione della query avviene attraverso il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">prepare</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` della classe *mysqli*. Ogni parametro della query che vogliamo collegare ad una variabile PHP dovrà essere indicato con un punto interrogativo utilizzato come segnaposto. Il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">prepare</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` invia la query a MySQL che ne controlla la validità e ne verifica la correttezza. Se tale controllo dà esito positivo, `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">prepare</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` restituisce un oggetto di tipo *mysqli\_stmt*.

 **2) collegamento delle variabili PHP ai parametri della query**

 Il collegamento avviene attraverso il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">bind_param</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` della classe *mysqli\_stmt* con cui specifichiamo il tipo di parametri e passiamo le variabili da collegare.

 **3) assegnazione dei valori alle variabili PHP collegate**

 Si tratta di normalissime assegnazioni di valori

 **4) esecuzione della query**

 Per eseguire lo *statement* ovvero la query così preparata viene impiegato il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">execute</span><span style="color: rgb(0, 119, 0);">()</span></span>` della classe *mysqli\_stmt*.

 Il seguente esempio chiarirà ulteriormente questa tecnica.

 ```
<pre class="brush: php">
<?php
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// preparo lo statement
$stmt = $mysqli->prepare('INSERT INTO mia_tabella VALUES (NULL, ? , ?)');

// collego i parametri
$stmt->bind_param('ss', $nome, $cognome);

// valorizzo i parametri
$nome = 'Luca';
$cognome = 'Armenise';

// eseguo lo statement
$stmt->execute();

// valorizzo i parametri
$nome = 'Nicola';
$cognome = 'Ruggiero';

// eseguo lo statement
$stmt->execute();
?>
```

 E' importante soffermarsi sulla sintassi del metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">bind_param</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>`. Il primo valore che gli viene passato è una stringa e nel nostro esempio vale "ss". Questa stringa contiene una lettera per ciascun parametro e sta ad indicare il tipo di dato che questo parametro contiene. Ecco le lettere che possono essere impiegate:

 | **Lettera** | **Tipo di dati** |
|---|---|
| i | numeri interi |
| d | numeri di tipo DOUBLE o FLOAT |
| b | appartenenti alla famiglia dei BLOB |
| s | stringhe (tutti i dati per cui si usano gli apici nelle query) |

 Nel nostro caso i parametri da passare sono costituiti da due stringhe ($nome e $cognome) pertanto sono state passate le lettere "ss".

 Il binding dei parametri risulta una tecnica particolarmente indicata quando una stessa query deve essere effettuata più volte con parametri diversi. In questi casi si ottiene una notevole riduzione dei tempi di esecuzione poiché la query viene preparata una sola volta ed il server MySQL non deve ricontrollarla ad ogni esecuzione.

 Si noti anche che non è stato necessario effettuare l'escape dei "caratteri pericolosi" quali gli apici con funzioni come addslashes() o mysql\_escape\_string() in quanto i parametri non vengono inseriti nella query.

 Anche sul fronte della sicurezza il binding dei parametri risulta vantaggioso. Infatti la query viene inviata al server MySQL priva di quei valori, spesso provenienti dall'utente, che se manipolati ad arte potrebbero modificarne pesantemente la valenza (SQL injection). Questi parametri vengono invece inviati in un secondo momento ed inoltre per ciascuno di essi, grazie al metodo *bind\_param*, viene specificata la natura.

 Purtroppo la presenza dei parametri all'interno della query è segnalata da semplici punti interrogativi, pertanto è necessario che le variabili legate ad esse con `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">bind_param</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` debbano rispettare rigorosamente l'ordine dei punti interrogativi. Sarebbe stato più comodo poter inserire degli alias al posto dei ? e riferirsi a questi per associare le variabili PHP.

- - - - - -

####  Binding dei risultati

 Grazie ai *prepared statement* è possibile anche legare alcune variabili PHP direttamente ai risultati restituiti da una query. La tecnica è molto semplice e segue questa procedura:

 **1) preparazione della query**

 La preparazione della query avviene attraverso il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">prepare</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` della classe *mysqli*. In questa fase è possibile anche inserire dei parametri nella query come mostrato precedentemente nella tecnica del binding dei parametri.

 **2) esecuzione della query**

 Questa operazione avviene impiegando il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">execute</span><span style="color: rgb(0, 119, 0);">()</span></span>` della classe *mysqli\_stmt*.

 **3) collegamento delle variabili PHP con i risultati della query**

 Questo collegamento avviene grazie al metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">bind_result</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` a cui passiamo in ordine le variabili PHP da valorizzare con i corrispondenti dati provenienti dalla query.

 **4) Fetch dei risultati**

 Il recupero dei risultati avviene con il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fetch</span><span style="color: rgb(0, 119, 0);">()</span></span>` della classe *mysqli\_stmt*.

 Vediamo ora un esempio di binding dei risultati:

 ```
<pre class="brush: php">
<?php
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// preparo lo statement
$stmt = $mysqli->prepare('SELECT * FROM mia_tabella');

// eseguo lo statement
$stmt->execute();

// collego i risultati
$stmt->bind_result($id, $nome, $cognome);

// eseguo la fetch dei risultati
while ($stmt->fetch())
{
	echo "ID utente:$id, nome:$nome, cognome:$cognome<br>\n";
}
?>
```

 Una volta collegate le variabili PHP ai risultati della query grazie al metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">bind_result</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` è possibile adoperarle all'interno dello script. Ad ogni chiamata del metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fetch</span><span style="color: rgb(0, 119, 0);">()</span></span>` queste variabili riceveranno i nuovi dati provenienti dal set dei risultati.

 Questa tecnica comunque non è particolarmente vantaggiosa, infatti anche se permette di valorizzare direttamente le variabili PHP, queste devono essere passate al metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">bind_result</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` nell'esatto ordine con cui vengono restituite dalla query: cosa accade se la query viene modificata?

- - - - - -

####  Transazioni

 Una transazione è una successione di query che si conclude con un successo o un insuccesso. Nel primo caso gli effetti prodotti dalle query diventano permanenti, altrimenti il database torna nello stato precedente l'inizio della transazione.

 Le tabelle di tipo MyISAM non supportano le transazioni, possibili in MySQL solo con tabelle di tipo InnoDB e BDB. Per i nostri esempi useremo sempre la tabella mia\_tabella costruita nel [primo articolo](http://www.phpnews.it/articoli/php/estensione-mysqli-i/), convertendola però in InnoDB attraverso la seguente query:

 ALTER TABLE mia\_tabella type = InnoDB

 La vecchia estensione *ext/mysql* non offre un supporto nativo alle transazioni che devono essere eseguite utilizzando direttamente delle query. Questo approccio è ovviamente ancora praticabile, pertanto risulta immediato convertire un vecchio script per l'utilizzo di *ext/mysqli*. Vediamo quindi come effettuare una transazione facendo uso esclusivamente di query.

 Di default MySQL funziona in modalità AUTOCOMMIT, ovvero tutte le query che modificano il contenuto del database (INSERT, DELETE, UPDATE) hanno un effetto duraturo ed non possono essere annullate.

 Per effettuare una transazione è possibile disabilitare l'AUTOCOMMIT ed utilizzare i comandi COMMIT e ROLLBACK per confermare o annullare gli effetti delle query eseguite. Oppure, lasciando l'impostazione AUTOCOMMIT abilitata, è possibile lanciare il comando START TRANSACTION (o il suo alias BEGIN) per indicare l'inizio della transazione ed impiegare i i comandi COMMIT e ROLLBACK per confermare o annullare gli effetti delle query eseguite.

 Vediamo un esempio che avvia una transazione con il comando START TRANSACTION:

 ```
<pre class="brush: php">
<?php
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// Avvio la transazione
$mysqli->query('START TRANSACTION');

// eseguo alcune query
$mysqli->query("INSERT INTO mia_tabella VALUES (NULL, 'Alberto', 'Rossi')");
$mysqli->query("UPDATE mia_tabella SET cognome='Bianchi' WHERE nome='Alberto'");

// chiudo la transazione con esito positivo
$mysqli->query('COMMIT');

// Avvio una nuova transazione
$mysqli->query('START TRANSACTION');

// eseguo una query
$mysqli->query("DELETE FROM mia_tabella");

// affected_rows funziona anche con le transazioni
// anche se non è detto che l'effetto della query sarà duraturo
echo 'Righe cancellate:', $mysqli->affected_rows;

// chiudo la transazione con esito negativo
$mysqli->query('ROLLBACK');
?>
```

 Adesso vediamo invece uno script che implementa le transazioni disabilitando l'AUTOCOMMIT:

 ```
<pre class="brush: php">
<?php
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// Disabilito l'AUTOCOMMIT delle query
$mysqli->query('SET AUTOCOMMIT=0');

// eseguo alcune query
$mysqli->query("INSERT INTO mia_tabella VALUES (NULL, 'Alberto', 'Rossi')");
$mysqli->query("UPDATE mia_tabella SET cognome='Bianchi' WHERE nome='Alberto'");

// chiudo la transazione con esito positivo
$mysqli->query('COMMIT');

// Adesso inizia implicitamente una nuova transazione
// poiché l'AUTOCOMMIT è disabilitato

// eseguo una query
$mysqli->query("DELETE FROM mia_tabella");

// chiudo la transazione con esito negativo
$mysqli->query('ROLLBACK');
?>
```

 Con l'estensione *ext/mysqli* abbiamo a disposizione i metodi `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">commit</span><span style="color: rgb(0, 119, 0);">()</span></span>`e `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">rollback</span><span style="color: rgb(0, 119, 0);">()</span></span>`della classe *mysqli* che, insieme al metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">autocommit</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` permettono di eseguire delle transazioni inviando al server esclusivamente le query costituenti la transazione stessa. La logica da seguire coincide con quella dell'ultimo esempio visto poiché questi metodi hanno la stessa valenza dei relativi comandi SQL:

 ```
<pre class="brush: php">
<?php
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// Disabilito l'AUTOCOMMIT delle query
$mysqli->autocommit(false);

// eseguo alcune query
$mysqli->query("INSERT INTO mia_tabella VALUES (NULL, 'Alberto', 'Rossi')");
$mysqli->query("UPDATE mia_tabella SET cognome='Bianchi' WHERE nome='Alberto'");

// chuda la transazione con esito positivo
$mysqli->commit();

// Adesso inizia implicitamente una nuova transazione
// poiché l'AUTOCOMMIT è disabilitato

// eseguo una query
$mysqli->query("DELETE FROM mia_tabella");

// chiudo la transazione con esito negativo
$mysqli->rollback();
?>
```

 Anche se non ci sono importanti benefici provenienti dall'uso di `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">autocommit</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>`, `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">commit</span><span style="color: rgb(0, 119, 0);">() </span></span>`e `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">rollback</span><span style="color: rgb(0, 119, 0);">()</span></span>`, il codice risulta comunque più chiaro ed evidenzia esclusivamente le query appartenenti alla transazione.

- - - - - -

####  Gestione dei BLOB

 Come accadeva per la vecchia *ext/mysql*, la gestione dei dati di tipo BLOB (**B**inary **L**arge **OB**ject) è analoga a quella dei dati normali. *ext/mysqli* in più introduce una nuova modalità di inserimento dei dati BLOB di gran lunga più efficiente. Attraverso l'uso del metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">send_long_data</span><span style="color: rgb(0, 119, 0);">()</span></span>` della classe *mysqli\_stmt* i voluminosi dati binari non devono essere contenuti nella query ed inviati tutti insieme ma, sfruttando il binding dei parametri, possono essere inviati al server MySQL un po' per volta.

 L'esempio che proponiamo è una rivisitazione dello script proposto per l'[upload e l'inserimento dei files all'interno di un database MySQL](http://www.phpnews.it/articoli/php/files-ed-immagini-in-mysql/2/). Questa volta useremo le classi messe a disposizione da *ext/mysqli* ed in particolare vedremo come impiegare il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">send_long_data</span><span style="color: rgb(0, 119, 0);">()</span></span>`.

 ```
<pre class="brush: php">
<?php
// se è stato inviato il file...
if(isset($_POST['invia']))
{
	// se ci sono stati problemi nell'upload del file
	if(!isset($_FILES['file_inviato']) OR $_FILES['file_inviato']['error'] != UPLOAD_ERR_OK)
	mostra_form("errore nell'invio del file. Riprova");

	// connessione e selezione del database
	$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

	// recupero alcune informazioni sul file inviato
	$nome_file_temporaneo = $_FILES['file_inviato']['tmp_name'];
	$nome_file_vero = $_FILES['file_inviato']['name'];
	$tipo_file = $_FILES['file_inviato']['type'];


	// preparo la query per inserire i dati nel DB
	$stmt = $mysqli->prepare("INSERT INTO tabella_files VALUES (NULL, ?, ?, ?)");

	// collego i parametri della query alle variabili PHP
	$stmt->bind_param('ssb', $nome_file_vero, $tipo_file, $dati_file);

	// apro il file in lettura
	$fp = fopen($nome_file_temporaneo,"rb");
	while ($dati_file = fread($fp,1024))
	{
		$stmt->send_long_data(2, $dati_file);
	}

	if ($stmt->execute())
	{
		$messaggio = "Memorizzazione del file <b>$nome_file_vero</b> nel database eseguita correttamente.";
	}
	else
	{
		$messaggio = "Errore nella memorizzazione del file <b>$nome_file_vero</b>";
	}

	// mostro nuovamente il form ed un messaggio di successo
	mostra_form("Memorizzazione del file <b>$nome_file_vero</b> nel database eseguita correttamente.");
}
else
{
	mostra_form();
}

/**
* Mostra il form per l'upload del file
*
*/
function mostra_form($messaggio = '')
{
	echo <<<END
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1">
<title>Carica file nel database</title>
</head>
<body>
	<p>$messaggio<br>Seleziona un file da memorizzare nel database: </p>
	<form name="form1" enctype="multipart/form-data" method="post" action="">
		<p>
			<input type="file" name="file_inviato">
		</p>
		<p>
			<input type="submit" name="invia" value="Invia file">
		</p>
	</form>
</body>
</html>
END;
exit();
}
?>
```

 Il punto di forza di questa tecnica consiste nell'accoppiata della funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fread</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` con il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">send_long_data</span><span style="color: rgb(0, 119, 0);">()</span></span>`. `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fread</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` infatti legge di volta in vola solo parte del file, `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">send_long_data</span><span style="color: rgb(0, 119, 0);">()</span></span>` a sua volta prende questi frammenti e li invia a MySQL.

 Lo script PHP quindi non dovrà tenere in memoria tutto il file, inoltre l'invio graduale dei dati non rischierà di far superare le dimensioni imposte dalla direttiva *max\_allowed\_packet* del server MySQL, ovvero la massima dimensione che può raggiungere una query. Si noti infine come sia comunque necessario richiamare il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">execute</span><span style="color: rgb(0, 119, 0);">()</span></span>` della classe *mysqli\_stmt* per eseguire realmente la query.