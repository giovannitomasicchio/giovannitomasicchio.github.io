---
title: "IIS 7 e PHP in Windows Vista con FastCGI"
author: "Marco Dario"
type: article
date: 2009-01-18T11:47:34+0000
description: "Guida all'installazione di PHP con IIS7 su Windows Vista con FastCGI"
url: /articoli/iis-7-e-php-in-windows-vista-con-fastcgi/
categories:
  - Articoli
tags:
  - "database"
  - "sicurezza"

  
---
 In questo articolo vedremo come installare e configurare il web server **IIS 7** (Internet Information Services) di Microsoft in **Windows Vista SP1** per ospitare applicazioni **PHP** impiegando il protocollo **FastCGI**. Prima di vedere i passi necessari proviamo a chiarire il ruolo di FastCGI nel connubio tra IIS e PHP.

 IIS 7, attraverso il modulo FastCGI, è in grado di eseguire programmi (come PHP) che supportano l’omonimo protocollo, garantendo **affidabilità** ed **alte prestazioni**. Il protocollo FastCGI è un'alternativa, molto più performante, al CGI (Common Gateway Interface), il sistema classico impiegato dai server web per interfacciarsi con applicazioni esterne.

 La differenza dei due approcci, CGI e FastCGI, si basa sulla modalità con cui il server web lancia i programmi esterni. I programmi basati su CGI sono lanciati dal web server ad ogni richiesta dell’utente, al fine di elaborare i dati ricevuti e generare una risposta dinamica da inviare al client (es. il browser). Poiché molti di questi programmi non supportano esecuzioni multi-threaded, IIS per rispondere alle diverse richieste dell’utente è costretto ad eseguire un processo separato per ciascuna di esse. Gli elevati “costi” di avvio e chiusura di un processo ad ogni richiesta influiscono negativamente sulle prestazioni complessive del sistema.

 Fino a non molto tempo fa, CGI era l’unico modo affidabile per utilizzare PHP con IIS. Infatti, anche se PHP può essere eseguito in ambiente multi-threaded, molte delle sue estensioni non sono thread-safe. Si deve alla collaborazione tra Zend, azienda che sviluppa PHP, e Microsoft la maturazione del modulo FastCGI per IIS.

 FastCGI risolve questo problema fornendo, da un lato, un meccanismo per il riutilizzo di uno stesso processo per rispondere a più richieste utente, dall’altro garantendo la compatibilità con i programmi non thread-safe, nel nostro caso alcune delle estensioni di PHP. Ne vien fuori quindi una architettura molto più performante e, nello stesso tempo, affidabile.

- - - - - -

###  Installazione e configurazione di PHP

 Scarichiamo l'ultima versione di PHP nella sezione [download](http://www.php.net/downloads.php) del sito ufficiale.

 La versione che a noi interessa è php-x.x.x-nts-Win32.zip (PHP x.x.x **Non-thread-safe** zip package) dove x.x.x. è la versione di PHP che al momento in cui scriviamo è la 5.2.8. Fate attenzione a scegliere la versione non-thread-safe poiché questa è più performante con FastCGI. A questo punto dovremmo decomprimere i file compressi in una cartella, che nel nostro caso sarà C:\\PHP.

 Fatto ciò, cominciamo a configurare il nostro file **php.ini** in modo ottimale per FastCGI.

 ![](images/stories/Articoli/IIS7FastCGI/01.gif)

 Se il file php.ini non esiste possiamo rinominare il file **php.ini-dist** in php.ini. Apriamo quindi il php.ini (con un editor di testo in **modalità amministratore**!!!) e andiamo ad impostare i seguenti parametri:

> **fastcgi.impersonate** = 1
>  FastCGI con IIS supporta la capacità di impersonare la sicurezza del client chiamante. Questo permette a IIS di definire il contesto di sicurezza che viene eseguito con la richiesta.
>  **cgi.fix\_pathinfo** = 1.
>  **cgi.force\_redirect** = 0.
>  **open\_basedir** deve puntare a una cartella o un percorso di rete in cui si trova il contenuto del sito web, generalmente "C:\\inetpub\\wwwroot" 
>  **extension\_dir** deve puntare alla cartella contenente le estensioni di PHP, generalmente per PHP 5.2.X deve essere impostato a "./Ext"

 Attiviamo anche le estensioni che riteniamo ci possano servire, togliendo il punto e virgola iniziale, ad esempio quelle per utilizzare alcuni database:

> extension=php\_mysql.dll

 Salvato il file php.ini appena modificato possiamo verificare se l'installazione di PHP è riuscita. Digitiamo da riga di comando:

> C:\\PHP&gt;php-cgi –info

 Se PHP è stato installato correttamente e tutte le sue dipendenze sono disponibili sulla macchina, allora questo comando visualizzerà l'attuale configurazione di PHP.

- - - - - -

###  Installazione e configurazione di IIS

 A questo punto possiamo installare IIS nel nostro sistema. Per far ciò andiamo in *Pannello di controllo* —&gt; *Programmi e funzionalità* —&gt; *Attivazione o disattivazione delle funzionalità di Windows* e selezioniamo per l'installazione **Internet Information Services**.

 ![](images/stories/Articoli/IIS7FastCGI/02.gif)

 Cosa molto importate è aggiungere la caratteristica **CGI** in *Internet Information Services* —&gt; *Servizi Web* —&gt; *Funzionalità per lo sviluppo di applicazioni*. Questo consente a IIS di far eseguire le applicazioni esterne sia in modalità CGI che FastCGI.

 ![](images/stories/Articoli/IIS7FastCGI/03.gif)

 Dopo i dovuti riavvii di sistema possiamo controllare se il nostro Web Server funziona. Apriamo il nostro browser e nella barra degli indirizzi inseriamo l'indirizzo del nostro server che nel nostro caso è [http://localhost](http://localhost/). Se tutto è andato bene dovremmo vedere la pagina di benvenuto di IIS 7.

 Ora passiamo alla configurazione vera e propria di IIS 7 per PHP. Apriamo la console di controllo e di impostazione di IIS 7 nel seguente modo: *Start* —&gt; *Inizia Ricerca* —&gt; digitiamo *InetMgr.exe* e lanciamo l'applicazione. Oppure fare clic sul pulsante *Start*, quindi scegliere *Pannello di controllo*, nel Pannello di controllo fare clic su *Sistema e manutenzione*, poi su *Strumenti di amministrazione* e poi su *Gestione Internet Information Services (IIS)*.

 ![](images/stories/Articoli/IIS7FastCGI/04.gif)

 Apriamo la funzionalità **Mapping gestori**. Clicchiamo su *Aggiungi mapping moduli* e specifichiamo le impostazioni di configurazione come indicato di seguito:

 ![](images/stories/Articoli/IIS7FastCGI/05.gif)

> **Percorso richiesta**: \*.php
>  **Modulo**: FastCgiModule
>  **Eseguibile**: "C: \\PHP\\ php-cgi.exe"
>  **Nome**: Php (parametro non critico, possiamo dargli qualsiasi nome.)

 Confermiamo le nostre impostazioni alla seguente richiesta.

 ![](images/stories/Articoli/IIS7FastCGI/06.gif)

 Bene, a questo punto non ci resta che verificare se il nostro Web Server riesce a gestire PHP. Creiamo con un semplice editor di testo un file di prova chiamandolo phpinfo.php ed inseriamo la classica funzione phpinfo().

> &lt;?php phpinfo(); ?&gt;

 Salviamo il file nella cartella C:\\inetpub\\wwwroot e lanciamolo col browser puntando all’indirizzo <http://localhost/phpinfo.php>. Se tutto è andato bene dovremmo ottenere una pagina simile alla seguente:

 ![](images/stories/Articoli/IIS7FastCGI/07.gif)

 Siamo giunti al termine di questa semplice guida, vi ricordo che questa procedura di installazione vale per Windows Vista SP1. Di seguito sono presenti dei link per aggiornare IIS7. L'aggiornamento per il modulo FastCGI di IIS 7 corregge diversi problemi di compatibilità noti con popolari applicazioni PHP.

 Aggiornamento per Windows Vista SP1:
 [http://www.microsoft.com/downloads/details.aspx?displaylang=it&amp;FamilyID=19600729-8470-4956-a276-200450d814bd](http://www.microsoft.com/downloads/details.aspx?displaylang=it&FamilyID=19600729-8470-4956-a276-200450d814bd)

 Aggiornamento per Windows Vista SP1 per sistemi basati su x64:
 [http://www.microsoft.com/downloads/details.aspx?displaylang=it&amp;FamilyID=c7066c3b-dcf7-4441-87bc-f7dcb51067d0](http://www.microsoft.com/downloads/details.aspx?displaylang=it&FamilyID=c7066c3b-dcf7-4441-87bc-f7dcb51067d0)

 Ulteriori informazioni sulla configurazione di IIS7 con PHP in modalità FastCGI sono presenti a questo indirizzo:
 <http://learn.iis.net/page.aspx/246/using-fastcgi-to-host-php-applications-on-iis-70/>