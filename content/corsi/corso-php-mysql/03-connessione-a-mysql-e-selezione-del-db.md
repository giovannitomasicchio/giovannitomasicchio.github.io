---
title: "Connessione a MySQL e selezione del DB"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:00:49+0000
description: "Connessione con PHP al server MySQL e selezione del database."
url: /corsi/corso-php-mysql/connessione-a-mysql-e-selezione-del-db/
categories:
  - Corso PHP MySQL
  
---
 Vediamo ora come realizzare un semplice script PHP in grado di connettersi ad un server MySQL. La connessione avviene grazie ala funzione **mysql\_connect()**. I dati necessari alla connessione sono:

- **indirizzo del server**: bisogna specificare su quale computer è in esecuzione il server MySQL. Nella maggior parte dei casi MySQL gira sulla stessa macchina in cui è in funzione il server web (e quindi il PHP) pertanto l'indirizzo da specificare è "localhost". Se invece il server si trova su un diverso computer, raggiungibile attraverso una connessione TCP/IP, allora bisogna specificare il nome di dominio (es. sql.mioserver.it) o l'indirizzo IP (es. 162.54.23.12) della macchina che lo ospita.
- **nome utente e password**: si tratta delle credenziali che permettono al server MySQL di identificare l'utente che effettua la richiesta di connessione. Se abbiamo appena installato MySQL allora possiamo usare il nome utente "root" e per password quella specificata durante l'installazione.
 
 La funzione mysql\_connect() restituisce come risultato un numero che costituisce l'*identificativo di connessione MySQL* ("MySQL link identifier" oppure " MySQL-Link resource") in caso di successo oppure FALSE in caso contrario. E' quindi possibile verificare l'esito della connessione semplicemente controllando il risultato restituito da mysql\_connect().

 Se qualcosa va storto possiamo inoltre ottenere maggiori informazioni sull'accaduto utilizzando un'altra funzione, **mysql\_error()**, che ci mostrerà un messaggio esplicativo sul problema verificatosi.

 Effettuata la connessione a MySQL si è soliti specificare il nome del database su cui si vogliono effettuare le query. Infatti uno script PHP generalmente effettua diverse query ma tutte su tebelle appartenenti ad uno stesso database. Per dichiarare il nome del database si usa la funzione **mysql\_select\_db()** che restituisce TRUE o FALSE a seconda dell'esito dell'operazione. Anche questa volta conviene subito controllare tale risultato ed eventualmente mostrare un messaggio di errore con la funzione mysql\_error().

 Anche se tutte le connessioni ai server MySQL vengono automaticamente chiuse alla fine dell'esecuzione dello script, è buona norma chiudere la connessione esplicitamente, appena lo script ha terminato le operazioni con il database. Per farlo basta richiamare la funzione **mysql\_close()** a cui passiamo come parametro l'identificativo di connessione ottenuto da mysql\_connect().

 Vediamo quindi un semplice esempio di connessione, selezione del DB e chiusura del link:

 ```
<pre class="brush: php">
<?php
$link = mysql_connect('localhost', 'nome_utente', 'password');
if (!$link) {
	die ('Non riesco a connettermi: ' . mysql_error());
}

$db_selected = mysql_select_db('prova', $link);
if (!$db_selected) {
	die ("Errore nella selezione del database: " . mysql_error());
}

echo 'connesso con successo';

mysql_close($link);
?>
```

 Ecco nel dettaglio il funzionamento del precedente script:

- la riga 2 effettua la connessione al server attraverso la funzione mysql\_connect() a cui viene passato l'indirizzo del server ('localhost'), il nome dell'utente di MySQL ('nome\_utente') e la relativa password ('password');
- la riga 3 controlla il valore di $link restituito da mysql\_connect(). Se vale FALSE la riga 4 mostra un messaggio di errore grazie alla funzione mysql\_error() e termina lo script;
- la riga 7 seleziona il database da utilizzare, richiamando la funzione mysql\_select\_db() a cui passiamo il nome del database 'prova' ed il $link che identifica la connessione appena effettuata.
- la riga 8 controlla il valore di $db\_selected restituito da mysql\_select\_db(). Se vale FALSE la riga 9 mostra un messaggio di errore grazie alla funzione mysql\_error() e termina lo script;
- la riga 14 infine chiude la connessione al server MySQL attraverso la funzione mysql\_close().
 
 La connessione va eseguita una sola volta per script, generalmente all'inizio del codice, poiché è necessario che questa preceda l'esecuzione delle query. E' possibile effettuare più connessioni all'interno di uno stesso script ma questo ha senso solo se ci si deve interfacciare con diversi server MySQL, circostanza piuttosto rara.

 Quella vista è la procedura di connessione comunemente impiegata negli script PHP/MySQL. Si possono verificare però condizioni e circostanze che richiedono un uso differente della funzione mysql\_connect(), mostrate di seguito attraverso degli esempi:

- per collegarsi al server MySQL presente sull'host miodominio.it (diverso da localhost) usando una porta differente da quella standard (3306), ad esempio la 3307:
   `<span style="color: rgb(0, 0, 0);">$link = mysql_connect('miodominio.it:3307', 'nome_utente', 'password');</span> `
- per collegarsi al server MySQL presente il localhost usando una porta differente da quella standard (3306), ad esempio la 3307:
   `<span style="color: rgb(0, 0, 0);">$link = mysql_connect('127.0.0.1:3307', 'nome_utente', 'password');</span> `
   Si noti che non è stato usato come host 'localhost:3307' perché PHP avrebbe creato una connessione attraverso socket (named pipe su Windows) invece di usare il TCP/IP e quindi avrebbe ignorato il numero di porta.
- per collegarsi a localhost specificando il percorso del socket:
   `<span style="color: rgb(0, 0, 0);">$link = mysql_connect('/tmp/mysql', 'nome_utente', 'password');</span> `
   oppure:
   `<span style="color: rgb(0, 0, 0);">$link = mysql_connect('localhost:/tmp/mysql.sock', 'nome_utente', 'password');</span> `
 
 Accanto all'indicazione del server, del nome utente e della password, la funzione mysql\_connect() accetta due ulteriori parametri:

 **new\_link**: se in uno script viene chiamata la funzione mysql\_connect() due o più volte con gli stessi parametri, questa restituirà sempre lo stesso identificativo di connessione. Questo perché mysql\_connect() evita di aprire nuovi (inutili) link ad uno stesso server MySQL. Se si vuole disabilitare questo comportamento basta porre il parametro new\_link a TRUE.

 **client\_flags**: una combinazione di flag (MYSQL\_CLIENT\_IGNORE\_SPACE, MYSQL\_CLIENT\_IGNORE\_SPACE, MYSQL\_CLIENT\_INTERACTIVE, MYSQL\_CLIENT\_SSL).

 Per i dettagli si rimanda alla [documentazione ufficiale](http://it2.php.net/manual/it/function.mysql-connect.php).

###  Connessioni persistenti

 La procedura di connessione vista comporta la creazione di un nuovo link tra PHP e MySQL ad ogni esecuzione dello script. Questo collegamento viene interrotto con la funzione mysql\_close() o automaticamente al termine dello script. Alla semplicità di questo approccio si contrappone un'evidente inefficienza: la procedura di connessione, ripetuta ad ogni esecuzione dello script, potrebbe risultare onerosa in termini di prestazioni, soprattutto se PHP e MySQL sono su macchine differenti.

 Per ovviare a questo problema è possibile utilizzare le connessioni persistenti. In pratica, attraverso la funzione **mysql\_pconnect()** che segue la stessa sintassi della mysql\_connect(), si cerca di riciclare vecchie connessioni precedentemente aperte e ancora attive. Il funzionamento è il seguente:

- per prima cosa la funzione mysql\_pconnect() cerca un link persistente ancora attivo, aperto da una precedente chiamata a mysql\_pconnect() effettuata impiegato gli stessi dati per host, username e password;
- se tale link viene trovato allora mysql\_pconnect() non apre una nuova connessione ma restituisce l'identificativo di connessione del vecchio collegamento, ovvero riutilizza la vecchia connessione;
- se non è disponibile un link persistente ancora attivo allora se ne crea uno nuovo;
- infine, al termine dello script, il link non viene chiuso, neanche se viene invocata la funzione mysql\_close().
 
 Come accennato, le connessioni persistenti hanno come unico obbiettivo la riduzione dell'overhead legato alla fase di creazione dei link tra PHP e MySQL. Purtroppo però l'uso delle connessioni persistenti richiede una certa abilità nel configurare il server. Se infatti MySQL è configurato per non aprire più di un certo numero di connessioni contemporaneamente, la richiesta di una ulteriore connessione da parte di PHP non verrà soddisfatta con conseguenze ovvie sul suo funzionamento dello script.

 Per questo, e per altri inconvenienti che si potrebbero verificare, si rimanda alla documentazione di PHP (in particolare a [questa pagina](http://it2.php.net/manual/en/features.persistent-connections.php)) e MySQL.