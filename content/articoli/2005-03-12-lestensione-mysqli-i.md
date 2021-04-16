---
title: "L'estensione mysqli - I"
author: "Giovanni Tomasicchio"
type: article
date: 2005-03-12T09:55:33+0000
description: "Introduzione all'estensione mysqli di PHP 5"
url: /articoli/lestensione-mysqli-i/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "sicurezza"
  - "debug"
  - "exception"
  - "mysqli"
  - "OOP"
  - "SQL"

  
---
 Dopo anni di onorato servizio l'estensione ext/mysql esce di scena lasciando il compito di permettere l'interazione tra PHP e MySQL alla nuova estensione ext/mysqli.

 Georg Richter, il responsabile dello sviluppo di questa libreria, ha affermato in una intervista che il codice di ext/mysql, a causa delle continue modifiche ed aggiornamenti subiti nel corso degli anni, era diventato praticamente illeggibile e ciò ha reso la sua manutenzione sempre più difficile.

 E' stato necessario riscrivere questa estensione anche a causa delle numerose caratteristiche del nuovo MySQL 4.1 e del futuro MySQL 5 che bisognava supportare.

 PHP 5 segna quindi un punto di svolta anche per l'interazione con il database MySQL che risulta così "improved" come suggerisce la "i" di mysqli, ovvero migliorata.

 Qualcuno ha visto invece in quella "i" l'iniziale della parola "incompleta". Infatti, nonostante le caratteristiche e le funzionalità principali possono essere considerate stabili e solide, questa estensione è ancora soggetta ad intenso sviluppo.

 Le principali novità che ext/mysqli introduce sono:

- la possibilità di utilizzare un approccio ad oggetti
- l'utilizzo di un protocollo di comunicazione binario che garantisce performance migliorate e permette il supporto delle *prepared statements*
- avanzate capacità di tracing e debugging
- supporto alle funzioni di replicazione, bilanciamento dei carichi, stored procedures e query multiple
- migliore sicurezza durante la procedura di autenticazione
 
 Dando un'occhiata alle funzioni di ext/mysqli risulta evidente lo sforzo fatto per rendere questa estensione il più possibile compatibile con la precedente. E' possibile infatti ritrovare quasi tutte le vecchie funzioni, semplicemente con una "i" aggiunta al nome. Ad esempio mysql\_connect è diventata mysqli\_connect, mysql\_query adesso si chiama mysqli\_query.

 Ma non bisogna farsi ingannare da questa apparente somiglianza, alcune funzioni infatti vanno usate in maniera diversa. Ad esempio non viene più supportata la connessione automatica al server MySQL, questa deve essere esplicitamente eseguita. Inoltre adesso è necessario passare alla funzione mysqli\_query il link al database.

 E' doveroso sottolineare come, nonostante PHP 5 e MySQL 4.1 siano stati dichiarati stabili e quindi adatti ad un contesto di produzione, bisognerà aspettare ancora del tempo prima che i vari fornitori di servizi di hosting aggiornino i loro server. Questo significa che abbiamo ancora un po' di tempo per familiarizzare con ext/mysqli ma anche che non possiamo ignorarne l'esistenza.

 In questa serie di articoli affronteremo sia le procedure più basilari per l'interazione con un server MySQL sia le caratteristiche avanzante sottolineando le novità introdotte da ext/mysqli e le differenze con ext/mysql.

 Per coloro che non hanno ancora installato PHP 5 e MySQL 4.1 rimandiamo a [questo articolo](/articoli/installare-apache-2-php-5-mysql-4.1-su-windows/) per la procedura dettagliata. Facoltativa ma consigliata è anche l'installazione di phpMyAdmin illustrata [qui](/articoli/guida-installazione-phpmyadmin/).

- - - - - -

####  Le classi di ext/mysqli

 La novità più eclatante introdotta da mysqli è sicuramente l'introduzione, accanto ad un consolidato ed ampliato set di funzioni, di una interfaccia ad oggetti per l'interazione con MySQL. Si tratta in particolare delle seguenti tre classi:

 **mysqli**

> è la classe che si occupa di aprire e chiudere una connessione tra PHP ed un server MySQL, di selezionare il database di default, effettuare le query, ottenere informazioni di debug, gestire le transazioni ecc.

 **mysqli\_stmt**

> questa classe si occupa invece della gestione delle *prepared statement*

 **mysqli\_result**

> è la classe che rappresenta un set di risultati ottenuti a seguito di una query effettuata sul database. Esegue la fetch e fornisce informazioni sui dati ottenuti.

 Scorrendo l'elenco delle classiche funzioni "procedurali" di ext/mysqli e confrontandole con i metodi e le proprietà di queste classi si nota subito come siano strettamente correlate. Infatti quasi per tutte le funzioni è possibile trovare un corrispondente metodo o una proprietà appartenente ad una delle tre classi citate. Ad esempio la funzione mysqli\_query è equivalente al metodo mysqli::query oppure la funzione mysqli\_num\_rows corrisponde alla proprietà mysqli\_result::num\_rows. Passare quindi dall'approccio procedurale a quello ad oggetti risulta quindi particolarmente semplice. Per questo motivo affronteremo tutte le procedure proponendo degli esempi sia in stile procedurale che ad oggetti.

 Alcune funzioni tuttavia fanno eccezione, ad esempio quelle per la gestione della replicazione non hanno una controparte nel modello ad oggetti.

- - - - - -

####  Connesssione al server MySQL

 Vediamo quindi come usare le funzioni o le classi messeci a disposizione da ext/mysqli per interagire con un server MySQL. Iniziamo vedendo come connetterci al server attraverso un approccio procedurale, ovvero usando le funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_connect</span></span>`:

 ```php
<?php
// provo a connettermi
$link = mysqli_connect('localhost', 'root', 'password_db', 'test');

// controllo l'esito della connessione
if (!$link) {
	echo 'Errore durante la connessione al server MySQL';
	exit();
}
else {
	echo 'Connessione effettuata con successo';
}

// chiudo la connessione
mysqli_close($link);
?>
```

 Come è possibile vedere nell'esempio precedente, passiamo alla funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_connect</span></span>` l'indirizzo del server MySQL `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(221, 0, 0);">'localhost'</span></span>`, il nome dell'utente del server `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(221, 0, 0);">'root'</span></span>`, la sua password `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(221, 0, 0);">'password_db'</span></span>` ed il database di default `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(221, 0, 0);">'test'</span></span>`. Ovviamente dovrete cambiare questi dati con quelli corretti per il vostro server, in particolare il nome e la password dell'utente. Controllate inoltre che il database `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(221, 0, 0);">'test'</span></span>`, comunemente creato durante l'installazione di MySQL, sia presente.

 La funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_connect</span></span>` restituisce, nel caso in cui la connessione abbia successo, un link al server MySQL in questione, che ci servirà successivamente per effettuare altre operazioni, oppure *false* in caso contrario.

 L'istruzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_close</span></span>` serve invece a chiudere la connessione con il server, procedura che comunque sarebbe stata eseguita automaticamente al termine dell'esecuzione dello script.

 Vediamo adesso come utilizzare la classe mysqi per connetterci al server in stile *Object Oriented*:

 ```php
<?php
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// controllo l'esito della connessione
if (mysqli_connect_errno()) {
	echo 'Errore durante la connessione al server MySQL';
	exit();

}
else {
	echo 'Connessione effettuata con successo';
}

// chiudo la connessione
$mysqli->close();
?>
```

 Come anticipato nelle precedenti pagine, i due approcci non si discostano molto l'uno dall'altro. In questo caso si tratta di istanziare un oggetto `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$mysqli</span></span>` dalla classe `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli</span></span>` passando al costruttore gli stessi parametri passati prima alla funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_connect</span></span>` ovvero indirizzo del server, nome utente, password e nome del database di default.

 In questo caso per controllare l'esito della connessione non possiamo testare il valore di `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$mysqli</span></span>` poiché questo, contrariamente a quanto riportato nella documentazione ufficiale, risulta essere un oggetto della classe mysqli indipendentemente dalla riuscita della connessione. Usiamo allora la funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_connect_errno</span></span>` che restituisce, in caso di errore di connessione, un codice relativo.

 Anche in questo caso il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">close</span></span>` è facoltativo.

- - - - - -

####  Invio di una query

 Adesso che abbiamo imparato a connetterci ad un server MySQL possiamo inviargli le nostre query. Iniziamo con una query che ci permette di creare una tabella che ci tornerà utile nei prossimi esempi. Vediamo l'approccio procedurale:

 ```php
<?php
// provo a connettermi
$link = mysqli_connect('localhost', 'root', 'password_db', 'test');

// controllo l'esito della connessione
if (!$link) {
	echo 'Errore durante la connessione al server MySQL';
	exit();
}

// effettuo la query
$result = mysqli_query($link, 'CREATE TABLE IF NOT EXISTS mia_tabella (
								user_id int(11) NOT NULL auto_increment,
								nome varchar(64),
								cognome varchar(64),
								PRIMARY KEY (user_id))');

// controllo l'esito della query
if (!$result) {
	echo 'Errore nella query';
}
else {
	echo 'Query effettuata con successo';
}

// chiudo la connessione
mysqli_close($link);
?>  
```

 Per effettuare la query abbiamo fatto uso della funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_query</span></span>` a cui abbiamo passato come parametri il `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$link</span></span>` al server MySQL e la stringa SQL. Contrariamente a quanto accadeva in passato con l'estensione ext/mysql, **adesso il link al server MySQL non è più un parametro opzionale** ma va necessariamente passato a questa funzione.

 Poiché il tipo di query che abbiamo lanciato non prevede la restituzione di record, cosa che invece avviene ad esempio con una *SELECT*, `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_query</span></span>` restituisce una variabile di tipo booleano che vale *true* in caso di successo, *false* altrimenti.

 Vediamo adesso l'approccio ad oggetti:

 ```php
<?php
// provo a connettermi
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// controllo l'esito della connessione
if (mysqli_connect_errno()) {
	echo 'Errore durante la connessione al server MySQL';
	exit();
}

// effettuo la query di creazione della tabella 'mia_tabella'
$result = $mysqli->query('CREATE TABLE IF NOT EXISTS mia_tabella (
							user_id int(11) NOT NULL auto_increment,
							nome varchar(64),
							cognome varchar(64),
							PRIMARY KEY (user_id))');

// controllo l'esito della query
if (!$result) {
	echo 'Errore nella query';
}
else {
	echo 'Query effettuata con successo';
}

// chiudo la connessione
$mysqli->close();
?>  
```

 Anche questa volta abbiamo istanziato un oggetto dalla classe `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli</span></span>`. Per lanciare la nostra query abbiamo chiamato il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli::query</span></span>` passandogli la stringa SQL. Ovviamente usando gli oggetti non dobbiamo passare il link al server poiché questo è già presente nell'oggetto stesso.

 Come già fatto nell'approccio procedurale, ci serviamo del valore restituito da `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli::query</span></span>` per verificare l'esito della query.

 Per permetterci di fare ulteriori test con il nostro database procediamo con l'inserimento di alcuni dati nella tabella appena creata. Lo faremo utilizzando solamente l'approccio procedurale dato che la versione ad oggetti non introdurrebbe nessuna novità rispetto all'esempio appena fatto.

 ```php
<?php
// provo a connettermi
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// controllo l'esito della connessione
if (mysqli_connect_errno()) {
	echo 'Errore durante la connessione al server MySQL';
	exit();
}

// Lancio una query per inserire tre righe nella tabella
$result = $mysqli->query("INSERT INTO mia_tabella VALUES
						(1,'Mario','Rossi'),
						(2,'Carlo','Bianchi'),
						(3,'Giuseppe','Verdi')");

// controllo l'esito della query
if (!$result) {
	echo 'Errore nella query';
}
else {
	echo 'Dati inseriti correttamente';
}

// chiudo la connessione
$mysqli->close();
?>
```

 Questo esempio va eseguito un'unica volta altrimenti si riceverà un errore che ci avvisa dell'impossibilità di inserire più volte gli stessi dati nella tabella.

- - - - - -

####  Fetch dei risultati

 Le query effettuate nei precedenti esempi servivano a creare una tabella ed a inserirvi alcuni dati al suo interno. Nessuna però prevedeva la restituzione di un set di risultati (result set). Vediamo invece adesso come si effettua una SELECT ovvero una estrazione di dati da una tabella e sopratutto come si recupera (fetch) il set di risultati. Iniziamo con la versione procedurale.

 Nel seguente esempio effettueremo una query per recuperare i dati precedentemente inseriti nella tabella. Questa volta il risultato restituito dalla funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_query</span></span>` non sarà un semplice *true* o *false* a seconda dell'esito della query. Se la query avrà successo ci verrà restituito un oggetto di tipo mysqli\_result che possiamo considerare come una sorta di contenitore delle righe restituite dalla query. L'operazione di estrazione di ogni singola riga da questo contenitore prende il nome di **fetch** e può essere eseguita in vari modi. Possiamo infatti estrarli in una array con indici numerici, oppure in un array associativo, in un array con indici sia numerici che associativi e addirittura nelle proprietà di un oggetto. Vediamo le quattro tecniche.

 ```php
<?php
// provo a connettermi
$link = mysqli_connect('localhost', 'root', 'password_db', 'test',3307);

// effettuo la query
$result = mysqli_query($link, 'SELECT user_id, nome, cognome FROM mia_tabella');

// eseguo la fetch dei risultati
while ($row = mysqli_fetch_row($result))
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row[0], $row[1],$row[2]);
}

// riporto il cursore della riga corrente a 0
mysqli_data_seek($result,0);

// eseguo la fetch dei risultati
while ($row = mysqli_fetch_assoc($result))
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row['user_id'], $row['nome'],$row['cognome']);
}

// riporto il cursore della riga corrente a 0
mysqli_data_seek($result,0);

// eseguo la fetch dei risultati
while ($row = mysqli_fetch_array($result))
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row[0], $row['nome'],$row[2]);
}

// riporto il cursore della riga corrente a 0
mysqli_data_seek($result,0);

// eseguo la fetch dei risultati
while ($row = mysqli_fetch_object($result))
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row->user_id, $row->nome, $row->cognome);
}

// chiudo il set dei risultati per liberare memoria
mysqli_free_result($result);

// chiudo la connessione
mysqli_close($link);
?>
```

 Tutte le istruzioni di fetch necessitano il passaggio del parametro `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$result</span></span>` e restituiscono una riga dei risultati della query. Ad ogni ulteriore chiamata la funzione restituisce la riga seguente fino all'esaurimento delle righe

 Per poter "riavvolgere" il set dei risultati in modo da ripetere l'estrazioni dalla prima riga chiamiamo la funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_data_seek</span></span>` a cui passiamo il set dei risultati da "riavvolgere" (`<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$result</span></span>`) e l'indice del primo risultato (0). Questa operazione è stata eseguita per poter sperimentare i vari tipi di istruzioni fetch e per sottolineare la completa indipendenza della fase di query dalla fase di fetch. Infatti abbiamo effettuato 4 cicli di fetch sui risultati prodotti da un'unica query.

 Una volta che abbiamo terminato le operazioni sul set dei risultati `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$result </span></span>`è opportuno liberare la memoria occupata da questo invocando la funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">mysqli_free_result</span></span>`.

 Ora vediamo come si procede utilizzando un approccio ad oggetti.

 ```php
<?php
// provo a connettermi
$mysqli = new mysqli('localhost', 'root', 'password_db', 'test');

// lancio la query
$result = $mysqli->query('SELECT user_id, nome, cognome FROM mia_tabella');

// eseguo la fetch dei risultati
while ($row = $result->fetch_row())
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row[0], $row[1],$row[2]);
}

// riporto il cursore della riga corrente a 0
$result->data_seek(0);

// eseguo la fetch dei risultati
while ($row = $result->fetch_assoc())
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row['user_id'], $row['nome'],$row['cognome']);
}

// riporto il cursore della riga corrente a 0
$result->data_seek(0);

// eseguo la fetch dei risultati
while ($row = $result->fetch_array())
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row[0], $row['nome'],$row[2]);
}

// riporto il cursore della riga corrente a 0
$result->data_seek(0);

// eseguo la fetch dei risultati
while ($row = $result->fetch_object())
{
	printf ("ID utente:%d, nome:%s, cognome:%s\n",
	$row->user_id, $row->nome, $row->cognome);
}

// chiudo il set dei risultati per liberare memoria
$result->close();

// chiudo la connessione
$mysqli->close();
?>
```

 Nell'esempio appena visto il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$mysqli</span><span style="color: rgb(0, 119, 0);">-></span><span style="color: rgb(0, 0, 187);">query</span></span>` ha restituito l'oggetto `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$result</span></span>`, ovvero il nostro contenitore dei risultati. Similmente a quanto fatto con l'approccio procedurale, grazie ai metodi `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fetch_row</span></span>`, `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fetch_assoc</span></span>`, `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fetch_array</span></span>` e `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">fetch_object</span></span>` abbiamo estratto le singole righe inserendole in array numerati o associativi e nelle proprietà di un oggetto. Con il metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">data_seek</span></span>` abbiamo invece riportato l'indicatore della riga corrente all'inizio. Grazie al metodo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">close</span></span>` dell'oggetto `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$result</span></span>` abbiamo invece liberato la memoria occupata dai risultati.

 E' importante osservare che invocando il metodo query (o lanciando la funzione mysqli\_query) i risultati vengono subito inviati dal server MySQL a PHP che li conserva in un apposito oggetto e ci da la possibilità di estrarli secondo la modalità che preferiamo e sopratutto nell'istante in cui ci sembra più opportuno. Possiamo addirittura effettuare altre query prima di aver estratto tutti i risultati provenienti da operazioni precedenti. Questo comportamento può portare a uno spreco di risorse se i risultati restituiti sono voluminosi. Esiste un modo per risolvere questo problema ma verrà trattato un prossimo articolo.

- - - - - -

####  Conclusioni

 Con questo primo articolo abbiamo introdotto soltanto le funzioni, messe a disposizione di ext/mysqli, che stanno alla base dell'interazione tra PHP e MySQL 4.1+. Abbiamo cercato di sottolineare come il nuovo approccio orientato agli oggetti sia stato concepito come una naturale estensione del tradizionale paradigma procedurale.

 Nel prossimo articolo ci addentreremo nel set di funzioni e metodi che ci fornisce l'estensione ext/mysqli e che ci permettono di avere un maggior controllo sul nostro database MySQL.