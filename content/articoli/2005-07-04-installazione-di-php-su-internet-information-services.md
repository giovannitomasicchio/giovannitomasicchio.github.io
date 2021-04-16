---
title: "Installazione di PHP su Internet Information Services"
author: "Giovanni Tomasicchio"
type: article
date: 2005-07-04T00:00:00+0000
description: "Guida all'installazione di PHP su IIS (Internet Information Services)"
url: /articoli/installazione-di-php-su-internet-information-services/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "GD"

  
---
 Chi ha detto che PHP gira solo su Apache? E' vero che la piattaforma LAMP (Linux - Apache - MySQL - PHP) è ormai uno standard de facto nella fornitura di servizi web basati su PHP ma diverse sono le esigenze che ci possono portare a preferire soluzioni basate su Windows e Internet Information Services.

 Infatti capita spesso che sviluppatori ASP o ASP.NET vogliano avvicinarsi al PHP. In questi casi risulta piuttosto scomodo far convivere IIS (necessario per l'esecuzione di script ASP) con Apache ed è quindi preferibile che Internet Information Services sia in grado di supportare anche il PHP. In questo articolo vedremo proprio i passi necessari per raggiungere questo scopo. L'installazione verrà effettuata su Windows XP Professional SP2 in cui è stato precedentemente installato l'IIS 5.1.

 E' doveroso a questo punto fare una premessa circa l'installazione di PHP in IIS. Questa integrazione può essere fatta in tre modi: come filtro ISAPI, attraverso le CGI oppure usando il Fast CGI.

 L'installazione come **filtro ISAPI** garantisce buone prestazioni nell'esecuzione degli script ma può riservare spiacevoli sorprese in quanto a stabilità. Perciò tale approccio è sconsigliato.

 Attraverso le **CGI** (Common Gateway Interface) l'IIS è in grado di chiamare il PHP ad ogni esecuzione di script, intercettarne l'output e inviarlo al browser. Se questa tecnica risulta particolarmente stabile, fornisce però prestazioni mediocri, adatte magari allo sviluppatore ma non sufficienti per la realizzazione di un server di produzione.

 Infine la modalità **Fast CGI** è la migliore sia sul fronte delle prestazioni che della stabilità. Attualmente questa soluzione è sviluppata dalla [Zend](http://www.zend.com/) e fa parte del prodotto [WinEnabler](http://www.zend.com/store/products/zend-win-enabler.php).

 In questo articolo vedremo i semplici passi per integrare PHP attraverso le CGI, soluzione ottimale per lo sviluppatore anche perché a costo zero.

- - - - - -

####  Impostazione del PATH

 Procuriamoci il pacchetto compresso di PHP 5 scaricandolo da [questa pagina](http://www.php.net/downloads.php). E' molto importante non scaricare l'installer di PHP 5 poiché questo non contiene quelle estensioni che permettono, tra le altre decine di funzionalità, di connetterci a database MySQL. Scegliamo quindi il file indicato con il nome PHP 5.0.4 zip package (o una versione più recente se disponibile). Dopo averlo scaricato, creiamo la cartella "C:\\php" e scompattiamo al suo interno il contenuto del pacchetto zippato di PHP 5.

 Adesso, per evitare di dover spostare diverse librerie di PHP nelle cartelle di sistema, modifichiamo la variabile di sistema "Path" in modo che contenga il percorso alla cartella di PHP appena creata. Per fare ciò apriamo il "Pannello di controllo", clicchiamo sull'icona "Sistema" e nella finestra che ci apparirà portiamo in primo piano il pannello "Avanzate". Premiamo quindi il bottone "Variabili d'ambiente" per visualizzare la seguente finestra di dialogo:

 ![](/image/articoli//IIS/path_1.png)

 Dall'elenco delle *Variabili di sistema* selezioniamo quella di nome "Path" come mostrato nell'immagine precedente e clicchiamo sul tasto Modifica. A questo punto dovremo modificare il valore della variabile aggiungendo in coda la scritta ";C:\\php" come mostrato di seguito. Attenzione a non dimenticare il punto e virgola!

 ![](/image/articoli//IIS/path_2.png)

 Fatto ciò dobbiamo riavviare Windows per far si che la modifica effettuata abbia effetto.

- - - - - -

####  Modifica del php.ini

  Torniamo adesso nella cartella C:\\php e rinominiamo il file php.ini-dist in php.ini. Questo è il file di configurazione di PHP a cui dobbiamo apportare però diverse modifiche. Apriamolo quindi con un editor di testi (il blocco note va benissimo) ed iniziamo la direttiva cgi.force\_redirect, come mostrato in figura:

 ![](/image/articoli//IIS/php_ini_1.png)

 modifichiamo questa riga in

 **cgi.force\_redirect = 0**

 ovvero togliamo il punto e virgola iniziale ed impostiamo il valore a 0.

 Adesso cerchiamo la direttiva doc\_root e impostiamo il suo valore a "C:\\Inetpub\\wwwroot" ovvero alla root directory di IIS. Dovremmo ottenere qualcosa di simile alla seguente immagine:

 ![](/image/articoli//IIS/php_ini_3.png)

 La prossima modifica va invece effettuata alla direttiva extension\_dir il cui valore andrà impostato a "C:\\php\\ext" come mostrato di seguito:

 ![](/image/articoli//IIS/php_ini_2.png)

 Adesso dobbiamo far si che il PHP carichi alcune librerie di utilizzo comune. Ecco i passi (facoltativi) da seguire:

\- togliere il punto e virgola all'inizio della direttiva "extension=php\_mbstring.dll". L'estensione mbstring è necessaria al corretto funzionamento di phpMyAdmin con i set di caratteri multibyte;

\- togliere il punto e virgola all'inizio della direttiva "extension=php\_gd2.dll" per caricare la libreria per la manipolazione delle immagini;

\- togliere il punto e virgola all'inizio della direttiva "extension=php\_mysql.dll". L'estensione mysql è necessaria per l'interazione con l'omonimo database.

 Infine conviene modificare anche il valore della direttiva session.save\_path in modo che punti ad una cartella di files temporanei del sistema, ad esempio C:\\WINDOWS\\Temp, come mostrato di seguito:

 ![](/image/articoli//IIS/php_ini_7.png)

 Salviamo quindi il file e procediamo alla configurazione di IIS.

- - - - - -

####  Configurazione di IIS

 Adesso non ci rimane che istruire l'Internet Information Services affinché usi l'interprete PHP (il file php-cgi.exe) per l'esecuzione dei files con estensione ".php".

 Avviamo quindi la console di IIS (Pannello di controllo -&gt; Strumenti di Amministrazione -&gt; Internet Information Services) ed espandiamo la struttura ad albero a sinistra fino a mostrare il "Sito Web predefinito". Clicchiamo quindi su questa icona con il tasto destro e selezioniamo la voce "Proprietà" del menù a tendina, come mostrato di seguito.

 ![](/image/articoli//IIS/IIS_1.png)

 Clicchiamo sul pannello "Home directory" per ottenere la seguente schermata:

 ![](/image/articoli//IIS/IIS_2.png)

 Clicchiamo ora sul bottone "configurazione" per accedere alle impostazioni di mapping, come visibile dalla seguente schermata.

 ![](/image/articoli//IIS/IIS_3.png)

 Per aggiungere il supporto agli script PHP dobbiamo cliccare sul pulsante "Aggiungi" e impostare la finestra di dialogo che ci verrà presentata nel seguente modo:

 ![](/image/articoli//IIS/IIS_4.png)

 Una volta impostato il percorso dell'eseguibile a "C:\\php\\php-cgi.exe", l'estensione a ".php" e le altre impostazioni come mostrato in figura (spuntare *Tutti i verbi*, *Modulo script* e *Verifica l'esistenza dei files*) premiamo su "Ok".

 L'impostazione di IIS è terminata!

- - - - - -

####  Test con phpinfo

 Completati tutti i passi precedentemente descritti non ci resta che verificare il buon esito della procedura. Lo faremo realizzando un piccolo script che ci permetterà anche di avere informazioni aggiuntive sulla nostra installazione.

 Apriamo il blocco note e scriviamo le seguenti istruzioni:

 | &lt;?php  phpinfo();   ?&gt; |
|---|

 

 Otterremo qualcosa di simile:

 ![](/image/articoli//Art1/PHP/php-03.png)

 Salviamo il file appena creato nella cartella "C:\\Inetpub\\wwwroot\\" impostando il nome a **info.php**. Per evitare che il blocco note automaticamente aggiunga l'estensione .txt è possibile salvare il file scrivendo il nome tra virgolette, ovvero nel campo *Nome file* della finestra *Salva con nome* scrivere **"info.php".** Adesso proviamo a lanciare il nostro piccolo script appena realizzato: apriamo il browser e digitiamo il seguente indirizzo: <http://localhost/info.php> Se tutto è stato eseguito correttamente otterremo la seguente pagina:

 ![](/image/articoli//IIS/fine.png)

 Attraverso questa pagina possiamo verificare diverse cose. Innanzitutto possiamo avere conferma che il file di configurazione "php.ini" sia stato correttamente caricato. Infatti il suo percorso (se non ci sono stati problemi) sarà presente tra i primi parametri visualizzati.

 Scorrendo la pagina ottenuta potremo anche verificare il corretto caricamento delle estensioni (ad es. mysql, mbstring, gd) che abbiamo impostato nel php.ini.

 Un'ultima nota: a volte si verificano problemi di "accesso negato" durante l'esecuzione di script PHP. Questi sono riconducibili principalmente a 2 fattori. Possono dipendere o dalle normali impostazioni sui diritti di accesso dei files in partizioni NTFS, oppure possono essere legate alle politiche di accesso del server IIS. Nel primo caso tali problemi si risolvono agendo sulle impostazioni di accesso di Windows XP relative ai file o alle cartelle coinvolte. Nel secondo caso i permessi vanno modificati dalla console di amministrazione di IIS.

 L'installazione è terminata, non mi resta che augurarvi buon divertimento con IIS e PHP!