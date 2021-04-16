---
title: "Guida all'installazione di phpMyAdmin"
author: "Giovanni Tomasicchio"
type: article
date: 2005-02-03T21:14:35+0000
description: "guida all'installazione e configurazione di phpMyAdmin"
url: /articoli/guida-allinstallazione-di-phpmyadmin/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "Apache"
  - "backup"
  - "cookie"
  - "crittografia"
  - "exception"
  - "FTP"
  - "MyISAM"
  - "mysqli"
  - "SQL"

  
---
 [phpMyAdmin](http://www.phpmyadmin.net/) è il più potente strumento per l'amministrazione di database MySQL scritto in PHP. Con phpMyAdmin si ha a disposizione una applicazione web in grado di soddisfare le più disparate esigenze di gestione e manipolazione del DBMS Open Source più popolare. Potremo creare database e tabelle, gestirne il contenuto, amministrare gli utenti, effettuare e ripristinare backup, lanciare singole query o interi file SQL, esportare i dati in vari formati ed effettuare tante altre operazioni avanzate.

 Grazie a phpMyAdmin possiamo amministrare sia i database installati in locale, ovvero sul nostro computer, sia quelli in remoto, in particolare quelli messi a disposizione dai diversi servizi di hosting per i nostri siti web.

 In questo breve articolo vedremo quali sono i passi necessari ad installare phpMyAdmin sia sul proprio computer che su un server remoto. Per tutti gli altri dettagli sulla configurazione nonché sull'uso di questo potente strumento rimandiamo alla [documentazione ufficiale](http://www.phpmyadmin.net/documentation/)

- - - - - -

####  Requisiti

 phpMyAdmin è un'applicazione web-based. Per il suo funzionamento è quindi necessario avere a disposizione, sulla macchina su cui si sta installando questo programma, un server web in grado di far girare script PHP. Apache o IIS (solo per citare qualche nome) se ben configurati andranno benissimo. Altri requisiti riguardano la versione di PHP e di MySQL:

 **PHP**

> La versione di **PHP** deve essere **almeno la 4.1.0**. E' necessario che sia caricata l'estensione mysql (per MySQL 4 e precedenti) o mysqli (per MySQL 4.1 e successivi) e l'estensione mbstring nel caso in cui si stia utilizzando un set di caratteri multibyte. E' richiesta inoltre la presenza delle librerie GD2 se si vuole usufruire delle miniature delle immagini jpeg. Con phpMyAdmin 2.6.1 le trasformazioni basate sui tipi MIME, che usano programmi esterni, richiedono la presenza di PHP 4.3.0 o successivi
> 
>  Nel caso si opti per un sistema di autenticazione basato sui cookie (più dettagli nelle prossime pagine) è fortemente consigliato, per ragioni di prestazioni, l'estensione mcrypt di PHP.
> 
>  phpMyAdmin funziona anche con il nuovo PHP 5. A causa però della necessaria retrocompatibilità da garantire con PHP 4, non è possibile impostare il livello di segnalazione degli errori di PHP 5 a E\_STRICT

 **MySQL**

> phpMyAdmin supporta tutte le versioni di **MySQL dalla 3.23.32 fino alla 5.0** con la sola eccezione delle versioni 4.1.0 e 4.1.1. E' ovvio che le versioni più datate saranno soggette a maggiori limitazioni e questo si rifletterà sulle capacità di phpMyAdmin.
> 
>  Al momento comunque phpMyAdmin non supporta MySQL 5.1.

- - - - - -

####  Installazione

 Per prima cosa dobbiamo procurarci l'archivio compresso di phpMyAdmin. Lo troveremo facilmente raggiungendo la [sezione download](http://www.phpmyadmin.net/home_page/downloads.php) del sito ufficiale. In questa pagina troveremo diverse versioni, a noi interessa la più recente tra le versioni stabili (nel momento in cui scriviamo l'articolo è la 2.6.1)

 ![](/image/articoli/phpmyadmin/cap9.png)

 Come è possibile vedere dalla precedente figura, per ogni versione di phpMyAdmin sono presenti 3 diversi archivi compressi. Ovviamente sono equivalenti, basta scegliere quello più consono al proprio ambiente operativo: gli utenti Windows preferiranno probabilmente l'archivio zip, chi invece lavora con Linux troverà più comodi gli archivi tar.bz2 o tar.gz.

 Una volta scaricato dovremo scompattare l'archivio in una cartella. La scelta di tale cartella dipende da dove abbiamo intenzione di installare phpMyAdmin.

 Se vogliamo farlo girare **sul nostro computer**, in cui avremo precedentemente installato un server web (ad es. Apache) e PHP, possiamo estrarre i files direttamente in una cartella accessibile al server web. Ad esempio, nella configurazione di default di Apache, questa cartella si chiama **htdocs** (un possibile percorso di questa cartella potrebbe quindi essere C:\\Programmi\\Apache Group\\Apache2\\htdocs). Se ci si trova in una situazione di questo genere o simile si potrebbe creare una cartella dal nome 'phpmyadmin' all'interno della cartella htdocs.

 ![](/image/articoli/phpmyadmin/cap1.png)

 Nel caso in cui vogliamo invece installare phpMyAdmin **su uno spazio web** messoci a disposizione dal nostro fornitore di hosting allora sarà sufficiente scompattare l'archivio appena scaricato in una cartella temporanea della nostra macchina. Solo dopo aver effettuato la configurazione (che vedremo nelle prossime pagine) andremo a creare una cartella, magari via FTP, sul server remoto ed al suo interno copieremo tutti i files estratti.

- - - - - -

####  Prima configurazione

 Individuiamo, nella cartella in cui abbiamo messo i files di phpmyadmin, il file dal nome config.inc.php (oppure il file config.default.php per phpMyAdmin 2.7 e successivi).

 ![](/image/articoli/phpmyadmin/cap2.png)

 Questo file è responsabile della configurazione di phpMyAdmin. Apriamolo usando un editor di testi. Sconsigliamo agli utenti Windows di usare il notepad e di preferire al suo posto il WordPad a causa di una errata gestione del carattere di a capo.

 ![](/image/articoli/phpmyadmin/cap0.png)

 Questo file è costituito da una serie di istruzioni di assegnazione in PHP del tipo:

 $cfg\['*nome\_valore\_da configurare*'\] = *valore\_da\_assegnare*;

 o, similmente:

 $cfg\['*nome\_sezione*'\]\['*nome\_valore\_da configurare*'\] = *valore\_da\_assegnare*;
 $cfg\['*nome\_sezione*'\]\['*sottosezione*'\]\['*nome\_valore\_da configurare*'\] = *valore\_da\_assegnare*;

 Tutte queste direttive sono precedute o hanno accanto un commento esplicativo (in inglese, preceduto da i caratteri // o /\*\*)

 Adesso vedremo quali sono le direttive su cui è necessario agire per realizzare la configurazione minima sufficiente al corretto funzionamento di phpMyAdmin.

- - - - - -

####  Direttive generali

 Dopo poco meno di 40 righe dall'inizio del file di configurazione, che dovremo accuratamente lasciare inalterate, troveremo le prime direttive:

 **$cfg\['PmaAbsoluteUri'\]**

> Il primo parametro da configurare è l'indirizzo assoluto (URL) del nostro phpMyAdmin ovvero quell'indirizzo che andrà scritto nel browser e che punterà direttamente a phpMyAdmin.
> 
>  Se stiamo installando phpMyAdmin in locale, ovvero sul nostro computer, e se lo abbiamo messo in una cartella di nome phpmyadmin all'interno della cartella htdocs di Apache allora l'indirizzo sarà '**http://localhost/phpmyadmin/**'.
> 
>  Nel caso in cui siamo invece intenzionati a installare phpMyAdmin sul nostro sito web allora, come già anticipato, dovremo creare una cartella sul server (ad es. phpmyadmin) e quindi l'indirizzo sarà del tipo '**http://www.miosito.it/phpmyadmin/**'.
> 
>  La documentazione ufficiale raccomanda di non dimenticare lo slash (il carattere '/') alla fine dell'indirizzo e di stare attenti alle maiuscole-minuscole.
> 
>  In realtà è possibile non settare questo parametro poiché phpMyAdmin cerca di ricavarlo automaticamente. Sia in caso di successo che di insuccesso phpMyAdmin comunque avverte dell'assenza di questa direttiva, come mostrato nella seguente figura:
> 
>  ![](/image/articoli/phpmyadmin/cap3.gif)
> 
>  Se, utilizzando phpMyAdmin, vi rendete conto che tutto funziona correttamente nonostante non avete configurato questo parametro allora potete disabilitare questo avvertimento semplicemente mettendo a **TRUE** la direttiva **$cfg\['PmaAbsoluteUri\_DisableWarning'\]** che trovate subito dopo $cfg\['PmaAbsoluteUri'\] (opzione non più presente con phpMyAdmin 2.7).

 **$cfg\['PmaNoRelation\_DisableWarning'\]**

> Alcune funzionalità avanzate di phpMyAdmin richiedono la creazione di un database, di alcune tabelle in esso e di un utente particolare. Se queste tabelle non vengono create e sopratutto se non vengono impostate nel file di configurazione le direttive, in alcune pagine di phpMyAdmin viene mostrato un avviso a riguardo.
> 
>  ![](/image/articoli/phpmyadmin/errore2.gif)
> 
>  Se non si ha intenzione di abilitare queste funzionalità (procedura non descritta da questo articolo e sconsigliata per una prima installazione), è possibile impostare questo parametro a **TRUE** così da disabilitare questo avviso.

 **$cfg\['blowfish\_secret'\]**

> Come spiegato in seguito, uno dei metodi utilizzati da phpMyAdmin per autenticare l'utente fa uso di cookies nel quale conservare la password. Tale password non viene memorizzata in chiaro all'interno del coockie bensì criptata attraverso l'algoritmo blowfish. Si tratta di un algoritmo che realizza una crittografia a chiave simmetrica, in grado cioè di criptare e decriptare una stringa (nel nostro caso la password) utilizzando un'unica *chiave*.
> 
>  Se nella nostra configurazione prevediamo di usare il sistema di autenticazione basato sui cookies allora dovremo impostare la direttiva $cfg\['blowfish\_secret'\] con una *chiave* di nostra invenzione.

- - - - - -

####  Configurazione dei server

 phpMyAdmin, attraverso un'unica installazione, è in grado di gestire più server MySQL che possono trovarsi sia sulla stessa macchina, sia su computer remoti. Per ogni server che vogliamo gestire dobbiamo impostare le seguenti direttive:

 **$cfg\['Servers'\]\[$i\]\['host'\]**

> Questo parametro specifica il nome dell'host o l'indirizzo IP della macchina che ospita il server MySQL che vogliamo amministrare. Se MySQL si trova sullo stesso computer in cui stiamo installando phpMyAdmin possiamo settare questa direttiva a '**localhost**'. Saremo in questa situazione sia se stiamo installando phpMyAdmin sul nostro computer e vogliamo amministrare MySql anch'esso installato sulla nostra macchina, sia se phpMyAdmin dovrà girare sul server del nostro sito internet e dovrà collegarsi a MySQL messoci a disposizione dal nostro fornitore di hosting.

 **$cfg\['Servers'\]\[$i\]\['port'\]**

> Questa direttiva serve a specificare il numero di porta usato dal server MySQL per le connessioni attraverso il protocollo TCP. Se non specificato (consigliato) viene utilizzato il valore di default pari a 3306.
> 
>  Se abbiamo settato il precedente parametro $cfg\['Servers'\]\[$i\]\['host'\] a 'localhost' allora il numero di porta, qualunque esso sia, verrà ignorato poiché phpMyAdmin non utilizzerà una connessione TCP per collegarsi a MySQL, ma lo farà bensì attraverso i socket.

 **$cfg\['Servers'\]\[$i\]\['socket'\]**

> Il percorso al file del socket di MySQL da usare. Lasciando vuota questo parametro (consigliato) verrà utilizzato il valore di default.

 **$cfg\['Servers'\]\[$i\]\['connect\_type'\]**

> Questo parametro imposta il metodo con cui phpMyAdmin si collegherà al server MySQL. I possibili valori sono **socket** e **tcp**. Se non specificato altrimenti verrà utilizzata una connessione attraverso TCP. Nel caso in cui MySQL sia sulla stessa macchina in cui stiamo installando phpMyAdmin allora potremo utilizzare i più performanti socket.

 **$cfg\['Servers'\]\[$i\]\['extension'\]**

> L'estensione, ovvero la libreria PHP che phpMyAdmin dovrà usare per connettersi e gestire MySQL. I valori possibili sono:
> 
>  **mysql**: è l'estensione che phpMyAdmin usa di default. Se non siamo in particolari condizioni (vedi sotto) allora questa estensione è quella consigliata.
> 
>  **mysqli**: è la nuova estensione per la gestione di MySQL, introdotta con PHP 5 e raccomandata solo se si vuole gestire un server MySQL 4.1.x. Il supporto di tali versioni di MySQL 4.1 è comunque tuttora sperimentale.

 **$cfg\['Servers'\]\[$i\]\['compress'\]**

> Specifica se usare una comunicazione compressa (e quindi più veloce) con MySQL. Questa modalità, ancora sperimentale, è disponibile sono se la versione di PHP installata è la 4.3.0 o successive. E' consigliato lasciare questo valore a **FALSE**.

 **$cfg\['Servers'\]\[$i\]\['controluser'\] - $cfg\['Servers'\]\[$i\]\['controlpass'\]**

> Questi due parametri servono per impostare rispettivamente il nome e la password di un particolare utente del server MySQL. Si tratta di un utente che phpMyAdmin usa per due scopi: attivare alcune funzionalità avanzate legate all'integrità referenziale di tabelle MyISAM e, per versioni di MySQL precedenti la 4.1.2, rendere possibile un accesso multiutente in caso di autenticazione basata su HTML o sui cookie (descritte in seguito)
> 
>  Se non si è interessati alle suddette funzionalità è possibile lasciare in bianco questi due parametri. Questa scelta è consigliata sopratutto in caso di prima installazione di phpMyAdmin.

 **$cfg\['Servers'\]\[$i\]\['auth\_type'\]**

> Agendo su questo parametro indicheremo a phpMyAdmin in che modo vogliamo effettuare l'autenticazione dell'utente e quindi in che modo effettueremo il log-in. Le alternative possibili sono 3: **http**, **cookie** o **config**. Dovremo scegliere tra uno di questi metodi, alcuni più pratici, altri più sicuri.
> 
>  **http -** Questo sistema di autenticazione (disponibile solo se si sta usando Apache come web server) si poggia sull'autenticazione HTML. Quando si accede a phpMyAdmin il browser mostrerà una finestra chiedendoci di inserire un nome ed una password.
> 
>  ![](/image/articoli/phpmyadmin/cap5.png)
> 
>  A questo punto dovremo inserire i dati di un utente del server MySQL. Ad esempio potremo autenticarci come l'utente principale di MySQL che, in molte installazioni, ha come nome di default 'root' e password vuota.
> 
>  **cookie** - Se scegliamo di usare il sistema di autenticazione basato sui cookies phpMyAdmin ci mostrerà una pagina di accesso chiedendo il nome e la password di un utente del server MySQL (procedura simile a quella vista prima).
> 
>  ![](/image/articoli/phpmyadmin/cap6.png)
> 
>  I dati immessi vengono memorizzati in un cookie (che ovviamente dovranno essere abilitati sul browser) con la password criptata con l'algoritmo blowfish attraverso la chiave impostata con la direttiva $cfg\['blowfish\_secret'\].
> 
>  **config** - In questa modalità di autenticazione il nome e la password di un utente del server MySQL vengono scritte direttamente nel file di configurazione config.inc.php (direttive $cfg\['Servers'\]\[$i\]\['user'\] e $cfg\['Servers'\]\[$i\]\['password'\]). Ovviamente si tratta del sistema meno sicuro di autenticazione ma che può risultare comodo nel caso si voglia utilizzare phpMyAdmin in locale sulla propria macchina di sviluppo.

 **$cfg\['Servers'\]\[$i\]\['user'\] - $cfg\['Servers'\]\[$i\]\['password'\]**

> Come appena spiegato, se abbiamo scelto la modalità di autenticazione *config* dobbiamo inserire in queste direttive il nome e la password di un utente del server MySQL. In caso contrario dobbiamo lasciarle in bianco.

 **$cfg\['Servers'\]\[$i\]\['only\_db'\]**

> Con questa direttiva possiamo specificare i nomi dei database che vogliamo gestire con phpMyAdmin. Quelli non specificati non verranno visualizzati. Se non indichiamo nulla verranno mostrati tutti i database presenti sul server (nel caso in cui se ne abbia diritto)
> 
>  La sintassi da seguire per specificare più di un database è la seguente:
> 
>  $cfg\['Servers'\]\[$i\]\['only\_db'\] = array('database1', 'database2');
> 
>  Questa direttiva può essere usata anche per decidere l'ordine con cui verranno mostrati i database.

 La configurazione del server contempla anche altre impostazioni e direttive ma queste non sono necessarie ad una corretta installazione di phpMyAdmin. Per informazioni su questi parametri consultare la [documentazione ufficiale](http://www.phpmyadmin.net/documentation/).

- - - - - -

####  Conclusioni

 Se avete seguito con attenzione i semplici passi proposti il vostro phpMyAdmin dovrebbe essere correttamente configurato ed installato. Possiamo quindi provare ad accedere a phpMyAdmin aprendo il browser e digitando l'indirizzo corretto. Questo potrebbe essere 'http://localhost/phpmyadmin/index.php' per coloro che hanno effettuato l'installazione sul proprio PC oppure 'http://www.miosito.it/phpmyadmin/index.php' se invece avete installato phpMyAdmin nel vostro spazio web.

 Come spiegato in precedenza la procedura di autenticazione cambia a seconda delle scelte fatte durante la configurazione. Una volta effettuata correttamente verrà mostrata la pagina principale di phpMyAdmin.

 Consigliamo vivamente, una volta presa confidenza con questo potente strumento, di rimettere mano al file di configurazione. Questo contiene infatti, oltre alle citate direttive, una grande quantità di altri parametri configurabili che permettono non solo di adattare meglio phpMyAdmin alle vostre esigenze ma anche di estenderne le possibilità e abilitare alcune funzionalità avanzate. Per avere maggiori informazioni a riguardo è sufficiente consultare la [documentazione ufficiale](http://www.phpmyadmin.net/documentation/).

 Non ci resta che augurarvi buon lavoro con phpMyAdmin!