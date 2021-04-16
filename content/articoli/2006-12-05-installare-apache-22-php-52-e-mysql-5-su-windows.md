---
title: "Installare Apache 2.2, PHP 5.2 e MySQL 5 su Windows"
author: "Giovanni Tomasicchio"
type: article
date: 2006-12-05T08:45:52+0000
description: "Guida all'installazione di Apache 2.2, PHP 5.2 e MySQL 5 su Windows XP"
url: /articoli/installare-apache-22-php-52-e-mysql-5-su-windows/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "Apache"
  - "sicurezza"
  - "email"
  - "mysqli"
  - "OOP"

  
---
###  Introduzione

 In questo tutorial mostreremo in dettaglio le procedure di installazione e configurazione di Apache 2.2, PHP 5.2 e MySQL 5 su Windows 2000/XP.

 Il nostro scopo principale è quello di ottenere una piattaforma di sviluppo per poter realizzare e testare le nostre applicazioni PHP-MySQL.

 Abbiamo preso in considerazione le versioni più recenti dei vari software così da poterne sperimentare le caratteristiche e novità, alcune delle quali saranno evidenti già in fase di installazione.

 La procedura verrà suddivisa nelle seguenti fasi:

- Installazione di Apache 2.2.3
- Installazione di PHP 5.2.0
- Installazione di MySQL 5.0
- Configurazione e test di PHP e MySQL con l'estensione mysqli e mysql
- Configurazione di apache per l'utilizzo dei file .htaccess e dei Server Side Includes
 
 **ATTENZIONE** Dall'esperienza di diversi utenti che hanno seguito questo tutorial è emerso che i problemi che più frequentemente si incontrano sono dovuti principalmente a precedenti installazioni dei suddetti software ancora presenti nel sistema. Quindi è estremamente consigliato verificare, prima di procedere, che sul vostro PC non siano presenti files o cartelle appartenenti a vecchie versioni di Apache, PHP e MySQL.

- - - - - -

###  Installare Apache 2.2

 Anche se Windows 2000 ed alcune versioni di Windows XP posseggono al loro interno un web server (Internet Information Services o più brevemente IIS) è preferibile, nello sviluppo di applicazioni PHP, l'utilizzo del server Apache. Infatti Apache non è solo un valido web server ed in molte situazioni superiore ad IIS, ma è anche il web server che più comunemente ci verrà messo a disposizione dai fornitori di servizi di hosting.

 L'installazione di Apache 2.2 è molto semplice, per prima cosa dobbiamo scaricare dal [sito ufficiale](http://httpd.apache.org/download.cgi) il file di installazione denominato "Win32 Binary (MSI Installer)". Nel momento in cui scriviamo, il nome del file associato è apache\_2.2.3-win32-x86-no\_ssl.msi

 Effettuato il download lanciamo l'installer che ci presenterà la seguente schermata:

 ![apache001.png](/image/articoli/WAMP/apache001.png)

 Dopo aver premuto il tasto Next ci verrà chiesto di accettare la licenza:

 ![apache002.png](/image/articoli/WAMP/apache002.png)

 Accettata la licenza ci verranno mostrate altre informazioni sul software che andiamo ad installare:

 ![apache003.png](/image/articoli/WAMP/apache003.png)

 Procedendo con l'installazione ci verrà chiesto di fornire alcune informazioni circa il dominio di rete, il nome del server ed un indirizzo email dell'amministratore. Poiché stiamo configurando un server di sviluppo, possiamo rispondere in maniera fantasiosa, senza compromettere il risultato finale.

 ![apache004.png](/image/articoli/WAMP/apache004.png)

 La prossima schermata ci chiederà di indicare il tipo di installazione che intendiamo fare: optiamo per una installazione tipica.

 ![apache005.png](/image/articoli/WAMP/apache005.png)

 Adesso ci viene data la possibilità di scegliere in quale cartella installare Apache. Accettiamo quella che ci viene proposta.

 ![apache008.png](/image/articoli/WAMP/apache008.png)

 Prima di procedere con la vera e propria installazione di Apache ci viene data un ultima possibilità di tornare indietro per effettuare eventuali modifiche alla configurazione:

 ![apache009.png](/image/articoli/WAMP/apache009.png)

 Procediamo premendo il tasto Install,dopo poche decine di secondi l'installazione sarà conclusa e ci verrà proposta la seguente schermata:

 ![apache010.png](/image/articoli/WAMP/apache010.png)

 A conferma della corretta installazione, una nuova icona nel system tray ci informa dello stato del server Apache. Una freccia verde indica che Apache è in esecuzione sulla nostra macchina.

 ![apache_icon_tray.png](/image/articoli/WAMP/apache_icon_tray.png)

 ![apache_tray_magnify.png](/image/articoli/WAMP/apache_tray_magnify.png)

 Per avere un'ulteriore prova del corretto funzionamento di Apache, apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/>

 Se tutto è andato bene ci verrà mostrata il seguente messaggio:

##  It works!

- - - - - -

###  Installare PHP 5.2.0

 L'installazione di PHP 5 e la sua integrazione in Apache spesso risultano ostiche ai novizi. In realtà la procedura da seguire è abbastanza semplice e richiede solo un po' di attenzione.

 Procuriamoci il pacchetto compresso di PHP 5 scaricandolo da [questa pagina](http://www.php.net/downloads.php). E' molto importante non scaricare l'installer di PHP 5 poiché, nonostante questo presenti una procedura di installazione semi-automatica, non può essere considerato affidabile e quindi conviene procedere manualmente. Scegliamo quindi il file indicato con il nome **PHP 5.2.0 zip package** (è l'ultima realease disponibile). Dopo averlo scaricato, creiamo la cartella **C:\\php** e scompattiamo al suo interno il contenuto del pacchetto zippato di PHP 5. Cerchiamo il file di nome **php5ts.dll** nella cartella C:\\php e spostiamolo nella cartella di Windows **C:\\WINDOWS**.

 ![move.png](/image/articoli/WAMP/move.png)

 Adesso dobbiamo integrare il PHP 5 all'interno di Apache. Per prima cosa apriamo la cartella nella quale abbiamo installato Apache, nel nostro caso **C:\\Programmi\\Apache Software Foundation\\Apache2.2** e cerchiamo al suo interno la cartella **conf**. In questa cartella è presente il file **httpd.conf** ovvero il principale file di configurazione di Apache. Apriamolo con un editor (il blocco note va benissimo) ed andiamo ad individuare al suo interno un lungo elenco di righe che iniziano con la parola *LoadModule*. Portiamoci alla fine di questo elenco ed aggiungiamo le seguenti righe e salviamo i cambiamenti.

 ```php
LoadModule php5_module "c:/php/php5apache2_2.dll"
AddType application/x-httpd-php .php
PHPIniDir "C:/php"
```

 ![apache_conf_phpmodule.png](/image/articoli/WAMP/apache_conf_phpmodule.png)

 Come ultima operazione dobbiamo tornare nella cartella **C:\\php** e rinominare il file **php.ini-dist** in **php.ini** che costituisce il file di configurazione di PHP. Per rendere effettive le modifiche fatte alla configurazione di Apache dobbiamo riavviarlo. Clicchiamo quindi due volte sull'icona di Apache presente nel system tray (l'icona a forma di piuma rossa con una freccia verde all'interno), ci apparirà la seguente schermata:

 ![apache011.png](/image/articoli/WAMP/apache011.png)

 Per riavviare Apache sarà sufficiente cliccare sul pulsante *Restart* (oppure prima su *Stop* e poi su *Start*). La procedura dura pochi secondi, se non vengono visualizzati errori significa che tutto è andato a buon fine. Per sincerarcene realizziamo un piccolo script PHP. Apriamo il blocco note e scriviamo le seguenti istruzioni:

 ```php
<?php
phpinfo();
?>
```

 Salviamo il file appena creato nella cartella **htdocs** di Apache (nel nostro caso **C:\\Programmi\\Apache Software Foundation\\Apache2.2\\htdocs**) impostando il nome a **"info.php"**. Per evitare che il blocco note automaticamente aggiunga l'estensione .txt è possibile salvare il file scrivendo il nome tra virgolette, ovvero nel campo Nome file della finestra Salva con nome scrivere **"info.php"**. Adesso proviamo a lanciare il nostro piccolo script appena realizzato: apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/info.php>. Se tutto è stato eseguito correttamente otterremo la seguente pagina:

 ![php5.png](/image/articoli/WAMP/php5.png)

- - - - - -

###  Installare MySQL 5.0

 Ultimamente il sito di MySQL AB ha subito piccoli aggiornamenti, ora i download sono raggiungibili nella sezione **Community**, ma per potete scaricarlo direttamente da questo [indirizzo](http://dev.mysql.com/downloads/mysql/5.0.html#downloads), il file da scaricare è Windows Essentials (x86).

 A partire da MySQL 4.1 è disponibile un pratico wizard che ci permette di installare e configurare MySQL con pochi click.

 ![mysql_first.png](/image/articoli/WAMP/mysql_first.png)

 Dopo aver premuto il tasto Next ci viene chiesto il tipo di installazione che intendiamo effettuare. Possiamo scegliere tra una installazione tipica, una completa ed una personalizzabile.

 ![mysql_second.png](/image/articoli/WAMP/mysql_second.png)

 Optiamo per l'installazione tipica e procediamo premendo il tasto Next:

 ![mysql_third.png](/image/articoli/WAMP/mysql_third.png)

 Adesso ci vengono elencate le opzioni di installazione che abbiamo indicato nei passi precedenti. Si noti che la cartella nella quale verranno copiati i files di MySQL è stata impostata automaticamente. Questo perchè abbiamo scelto di procedere con una installazione tipica. Se tutto è corretto possiamo procedere:

 ![mysql_skip.png](/image/articoli/WAMP/mysql_skip.png)

 Ora ci viene proposto di registrarci al sito MySQL.com per la creazione di un account. Questa procedura non è necessaria e quindi possiamo saltarla.

 ![mysql_configurenow.png](/image/articoli/WAMP/mysql_configurenow.png)

 La precedente schermata ci avvisa che l'installazione è conclusa. Possiamo procedere con la configurazione del nostro server MySQL

 ![mysql_conf_first.png](/image/articoli/WAMP/mysql_conf_first.png)

 Dopo aver premuto il tasto Next ci verrà chiesto che tipo di configurazione vogliamo fare: dettagliata o standard.

 ![mysql_conf_standardconf.png](/image/articoli/WAMP/mysql_conf_standardconf.png)

 La configurazione dettagliata permette di impostare il server MySQL con parametri appropriati al tipo di utilizzo che se ne intende fare. Quella standard invece imposterà settaggi generici ed è indicata come configurazione iniziale. Procediamo quindi con la configurazione standard.

 ![mysql_conf_istance.png](/image/articoli/WAMP/mysql_conf_istance.png)

 Adesso ci viene chiesto se vogliamo che MySQL sia installato come servizio di Windows. Come ci viene suggerito, questa è la soluzione migliore. Impostiamo anche il nome da dare al servizio (MySQL va benissimo) ed indichiamo che dovrà essere lanciato automaticamente all'avvio di Windows.

 ![mysql_conf_root.png](/image/articoli/WAMP/mysql_conf_root.png)

 Questa finestra invece serve per impostare le opzioni di sicurezza. Scegliamo di modificare le impostazioni di sicurezza e forniamo una password per l'utente root (l'utente principale di MySQL). Per questo esempio useremo la password '**testpass**'. E' importante non dimenticare la password che andiamo ad inserire in questa schermata, sarà quella che useremo per connetterci al database nei prossimi esempi. Dato che stiamo realizzando una piattaforma di sviluppo selezioniamo anche l'opzione per impedire all'utente root l'accesso a MySQL da altri computers. Procediamo quindi premendo il tasto Next.

 ![mysql_conf_execute.png](/image/articoli/WAMP/mysql_conf_execute.png)

 Dopo pochi secondi necessari alla preparazione della configurazione e all'avvio di MySQL ci verrà mostrata la seguente schermata conclusiva:

 ![mysql_conf_finish.png](/image/articoli/WAMP/mysql_conf_finish.png)

 Se vogliamo avere conferma che MySQL è stato avviato ed è attivo sulla nostra macchina è sufficiente visualizzare l'elenco dei servizi installati, raggiungibile dal pannello di controllo.

- - - - - -

###  Usare PHP 5 con MySQL 5

 Con le novità introdotte ai tempi di MySQL 4.1 il team di PHP ha sviluppato una nuova API (insieme di funzioni) che permette a PHP di interagire con MySQL, contenuta in una estensione chiamata ***mysqli*** (mysql *improved*). Tale estensione si affianca quindi alla più vecchia estensione *mysql*, estendendone le funzionalità ed introducendo anche un'interessante interfaccia ad oggetti.

 Mostreremo quindi i passi necessari per abilitare sia la classica estensione *mysql* sia la nuova *mysqli*. Bisognerà scegliere quale delle due procedure eseguire in relazione alle applicazioni PHP che si intende far giare.

 Se si vogliono provare le nuove caratteristiche di MySQL 5 dovremo installare l'estensione *mysqli* mentre se vorremo testare la compatibilità di script PHP di non recente realizzazione l'estensione *mysql* farà al caso nostro. E' comunque possibile installarle entrambe.

- - - - - -

###  Installare l'estensione mysqli

 Per prima cosa cerchiamo il file **libmysql.dll** nella cartella di PHP (**C:\\php**) e copiamolo nella cartella di windows (**C:\\WINDOWS**).

 ![move_libmysql.png](/image/articoli/WAMP/move_libmysql.png)

 Apriamo poi con un editor (nel nostro esempio useremo il blocco note) il file **php.ini** presente nella cartella **C:\\php**. Cerchiamo la riga che inizia per **extension\_dir** e modifichiamola in modo che punti alla cartella delle estensioni di PHP **C:\\php\\ext** come mostrato nella seguente figura:

 ![extdir.png](/image/articoli/WAMP/extdir.png)

 Fatto ciò scorriamo il file cercando un lungo elenco di righe che iniziano tutte per **;extension**. Portiamoci alla fine di questo elenco e scriviamo la seguente riga:

 **extension=php\_mysqli.dll**

 Il risultato dovrà essere simile a quello mostrato nella seguente finestra

 ![extmysqli.png](/image/articoli/WAMP/extmysqli.png)

 A questo punto riavviamo Apache come mostrato precedentemente (doppio clic sull'icona di Apache nel system tray, e premere il pulsante Restart Apache). Per verificare che PHP ha caricato correttamente l'estensione mysqli apriamo il browser e digitiamo l'indirizzo <http://localhost/info.php>. Circa a metà della pagina che ci verrà mostrata troveremo le seguenti indicazioni:

 ![mysqli.png](/image/articoli/WAMP/mysqli.png)

 Adesso proviamo a collegarci con uno script PHP al nostro database MySQL. Apriamo il blocco note (o l'editor che preferite) e scriviamo il seguente codice:

 ```php
<?php
$link = mysqli_connect("localhost", "root", "testpass");
if (!$link)
{
   printf("Connessione non riuscita: %sn", mysqli_connect_error());
   exit();
} else {
   echo 'Connesso con successo';
}
mysqli_close($link);
?>
```

 Se volete connettervi a MySQL con il nuovo approccio OO ( Object Oriented ) fornito da mysqli

 ```php
<?php
$mysqli = new mysqli("localhost","root","testpass");
if(mysqli_connect_errno())
{
   printf("Connessione non riuscita: %sn", mysqli_connect_error());
   exit();
} else {
   echo 'Connesso con successo';
}
?>
```

 Fate attenzione ad inserire nel codice (seconda riga) la corretta password di accesso. Dovrà essere la stessa che avete usato durante l'installazione di MySQL. Nel nostro esempio è impostata a "testpass".

 Salvate lo script appena realizzato con il nome **testmysql.php** nella cartella **htdocs** di Apache (nel nostro caso **C:\\Programmi\\Apache Software Foundation\\Apache2.2\\htdocs**) . Ricordiamo che per evitare che il blocco note automaticamente aggiunga l'estensione .txt è possibile salvare il file scrivendo il nome tra virgolette (**"testmysql.php"**). Adesso proviamo a lanciare il nostro piccolo script di test: apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/testmysql.php>. Se tutto è stato eseguito correttamente visualizzeremo il messaggio "Connesso con successo".

 Nel caso la connessione non riuscisse si otterrà un messaggio di errore esplicativo.

- - - - - -

###  Installare l'estensione mysql

 Per prima cosa cerchiamo il file **libmysql.dll** nella cartella di PHP (**C:\\php**) e copiamolo nella cartella di windows (**C:\\WINDOWS**).

 ![move_libmysql.png](/image/articoli/WAMP/move_libmysql.png)

 Apriamo poi con un editor (nel nostro esempio useremo il blocco note) il file **php.ini** presente nella cartella **C:\\php**. Cerchiamo la riga che inizia per **extension\_dir** e modifichiamola in modo che punti alla cartella delle estensioni di PHP **C:\\php\\ext** come mostrato nella seguente figura:

 ![extdir.png](/image/articoli/WAMP/extdir.png)

 Fatto ciò scorriamo il file cercando un lungo elenco di righe che iniziano tutte per ;extension. Individuiamo in questo elenco la riga **;extension=php\_mysql.dll** e cancelliamo il punto e virgola iniziale come mostrato nella successiva schermata:

 ![mysql.png](/image/articoli/WAMP/mysql.png)

 Adesso salviamo il file php.ini appena modificato e riavviamo Apache come mostrato precedentemente (doppio clic sull'icona di Apache nel system tray, e premere il pulsante Restart Apache). Per verificare che PHP ha caricato correttamente l'estensione di MySQL apriamo il browser e digitiamo l'indirizzo <http://localhost/info.php>. Circa a metà della pagina che ci verrà mostrata troveremo le seguenti indicazioni:

 ![mysql_ini.png](/image/articoli/WAMP/mysql_ini.png)

 Ora tutto è pronto per testare uno script PHP-MySQL. Apriamo il blocco note (o l'editor che preferite) e scriviamo il seguente codice:

 ```php
<?php
$connessione = mysql_connect("localhost", "root", "testpass")
   or die("Connessione non riuscita: " . mysql_error());
print ("Connesso con successo");
mysql_close($connessione);
?>
```

 Fate attenzione ad inserire nel codice (seconda riga) la corretta password di accesso. Dovrà essere la stessa che avete usato durante l'installazione di MySQL. Nel nostro esempio è impostata a "testpass".

 Salvate lo script appena realizzato con il nome **testmysql.php** nella cartella **htdocs** di Apache (nel nostro caso **C:\\Programmi\\Apache Software Foundation\\Apache2.2\\htdocs**) . Ricordiamo che per evitare che il blocco note automaticamente aggiunga l'estensione .txt è possibile salvare il file scrivendo il nome tra virgolette (**"testmysql.php"**). Adesso proviamo a lanciare il nostro piccolo script di test: apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/testmysql.php> .Se tutto è stato eseguito correttamente otterremo il messaggio "Connesso con successo".

 Se invece PHP non riesce a connettersi al nostro database MySQL ci verrà mostrato un messaggio di errore contenente indicazioni sul problema verificatosi.

- - - - - -

###  Ultimi accorgimenti per Apache

 E' possibile apportare ulteriori modifiche al file di configurazione di Apache in modo da abilitare alcune interessanti caratteristiche del server web. Di seguito viene spiegato come attivare l'uso dei file .htaccess, i SSI ed il caricamento automatico dei file index.php.

 I file **.htaccess** sono dei semplici file di testo che ci permettono di configurare apache senza dover accedere al suo file di config principale.

 Per attivare l'utilizzo dei file .htaccess ed SSI (Server Side Include) è necessario aprire il file di configurazione di apache che si trova in **C:\\Programmi\\Apache Software Foundation\\Apache2.2\\conf\\httpd.conf** e sostituire alcune direttive.

 Cercare:

 Options Indexes FollowSymLinks MultiViews

 e modificarlo in:

 Options Indexes FollowSymLinks MultiViews +Includes

 cercare poi:

 AllowOverride None

 e modificarlo in:

 AllowOverride All

 Per abilitare il caricamento automatico dei file index.php invece bisogna cercare le righe:

 &lt;IfModule dir\_module&gt;

 DirectoryIndex index.html

 &lt;/IfModule&gt;

 e sostituirle con:

 &lt;IfModule dir\_module&gt;

 DirectoryIndex index.html index.php

 &lt;/IfModule&gt;

 Salvate infine il file ed uscite, riavviate apache e controllate che non dia nessun errore.