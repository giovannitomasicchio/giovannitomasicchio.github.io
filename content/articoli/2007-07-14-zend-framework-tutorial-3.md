---
title: "Zend Framework Tutorial - 3"
author: "Giovanni Tomasicchio"
type: article
date: 2007-07-14T09:23:58+0000
description: "Accesso ai dati con lo Zend Framework"
url: /articoli/zend-framework-tutorial-3/
categories:
  - Articoli
tags:
  - "database"
  - "Zend Framework"
  - "MySQL"
  - "exception"
  - "SQL"
  - "UTF-8"

  
---
###  Model

 Poiché tutte le funzionalità del sito che stiamo realizzando fanno accesso ai dati presenti nel database, prima di realizzare i Controller del nostro progetto analizziamo il funzionamento del Model, ovvero della classe responsabile di tutte le operazioni eseguite direttamente sulla base di dati. Per realizzare il Model adotteremo il classico approccio di una applicazione PHP/MySQL basato sull'esecuzione esplicita di query attraverso lo Zend\_Db\_Adapter.

 Il Model per la gestione delle news sarà quindi una classe con un metodo per ciascuna funzionalità fondamentale sui dati della news: getAllNews per lettura di tutte le news (per la home-page), getNewsById per lettura di una singola news (impiegata nelle pagine di visualizzazione e modifica di una singola news), insertUpdateNews per l'inserimento e la modifica e deleteNews per la cancellazione.

 Ricordo che la connessione al database, operazione necessaria quasi a tutte le Action dell'applicazione, è stata già realizzata nel file di bootstrap e l'oggetto Zend\_Db\_Adapter è stato conservato nello Zend\_Registry. Prima di vedere il funzionamento del nostro Model però analizziamo la struttura del database.

- - - - - -

###  Struttura del database

 La nostra applicazione per la gestione delle news necessita di un database veramente semplice, costituito da una sola tabella. I campi in essa contenuti sono: news\_id (la chiave primaria, un intero autoincrement), autore, data (in formato timestamp), titolo, testo\_intro e testo\_completo.

 Per creare il database e la tabella nelle news possiamo eseguire queste 3 semplici query:

 ```sql
create database if not exists `zf-tutorial`;
USE `zf-tutorial`;
CREATE TABLE `news` (
  `news_id` int(11) NOT NULL auto_increment,
  `autore` varchar(255) default NULL,
  `data` int(11) default NULL,
  `titolo` varchar(255) default NULL,
  `testo_intro` text,
  `testo_completo` text,
  PRIMARY KEY  (`news_id`)
) DEFAULT CHARSET=latin1;
```

 Si noti il charset di default impostato a latin1 (ovvero ISO 8859-1), poiché tutta la nostra applicazione è realizzata secondo quel set di caratteri. Nonostante PHP 5 non supporta nativamente l'UTF8, lo Zend Framework è in grado di realizzare siti web in unicode. Anzi alcuni suoi componenti, quelli per l'internazionalizzazione delle applicazioni, sono progettati per lavorare esclusivamente con l'UTF8.

- - - - - -

###  getAllNews

 Di seguito viene riportato il codice del metodo getAllNews() che ha lo scopo di restituire tutte le news registrate nel database in modo da mostrarle nella home page del sito. Il funzionamento è piuttosto semplice. Per prima cosa viene recuperato l'oggetto Zend\_Db\_Adapter con Zend\_Registry::get(). L'esecuzione della query ed il recupero di tutti i risultati ottenuti viene eseguito con un solo comando: $db-&gt;fetchAll(). Questo metodo restituisce un array di array associativi, composti da coppie nome\_campo=&gt;valore. Per una visualizzazione più "umana" dei timestamp delle date viene poi eseguita una conversione con la funzione date(). Si noti che per rendere le conversioni permanenti il ciclo foreach viene eseguito per riferimento (&amp;$row).

 ```php
public function getAllNews()
{
    $db = Zend_Registry::get('db');
    $sql = 'SELECT * FROM news ORDER BY data DESC';
    $rows = $db->fetchAll($sql);

    foreach ($rows as &$row) {
        $row['data'] = date('j/m/y - G:i',$row['data']);
    }

    return $rows;
}
```

 Lo Zend Framework mette a disposizione un sofisticato componente per la gestione delle date, lo Zend\_Date, ma come precedentemente accennato, questo è uno dei componenti che richiede l'utilizzo del set di caratteri UTF-8, pertanto il suo impiego avrebbe aggiunto ulteriore complessità al tutorial.

- - - - - -

###  getNewsById

 Questo metodo è molto simile al precedente. Si occupa di estrarre tutti i dati di una singola news. L'estrazione della particolare news avviene grazie al suo ID passato al metodo attraverso la variabile $newsId. Anche questa volta, recuperato l'oggetto Zend\_Db\_Adapter dallo Zend\_Registry, si procede all'esecuzione della query ed alla lettura del record attraverso un'unica istruzione: $db-&gt;fetchRow($sql). Questa restituisce un array associativo composto da coppie nome\_campo=&gt;valore. Doverosa infine la conversione del timestamp della data con l'istruzione date();

 ```php
public function getNewsById($newsId)
{
    $db = Zend_Registry::get('db');
    $sql = 'SELECT * FROM news WHERE news_id = ' . (int)$newsId;
    $row = $db->fetchRow($sql);

    if($row) {
        $row['data'] = date('j/m/y - G:i',$row['data']);
    }

    return $row;
}
```

 Si noti come l'inserimento dell'id $newsId all'interno dell'SQL della query è stato eseguito semplicemente accodando due stringhe. L'unico accorgimento preso per evitare l'SQL injection è costituito dal casting ad intero (int) della stringa $newsId. Se invece avessimo dovuto comporre dinamicamente l'SQL, accodando una stringa al posto di un numero intero allora avremmo dovuto utilizzare i metodi quote o quoteInto:

 ```php
$titolo_news = $db->quote("Notizia dell'anno");
echo $titolo_news; // 'Notizia dell\'anno'

$sql = "SELECT * FROM news WHERE titolo = $titolo_news";
echo $sql; // SELECT * FROM news WHERE titolo = 'Notizia dell\'anno'
```

 oppure più semplicemente:

 ```php
$sql = $db->quoteInto("SELECT * FROM news WHERE titolo = ?", "Notizia dell'anno");
echo $sql; // SELECT * FROM news WHERE titolo = 'Notizia dell\'anno'
```

 Si noti il punto interrogativo utilizzato come segna posto per la sostituzione della stringa.

- - - - - -

###  insertUpdateNews

 Questo metodo si occupa sia dell'inserimento di una nuova news nel database, sia dell'aggiornamento di una news precedentemente creata. Nonostante le poche righe di codice che lo compongono, diverse sono le osservazioni che si possono fare. Per prima cosa si può notare che se si verifica l'assenza di uno dei dati obbligatori che costituisce la news ($autore, $titolo, $testoIntro) il metodo solleva un'eccezione di tipo Zend\_Exception, passando un messaggio esplicativo. L'utilità di questo approccio risulterà più evidente quando vedremo il funzionamento del Controller che si occupa di inserire e modificare le news. Per adesso ci basta sapere che se viene sollevata una eccezione l'esecuzione del metodo viene interrotta e il flusso del programma ritorna al chiamante (il Controller) il quale provvede a mostrare il messaggio di errore all'utente.

 Se invece tutti i controlli vanno a buon fine viene costruito un array associativo $dati contenente le coppie nome\_campo=&gt;valore che stanno per essere inserite nel database. Per capire se si tratta di un inserimento o un aggiornamento viene verificato il valore del parametro $newsId.

 Se presente si tratta di un aggiornamento, quindi viene richiamato il metodo $db-&gt;update() che accetta 3 parametri: il nome della tabella nella quale effettuare l'update, i dati da aggiornare e una stringa contenente la clausola WHERE dell'SQL per individuare le righe che saranno coinvolte dall'aggiornamento. Nel nostro caso vogliamo aggiornare solo il record con news\_id pari a $newsId.

 Se invece si tratta di un inserimento allora viene richiamato il metodo $db-&gt;insert() a cui viene passato il nome della tabella nella quale effettuare l'inserimento ed i dati da inserire.

 ```php
public function insertUpdateNews($newsId, $autore, $titolo, $testoIntro, $testoCompleto)
{
    $db = Zend_Registry::get('db');

    $autore     = trim($autore);
    $titolo     = trim($titolo);
    $testoIntro = trim($testoIntro);

    if(!$autore) {
        throw new Zend_Exception("Non hai inserito il nome dell'autore");
    }
    if(!$titolo) {
        throw new Zend_Exception("Non hai inserito il titolo");
    }
    if(!$testoIntro) {
        throw new Zend_Exception("Il testo introduttivo è obbligatorio");
    }

    $dati = array(
    'autore'         => $autore,
    'data'           => time(),
    'titolo'         => $titolo,
    'testo_intro'    => $testoIntro,
    'testo_completo' => $testoCompleto);

    if($newsId) {
        $where = "news_id = " . (int)$newsId;
        return $db->update('news', $dati, $where);
    } else {
        return $db->insert('news', $dati);
    }
}
```

- - - - - -

###  deleteNews

 L'ultimo metodo della classe News è quello che permette la cancellazione di una news dal database. Il suo funzionamento è davvero semplice, si basa sul comando $db-&gt;delete() al quale viene passato il nome della tabella nella quale effettuare la cancellazione delle righe e una stringa contenente la clausola WHERE dell'SQL per individuare le righe da cancellare.

 Anche in questo caso la protezione dall'SQL Injection avviene con un semplice cast ad intero di $newsId ma vale il discorso precedentemente fatto sull'utilizzo dei metodi quote() e quoteInto().

 ```php
public function deleteNews($newsId)
{
    $db = Zend_Registry::get('db');
    $where = "news_id = " . (int)$newsId;
    return $db->delete('news', $where);
}
```