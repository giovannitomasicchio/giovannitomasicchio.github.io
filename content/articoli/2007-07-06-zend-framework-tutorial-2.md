---
title: "Zend Framework Tutorial - 2"
author: "Giovanni Tomasicchio"
type: article
date: 2007-07-06T23:02:11+0000
description: "Struttura di una applicazione realizzata con lo Zend Framework"
url: /articoli/zend-framework-tutorial-2/
categories:
  - Articoli
tags:
  - "database"
  - "Zend Framework"
  - "MySQL"
  - "CSS"
  - "exception"
  - "mod_rewrite"
  - "PDO"

  
---
###  Struttura del progetto

 Nel precedente articolo abbiamo cominciato a vedere quali sono gli elementi che costituiscono la nostra applicazione web realizzata con lo Zend Framework. Abbiamo detto che sono necessarie delle classi di tipo Controller per ciascun gruppo di funzionalità correlate, un Controller per gestire la home page (IndexController) ed un altro per la pagina di errore (ErrorController). Abbiamo detto poi che avremmo utilizzato diversi file per conservare i template HTML e uno o più classi Model per effettuare l'accesso ai dati. Infine abbiamo visto come il processo di generazione di una qualunque pagina parte da un unico file, il file di bootstrap, che nel nostro esempio si chiama index.php e che contiene un oggetto Front Controller in grado di smistare tutte le richieste dell'utente al Controller adeguato.

 Vediamo adesso come questi file sono organizzati nell'alberatura delle cartelle del progetto. Supponiamo di conservare tutti i file del progetto in una cartella "ZF-tutorial" all'interno della document root di Apache (la cartella "htdocs"). Per sfruttare alcuni comodi automatismi dello Zend Framework è conveniente organizzare i file contenenti le classi Controller ed i template HTML secondo un preciso schema. In questo modo ridurremo al minimo lo sforzo di specificare i percorsi dei file.

 Secondo lo schema proposto dallo Zend Framework ciascuna classe Controller va salvata in un file dedicato. La classe deve chiamarsi secondo lo schema *NomeClasse*Controller ed il file che la contiene deve avere lo stesso nome. Ad esempio il Controller che gestisce le funzionalità associate alle news (visualizzazione, inserimento, modifica e cancellazione) si chiamerà NewsController e verrà salvato nel file NewsController.php. Tutti i Controller devono essere posti in una cartella, che nel nostro esempio si chiama "controllers" (che fantasia!).

 I file contenenti i template HTML devono essere invece conservati in una cartella di nome "views", situata allo stesso livello della cartella dei Controller. La cartella "views" deve contenere una cartella "scripts" che a sua volta deve contenere una cartella per ciascun Controller, nella quale inserire solo i template che verranno utilizzati da quel particolare Controller. Queste cartelle devono avere lo stesso nome del Controller, con sole lettere minuscole e senza la desinenza "Controller". I file dei template devono avere l'estensione phtml e, se hanno lo stesso nome di una Action del Controller, allora verranno caricati in automatico all'esecuzione di quella particolare Action. Riprenderemo questo discorso quando commenteremo il codice dei Controller realizzati per la nostra applicazione.

 L'immagine seguente mostra l'organizzazione dei file appena descritta.

 ![cartelle.gif](/image/articoli/cartelle.gif)

 In figura si notano quindi:

- i tre file contenenti i tre Controller (ErrorController, IndexController e NewsController)
- la cartella dei template "views", contenente la cartella "scripts" che a sua volta contiene: una cartella per i template usati dall'ErrorController (error.phtml), una per quelli usati dall'IndexController (index.phtml) ed una per quelli usati dal NewsController (news-form.phtml e view.phtml)
- il file di bootstrap index.php
 
 Oltre a quelli precedentemente citati, in figura sono presenti altri file:

- NewsModel.php, l'unico Model della nostra applicazione, che contiene la classe responsabile della gestione dei dati.
- main.css che è un semplice file CSS, richiamato in tutte le pagine del sito
- header.phtml e foother.phtml sono 2 template HTML che vengono inclusi da tutti gli altri template dell'applicazione e che contengono rispettivamente l'intestazione ed il pie' di pagina comuni a tutte le pagine del nostro sito.
- .htaccess, file che ci permette di configurare alcune opzioni di Apache e PHP direttamente dalla cartella della nostra applicazione, che introdurremo nella prossima pagina.
 
 E' doveroso tener presente che l'aver posto le cartelle dei Controller, dei Model e dei template all'interno della root di Apache in realtà non è la migliore scelta possibile, poiché così facendo diamo la possibilità agli utenti di accedere direttamente ad ogni singolo file. Sarebbe stato meglio mettere queste cartelle al di fuori della document root del server web. La scelta fatta in questo tutorial è dettata solo da scopi didattici. Ricordo infine che questa organizzazione dei file, come tutte le caratteristiche dello Zend Framework, può essere completamente personalizzata, perdendo semmai qualche automatismo.

- - - - - -

###  Il file .htaccess

 Nel precedente articolo avevamo detto che tutte le richieste effettuate dall'utente, tutti gli URL della nostra applicazione, vengono in realtà intercettati da un unica pagina di nome index.php, il file di bootstrap. Per ottenere questo risultato è necessario configurare opportunamente il server web, nel nostro caso Apache, ed in questo tutorial utilizzeremo un file .htaccess contenete le seguenti direttive:

 ```php
RewriteEngine on
RewriteRule !\.(js|ico|gif|jpg|png|css)$ index.php

php_flag register_globals off
php_flag magic_quotes_gpc off
```

 Con le prime 2 righe attiviamo il motore di conversione degli URL di Apache e lo configuriamo affinché gli URL che puntano a file con estensione diverse da js, ico, gif, jpg, png o css vengano tutti diretti alla pagina index.php. Ricordo che per utilizzare questa funzionalità è necessario attivare l'estensione mod\_rewrite dal file http.conf e inoltre è necessario abilitare l'utilizzo dei file .htaccess, come descritto in [questa pagina](/articoli/installare-apache-22-php-52-mysql-5-su-windows/7/).

 La terza e la quarta riga del file .htaccess invece non sono direttamente collegata all'utilizzo dello Zend Framework ma servono a disabilitare due funzionalità di PHP che possono comportare diversi problemi e malfunzionamenti della nostra applicazione: il register global e il magic quote.

- - - - - -

###  Il file di bootstrap

 Finalmente possiamo iniziare ad analizzare il codice vero e proprio della nostra applicazione. Partiamo dal file index.php, il file di bootstrap al quale arrivano tutte le richieste dell'utente. Questo file ha un compito molto importante: attivare il Front Controller, il sistema di smistamento delle richieste verso gli appositi Controller e relative Action. Poiché il file di bootstrap viene richiamato sempre e per primo, lo utilizzeremo anche per mettere le istruzioni comuni a tutta l'applicazione. Nell'esempio infatti attiveremo una connessione al database utilizzata (indirettamente) da quasi tutte le Action del nostro sito.

 Ecco il codice:

 ```php
<?php
// includo la cartella del framework e quella dei models
set_include_path(get_include_path().PATH_SEPARATOR.'C:\Programmi\ZendFramework\library'.PATH_SEPARATOR.'.\models');

require_once('Zend/Loader.php');

Zend_Loader::registerAutoload();

date_default_timezone_set('Europe/Rome');

try {
    $db = Zend_Db::factory('Pdo_Mysql', array(
    'host'     => 'localhost',
    'username' => 'root',
    'password' => 'secret',
    'dbname'   => 'ZF-tutorial'
    ));

    $db->getConnection();
} catch (Zend_Db_Adapter_Exception $e) {
    die("Zend_Db_Adapter_Exception: ".$e->getMessage());
} catch (Zend_Exception $e) {
    die("Zend_Exception".$e->getMessage());
}

Zend_Registry::set('db', $db);

define('BASE_URL', str_replace('index.php','',$_SERVER['PHP_SELF']));
Zend_Controller_Front::run('controllers');
?>
```

 Analizziamo ciascuna riga del file:

- Con set\_include\_path() rendiamo accessibile alla nostra applicazione sia le classi dello Zend Framework, sia le classi contenute nella cartella models della nostra applicazione. Difficilmente infatti possiamo metter mano al php.ini e modificare l'include\_path.
- Con require\_once ci preoccupiamo di caricare la classe Zend\_Loader, un comodo componente del Framework che si occupa di effettuare automaticamente le inclusioni dei file contenenti le classi che richiameremo nel resto dell'applicazione. Da questo momento in poi non abbiamo più bisogno di utilizzare le istruzioni include o require!
- Con Zend\_Loader::registerAutoload abilitiamo la suddetta funzionalità (nessuna magia, si basa sulla funzione \_\_autoload di PHP).
- Con date\_default\_timezone\_set() impostiamo il valore di default del timezone che verrà utilizzato implicitamente da tutte le funzioni sulle date impiegate nel codice.
- All'interno di un blocco try-catch effettuiamo la creazione di un oggetto Zend\_Db\_Adapter, responsabile della comunicazione con un DB. Tale operazione avviene grazie al metodo statico Zend\_Db::factory al quale passiamo i parametri di connessione (modificateli per adattarli al vostro sistema). Notate come viene specificato anche il tipo di Adapter che vogliamo usare per collegarsi al database, nel nostro caso 'Pdo\_Mysql'. Zend\_Db::factory ci restituirà quindi un oggetto di tipo Zend\_Db\_Adapter\_Pdo\_Mysql che al suo interno usa l'estensione PDO per collegarsi ad un database MySQL.
- $db-&gt;getConnection() effettua la vera connessione al database.
- Se qualcosa va storto Zend\_Db solleverà un'eccezione, gestita dalle righe sottostanti. Un'eccezione del tipo Zend\_Db\_Adapter\_Exception viene sollevata se la connessione al database non ha successo (username o password errati, database irraggiungibile o spento, ecc.). Altre eccezioni possono essere sollevate, ad esempio se il framework non riesce a caricare l'Adapter richiesto. Ecco il perché abbiamo posto un secondo blocco catch che intercetta la generica Zend\_Exception.
- Per rendere l'oggetto Zend\_Db\_Adapter accessibile al resto dell'applicazione, evitando di usare l'accesso alle variabili globali, utilizziamo il componente Zend\_Registry che si comporta come un "cassetto" nel quale conservare oggetti (Zend\_Registry::set) e recuperarli all'occorrenza (Zend\_Registry::get).
- Definiamo la costante BASE\_URL per memorizzare l'URL di origine della nostra applicazione web, che impiegheremo della costruzione dei template.
- Infine attiviamo il Front Controller, specificando il nome della cartella contenente i Controller della nostra applicazione. A questo punto il framework istanzia il Controller adeguato ed esegue una sua Action che si occuperà di creare la pagina relativa all'URL richiesto dall'utente.
 
 Nel file di bootstrap abbiamo quindi incontrato i seguenti componenti del framework:

- Zend\_Loader: responsabile del caricamento automatico "al volo" dei file contenenti le classi utilizzate nell'applicazione.
- Zend\_Db: costruisce gli oggetti Zend\_Db\_Adapter che ci permettono di accedere ai database
- Zend\_Db\_Adapter\_Exception e Zend\_Exception: sono 2 oggetti eccezione, il primo generato da un Zend\_Db\_Adapter mentre il secondo è la generica eccezione generabile dal Framework.
- Zend\_Registry: contenitore in grado di conservare e restituire oggetti, in modo da facilitare l'accesso degli stessi da qualsiasi parte dell'applicazione, senza che si faccia diretto accesso alle variabili globali
- Zend\_Controller\_Front: il Front Controller descritto nel precedente articolo. Si occupa, insieme ad altri componenti del Framework che lavorano dietro le quinte, di leggere l'URL richiesto dall'utente ed identificare Controller e Action che soddisferanno tale richiesta.