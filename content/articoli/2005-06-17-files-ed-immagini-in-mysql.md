---
title: "Files ed immagini in MySQL"
author: "Giovanni Tomasicchio"
type: article
date: 2005-06-17T23:09:48+0000
description: "Memorizzare file e immagini in MySQL con PHP"
url: /articoli/files-ed-immagini-in-mysql/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "BLOB"
  - "FTP"
  - "mysqli"
  - "upload"

  
---
 Spesso si ha la necessità di realizzare un sito che permetta all'utente di scaricare dei files. Altre volte invece bisogna realizzare delle gallerie di immagini. Nulla di particolarmente complicato, basta preparare delle pagine con dei classici links ai files in questione che avremo salvato sul server, magari in una apposita cartella, ed il gioco è fatto. E se volessimo rendere queste risorse accessibili solo a determinati utenti? In questo articolo vedremo come poter risolvere questo problema attraverso la **memorizzazione dei files all'interno di MySQL**.

Se infatti i files sono conservati nel database, sarà l'applicazione PHP a verificare se l'utente ha i diritti necessari per accedervi e l'utente non potrà bypassare in alcun modo questi controlli poiché sarà il PHP a fornirgli i files che richiede. Le possibilità sono innumerevoli, dalla creazione di complessi sistemi di distribuzione file alla realizzazione di tecniche anti-leach che eviteranno la creazione di links diretti di altri siti alle vostre risorse.

 Per illustrare le linee guida da seguire nella creazione di un sistema di memorizzazione e recupero di files da MySQL realizzeremo tre semplici script. Il primo avrà il compito di prelevare un file inviato da un utente e di inserirlo in MySQL. Il secondo script genererà un elenco dei files presenti all'interno del database. Cliccando su uno di questi link verrà lanciato il terzo script, che si occuperà dell'estrazione del file selezionato e del suo invio al browser.

 Per ragioni didattiche gli script proposti contengono le sole istruzione necessarie al raggiungimento nei vari scopi. Non possono essere quindi considerati pronti per l'utilizzo in un sito web di produzione, ma costituiscono sicuramente un buon punto di partenza per coloro che vorranno implementare tali tecniche.

- - - - - -

####  Creazione della tabella dei files

 Per prima cosa dovremo creare una tabella all'interno del nostro database MySQL che conterrà i files. Questa tabella sarà costituita dai seguenti campi:

 Campo Tipo
\--------- --------------
 id\_file int(11) chiave primaria (autoincrement)
 nome\_file varchar(255)
 tipo\_file varchar(128)
 dati\_file blob

 Vediamo il significato e l'utilità dei diversi campi:

- **id\_file**
   si tratta di un numero progressivo (ID) che ci servirà per identificare univocamente un file nel database.
- **nome\_file**
   conterrà il nome del file che va memorizzato esplicitamente.
- **tipo\_file**
   con questo campo teniamo nota del tipo di file. Memorizzeremo una stringa (tipo MIME) che ci verrà fornita dal browser durante l'upload del file e che ci tornerà utile quando dovremo reinviare il file all'utente.
- **dati\_file**
   questo campo conterrà il file vero e proprio, ovvero tutti i byte che lo costituiscono.
 
 Di particolare interessa è il campo dati\_file di tipo **blob**. Il tipo blob (**b**inary **l**arge **ob**ject) è proprio quello che MySQL mette a disposizione per la memorizzazione di grosse quantità di dati binari ed è quindi adatto a conservare un intero file. In realtà ci sono diversi tipi blob, ciascuno caratterizzato da un valore massimo di byte che può contenere:

 | **Nome campo** | **Massima capacità** |
|---|---|
| TINYBLOB | 256 Byte |
| BLOB | 64 KByte |
| MEDIUMBLOB | 16 MByte |
| LONGBLOB | 4 GByte |

 Per creare la tabella possiamo eseguire la seguente query (ad esempio usando phpMyAdmin o direttamente con il MySQL Command Line Client):

 ```sql
CREATE TABLE tabella_files ( 
	id_file int(11) NOT NULL auto_increment, 
	nome_file varchar(255) default NULL, 
	tipo_file varchar(128) default NULL, 
	dati_file blob, 
	PRIMARY KEY (`id_file`) 
);
```

- - - - - -

####  Upload e inserimento in MySQL

 Il seguente script si occupa di inserire all'interno della tabella appena creata un file inviato dall'utente attraverso un form. Il principio di funzionamento è abbastanza semplice: la prima volta che viene eseguito lo script, viene chiamata la funzione`<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);"> mostra_form</span></span>` che si occupa di preparare il modulo per l'upload del file. All'invio del file si procede con il recupero del suo contenuto e con l'inserimento nel database.

 Non ci soffermiamo sui dettagli della procedura di upload del file, che non rientra nello scopo di questo articolo. Si noti solamente come sia possibile ricavare dall'array `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$_FILES</span></span>` alcune informazioni sul file ricevuto, in particolare il suo tipo MIME. Si tratta di una stringa che lo stesso browser invia insieme al file e che ne specifica il tipo. A esempio per una immagine in formato GIF questa stringa sarà "image/gif". Per un elenco dei diversi tipi MIME è possibile consultare [questa pagina](http://www.utoronto.ca/webdocs/HTMLdocs/Book/Book-3ed/appb/mimetype.html).

 ```php
<?php 
// se è stato inviato il file...
if(isset($_POST['invia']))
{
	// se ci sono stati problemi nell'upload del file
	if(!isset($_FILES['file_inviato']) OR $_FILES['file_inviato']['error'] != UPLOAD_ERR_OK)
	mostra_form("errore nell'invio del file. Riprova");

	// connessione e selezione del database
	mysql_connect('localhost', 'utente_db', 'password_db')
	or die('Connessione non riuscita: ' . mysql_error());

	if(!mysql_select_db('nome_database'))
	die('Selezione database fallita!');

	// recupero alcune informazioni sul file inviato
	$nome_file_temporaneo = $_FILES['file_inviato']['tmp_name'];
	$nome_file_vero = $_FILES['file_inviato']['name'];
	$tipo_file = $_FILES['file_inviato']['type'];

	// leggo il contenuto del file
	$dati_file = file_get_contents($nome_file_temporaneo);

	// preparo il contenuto del file per la query
	$dati_file = addslashes($dati_file);

	// query per inserire il file nel DB
	$query = "INSERT INTO tabella_files SET
				nome_file = '$nome_file_vero', 
				tipo_file = '$tipo_file', 
				dati_file = '$dati_file'"; 

	mysql_query($query)
	OR die('Query non valida: ' . mysql_error());

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
	?> 
	 
	<head> 
	<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1"> 
	<title>Carica file nel database</title> 
	</head> 
	 
	<p><?php echo $messaggio?> <br /> Seleziona un file da memorizzare nel database: <p></p> 
	<form name="form1" enctype="multipart/form-data" method="post" action=""> 
	<p> 
	<input type="file" name="file_inviato"> 
	<p></p> 
	<p> 
	<input type="submit" name="invia" value="Invia file"> 
	<p></p> 
	</form> 
	 
	 
	<?php 
	exit();
}
?>
```

 Questo script non necessita di molti commenti, già presenti all'interno del codice. Si noti come l'inserimento di un file in MySQL si effettua attraverso una comune query INSERT.

- - - - - -

####  Elenco dei files

 Per permettere agli utenti di poter prelevare i files conservati nel database dobbiamo mostrarne un elenco. Niente di più semplice: il seguente script, attraverso una comune SELECT, recupera i nomi e gli ID dei files disponibili e costruisce dei links.

 Questi links ovviamente non punteranno ai files, poiché questi non sono accessibili direttamente. I collegamenti saranno invece del tipo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(221, 0, 0);">mostra.php?id=32</span></span>` e punteranno quindi ad un ulteriore script che si occuperà dell'invio dei files all'utente, e che realizzeremo nella prossima pagina.

 ```php
<head> 
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1"> 
<title>Carica file nel database</title> 
</head> 
 
<p><b>Clicca su uno dei seguenti file</b><p><p></p> 
<?php 
// connessione e selezione del database
mysql_connect('localhost', 'utente_db', 'password_db')
or die('Connessione non riuscita: ' . mysql_error());

if(!mysql_select_db('nome_database'))
die('Selezione database fallita!');

// query per ottenere l'elenco dei files nel DB
$query = "SELECT * FROM tabella_files";

$risultato = mysql_query($query)
or die('Query non valida: ' . mysql_error());

// se ci sono files nel DB
if(mysql_numrows($risultato))
{
	// estrazione dei risultati e stampa dei links ai files
	while ($tmp = mysql_fetch_array($risultato))
	{
		echo "<p><a href=\"mostra.php?id=$tmp[id_file]\">$tmp[nome_file]</a></p>\n";
	}
}
else
{
	echo '<p>Nessun file presente nel database</p>';
}
?>  
```

- - - - - -

####  Estrazione del file

 Come abbiamo accennato precedentemente, l'utente richiederà un file cliccando su un link del tipo `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 0);"><span style="color: rgb(221, 0, 0);">mostra.php?id=32</span></span></span>`, ovvero passerà ad un ulteriore script (mostra.php) l'ID del file di interesse. Il codice di seguito riportato si occupa proprio di gestire queste richieste. Niente di complicato: una volta recuperato l'ID del file in questione si tratta di effettuare una SELECT per estrarre il file e inviarne il risultato al browser.

 Per permettere al browser di maneggiare correttamente il file dobbiamo far uso di una intestazione (header) contenente il corretto tipo MIME associato a tale file. Questa informazione è presente nel campo tipo\_file della nostra tabella.

 **mostra.php**

 ```php
<?php 
// connessione e selezione del database
mysql_connect('localhost', 'utente_db', 'password_db')
or die('Connessione non riuscita: ' . mysql_error());

if(!mysql_select_db('nome_database'))
die('Selezione database fallita!');

// query per recuperare il file
$query = 'SELECT * FROM tabella_files WHERE id_file = '.$_GET['id'];
$risultato = mysql_query($query) or die('Query non valida: ' . mysql_error());
$tmp = mysql_fetch_array($risultato);

// invio una intestazione contenente il tipo MIME
header('Content-Type: '.$tmp['tipo_file']);

// invio il contenuto del file
echo $tmp['dati_file'];
?>  
```

 Il funzionamento dello script si basa su 3 semplici passaggi: la query per il recupero del file il cui ID è conservato in `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$_GET</span><span style="color: rgb(0, 119, 0);">[</span><span style="color: rgb(221, 0, 0);">'id'</span><span style="color: rgb(0, 119, 0);">]</span></span></span>`, l'invio dell'intestazione contenente il tipo MIME attraverso l'uso della funzione `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">header</span></span></span>`, l'invio del file al browser con una semplice `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 119, 0);">echo</span></span></span>`.

 Nel caso in cui vogliamo che il browser non apra il file ma che proponga all'utente di salvarlo sul PC, ovvero che venga effettuato il download, allora al posto dell'intestazione:

 `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">header</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(221, 0, 0);">'Content-Type: '</span><span style="color: rgb(0, 119, 0);">.</span><span style="color: rgb(0, 0, 187);">$tmp</span><span style="color: rgb(0, 119, 0);">[</span><span style="color: rgb(221, 0, 0);">'tipo_file'</span><span style="color: rgb(0, 119, 0);">]);</span></span></span>`

 dovremo inviare le seguenti:

 `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">header</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(221, 0, 0);">"Content-Type: application/octet-stream"</span><span style="color: rgb(0, 119, 0);">);<br></br>	</span><span style="color: rgb(0, 0, 187);">header</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(221, 0, 0);">"Content-Disposition: attachment; filename=\"$tmp[nome_file]\""</span><span style="color: rgb(0, 119, 0);">);</span></span>`

- - - - - -

####  Alcune considerazioni

 Diverse sono le problematiche da considerare quando si realizza un sistema di memorizzazione files in un database. Se infatti le potenzialità insite in questa tecnica sono molteplici e limitate solo dalla fantasia dello sviluppatore, d'altro canto tali procedure non sono prive di inconvenienti.

 Se ad esempio si lavora con grossi files la procedura di upload e memorizzazione potrebbe diventare estremamente onerosa e talvolta impossibile. Le cause potrebbero essere diverse e le variabili in gioco molteplici. Per convincersene basta pensare a quali e quanti siano gli elementi coinvolti in questa procedura:

- browser dell'utente
- rete internet
- server web
- php
- mysql
 
 Oltre ad una adeguata configurazione del server web, di PHP (direttiva *upload\_max\_filesize* del php.ini) e MySQL (*max\_allowed\_packet*) , si potrebbe pensare di evitare la procedura di upload attraverso il browser e di portare i files direttamente sul server, magari via FTP, per poi caricarne il contenuto all'interno del database.

 Poiché nella creazione della tabella destinata alla memorizzazione dei file abbiamo utilizzato un campo blob, la massima dimensione che questi possono avere è 64KByte. Per inserire file più grandi basta modificare il tipo di campo, magari in MEDIUMBLOG o LONGBLOB.

 Sempre a causa delle dimensioni, la procedura proposta per l'inserimento del file in MySQL potrebbe risultare inefficiente. Questa infatti prevede la lettura completa del file che viene riversato in una variabile, inserita nella query ed inviata al database. E' possibile risolvere questa inefficiente gestione della memoria solo con l'utilizzo delle funzioni messe a disposizione della nuova estensione *ext/mysqli*. In particolare il metodo [send\_long\_data](http://www.php.net/manual/it/function.mysqli-send-long-data.php) della classe mysqli\_stmt, insieme all'istruzione fread per la lettura del file, permetterebbero una processazione del file a blocchi di byte. Per maggiori informazioni potete consultare [questa pagina](/articoli/estensione-mysqli-iii/4/).

 Infine occorre considerare che la conservazione di files all'interno di MySQL complica le normali operazioni di back-up e ripristino del database, costringendoci a maneggiare archivi di grosse dimensioni.