---
title: "Aggiornamento dei dati"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:03:26+0000
description: "Modificare i dati di una tabella MySQL con PHP. Eseguide una UPDATE con PHP e MySQL"
url: /corsi/corso-php-mysql/aggiornamento-dei-dati/
categories:
  - Corso PHP MySQL
  
---
L'aggiornamento dei dati presenti in una tabella MySQL è affidato ad una query di tipo UPDATE. Ad esempio, per modificare i dati associati al record con id = 5 della tabella "utenti" potremmo impiegare la seguente query:

 ```
<pre class="brush: mysql">
UPDATE 
   utenti
SET
   nome = 'Mario',
   email = 'mario@email.it',
   sesso = 1,
   newsletter = 0,
   attivita = 2,
   messaggio = 'Ciao a tutti!'
WHERE
   id = 5
```

La semplice esecuzione della query in PHP richiede la realizzazione di un banale script:

 ```
<pre class="brush: php">
<?php
// richiamo il file di configurazione
require 'config.php';

// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

// preparo la query di aggiornamento
$query = "UPDATE utenti SET
				nome = 'Mario',
				email = 'mario@email.it',
				sesso = 1,
				newsletter = 0,
				attivita = 2,
				messaggio = 'Ciao a tutti!'
				WHERE id = 5";

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

Similmente a quanto visto nelle precedenti lezioni, anche l'aggiornamento dei dati all'interno di una reale applicazione web può richiedere ulteriori accorgimenti. Spesso infatti è necessario interagire con l'utente per determinare quale record aggiornare. Inoltre, se è l'utente a dover inserire i nuovi dati, è conveniente che possa farlo partendo da quelli attualmente presenti nel record. Un ipotetico script interattivo per la modifica dei dati della tabella "utenti" quindi dovrà prevedere le seguenti fasi:

- mostrare un elenco dei record presenti nella tabella, associando a ciascuno un link che punta alla pagina di modifica dei relativi dati;
- mostrare un form contenente i dati correntemente associati al record selezionato;
- aggiornare i dati del record con quelli inviati dall'utente attraverso il form.
 
Vediamo il codice necessario a realizzare questo script:

 ```
<pre class="brush: php">
<?php
// richiamo il file di configurazione
require 'config.php';

// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

if($_POST && isset($_GET['id']))
{
	aggiorna_record();
}
elseif(isset($_GET['id']))
{
	mostra_record();
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
	<table border="1">
		<tr>
			<th>Nome</th>
			<th>&nbsp;</th>
		</tr>';

	while ($row = mysql_fetch_assoc($result))
	{
		$nome = htmlspecialchars($row['nome']);

		// preparo il link per la modifica dei dati del record
		$link = $_SERVER['PHP_SELF'] . '?id=' . $row['id'];

		echo "<tr>
				<td>$nome</td>
				<td><a href=\"$link\">modifica</a></td>
			</tr>";
	}

	echo '</table>';

	// libero la memoria di PHP occupata dai record estratti con la SELECT
	mysql_free_result($result);

	// chiudo la connessione a MySQL
	mysql_close();
}

function aggiorna_record()
{
	// recupero i campi di tipo "stringa"
	$nome      = trim($_POST['nome']);
	$email     = trim($_POST['email']);
	$messaggio = trim($_POST['messaggio']);

	// verifico se devo eliminare gli slash inseriti automaticamente da PHP
	if(get_magic_quotes_gpc())
	{
		$nome      = stripslashes($nome);
		$email     = stripslashes($email);
		$messaggio = stripslashes($messaggio);
	}

	// effettuo l'escape dei caratteri speciali per inserirli all'interno della query
	$nome      = mysql_real_escape_string($nome);
	$email     = mysql_real_escape_string($email);
	$messaggio = mysql_real_escape_string($messaggio);

	// recupero gli altri campi del form
	$sesso      = isset($_POST['sesso']) ? intval($_POST['sesso']) : 0;
	$newsletter = isset($_POST['newsletter']) ? 1 : 0;
	$attivita   = intval($_POST['attivita']);

	$id = intval($_GET['id']);

	// verifico la presenza dei campi obbligatori
	if(!$nome)
	{
		$messaggio = urlencode("Non hai inserito il nome");
		header("location: $_SERVER[PHP_SELF]?id=$id&msg=$messaggio");
		exit;
	}

	// preparo la query
	$query = "UPDATE utenti SET
				nome = '$nome',
				email = '$email',
				sesso = $sesso,
				newsletter = $newsletter,
				attivita = $attivita,
				messaggio = '$messaggio'
				WHERE id = $id";

	// invio la query
	$result = mysql_query($query);

	// controllo l'esito
	if (!$result) {
		die("Errore nella query $query: " . mysql_error());
	}

	// chiudo la connessione a MySQL
	mysql_close();

	$messaggio = urlencode('Aggiornamento effettuato con successo');
	header("location: $_SERVER[PHP_SELF]?msg=$messaggio");
}

function mostra_record()
{
	// mostro un eventuale messaggio
	if(isset($_GET['msg']))
		echo '<b>'.htmlentities($_GET['msg']).'</b><br /><br />';

	$id = intval($_GET['id']);

	// preparo la query
	$query = "SELECT nome,email,sesso,newsletter,attivita,messaggio FROM utenti WHERE id = $id";

	// invio la query
	$result = mysql_query($query);

	// controllo l'esito
	if (!$result) {
		die("Errore nella query $query: " . mysql_error());
	}

	// controllo che la SELECT abbia restituito un record
	// l'id passato via GET potrebbe essere stato manipolato
	if(mysql_num_rows($result) != 1) {
		die("l'ID passato via GET è errato");
	}

	list($nome,$email,$sesso,$newsletter,$attivita,$messaggio) = mysql_fetch_row($result);

	$nome      = htmlspecialchars($nome);
	$email     = htmlspecialchars($email);
	$messaggio = htmlspecialchars($messaggio);

	?>
	<form name="form_registrazione" method="post" action="">
	  <label>nome:
	  <input name="nome" type="text" value="<?echo $nome?>" />
	  </label>
	  <p>
	    <label>email:
	    <input name="email" type="text" value="<?echo $email?>" />
	    </label>
	  </p>
	  <p> Sesso:
	    <label>
	    <input type="radio" name="sesso" value="1" <?if($sesso==1) echo 'checked="checked"'?> />
	    M</label>
	    <label>
	    <input type="radio" name="sesso" value="2" <?if($sesso==2) echo 'checked="checked"'?>/>
	    F</label>
	  </p>
	  <p>
	    <label>inviami newletter:
	    <input name="newsletter" type="checkbox" value="1" <?if($newsletter) echo 'checked="checked"'?> />
	    </label>
	  </p>
	  <p>
	    <label>attivit&agrave;:
	    <select name="attivita">
	      <option value="0">:: seleziona ::</option>
	      <option value="1" <?if($attivita==1) echo 'selected="selected"'?>>studente</option>
	      <option value="2" <?if($attivita==2) echo 'selected="selected"'?>>lavoratore</option>
	      <option value="3" <?if($attivita==3) echo 'selected="selected"'?>>disoccupato</option>
	    </select>
	    </label>
	  </p>
	  <p>
	    <label>messaggio:<br />
	    <textarea name="messaggio" cols="40" rows="5"><?echo $messaggio?></textarea>
	    </label>
	  </p>
	  <p>
	    <input name="invia" type="submit" value="Invia" />
	  </p>
	</form>
	<?
}
?>
```

Analizziamo nel dettaglio il funzionamento dello script. Per prima cosa è necessario capire in quale fase della procedura di aggiornamento ci troviamo. Per farlo verifichiamo la presenza di dati inviati dall'utente ($\_POST) e dell'id del record passato attraverso un link ($\_GET\['id'\]). Se non è presente nessuno di questi dati allora viene richiamata la funzione mostra\_lista(). Se invece è presente solo l'id allora l'utente avrà cliccato su un link e quindi mostriamo il form per la modifica dei dati, attraverso la funzione mostra\_record(). Se infine è presente sia l'id, sia i dati inviati col form allora procediamo all'aggiornamento del record, richiamando aggiorna\_record(). Le righe 8-17 fungono quindi da "gestore degli eventi" del nostro script

La funzione mostra\_lista() si occupa di estrarre e visualizzare l'elenco dei record presenti nella tabella "utente", associando a ciascuno di essi un link per l'aggiornamento dei dati (riga 48). Le righe 22-23 mostrano un eventuale messaggio generato da aggiorna\_record(), funzione discussa in seguito. Si noti come i testi dinamici inseriti nella pagina vengono adeguatamente passati prima alla funzione htmlentities() per la conversione dei caratteri in entità HTML (righe 23,45).

La funzione mostra\_record() recupera i dati associati al record da modificare, individuato da $\_GET\['id'\], e li inserisce in un form. La funzione intval() applicata a $\_GET\['id'\] ci assicura che l'id passato alla query è costituito da un numero intero (riga 131). Effettuata l'interrogazione al database viene verificata l'effettiva restituzione del record (riga 146) come controllo sulla validità dell'id. Anche in questo caso le stringhe dinamiche da inserire nel form ($nome, $email, $messaggio) vengono preventivamente passate alla funzione htmlentities() (righe 152-154).

La funzione aggiorna\_record() ha infine il compito di effettuare la query di UPDATE con i nuovi dati inviati dall'utente. Eliminati i possibili spazi vuoti alle estremità delle stringhe $nome, $email e $messaggio con la funzione trim (righe 68-70), la funzione si occupa di effettuare un corretto escape delle stesse (righe 73-83). Come ampiamente discusso nella [Lezione 5](http://www.phpnews.it/corsi/corso-php-mysql/inserimento-dati/), dapprima si eliminano i backslash eventualmente inseriti da PHP e poi si procede ad effettuare l'escape con la funzione mysql\_real\_escape\_string(). Viene verificato anche se l'utente ha inserito del testo nel campo "nome" considerato obbligatorio nell'esempio. In caso contrario viene ricaricata la pagina accodando all'URL un messaggio esplicativo (righe 93-97). Se invece il controllo ha esito positivo si procede con l'esecuzione della query di aggiornamento dati e si conclude lo script con un redirect del browser che riporta l'utente alla fase iniziale. Si noti ancora una volta l'uso fatto del redirect attraverso la funzione header() (righe 96, 122) come possibile soluzione al problema del refresh della pagina, già discusso nella [Lezione 6](http://www.phpnews.it/corsi/corso-php-mysql/selezione-dati-recupero-risultati-i/).

Tutta la logica di funzionamento dello script si basa sull'id del record passato attraverso l'URL ($\_GET\['id'\]) pertanto il codice è potenzialmente affetto da un problema di sicurezza legato alla possibile alterazione di tale valore da parte dell'utente. Continuano a valere le considerazioni fatte a riguardo nella [Lezione 8](http://www.phpnews.it/corsi/corso-php-mysql/cancellazione-dati/).

Prima di concludere è doveroso fare un breve riepilogo ed alcune precisazioni sull'uso della funzioni mysql\_num\_rows() e mysql\_affected\_rows() incontrate più volte nei precedenti esempi:

- mysql\_num\_rows() restituisce il numero di record appartenenti ad un result set, prodotto ad esempio (ma non solo!) da una query di tipo SELECT;
- mysql\_num\_rows() non funziona correttamente con mysql\_unbuffered\_query(), poiché restituirà il risultato corretto solo al termine della procedura di fetch;
- mysql\_affected\_rows() restituisce il numero di record realmente interessato dall'ultima query di tipo INSERT, UPDATE o DELETE;
- nelle versioni di MySQL precedenti alla 4.1.2, se viene effettuata una DELETE senza la clausola WHERE, mysql\_affected\_rows() restituisce sempre 0;
- mysql\_affected\_rows() per una UPDATE non considera i record aggiornati con dati identici a quelli preesistenti. Solo i record realmente modificati vengono conteggiati. Tale comportamento può essere modificato passando il numero 2 come quinto parametro della mysql\_connect(): mysql\_connect("localhost", "user", "password", false, 2);
- mysql\_affected\_rows() per una query di tipo REPLACE restituisce la somma dei record cancellati e di quelli inseriti;
- se si è all'interno di una transazione non bisogna attendere la COMMIT per eseguire mysql\_affected\_rows() ma è necessario lanciarla subito dopo la query di INSERT, UPDATE o DELETE.