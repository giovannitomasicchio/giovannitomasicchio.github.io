---
title: "Installare Apache 2, PHP 5, MySQL 4.1 su Windows"
author: "Giovanni Tomasicchio"
type: article
date: 2004-11-05T22:44:58+0000
description: "Guida all'installazione di Apache 2, PHP 5, MySQL 4.1 su Windows 2000 XP"
url: /articoli/installare-apache-2-php-5-mysql-41-su-windows/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "Apache"
  - "sicurezza"
  - "email"
  - "mysqli"

  
---
 **ATTENZIONE !!!**
 La versione più aggiornata di questa guida è [disponibile qui](/articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)

In questo tutorial mostreremo in dettaglio le procedure di installazione e configurazione di Apache 2, PHP 5 e MySQL 4.1 su Windows 2000/XP. Il nostro scopo principale è quello di ottenere una piattaforma di sviluppo per poter realizzare e testare le nostre applicazioni PHP-MySQL.

Abbiamo preso in considerazione le versioni più recenti dei vari software così da poterne sperimentare le caratteristiche e novità, alcune delle quali saranno evidenti già in fase di installazione. Per questo motivo il sistema che andiamo a realizzare non è adatto per un utilizzo come server di produzione infatti, nonostante molti servizi di hosting propongano server Apache 2 - PHP 5, questa accoppiata è ancora considerata instabile e l'installazione di Apache 1.3 è da preferirsi in questi casi.

La procedura verrà suddivisa nelle seguenti fasi:

- Installazione di Apache 2
- Installazione di PHP 5
- Installazione di MySQL 4.1
- Configurazione e test di PHP5-MySQL4.1 con l'estensione mysqli e mysql
 
Dall'esperienza di diversi utenti che hanno seguito questo tutorial è emerso che i problemi che più frequentemente si incontrano sono dovuti principalmente a precedenti installazioni dei suddetti software ancora presenti nel sistema. Quindi è estremamente consigliato verificare, prima di procedere, che sul vostro PC non siano presenti files o cartelle appartenenti a vecchie versioni di Apache, PHP e MySQL.

 

- - - - - -

 **ATTENZIONE !!!**
 La versione più aggiornata di questa guida è [disponibile qui](articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)

### Installare Apache 2

Anche se Windows 2000 ed alcune versioni di Windows XP posseggono al loro interno un web server (Internet Information Services o più brevemente IIS) è preferibile, nello sviluppo di applicazioni PHP, l'utilizzo del server Apache. Infatti Apache non è solo un valido web server ed in molte situazioni superiore ad IIS, ma è anche il web server che più comunemente ci verrà messo a disposizione dai fornitori di servizi di hosting.

L'installazione di Apache 2 è molto semplice, per prima cosa dobbiamo scaricare dal [sito ufficiale](http://httpd.apache.org/download.cgi) il file di installazione denominato "Win32 Binary (MSI Installer)". Nel momento in cui scriviamo, il nome del file associato è apache\_2.0.52-win32-x86-no\_ssl.msi

Effettuato il download lanciamo l'installer che ci presenterà la seguente schermata:

![apache-01.png](/image/articoli/Art1/Apache/apache-01.png)

Dopo aver premuto il tasto *Next* ci verrà chiesto di accettare la licenza:

![apache-02.png](/image/articoli/Art1/Apache/apache-02.png)

Accettata la licenza ci verranno mostrate altre informazioni sul software che andiamo ad installare:

![apache-03.png](/image/articoli/Art1/Apache/apache-03.png)

Procedendo con l'installazione ci verrà chiesto di fornire alcune informazioni circa il dominio di rete, il nome del server ed un indirizzo email dell'amministratore. Poiché stiamo configurando un server di sviluppo, possiamo rispondere in maniera fantasiosa, senza compromettere il risultato finale.

![apache-04.png](/image/articoli/Art1/Apache/apache-04.png)

La prossima schermata ci chiederà di indicare il tipo di installazione che intendiamo fare: optiamo per una installazione tipica.

![apache-05.png](/image/articoli/Art1/Apache/apache-05.png)

Adesso ci viene data la possibilità di scegliere in quale cartella installare Apache. Accettiamo quella che ci viene proposta.

![apache-06.png](/image/articoli/Art1/Apache/apache-06.png)

Prima di procedere con la vera e propria installazione di Apache ci viene data un ultima possibilità di tornare indietro per effettuare eventuali modifiche alla configurazione:

![apache-07.png](/image/articoli/Art1/Apache/apache-07.png)

Procediamo premendo il tasto *Install*.

![apache-08.png](/image/articoli/Art1/Apache/apache-08.png)

Dopo poche decine di secondi l'installazione sarà conclusa e ci verrà proposta la seguente schermata:

![apache-09.png](/image/articoli/Art1/Apache/apache-09.png)

A conferma della corretta installazione, una nuova icona nel system tray ci informa dello stato del server Apache. Una freccia verde indica che Apache è in esecuzione sulla nostra macchina.

![apache-10.png](/image/articoli/Art1/Apache/apache-10.png)

Per avere un'ulteriore prova del corretto funzionamento di Apache, apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/>.

Se tutto è andato bene ci verrà mostrata la seguente pagina.

![apache-11.png](/image/articoli/Art1/Apache/apache-11.png)

- - - - - -

 **ATTENZIONE !!!**
 La versione più aggiornata di questa guida è [disponibile qui](articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)

### Installare PHP 5

L'installazione di PHP 5 e la sua integrazione in Apache spesso risultano ostiche ai novizi. In realtà la procedura da seguire è abbastanza semplice e richiede solo un po' di attenzione.

Procuriamoci il pacchetto compresso di PHP 5 scaricandolo da [questa pagina](http://www.php.net/downloads.php). E' molto importante non scaricare l'installer di PHP 5 poiché questo non contiene quelle estensioni che permettono, tra le altre decine di funzionalità, di connetterci a database MySQL. Scegliamo quindi il file indicato con il nome **PHP 5.0.4 zip package** (o una versione più recente se disponibile). Dopo averlo scaricato, creiamo la cartella **C:\\php** e scompattiamo al suo interno il contenuto del pacchetto zippato di PHP 5. Cerchiamo il file di nome **php5ts.dll** nella cartella C:\\php e spostiamolo nella cartella di Windows **C:\\windows**.

Adesso dobbiamo integrare il PHP 5 all'interno di Apache. Per prima cosa apriamo la cartella nella quale abbiamo installato Apache, nel nostro caso **C:\\Programmi\\Apache Group\\Apache2** e cerchiamo al suo interno la cartella **conf**. In questa cartella è presente il file httpd.conf ovvero il principale file di configurazione di Apache. Apriamolo con un editor (il blocco note va benissimo) ed andiamo ad individuare al suo interno un lungo elenco di righe che iniziano con la parola *LoadModule*. Portiamoci alla fine di questo elenco ed aggiungiamo le seguenti righe e salviamo i cambiamenti.

 ```php
LoadModule php5_module "c:/php/php5apache2.dll"
AddType application/x-httpd-php .php
PHPIniDir "C:/php"
```

Ecco il risultato ottenuto usando il blocco note:

![php-01.png](/image/articoli/Art1/PHP/php-01.png)

Come ultima operazione dobbiamo tornare nella cartella **C:\\php** e rinominare il file **php.ini-dist** in **php.ini** che costituisce il file di configurazione di PHP. Per rendere effettive le modifiche fatte alla configurazione di Apache dobbiamo riavviarlo. Clicchiamo quindi due volte sull'icona di Apache presente nel system tray (l'icona a forma di piuma rossa con una freccia verde all'interno), ci apparirà la seguente schermata:

![php-02.png](/image/articoli/Art1/PHP/php-02.png)

Per riavviare Apache sarà sufficiente cliccare sul pulsante *Restart*. La procedura dura pochi secondi, se non vengono visualizzati errori significa che tutto è andato a buon fine. Per sincerarcene realizziamo un piccolo script PHP. Apriamo il blocco note e scriviamo le seguenti istruzioni:

 ```php
<?php
phpinfo();
?>
```

Otterremo qualcosa di simile:

![php-03.png](/image/articoli/Art1/PHP/php-03.png)

Salviamo il file appena creato nella cartella **htdocs** di Apache (nel nostro caso **C:\\Programmi\\Apache Group\\Apache2\\htdocs**) impostando il nome a **info.php**. Per evitare che il blocco note automaticamente aggiunga l'estensione .txt è possibile salvare il file scrivendo il nome tra virgolette, ovvero nel campo Nome file della finestra Salva con nome scrivere **"info.php"**. Adesso proviamo a lanciare il nostro piccolo script appena realizzato: apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/info.php> Se tutto è stato eseguito correttamente otterremo la seguente pagina:

![php_05.png](/image/articoli/Art1/PHP/php_05.png)

- - - - - -

 **ATTENZIONE !!!**
 La versione più aggiornata di questa guida è [disponibile qui](articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)

### Installare MySQL 4.1

MySQL 4.1 è stato dichiarato pronto per la produzione e quindi non esitiamo ad installare questa nuova versione del Database Menagement System più comunemente utilizzato in applicazione PHP.

Prima di tutto, procuriamoci il file di installazione di MySQL 4.1 che possiamo scaricare a [questo indirizzo](http://dev.mysql.com/downloads/mysql/4.1.html). Il nome del file in questione è **mysql-4.1.7-win.zip** ma la seguente procedura dovrebbe rimanere valida anche in caso siano disponibili versioni 4.1.x più recenti. Scompattiamo il contenuto del file in una cartella e lanciamo il file setup ottenuto dalla precedente operazione.

Tra le novità presenti nella versione 4.1 di MySQL, spicca subito il nuovo programma di installazione e configurazione, chiaro e semplice da usare. Ecco la schermata che introduce il processo di installazione:

![mysql-01.png](/image/articoli/Art1/MySQL/mysql-01.png)

Dopo aver premuto il tasto *Next* ci viene chiesto il tipo di installazione che intendiamo effettuare. Possiamo scegliere tra una installazione tipica, una completa ed una personalizzabile.

![mysql-02.png](/image/articoli/Art1/MySQL/mysql-02.png)

Optiamo per l'installazione tipica e procediamo premendo il tasto *Next*:

![mysql-03.png](/image/articoli/Art1/MySQL/mysql-03.png)

Adesso ci vengono elencate le opzioni di installazione che abbiamo indicato nei passi precedenti. Si noti che la cartella nella quale verranno copiati i files di MySQL è stata impostata automaticamente. Questo perchè abbiamo scelto di procedere con una installazione tipica. Se tutto è corretto possiamo procedere:

![mysql-04.png](/image/articoli/Art1/MySQL/mysql-04.png)

Ora ci viene proposto di registrarci al sito MySQL.com per la creazione di un account. Questa procedura non è necessaria e quindi possiamo saltarla.

![mysql-05.png](/image/articoli/Art1/MySQL/mysql-05.png)

La precedente schermata ci avvisa che l'installazione è conclusa. Possiamo procedere con la configurazione del nostro server MySQL

![mysql-06.png](/image/articoli/Art1/MySQL/mysql-06.png)

Dopo aver premuto il tasto Next ci verrà chiesto che tipo di configurazione vogliamo fare: dettagliata o standard.

![mysql-07.png](/image/articoli/Art1/MySQL/mysql-07.png)

La configurazione dettagliata permette di impostare il server MySQL con parametri appropriati al tipo di utilizzo che se ne intende fare. Quella standard invece imposterà settaggi generici ed è indicata come configurazione iniziale. Procediamo quindi con la configurazione standard.

![mysql-08.png](/image/articoli/Art1/MySQL/mysql-08.png)

Adesso ci viene chiesto se vogliamo che MySQL sia installato come servizio di Windows. Come ci viene suggerito, questa è la soluzione migliore. Impostiamo anche il nome da dare al servizio (MySQL va benissimo) ed indichiamo che dovrà essere lanciato automaticamente all'avvio di Windows.

![mysql-09.png](/image/articoli/Art1/MySQL/mysql-09.png)

Questa finestra invece serve per impostare le opzioni di sicurezza. Scegliamo di modificare le impostazioni di sicurezza e forniamo una password per l'utente root (l'utente principale di MySQL). Per questo esempio useremo la password testpass. E' importante non dimenticare la password che andiamo ad inserire in questa schermata, sarà quella che useremo per connetterci al database nei prossimi esempi. Dato che stiamo realizzando una piattaforma di sviluppo selezioniamo anche l'opzione per impedire all'utente root l'accesso a MySQL da altri computers. Procediamo quindi premendo il tasto *Next*.

![mysql-10.png](/image/articoli/Art1/MySQL/mysql-10.png)

Dopo pochi secondi necessari alla preparazione della configurazione e all'avvio di MySQL ci verrà mostrata la seguente schermata conclusiva:

![mysql-11.png](/image/articoli/Art1/MySQL/mysql-11.png)

Se vogliamo avere conferma che MySQL è stato avviato ed è attivo sulla nostra macchina è sufficiente visualizzare l'elenco dei servizi installati, raggiungibile dal pannello di controllo.

![mysql-12.png](/image/articoli/Art1/MySQL/mysql-12.png)

Come possiamo osservare, il servizio "MySQL" è attivo.

- - - - - -

 **ATTENZIONE !!!**
 La versione più aggiornata di questa guida è [disponibile qui](articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)

### Usare PHP 5 con MySQL 4.1

Le novità introdotte con la versione 4.1 di MySQL sono diverse e perciò gli sviluppatori di PHP, invece di aggiornare l'attuale estensione ***mysql***, ovvero la libreria che permette al PHP di interagire con MySQL, ne hanno realizzato una nuova, dal nome ***mysqli*** (mysql improved).

Mysqli non solo permette di sfruttare a pieno le nuove caratteristiche di MySQL 4.1 ma offre anche interessanti novità come ad esempio un completo set di classi che permettono un approccio della programmazione orientato agli oggetti. Se siete interessati ad approfondire l'argomento potete consultare [questo articolo](/articoli/estensione-mysqli-i/).

Mostreremo quindi i passi necessari per abilitare sia l'estensione mysql sia la nuova mysqli. Bisognerà scegliere quale delle due procedure eseguire in relazione alle applicazioni PHP che si intende far giare.

Se si vogliono provare le nuove caratteristiche di MySQL 4.1 dovremo installare l'estensione mysqli mentre se vorremo testare la compatibilità di script PHP di non recente realizzazione l'estensione mysql farà al caso nostro. E' comunque possibile installarle entrambe.

- - - - - -

 **ATTENZIONE !!!**
 La versione più aggiornata di questa guida è [disponibile qui](articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)

### Installare l'estensione mysqli

Come abbiamo detto, l'estensione mysqli supporta nativamente MySQL 4.1 e ci permette di sfruttarne a pieno le nuove capacità. Per installarla eseguiamo la seguente procedura.

Per prima cosa cerchiamo il file **libmysql.dll** nella cartella di PHP (**C:\\php**) e copiamolo nella cartella di windows (**C:\\windows**).

Apriamo poi con un editor (nel nostro esempio useremo il blocco note) il file **php.ini** presente nella cartella **C:\\php**. Cerchiamo la riga che inizia per **extension\_dir** e modifichiamola in modo che punti alla cartella delle estensioni di PHP **C:\\php\\ext** come mostrato nella seguente figura:

![mysql-php-04.png](/image/articoli/Art1/PHP_MySQL/mysql-php-04.png)

Fatto ciò scorriamo il file cercando un lungo elenco di righe che iniziano tutte per **;extension**. Portiamoci alla fine di questo elenco e scriviamo la seguente riga:

**extension=php\_mysqli.dll**

Il risultato dovrà essere simile a quello mostrato nella seguente finestra

![mysqli1.png](/image/articoli/Art1/PHP_MySQL/mysqli1.png)

A questo punto riavviamo Apache come mostrato precedentemente (doppio clic sull'icona di Apache nel system tray, e premere il pulsante *Restart Apache*). Per verificare che PHP ha caricato correttamente l'estensione mysqli apriamo il browser e digitiamo l'indirizzo <http://localhost/info.php>. Circa a metà della pagina che ci verrà mostrata troveremo le seguenti indicazioni:

![mysqli3.png](/image/articoli/Art1/PHP_MySQL/mysqli3.png)

Adesso proviamo a collegarci con uno script PHP al nostro database MySQL. Apriamo il blocco note (o l'editor che preferite) e scriviamo il seguente codice:

 ```php
<?php 
$link = mysqli_connect("localhost", "root", "testpass");
if (!$link) {
	printf("Connessione non riuscita: %sn", mysqli_connect_error());
	exit();
}
else {
	echo 'Connesso con successo';
}
mysqli_close($link);
?>
```

Fate attenzione ad inserire nel codice (seconda riga) la corretta password di accesso. Dovrà essere la stessa che avete usato durante l'installazione di MySQL. Nel nostro esempio è impostata a "testpass".

Ecco come appare la finestra del blocco note usata nel nostro esempio:

![mysqli2.png](/image/articoli/Art1/PHP_MySQL/mysqli2.png)

Salvate lo script appena realizzato con il nome **testmysql.php** nella cartella **htdocs** di Apache (nel nostro caso **C:\\Programmi\\Apache Group\\Apache2\\htdocs**) . Ricordiamo che per evitare che il blocco note automaticamente aggiunga l'estensione .txt è possibile salvare il file scrivendo il nome tra virgolette (**"testmysql.php"**). Adesso proviamo a lanciare il nostro piccolo script di test: apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/testmysql.php>. Se tutto è stato eseguito correttamente otterremo il seguente risultato:

![mysql-php-08.png](/image/articoli/Art1/PHP_MySQL/mysql-php-08.png)

Nel caso la connessione non riuscisse si otterrà un messaggio di errore esplicativo.

- - - - - -

 **ATTENZIONE !!!**
 La versione più aggiornata di questa guida è [disponibile qui](articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)

### Installare l'estensione mysql

Se vogliamo provare a fare girare script PHP di non recente realizzazione che usano le tradizionali funzioni di connessione a MySQL (in realtà si tratta ancora oggi della stragrande maggioranza) dobbiamo installare la vecchia estensione mysql.

Per prima cosa cerchiamo il file **libmysql.dll** presente nella cartella di PHP (**C:\\php**) e copiamolo nella cartella di windows (**C:\\windows**).

Apriamo poi con un editor (nel nostro esempio useremo il blocco note) il file **php.ini** presente nella cartella **C:\\php**. Cerchiamo la riga che inizia per **extension\_dir** e modifichiamola in modo che punti alla cartella delle estensioni di PHP **C:\\php\\ext** come mostrato nella seguente figura:

![mysql-php-04.png](/image/articoli/Art1/PHP_MySQL/mysql-php-04.png)

Fatto ciò scorriamo il file cercando un lungo elenco di righe che iniziano tutte per ;extension. Individuiamo in questo elenco la riga **;extension=php\_mysql.dll** e cancelliamo il punto e virgola iniziale come mostrato nella successiva schermata:

![mysql-php-05.png](/image/articoli/Art1/PHP_MySQL/mysql-php-05.png)

Adesso riavviamo Apache come mostrato precedentemente (doppio clic sull'icona di Apache nel system tray, e premere il pulsante *Restart Apache*). Per verificare che PHP ha caricato correttamente l'estensione di MySQL apriamo il browser e digitiamo l'indirizzo <http://localhost/info.php>. Circa a metà della pagina che ci verrà mostrata troveremo le seguenti indicazioni:

![mysql_php_06.png](/image/articoli/Art1/PHP_MySQL/mysql_php_06.png)

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

Ecco come appare la finestra del blocco note usata nel nostro esempio:

![mysql-php-07.png](/image/articoli/Art1/PHP_MySQL/mysql-php-07.png)

Salvate lo script appena realizzato con il nome **testmysql.php** nella cartella **htdocs** di Apache (nel nostro caso **C:\\Programmi\\Apache Group\\Apache2\\htdocs**) . Ricordiamo che per evitare che il blocco note automaticamente aggiunga l'estensione .txt è possibile salvare il file scrivendo il nome tra virgolette (**"testmysql.php"**). Adesso proviamo a lanciare il nostro piccolo script di test: apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/testmysql.php> .Se tutto è stato eseguito correttamente otterremo il seguente risultato:

![mysql-php-08.png](/image/articoli/Art1/PHP_MySQL/mysql-php-08.png)

Se invece PHP non riesce a connettersi al nostro database MySQL ci verrà mostrato un messaggio di errore contenente indicazioni sul problema verificatosi.