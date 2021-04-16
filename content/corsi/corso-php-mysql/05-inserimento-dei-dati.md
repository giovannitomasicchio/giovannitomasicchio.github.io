---
title: "Inserimento dei dati"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:01:40+0000
description: "Inserire dati in una tabella MySQL con PHP"
url: /corsi/corso-php-mysql/inserimento-dei-dati/
weight: 6
categories:
  - Corso PHP MySQL
  
---
Adesso che sappiamo connetterci al server MySQL vediamo come effettuare le query. Cominciamo con un inserimento dati, che segue la sintassi:

 ```

INSERT INTO 
   nome_tabella 
SET 
   nome_campo_1 = valore_1,
   nome_campo_2 = valore_2
```

oppure:

 ```

INSERT INTO nome_tabella 
   (nome_campo_1, nome_campo_2) 
VALUES 
   (valore_1, valore_2)
```

In generale per lanciare una query in uno script PHP dobbiamo semplicemente chiamare la funzione **mysql\_query()** a cui passare come parametro la stringa contenete la query. Nel caso di un inserimento mysql\_query() restituisce TRUE o FALSE, a seconda dell'esito dell'operazione. Ci basta quindi controllare tale valore per verificare se i dati sono stati correttamente immessi nella tabella. In caso negativo con la funzione **mysql\_error()** possiamo ottenere maggiori indicazioni su cosa è andato storto.

Il seguente esempio fa riferimento al database ed alla tabella presentati nella [Lezione 2](http://www.phpnews.it/corsi/corso-php-mysql/setup-del-server-e-creazione-del-database/). La connessione avviene grazie agli script config.php e connect.php presentati nella [Lezione 4](http://www.phpnews.it/corsi/corso-php-mysql/script-di-connessione/).

 ```

<?php
// richiamo il file di configurazione
require 'config.php';

// richiamo lo script responsabile della connessione a MySQL
require 'connect.php';

// preparo la query
$query = "INSERT INTO utenti (nome,email,sesso,newsletter,attivita,messaggio)
		VALUES ('Mario Rossi','m.rossi@email.it',1,1,2,'inviatemi del materiale illustrativo')";

// lancio la query
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

E' importante osservare che si è preferito non passare direttamente alla funzione mysql\_query() la stringa contenente la query. Tale stringa è stata memorizzata nella variabile $query perché potrebbe risultare utile mostrarla in caso di errore, soprattutto se è stata costruita dinamicamente in funzione di dati provenienti dall'utente (caso mostrato più avanti).

Se avessimo effettuato due o più connessioni a diversi server MySQL, per specificare a quale di questi stiamo inviando la query avremmo dovuto passare alla funzione mysql\_query() come secondo parametro anche l'identificativo di connessione ottenuto durante la procedura di connessione al server.

 

Vediamo ora come eseguire la query di inserimento nel caso in cui i dati provengano da un form compilato da un utente. L'interazione con l'utente complica l'esecuzione della query poiché questa volta non sappiamo a priori cosa verrà inviato con il form. Sono necessarie diverse precauzioni quindi e nel seguente esempio ci concentreremo su quelle che assicurano la creazione di una query corretta, tralasciando altri controlli di validità dei dati non meno importanti ma al di la degli scopi di questo corso.

Lo script che segue si occupa di presentare all'utente un form composto da diversi campi (nome, email, sesso, newsletter, attivita, messaggio) appartenenti a differenti tipologie di controlli (textbox, radio button, checkbox, lista, textarea). Quando l'utente invia il form, lo script estrae dall'array $\_POST le informazioni necessarie a construire la query di inserimento.

Uno dei principali problemi di questo processo è costituito dall'inserimento all'interno della query di stringhe arbitrarie inviate dall'utente. Sappiamo infatti che in una query le stringhe vanno racchiuse da apici:

 ```

INSERT INTO elenco_film SET titolo = 'Al bar dello sport'
```

ma cosa succede se la stringa contiene a sua volta un apice (apostrofo)?

 ```

INSERT INTO elenco_film SET titolo = 'L'allenatore nel pallone'
```

E' evidente che questa query non è corretta, infatti non è possibile distinguere l'apostrofo del titolo del film dall'apice che delimita la fine della stringa. Per evitare l'ambiguità la query va corretta nel seguente modo, inserendo il carattere \\ (backslash) prima dell'apostrofo:

 ```

INSERT INTO elenco_film SET titolo = 'L\'allenatore nel pallone'
```

Il PHP possiede una funzionalità chiamata **magic quotes GPC** che consiste nell'eseguire automaticamente l'**escape**, ovvero l'inserimento di un backslash, appena prima di un ' (apice singolo), " (apice doppio), \\ (backslash) e NUL per tutti i dati provenienti da GPC (Get, Post e Cookie), ma solo se nel php.ini è abilitata la direttiva magic\_quotes\_gpc. Sembrerebbe quindi che il problema sia immediatamente risolvibile abilitando tale direttiva.

In realtà la questione è più complessa, infatti ci sono altri caratteri che potrebbero creare dei problemi se inseriti in una query ed inoltre questi dipendono anche dal set di caratteri impiegato nella connessione a MySQL. L'approccio più corretto quindi è disabilitare la funzionalità "magic quotes GPC" ed affidarsi ad una particolare funzione, **mysql\_real\_escape\_string()**, che effettua il corretto escape di tutti i caratteri potenzialmente pericolosi, tenendo in conto il particolare set di caratteri impiegato nella connessione tra PHP e MySQL (alcuni programmatori utilizzano la funzione addslash() al posto di mysql\_real\_escape\_string(), approccio non del tutto errato ma sicuramente meno specifico e sicuro per un database MySQL).

Se non è possibile disabilitare il "magic quotes GPC", oppure se vogliamo realizzare uno script indipendente da tale funzionalità, è possibile procedere in questo modo. Dapprima controlliamo, interrogando la funzione **get\_magic\_quotes\_gpg()**, se il magic quote è attivato. In caso affermativo rimuoviamo i backslash inseriti dal PHP con la funzione **stripslashes()**. Procediamo poi con mysql\_real\_escape\_string() per effettuare un corretto escape di tutte le stringhe da inserire nella query.

Ciò che stiamo facendo è proteggere il nostro codice da uno dei più pericolosi problemi di sicurezza: l'**SQL Injection**.

Vediamo lo script completo:

 ```

<?php
if($_POST) {
	inserisci_record();
}
else {
	mostra_form();
}

function inserisci_record()
{
	// richiamo il file di configurazione
	require 'config.php';

	// richiamo lo script responsabile della connessione a MySQL
	require 'connect.php';

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

	$nome      = mysql_real_escape_string($nome);
	$email     = mysql_real_escape_string($email);
	$messaggio = mysql_real_escape_string($messaggio);

	// recupero gli altri campi del form
	$sesso      = isset($_POST['sesso']) ? intval($_POST['sesso']) : 0;
	$newsletter = isset($_POST['newsletter']) ? 1 : 0;
	$attivita   = intval($_POST['attivita']);

	// verifico la presenza dei campi obbligatori
	if(!$nome)
	{
		$messaggio = urlencode("Non hai inserito il nome");
		header('location: '.$_SERVER['PHP_SELF'].'?msg='.$messaggio);
		exit;
	}

	// preparo la query
	$query = "INSERT INTO utenti (nome,email,sesso,newsletter,attivita,messaggio)
			  VALUES ('$nome','$email',$sesso,$newsletter,$attivita,'$messaggio')";

	// invio la query
	$result = mysql_query($query);

	// controllo l'esito
	if (!$result) {
		die("Errore nella query $query: " . mysql_error());
	}

	// recupero l'id autoincrement generato da MySQL per il nuovorecord inserito
	$id_inserito = mysql_insert_id();

	// chiudo la connessione a MySQL
	mysql_close();

	$messaggio = urlencode("Inserimento effettuato con successo (ID=$id_inserito)");
	header('location: '.$_SERVER['PHP_SELF'].'?msg='.$messaggio);
}

function mostra_form()
{
	// mostro un eventuale messaggio
	if(isset($_GET['msg']))
		echo '<b>'.htmlentities($_GET['msg']).'</b><br /><br />';
	?>
	<form name="form_registrazione" method="post" action="">
	  <label>nome:
	  <input name="nome" type="text" />
	  </label>
	  (obbligatorio)
	  <p>
	    <label>email:
	    <input name="email" type="text" />
	    </label>
	  </p>
	  <p> Sesso:
	    <label>
	    <input type="radio" name="sesso" value="1" />
	    M</label>
	    <label>
	    <input type="radio" name="sesso" value="2" />
	    F</label>
	  </p>
	  <p>
	    <label>inviami newletter:
	    <input name="newsletter" type="checkbox" value="1" />
	    </label>
	  </p>
	  <p>
	    <label>attivit&agrave;:
	    <select name="attivita">
	      <option value="0">:: seleziona ::</option>
	      <option value="1">studente</option>
	      <option value="2">lavoratore</option>
	      <option value="3">disoccupato</option>
	    </select>
	    </label>
	  </p>
	  <p>
	    <label>messaggio:<br />
	    <textarea name="messaggio" cols="40" rows="5"></textarea>
	    </label>
	  </p>
	  <p>
	    <input name="invia" type="submit" value="Invia" />
	  </p>
	</form>
	<?php
}
?>
```

Tutto lo script si basa su due funzioni, mostra\_form() per mostrare il form all'utente e inserisci\_record() per prelevare i dati inviati via POST, preparare ed eseguire la query di inserimento.

La funzione mostra\_form() non necessita di particolari commenti. Si osservino le righe 72-73 che permettono di mostrare un messaggio passato via GET la cui utilità verrà spiegata in seguito.

La funzione inserisci\_record() invece presenta diversi aspetti su cui vale la pena soffermarsi:

- E' buona norma eliminare eventuali spazi vuoti presenti all'estremità delle stringhe inviate dall'utente. Questi spazi, oltre a costituire uno spreco di risorse del database, possono compromettere il corretto funzionamento dei controlli sulla stringa stessa. Si pensi ad esempio alla verifica della lunghezza della stringa. Per eliminare questi spazio basta usare la funzione trim() (righe 18-20).
- Come ampiamente discusso in precedenza, è necessario effettuare l'escape dei caratteri potenzialmente "pericolosi" presenti nelle stringhe inviate dall'utente. Per prima cosa controlliamo se il PHP (probabilmente in maniera non adeguata) ha già effettuato tale operazione interrogando **get\_magic\_quotes\_gpc()** (riga 23). In caso affermativo dobbiamo eliminare i backslash inseriti con **stripslashes()** (righe 25-27). Procediamo poi al corretto escape delle stringhe con la funzione **mysql\_real\_escape\_string()** (righe 30-32).
- Particolare attenzione bisogna prestare al trattamento dei dati provenienti dalle checkbox e dai radio button. Se una checkbox viene selezionata dall'utente allora il suo valore sarà presente nell'array $\_POST, altrimenti tale valore sarà assente. Pertanto usiamo la funzione **isset()** per effettuare tale verifica. Anche un set di radio buttons potrebbe essere privo della selezione dell'utente, per cui usiamo anche in questo caso la funzione isset().
- Se un campo della tabella ospita un intero (come i campi "sesso" e "attivita") allora è meglio assicurarci che la query contenga un valore intero da inserire. Per farlo utilizziamo la funzione **intval()**. Anche questo accorgimento serve a proteggere lo script da una SQL Injection.
- Quando si inserisce un record in una tabella è necessario assicurarsi che almeno un campo sia stato riempito. Nell'esemio riportato si è scelto di verificare che il campo "nome" non venga lasciato vuoto. Questo controllo sarebbe stato inefficace se non avessimo usato precedentemente la funzione **trim()**.
- E' importante preparare la query e conservarla in una variabile ($query) invece di passarla direttamente alla funzione mysql\_query(). In caso di errore è molto utile visualizzare la query costruita con i dati dell'utente per cercare al suo interno eventuali errori causati dalla sua realizzazione dinamica.
- Una tabella contenente un campo auto\_increment genera automaticamente un numero intero ad ogni inserimento di un nuovo record. Questo numero spesso rappresenta l'identificativo (ID) del record inserito e pertanto potrebbe essere utile recuperarlo dopo aver eseguito la query. La funzione **mysql\_insert\_id()** serve proprio ad ottenere questo numero.
 
Uno dei problemi più comuni in questo tipo di script è la gestione del refresh della pagina. Se l'utente, dopo aver inviato il form, ricarica la pagina (pulsante "aggiorna" del browser) è possibile che lo script di inserimento venga mandato nuovamente in esecuzione e che quindi la query di inserimento venga ripetuta. Una soluzione è quella di reindirizzare il browser verso un URL qualsiasi, anche quello dello stesso script di inserimento, in modo da perdere i dati inviati dall'utente (POST). Pertanto, eseguita l'interazione col database, lo script termina chiamando la funzione **header()** che conduce il browser all'indirizzo specificato. Conviene comunque mostrare un messaggio all'utente, per informarlo dell'esito dell'operazione. Per farlo possiamo accodare all'URL utilizzato per il redirect il messaggio da mostrare all'utente. Siccome l'URL non può contenere qualsiasi carattere allora prepariamo il messaggio convertendo gli eventuali caratteri illeciti con la funzione urlencode(). Ecco spiegato anche l'utilità della riga 73 che provvede a visualizzare il contenuto di $\_GET\['msg'\].