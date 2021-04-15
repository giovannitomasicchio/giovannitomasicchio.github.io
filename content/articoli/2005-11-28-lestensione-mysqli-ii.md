---
title: "L'estensione mysqli - II"
author: "Giovanni Tomasicchio"
type: article
date: 2005-11-28T11:54:08+0000
description: "Utilizzare l'estenzione mysqli di PHP."
url: /articoli/lestensione-mysqli-ii/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "sicurezza"
  - "BLOB"
  - "debug"
  - "mysqli"

  
---
 In questa seconda parte riprenderemo alcune tecniche già esposte per approfondire diversi aspetti, in particolare vedremo come inviare a MySQL alcune opzioni per la connessione, analizzeremo inoltre la differenza tra query buffered ed unbuffered e vedremo come inviare query multiple. Infine elencheremo altre funzioni di uso comune.

 Con il [primo articolo](http://www.phpnews.it/articoli/php/estensione-mysqli-i/) della serie dedicata all'estensione *ext/mysqli* abbiamo iniziato ad esplorare il nuovo set di funzioni ed oggetti messi a disposizione per l'interazione con MySQL 4.1 e versioni successive. Abbiamo visto come effettuare la connessione al database, come inviare una query e come estrarre eventuali risultati, usando sia il classico stile procedurale sia il nuovo approccio ad oggetti.

 In questa seconda parte riprenderemo alcune tecniche già esposte per approfondire diversi aspetti, in particolare vedremo come inviare a MySQL alcune opzioni per la connessione, analizzeremo inoltre la differenza tra query buffered ed unbuffered e vedremo come inviare query multiple. Infine elencheremo altre funzioni di uso comune.

 Gli esempi che proporremo questa volta utilizzeranno solo l'approccio orientato agli oggetti offerto da *ext/mysqli*, sia perché questo risulta particolarmente diverso dallo stile procedurale della vecchia estensione *ext/mysql*, sia perché permette agli sviluppatori più esperti di poter estendere le classi per ottenere strutture più complesse.

 Inoltre continueremo a far riferimento alla tabella ed ai dati in essa inseriti nel [precedente articolo](http://www.phpnews.it/articoli/php/estensione-mysqli-i/).

- - - - - -

####  Opzioni di connessione

 A volte può essere necessario inviare al server MySQL alcune opzioni per la connessione. In tal caso non è possibile istanziare l'oggetto *mysqli* con l'operazione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 119, 0);">new </span><span style="color: rgb(0, 0, 187);">mysqli</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(221, 0, 0);">...</span><span style="color: rgb(0, 119, 0);">)</span></span>` poiché questa operazione determina anche l'effettiva connessione al server. Per l'invio di opzioni che devono essere impostate prima che la connessione sia instaurata deve essere seguito un altro procedimento:

 1) richiamare la funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_init</span><span style="color: rgb(0, 119, 0);">()</span></span>` che restituisce un oggetto *mysqli* non ancora connesso al server MySQL

 2) usare il metodo `<span style="color: rgb(0, 0, 187);">options</span><span style="color: rgb(0, 119, 0);">()</span>` dell'oggetto *mysqli* per impostare ciascuna opzione di connessione

 3) richiamare il metodo `<span style="color: rgb(0, 0, 187);">real_connect</span><span style="color: rgb(0, 119, 0);">()</span>` dell'oggetto *mysqli*, impostando eventualmente ulteriori opzioni, per effettuare la vera connessione a MySQL

 Vediamo un esempio tratto dalla documentazione ufficiale:

 ```php
<?php 
/* crea un oggetto connessione non ancora connesso */
$mysqli = mysqli_init();

/* imposto le opzioni di connessione */
$mysqli->options(MYSQLI_INIT_COMMAND, "SET AUTOCOMMIT=0");
$mysqli->options(MYSQLI_OPT_CONNECT_TIMEOUT, 5);

/* adesso effettuo la vera connessione al server */
$mysqli->real_connect('localhost', 'root', 'password_db', 'test');

/* controllo l'esito della connessione */
if (mysqli_connect_errno()) {
	printf("Connessione fallita: %s\n", mysqli_connect_error());
	exit();
}

printf ("Connessione: %s\n.", $mysqli->host_info);

$mysqli->close();
?> 
```

 Nella seguente tabella sono elencate le diverse opzioni che possono essere impostate attraverso il metodo `<span style="color: rgb(0, 0, 187);">options</span><span style="color: rgb(0, 119, 0);">()</span>`:

 | Name | Descrizione |
|---|---|
| MYSQLI\_OPT\_CONNECT\_TIMEOUT | Imposta la scadenza della connessione espressa in secondi |
| MYSQLI\_OPT\_LOCAL\_INFILE | Abilita e disabilita l'uso del comando LOAD LOCAL INFILE |
| MYSQLI\_INIT\_CMD | Specifica un comando da eseguire subito dopo la connessione al server MySQL |
| MYSQLI\_READ\_DEFAULT\_FILE | Specifica il nome del file che contiene le impostazioni, da usare al posto di my.cnf |
| MYSQLI\_READ\_DEFAULT\_GROUP | Legge le opzioni del gruppo specificato presente nel file my.cnf oppure nel file specificato con MYSQL\_READ\_DEFAULT\_FILE. |

 Come già anticipato, anche il metodo `<span style="color: rgb(0, 0, 187);">real_connect</span><span style="color: rgb(0, 119, 0);">()</span>` accetta diversi opzioni di connessione, da passare come ultimo parametro. Ecco i flag possibili:

 | Nome | Descrizione |
|---|---|
| MYSQLI\_CLIENT\_COMPRESS | Imposta l'utilizzo di un protocollo di compressione per la comunicazione |
| MYSQLI\_CLIENT\_FOUND\_ROWS | Impone a MySQL di fornire il numero di righe che soddisfano le condizioni della clausola WHERE di una query e non il numero delle sole righe realmente interessate (modificate) dalla query. Questo numero viene restituito con il metodo affected\_rows |
| MYSQLI\_CLIENT\_IGNORE\_SPACE | Permette la presenza di spazi dopo il nome delle funzioni. Rende i nomi delle funzioni termini riservati. |
| MYSQLI\_CLIENT\_INTERACTIVE | Specifica i secondi di inattività secondo un interactive\_timeout (al posto di un wait\_timeout) prima che la connessione venga chiusa |
| MYSQLI\_CLIENT\_SSL | Abilita l'uso di una connessione criptata con il server via SSL |

- - - - - -

####  Query buffered ed unbuffered

 Quando effettuiamo una query che prevede la restituzione di un risultato, ad esempio quando lanciamo una SELECT, dobbiamo tener presente che tale operazione coinvolge più attori: il client, ovvero il motore PHP che effettua la richiesta, il server, ovvero MySQL che riceve la richiesta ed i dati che viaggiano dal server al client.

 Il passaggio dei dati tra MySQL e PHP può avvenire in 2 modi distinti: **buffered** ed **unbuffered**

 Una **query buffered** prevede le seguenti operazioni:

 1) PHP attraverso l'estensione *ext/mysqli* invia la query al server MySQL

 2) MySQL esegue la query ed invia il set completo dei risultati a PHP

 3) PHP conserva i risultati in memoria e li rende accessibili allo script attraverso le operazioni di fetch

 Una **query unbuffered** invece funziona nel seguente modo:

 1) PHP attraverso l'estensione *ext/mysqli* invia la query al server MySQL

 2) MySQL esegue la query e conserva il set dei risultati

 3) PHP accede ai risultati conservati sul server MySQL con le istruzioni di fetch

 Risulta evidente quindi che nel caso delle query buffered i risultati risiedono nella memoria di PHP mentre nel caso delle query unbuffered i dati rimangono sul server. A questo punto verrebbe da chiedersi come mai PHP implementa entrambe le tecniche. La risposta risiede nel fatto che nessuna delle due è a priori migliore dell'altra e che anzi ciascuna ha pregi e difetti e pertanto il loro impiego è indicato in precise situazioni.

 Con le query buffered i dati vengono conservati da PHP che ha quindi la possibilità di poterli manipolare più agevolmente. E' infatti possibile utilizzare il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">data_seek</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` della classe *mysqli\_stmt* per spostarsi liberamente all'interno dei risultati. Inoltre è possibile accedere alla varabile `<span style="color: rgb(0, 0, 187);">num_rows</span>` della classe *mysqli* per sapere subito quante righe sono state restituite dall'ultima query. Liberando immediatamente MySQL, le query buffered permettono al server di sbloccare subito i dati coinvolti dalla query in modo che altri processi possano accedervi.

 L'inconveniente di questo tipo di approccio è l'occupazione di memoria, a volte considerevole, necessaria alla conservazione dei risultati da parte di PHP. Inoltre lo script non può procedere nella sua esecuzione fino a quando tutti i dati non hanno raggiunto il motore PHP, quindi il primo risultato è disponibile solo quando tutti i risultati non sono stati memorizzati dal PHP.

 Per contro le query unbuffered prevedono la conservazione dei dati sul server MySQL. Questi sono accessibili al PHP esclusivamente in maniera sequenziale ma sono disponibili non appena MySQL inizia a restituirli. Il beneficio di non occupare la memoria di PHP deve essere pagato però con un altro inconveniente: poiché l'interazione tra PHP e MySQL non si conclude con l'invio della query, prima che ne venga eseguita una nuova è necessario prelevare tutti i risultati (fetch) della precedente oppure chiudere il set di risultati (free\_result).

 Chiariti i pro ed i contro di ciascun approccio sarà semplice decidere di volta in volta quale tecnica utilizzare. Riassumendo, le query buffered consentono una maggiore elasticità nell'accesso ai risultati e permettono di effettuare nuove query anche se si sta ancora lavorando su risultati precedenti. Al contrario le query unbuffered garantiscono un accesso più immediato al set di risultati e permettono un risparmio in termini di allocazione di memoria da parte di PHP proporzionale alla dimensione dei risultati.

 Per effettuare query buffered ed unbuffered ci sono due alternative: utilizzare il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">real_query</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` della classe *mysqli* insieme ai metodi `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">store_result</span><span style="color: rgb(0, 119, 0);">()</span></span>` e `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">use_result</span><span style="color: rgb(0, 119, 0);">()</span></span>` sempre della classe *mysqli*, oppure usare il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">query</span><span style="color: rgb(0, 119, 0);">()</span></span>` della classe *mysqli*, già incontrato nel precedente articolo, a cui passare un ulteriore parametro (`<span style="color: rgb(0, 0, 187);">MYSQLI_USE_RESULT</span>` o `<span style="color: rgb(0, 0, 187);">MYSQLI_STORE_RESULT</span>`) che specifica il tipo di query.

 Di seguito riportiamo due esempi, ciascuno esegue la stessa query sia in modalità buffered che unbuffered. Il primo usa i metodi `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">store_result</span><span style="color: rgb(0, 119, 0);">()</span></span>` e `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">use_result</span><span style="color: rgb(0, 119, 0);">()</span></span>` mentre il secondo impiega il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">query</span><span style="color: rgb(0, 119, 0);">()</span></span>` insieme ad i parametri `<span style="color: rgb(0, 0, 187);">MYSQLI_USE_RESULT</span>` o `<span style="color: rgb(0, 0, 187);">MYSQLI_STORE_RESULT</span>`

 ```php
<?php 
// provo a connettermi
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

echo "- Eseguo una query unbuffered -<br /><br />\n";

// lancio la query
$mysqli->real_query('SELECT user_id, nome, cognome FROM mia_tabella');

// dichiaro di voler utilizzare i risultati in modo unbuffered
$result = $mysqli->use_result();

// provo ad leggere il numero di righe restituite
// otterrò un risultato errato
echo 'Risultati ottenuti: ' , $result->num_rows , " (errato!)<br />\n";

// eseguo la fetch dei risultati
while ($row = $result->fetch_row())
{
	printf ("ID utente:%d, nome:%s, cognome:%s<br />\n",
	$row[0], $row[1],$row[2]);
}
echo 'Risultati ottenuti: ' , $result->num_rows , " (corretto)<br /><br />\n";

// --------------------------------------------------------

echo "- Eseguo una query buffered -<br /><br />\n";

// lancio la query
$mysqli->real_query('SELECT user_id, nome, cognome FROM mia_tabella');

// dichiaro di voler utilizzare i risultati in modo buffered
$result = $mysqli->store_result();

// provo ad leggere il numero di righe restituite
// otterrò un risultato errato
echo 'Risultati ottenuti: ' , $result->num_rows , " (corretto)<br />\n";

// eseguo la fetch dei risultati
while ($row = $result->fetch_row())
{
	printf ("ID utente:%d, nome:%s, cognome:%s<br />\n",
	$row[0], $row[1],$row[2]);
}
?>  
```

 Adesso vediamo l'uso dei paramentri `<span style="color: rgb(0, 0, 187);">MYSQLI_USE_RESULT</span>` e `<span style="color: rgb(0, 0, 187);">MYSQLI_STORE_RESULT</span>`

 ```php
<?php 
// provo a connettermi
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

echo "- Eseguo una query unbuffered -<br /><br />\n";

// lancio la query unbuffered
$result = $mysqli->query('SELECT user_id, nome, cognome FROM mia_tabella',MYSQLI_USE_RESULT);

// provo ad leggere il numero di righe restituite
// otterrò un risultato errato
echo 'Risultati ottenuti: ' , $result->num_rows , " (errato!)<br />\n";

// eseguo la fetch dei risultati
while ($row = $result->fetch_row())
{
	printf ("ID utente:%d, nome:%s, cognome:%s<br />\n",
	$row[0], $row[1],$row[2]);
}
echo 'Risultati ottenuti: ' , $result->num_rows , " (corretto)<br /><br />\n";

// --------------------------------------------------------

echo "- Eseguo una query buffered -<br /><br />\n";

// lancio la query unbuffered
$result = $mysqli->query('SELECT user_id, nome, cognome FROM mia_tabella',MYSQLI_STORE_RESULT);

// provo ad leggere il numero di righe restituite
// otterrò un risultato errato
echo 'Risultati ottenuti: ' , $result->num_rows , " (corretto)<br />\n";

// eseguo la fetch dei risultati
while ($row = $result->fetch_row())
{
	printf ("ID utente:%d, nome:%s, cognome:%s<br />\n",
	$row[0], $row[1],$row[2]);
}
?>  
```

 Nel caso al metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">query</span><span style="color: rgb(0, 119, 0);">()</span></span>` non venga passato alcun parametro, le impostazioni di default prevedono che la query sia buffered. Si noti infine come nel caso di query unbuffered la variabile `<span style="color: rgb(0, 0, 187);">num_rows</span>` restituisce valori errati se chiamato prima della conclusione della fase di fetch.

- - - - - -

####  Query multiple

 Una tra le più interessanti novità introdotte con l'estensione *ext/mysqli* è sicuramente la possibilità di inviare al server MySQL più query con un'unica chiamata di funzione. Basterà infatti invocare il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">multi_query</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` della classe *mysqli* e passargli una stringa contenente le diverse query separate da un punto e virgola.

 Risulta però più complessa l'estrazione dei risultati provenienti dalle singole query inviate. Nell'esempio che segue vedremo come risolvere il problema. Verranno eseguite due query e lanciate con un'unica chiamata al metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">multi_query</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>`. Per recuperare il primo set di risultati useremo il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">store_result</span><span style="color: rgb(0, 119, 0);">()</span></span>` (query buffered) seguito da una normale fetch. Dopo aver chiuso il set di risultati corrente (`<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">close</span><span style="color: rgb(0, 119, 0);">()</span></span>`) si passerà a quello successivo attraverso il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">next_result</span><span style="color: rgb(0, 119, 0);">()</span></span>` e l'intera procedura verrà ripetuta.

 ```php
<?php 
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// preparo le query da inviare al server MySQL
$query = "SELECT COUNT(*) FROM mia_tabella;";
$query .= "SELECT * FROM mia_tabella";

// eseguo le query
$mysqli->multi_query($query);

// recupero il primo set di risultati (metodo buffered)
$result = $mysqli->store_result();

// estraggo i risultati
$row = $result->fetch_row();
printf("Numero utenti: %s<br /><br />\n", $row[0]);


// chiudo il primo set di risultati
$result->close();

// passo al secondo set di risultati
$mysqli->next_result();

// recupero il secondo set di risultati (metodo buffered)
$result = $mysqli->store_result();

// estraggo i risultati
echo "Elenco utenti:<br />\n";
while ($row = $result->fetch_assoc())
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n<br />",
	$row['user_id'], $row['nome'],$row['cognome']);
}

// chiudo il secondo set di risultati
$result->close();

// chiudo la connessione al server MySQL
$mysqli->close();
?> 
```

 E' importante osservare che l'invio di query multiple al server MySQL espone lo script a potenziali rischi di sicurezza. Infatti se una query contiene dati inviati dall'utente, questa potrebbe venir abilmente manipolata. Si pensi ad esempio alle query:

 SELECT user\_id FROM utenti WHERE nome\_utente = '$\_POST\[nome\_utente\]'; SELECT COUNT(\*) FROM UTENTI

 se $\_POST\[nome\_utente\] vale

 test'; DELETE \* FROM utenti; SELECT '

 la query diventa:

 SELECT user\_id FROM utenti WHERE nome\_utente = 'test'; DELETE \* FROM utenti; SELECT ' '; SELECT COUNT(\*) FROM UTENTI

 Siamo riusciti a cancellare tutti i dati dalla tabella utenti!

- - - - - -

####  Altre funzioni di ext/mysqli

 Prima di concludere questo articolo elenchiamo alcune funzioni messe a disposizione dall'estensione *ext/mysqli* che risultano estremamente utili e di uso comune. La maggior parte di queste comunque era già disponibile con la vecchia estensione *ext/mysql* ma risulta comunque interessante analizzare il loro utilizzo in un approccio orientato agli oggetti.

 **mysqli::affected\_rows**
 (mysqli\_affected\_rows)

 Restituisce il numero di righe interessate dall'ultima query. `<span style="color: rgb(0, 0, 187);">affected_rows</span>` va usato solo quando la query modifica il contenuto del database, come nel caso di INSERT, UPDATE e DELETE e non deve essere impiegato con le SELECT. Nel caso di una query di tipo UPDATE `<span style="color: rgb(0, 0, 187);">affected_rows</span>` restituisce il numero di righe realmente modificate e non il numero di righe che rispondono alla clausola WHERE della query stessa. Questo comportamento può essere modificato agendo sui parametri di connessione come mostrato all'inizio di questo articolo.

 ```php
<?php 
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// preparo le query di inserimento
$query = "INSERT INTO mia_tabella SET nome = 'Gianni', cognome = 'Rossi'";

// eseguo le query
$mysqli->multi_query($query);

// mostro il numero delle righe interessate dall'ultima query
echo 'Numero di righe inserite: ', $mysqli->affected_rows , '<br />';

// preparo le query di cancellazione
$query = "DELETE FROM mia_tabella WHERE nome = 'Gianni'";

// eseguo le query
$mysqli->multi_query($query);

// mostro il numero delle righe interessate dall'ultima query
echo 'Numero di righe cancellate: ', $mysqli->affected_rows;

// chiudo la connessione al server MySQL
$mysqli->close();
?> 
```

 **mysqli\_connect\_error e mysqli\_connect\_errno**

 Forniscono rispettivamente un messaggio di errore ed un relativo codice numerico associato nel caso in cui la connessione al server MySQL non abbia avuto esito positivo. In particolare`<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);"> mysqli_connect_errno</span><span style="color: rgb(0, 119, 0);">()</span></span>` può essere impiegato per verificare l'esito della connessione semplicemente con un IF.

 ```php
<?php 
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// Verifico l'esito della connessione
if (mysqli_connect_errno())
{
	printf("Connessione fallita: %s\n", mysqli_connect_error());
	exit();
}
?> 
```

 **mysqli\_result::data\_seek**
 (mysqli\_data\_seek)

 Nel caso di query buffered permette di posizionarsi su una qualsiasi delle righe restituite dalla query

 ```php
<?php 
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// preparo le query da inviare al server MySQL
$query = "SELECT * FROM mia_tabella";

// eseguo le query
$result = $mysqli->query($query);

// sposto il puntatore dei risultati alla terza riga
$result->data_seek(2);

// estraggo la riga
$row = $result->fetch_row();

// mostro i risultati
printf ("ID utente:%d, nome:%s, cognome:%s<br />\n",$row[0],$row[1],$row[2]);

// chiudo la connessione al server MySQL
$mysqli->close();
?>
```

 **mysqli::error e mysqli::errno**
 (mysqli\_errno e mysqli\_error)

 Forniscono rispettivamente un messaggio di errore ed un relativo codice numerico associati all'ultimo errore verificatosi nell'interazione con il database MySQL. Utili per effettuare il debug delle query

 ```php
<?php 
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// eseguo le query
if(!$mysqli->query("SELECT * FROM tabella_inesistente"))
{
	printf('Errore nella query (codice %d): %s', $mysqli->errno,$mysqli->error);
}
?> 
```

 **mysqli\_result::field\_count**
 ( mysqli\_field\_count)

 Permette di sapere quanti campi (colonne) contiene un set di risultati. Particolarmente utile quando viene eseguito il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">store_result</span><span style="color: rgb(0, 119, 0);">()</span></span>` e non è noto a priori se la query restituirà dei risultati (es. una SELECT) o meno (INSER, UPDATE, ...)

 ```php
<form id="form1" name="form1" method="post" action=""> 
<label>Inserisci una query: 
<input name="query" type="text" size="50" /> 
</label> 
<input type="submit" name="Submit" value="&lt;- Esegui" /> 
</form> 
<?php 
if($_POST)
{
	// provo a connettermi al server MySQL
	$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

	// eseguo le query
	$result = $mysqli->real_query($_POST['query']);

	// se la query ha prodotto un risultato
	if($mysqli->field_count)
	{
		$result = $mysqli->store_result();

		while ($row = $result->fetch_assoc())
		{
			foreach ($row as $key => $value)
			{
				echo "$key=>$value ";
			}
			echo '<br />';
		}
	}
}
?> 
```

 **mysqli::insert\_id**
 (mysqli\_insert\_id)

 Fornisce l'ultimo ID autogenerato da MySQL per un campo auto\_increment.

 ```php
<?php 
// provo a connettermi al server MySQL
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// eseguo le query
$result = $mysqli->query("INSERT INTO mia_tabella SET nome='Gigi', cognome='Cremeria'");

echo "L'ID del nuovo utente inserito è ", $mysqli->insert_id;
?>
```

 **mysqli::more\_results**
 ( mysqli\_more\_results)

 Nel caso di query multiple restituisce true se sono presenti ulteriori set di risultati provenienti da una precedente chiamata alla funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">multi_query</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>`. Utile quando non si conosce a priori il numero delle query eseguite con una `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">multi_query</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 119, 0);">)</span></span>` e pertanto non è noto il numero di set di risultati da elaborare.

 Nel prossimo articolo, che concluderà la serie dedicata a ext/mysqli, vedremo come sfruttare i prepared statements per ottenere il binding dei parametri e dei risultati, come effettuare le transazioni e come gestire i BLOB.