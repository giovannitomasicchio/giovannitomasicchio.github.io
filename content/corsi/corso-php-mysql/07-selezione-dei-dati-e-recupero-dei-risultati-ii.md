---
title: "Selezione dei dati e recupero dei risultati - II"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:02:31+0000
description: "Script PHP per la lettura dei dati di una tabella MySQL"
url: /corsi/corso-php-mysql/selezione-dei-dati-e-recupero-dei-risultati-ii/
weight: 4
categories:
  - Corso PHP MySQL
  
---
 Con gli esempi della nella precedente lezione abbiamo visto come recuperare dei dati da un database MySQL impiegando le diverse procedure di fetch dei risultati di una query. Nella maggior parte dei casi questi dati devono poi essere mostrati all'utente, all'interno di una pagina (X)HTML, per cui è necessario prestare attenzione ad ulteriori aspetti, non meno importanti, legati sia a questioni estetiche e di rispetto degli standard, sia alla sicurezza dello script. In particolare è importante tener presente che:

- alcuni caratteri non possono essere inseriti così come si presentano all'interno di una pagina HTML ma è necessario prima convertirli nelle relative "enità HTML". Questa conversione risolve seri problemi di sicurezza che si possono verificare se tali stringhe sono state precedentemente inserite nel database da un utente.
- gli "a capo" presenti in un testo non hanno alcun effettuo sulla formattazione di una pagina HTML. Devono pertanto essere convertiti negli "a capo" dell' HTML, ovvero nei tag &lt;br&gt; .
- in alcune circostanze, ad esempio quando si inseriscono contenuti in una tabella HTML, bisogna sostituire le stringhe vuote con qualcosa di trasparente, ad esempio l'entità &amp;nbsp;
- molte informazioni memorizzate nel DB hanno una qualche forma di codifica che va correttamente trattata. Ad esempio il campo "attivita" della tabella "utenti" codifica con i numeri da 0 a 3 le attività "non dichiarata","studente", "impiegato", "disoccupato". All'utente andrà mostrata l'attività, non il numero che la rappresenta.
 
 Vediamo quindi come tradurre in codice le precedenti osservazioni, considerando uno script che mostra i dati contenuti nella tabella "utenti" all'interno di una tabella HTML.

 ```php
<?php
// richiamo il file di configurazione
require 'config.php';

// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

// preparo la query
$query = "SELECT nome,email,sesso,newsletter,attivita,messaggio FROM utenti";

// invio la query
$result = mysql_query($query);

// controllo l'esito
if (!$result) {
	die("Errore nella query $query: " . mysql_error());
}

$sesso_arr = array('&nbsp;','M','F');
$attivita_arr = array('&nbsp;','studente','lavoratore','disoccupato');

echo '
<table border="1">
	<tr>
		<th>Nome</th>
		<th>Email</th>
		<th>Sesso</th>
		<th>Newsletter</th>
		<th>Attivit&agrave;</th>
		<th>Messaggio</th>
	</tr>';

while ($row = mysql_fetch_assoc($result)) {
	$nome      = htmlentities($row['nome']);
	$email     = htmlentities($row['email']);
	$messaggio = htmlentities($row['messaggio']);
	$messaggio = nl2br($messaggio);

	if(!$email)     $email = '&nbsp;';
	if(!$messaggio) $messaggio = '&nbsp;';

	$sesso = $sesso_arr[$row['sesso']];
	$newsletter = $row['newsletter'] ? 'Si' : 'No';
	$attivita = $attivita_arr[$row['attivita']];

	echo "<tr>
			<td>$nome</td>
			<td>$email</td>
			<td>$sesso</td>
			<td>$newsletter</td>
			<td>$attivita</td>
			<td>$messaggio</td>
		</tr>";
}

echo '</table>';

// libero la memoria di PHP occupata dai record estratti con la SELECT
mysql_free_result($result);

// chiudo la connessione a MySQL
mysql_close();
?>
```

 Le righe 34-36 provvedono alla conversione dei caratteri speciali con le relative entità HTML attraverso l'istruzione **htmlentities()**. La corretta visualizzazione degli "a capo" è invece affidata alla funzione **nl2br()** (riga 37)

 La decodifica delle informazioni del sesso e dell'attività avviene attraverso l'uso degli array $sesso\_arr e $attivita\_arr. Per evitare la creazione di celle vuote abbiamo utilizzato l'entità &amp;nbsp; sia negli array $sesso\_arr e $attivita\_arr (righe 19-20), sia come alternativa alla stringa vuota per le variabili $email e $messaggio (righe 39-40).

 Al di là delle precauzioni prese in questo script di esempio, ciò che è doveroso sottolineare è l'importanza di una corretta valutazione del formato di destinazione dei dati prelevati dal database ((X)HTML, PDF, email, file di testo, XML, ecc.). Lo script PHP infatti ha il compito di fare da tramite tra il database e l'output finale pertanto non si può limitare ad estrarre i record dalla tabella ma deve effettuare un'adeguata conversione degli stessi, operazione che se sottovalutato può condurre a seri problemi di sicurezza.