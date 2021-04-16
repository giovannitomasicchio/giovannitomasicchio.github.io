---
title: "Le sessioni"
author: "Giovanni Tomasicchio"
type: article
date: 2004-11-15T15:13:39+0000
description: "Utilizzare le sessioni di PHP"
url: /articoli/le-sessioni/
categories:
  - Articoli
tags:
  - "database"
  - "sicurezza"
  - "cookie"

  
---
Un problema comune nello sviluppo di applicazioni Web è il trasferimento di informazioni legate all'utente da una pagina all'altra. Infatti l'HTTP, ovvero il protocollo che sta alla base del WEB, è senza memoria ed i server web trattano ogni connessione e richiesta di una pagina come se fosse unica e completamente indipendente dalla precedente. Ciò significa che non esiste un sistema nativo che consenta di associare ad ogni richiesta l'utente che l'ha effettuata.

Gli sviluppatori PHP, prima che le sessioni fossero introdotte, per associare delle informazioni all'utente corrente e renderle persistenti tra una pagina e l'altra, usavano i cookies o gli URL (query string). Le informazioni si trasferivano dal server al client e viceversa realizzando una sorta di palleggiamento di dati che ne garantiva la persistenza tra richieste successive.

Sorgevano però evidenti problemi di sicurezza, soprattutto perché queste tecniche prevedono che i dati passino dal client, dando così la possibilità all'utente di modificarli. La dimensione massima di queste informazioni è vincolata alla limitata capacità dei cookies e della query string. L'utilizzo delle query string inoltre obbliga il trattamento di tutti i link presenti nelle pagine (non si sa quale l'utente userà per vedere la prossima pagina).

Le sessioni sono state introdotte in PHP 4 proprio per risolvere questi problemi in maniera elegante ed il più possibile trasparente allo sviluppatore.

- - - - - -

### Come funzionano le sessioni

Anche le sessioni si basano su tecniche che fanno uso di cookie e dell'URL con la sostanziale differenza che questi mezzi vengono usati esclusivamente per la memorizzazione di un codice che identifichi univocamente l'utente proprietario. Ad ogni utente, riconosciuto attraverso questo identificativo, viene associato sul server un file di testo contenente tutte le informazioni ad egli legate. Questo procedimento è curato interamente da PHP.

![1.png](/image/articoli/sessioni/1.png)

All'avvio di una sessione, che avviene chiamando il comando [session\_start()](http://www.php.net/manual/it/function.session-start.php), il PHP crea un codice univoco (session ID) che identificherà l'utente corrente nelle successive connessioni e lo invia al client (il browser) attraverso un cookie o accodandolo a tutti gli URL presenti nella pagina richiesta. La scelta tra uno dei due metodi di propagazione del session id viene fatta relativamente alle capacità del browser ed alle impostazioni presenti nel php.ini.

Il nome del cookie e della variabile passata via URL sono impostati di default a PHPSESSID. Questa stringa, detta anche nome della sessione, può essere modificata sia nel php.ini, agendo sulla direttiva session.name, sia attraverso la funzione [session\_name()](http://www.php.net/manual/it/function.session-name.php).

PHP crea poi un file di testo sul server, anch'esso associato al session ID appena generato. Questo file costituirà il contenitore di tutte le informazioni che vorremo rendere persistenti. Queste stesse informazioni sono invece presentate allo script PHP attraverso l'array globale $\_SESSION.

Quando l'utente effettua una nuova richiesta, dopo che è stato lanciato il comando session\_start(), il PHP riconosce l'utente dal session id che possiede, avendolo recuperato dal cookie o dalla query string. Carica quindi i dati presenti nel file di testo sul server, associato al session id, e li mappa all'interno dell'array superglobale **$\_SESSION**.

![2.png](/image/articoli/sessioni/2.png)

All'interno di uno script è possibile inserire, modificare o cancellare dati della sessione agendo sull'array $\_SESSION come si farebbe con qualsiasi altro array. Tutte le modifiche apportate ai dati di sessione (all'array $\_SESSION) vengono riportate automaticamente sul file di testo sul server al termine dello script.

Due sono gli elementi che tengono in piedi una sessione: il session id fornito dal client ed il file dei dati di sessione conservato sul server. Se uno di questi due elementi viene meno la sessione scade. Il client smette di inviare il session id quando scade il cookie di sessione. Questa scadenza viene regolata dalla funzione [session\_set\_cookie\_params()](http://it.php.net/manual/it/function.session-set-cookie-params.php) e dalla direttiva **session.cookie\_lifetime** del php.ini.

Anche il file dei dati di sessione è soggetto a scadenza, evento che accade dopo un determinato numero di secondi di inattività impostato dalla direttiva session.gc\_maxlifetime del php.ini. Trascorso questo intervallo di secondi, il file viene considerato spazzatura (garbage) e sarà cancellato da una routine spazzina (garbage collector) che si attiva con una probabilità impostata dalle direttive **session.gc\_probability** e **session.gc\_divisor**. Poiché questa probabilità è impostata di default all'1% non è possibile fare affidamento sul garbage collector per la scadenza di una sessione poiché queste potrebbero venir cancellate molto tempo dopo il session.gc\_maxlifetime impostato. Inoltre il garbage collector non elimina mai la sessione relativa allo script corrente, anche se questa è scaduta!

E' necessario fare alcune considerazioni circa l'uso delle sessioni e la sicurezza degli script. Immaginiamo un sistema di autenticazione utenti basato sulle sessioni (cosa che comunemente accade). Il sistema attribuisce dei privilegi all'utente sulla base dei dati di sessione che ad ogni pagina vengono caricati utilizzando il session id che l'utente stesso invia (attraverso il cookie o l'URL). Cosa succede se qualcuno riesce a rubare il session id dell'utente e ad usarlo magari da un altro PC? Il sistema di autenticazione crederà che il ladro sia l'utente di prima e gli attribuirà tutti i privilegi. Il furto di session id non è un'operazione così semplice da effettuare ma quando questo codice non viene memorizzato in un cookie bensì nell'URL allora i rischi aumentano. Quindi, anche se questo non garantisce una sicurezza al 100%, è consigliato impostare il php.ini affinché il session id venga conservato in un cookie del client. L'unica soluzione definitiva a queste problematiche si può ottenere attraverso l'utilizzo di SSL.

Le sessioni sono state introdotte con PHP 4 mentre l'array superglobale $\_SESSION con PHP 4.1. Questo ci suggerisce che inzialmente l'interazione con i dati di sessione doveva avvenire attraverso l'uso di altre tecniche. Ecco il perché dell'esistenza delle funzioni [session\_is\_registered()](http://www.php.net/manual/it/function.session-is-registered.php) , [session\_register()](http://www.php.net/manual/it/function.session-register.php), [session\_unregister()](http://www.php.net/manual/it/function.session-unregister.php), [session\_unset()](http://www.php.net/manual/it/function.session-unset.php). Queste funzioni permettevano l'interfacciamento degli script PHP con i dati di sessione ma il loro utilizzo, insieme alla direttiva register\_globals impostata ad On di default, poteva costituire un pericolo per la sicurezza del codice. Con PHP 4.2 la direttiva register\_globals è stata impostata di default ad Off e l'uso del precedente approccio è stato deprecato a favore dell'utilizzo dell'array $\_SESSION.

 

- - - - - -

### Esempio di utilizzo delle sessioni

Per chiarire il funzionamento delle sessioni consideriamo il seguente esempio estremamente semplificato. Supponiamo di voler realizzare un sistema che permetta l'accesso a determinate pagine private del nostro sito solo agli utenti che hanno effettuato correttamente una procedura di log-in. Sarà necessario quindi realizzare una pagina che verifichi l'identità dell'utente e ne memorizzi l'esito in modo da poter regolamentare l'accesso alle pagine private del nostro sito.

Le sessioni si prestano bene alla risoluzione di questo problema, vediamo quali sono le istruzioni necessarie.

La pagina login.php controlla il nome e la password dell'utente e se sono corretti memorizza che l'autenticazione è avvenuta con successo:

 ```php
<?php 
session_start();

/**
* qui effettueremo i controlli per verificare i
* dati inseriti dall'utente per effetuare il login
*/

if ($login_effettuato_correttamente)
{
	$_SESSION['login_effettuato'] = true;
}
?>
```

Si noti che la funzione session\_start è stata posta proprio all'inizio dello script. Questo perché session\_start necessita di inviare degli header, informazioni che devono precedere qualsiasi altro output dello script. In caso contrario si riceverebbe il seguente errore : "Warning: Cannot modify header information - headers already sent by (output started at...". Anche un solo spazio tra l'inizio del file dello script ed il tag &lt;?php costituirebbe un output e quindi causerebbe errore.

La semplice assegnazione $\_SESSION\['login\_effettuato'\] = true; renderà persistente l'informazione che il log-in è stato effettuato con successo.

Immaginiamo adesso come proteggere una pagina privata, ovvero accessibile solo agli utenti loggati. Lo script potrebbe cominciare nel seguente modo:

 ```php
<?php 
session_start();

if (!$_SESSION['login_effettuato'])
{
	echo 'Accesso non consentito.';
	exit();
}
```

Anche questa volta per prima cosa dobbiamo chiamare la funzione session\_start, altrimenti l'array $\_SESSION non sarà disponibile. Come è evidente, la verifica del contenuto di una variabile di sessione si effettua allo stesso modo di una qualsiasi variabile.

Anche l'eliminazione di una variabile di sessione si effettua in maniera identica a quanto accade per una comune variabile, cioè attraverso l'utilizzo della funzione [unset()](http://it2.php.net/manual/it/function.unset.php). Ad esempio la pagina per effettuare il log-out potrebbe cominciare così:

 ```php
<?php 
session_start();

unset($_SESSION['login_effettuato']);

echo 'Log-out effettuato correttamente.';
```

In questo esempio abbiamo usato una semplice variabile booleana (true/false) per memorizzare l'esito del log-in. Ovviamente qualsiasi variabile o struttura può essere memorizzata nell'array $\_SESSION. Anche gli oggetti possono essere salvati tra i dati di sessione a patto che la definizione della classe relativa preceda la funzione session\_start. Le uniche variabili che non possono essere memorizzate in $\_SESSION sono gli identificatori di risorsa come ad esempio l'id di una connessione a database o il puntatore di un file restituito dalla funzione [fopen()](http://it2.php.net/manual/it/function.fopen.php).

Nel caso si vogliano cancellare tutte i dati contenuti in una sessione è possibile procedere nel seguente modo, assegnando a $\_SESSION un array vuoto:

 ```php
<?php 

session_start();

// cancello tutti i dati di sessione
$_SESSION = array();
?>
```

In questo modo però la sessione sarà ancora attiva, anche se priva di dati. Per distruggere completamente una sessione bisogna eliminare il file sul server contenente i dati di sessione usando la funzione [session\_destroy()](http://www.php.net/manual/it/function.session-destroy.php) e cancellare l'eventuale cookie usato per far propagare l'id di sessione. L'esempio seguente è preso direttamente dalla documentazione ufficiale:

 ```php
<?php 
session_start();

// cancello tutti i dati di sessione
$_SESSION = array();

// Cancelliamo l'eventuale cookie di sessione
if (isset($_COOKIE[session_name()]))
{
	setcookie(session_name(), '', time()-42000, '/');
}

// distruggiamo la sessione
session_destroy();
?>
```

- - - - - -

### Funzioni per la gestione delle sessioni

Diverse sono le funzioni che il PHP mette a disposizione dello sviluppatore per la gestione e l'utilizzo delle sessioni. Nella maggior parte dei casi comunque non se ne farà un intenso uso poiché, come abbiamo visto nell'esempio precedente, operare con le sessioni significa il più delle volte operare con l'array globale $\_SESSION.

Di seguito ne riportiamo alcune:

[session\_destroy()](http://www.php.net/manual/it/function.session-destroy.php)

distrugge tutti i dati associati alla sessione corrente (che rimangono comunque accessibili per tutto lo script corrente)

[session\_id()](http://www.php.net/manual/it/function.session-id.php)

restituisce o imposta il valore del session id associato alla corrente sessione

[session\_name()](http://www.php.net/manual/it/function.session-name.php)

serve ad ottenere o ad impostare il nome della sessione corrente, ovvero il nome del cookie o della variabile passata via URL che hanno il compito di far propagare il session id

[session\_regenerate\_id()](http://www.php.net/manual/it/function.session-regenerate-id.php)

permette di generare un nuovo session id per la sessione corrente senza che i dati vengano perduti

[session\_save\_path()](http://www.php.net/manual/it/function.session-save-path.php)

se viene fornita una stringa, questa rappresenterà la nuova directory nella quale salvare i files contenenti i dati di sessione. Altrimenti restituisce l'attuale directory dei files di sessione

[session\_set\_cookie\_params()](http://www.php.net/manual/it/function.session-set-cookie-params.php), [session\_get\_cookie\_params() ](http://www.php.net/manual/it/function.session-get-cookie-params.php)

permettono di impostare e ottenere la validità in secondi dei cookies di sessione. Se impostata a 0 i cookies (e la sessione) scadranno alla chiusura del browser

[session\_set\_save\_handler()](http://www.php.net/manual/it/function.session-set-save-handler.php)

attraverso questa funzione è possibile comunicare a PHP di usare procedure alternative al normale sistema di memorizzazione dei dati di sessione su file di testo sul server

[session\_start()](http://www.php.net/manual/it/function.session-start.php)

inizializza una sessione, va posto all'inizio di ogni pagina che intende far uso delle variabili di sessione

[session\_write\_close()](http://www.php.net/manual/it/function.session-write-close.php)

questa funzione forza il PHP a salvare i dati e a terminare la sessione. Utile quando si realizzano siti con diversi frames che usano le sessioni poiché permette di sbloccare prima possibile il file di sessione sul server il cui accesso è consentito ad un solo frame (script) per volta

[session\_is\_registered()](http://www.php.net/manual/it/function.session-is-registered.php), [session\_register()](http://www.php.net/manual/it/function.session-register.php), [session\_unregister()](http://www.php.net/manual/it/function.session-unregister.php), [session\_unset()](http://www.php.net/manual/it/function.session-unset.php)

L'utilizzo di queste funzioni, tuttora disponibili, è da evitare poiché con le recenti versioni di PHP si preferisce gestire le sessioni attraverso l'interazione diretta con l'array $\_SESSION

- - - - - -

### Gestione alternativa delle sessioni

Come abbiamo detto fin qui, il PHP memorizza i dati di ogni singola sessione all'interno di un file di testo in una cartella del server. In realtà qualsiasi sistema di memorizzazione accessibile dal server può essere usato per questo scopo. Se vogliamo usufruire di un sistema alternativo di memorizzazione dobbiamo pagare lo scotto di realizzare personalmente le routine necessarie e di comunicarle al PHP. La registrazione di queste procedure avviene attraverso la funzione [session\_set\_save\_handler()](http://it.php.net/manual/it/function.session-set-save-handler.php) a cui vanno passati i nomi delle funzioni che si occuperanno di archiviare e recuperare i dati associati ad una sessione.

L'esempio seguente mostra come definire tali funzioni e come queste vengano chiamate dal PHP ad ogni evento legato alle sessioni. Per evidenziare l'attivazione del garbage collector abbiamo impostato la sua probabilità di avvio al 50% attraverso la funzione ini\_set.

 ```php
<?php 
// imposto la scadenza delle sessioni a 20 minuti
ini_set('session.gc_maxlifetime',20*60);

// imposto la probabilità di avvio del garbage collector al 50%
ini_set('session.gc_probability',50);
ini_set('session.gc_divisor',100);

// funzioni che gestiranno gli eventi delle sessioni
function s_open() {
	echo "- Apertura sessione<br>";
}

function s_close() {
	echo "- Chiusura sessione<br>";
}

function s_read($ses_id) {
	echo "- Lettura dati di sessione (session_id=$ses_id)<br>";
}

function s_write($ses_id, $data) {
	echo "- Scrittura dei dati di sessione:<br>
session_id=$ses_id<br>
dati: $data<br>";
}

function s_destroy($ses_id) {
	echo "- Distruzione dei dati di sessione<br>";
}

function s_gc($life) {
	echo "- Pulizia delle sessioni scadute (max:$life secondi)<br>";
}

// dichiaro quali funzioni gestiranno gli eventi delle sessioni
session_set_save_handler ('s_open','s_close','s_read','s_write','s_destroy','s_gc');

// per non avere problemi con i cookies che invierà la sessione
// evito di mostrare immediatamente l'output dello script
ob_start();

// avvio la sessione
session_start();

// memorizzo un valore nella sessione
$_SESSION['nome'] = 'Marco';

// adesso posso mostrare l'output dello script
ob_end_flush();
?>
```

Questa flessibilità delle sessioni ci permette ad esempio di utilizzare un database come sistema di memorizzazione. Si tratta di realizzare una tabella in cui ogni record rappresenta una sessione attiva. Questa tabella dovrà essere costituita da almeno 3 campi: uno per il session\_id che verrà usato come chiave primaria, uno per la data dell'ultimo accesso alla sessione ed uno che contiene i dati di sessione. Ovviamente dovremo anche scrivere le funzioni che avranno il compito di inserire e recuperare i dati dal database.

Diversi sono i vantaggi che possono derivare da una gestione personalizzata delle sessioni. In primo luogo avremo un incremento considerevole della sicurezza, poiché i dati di sessione non risiederanno su file potenzialmente accessibili da altri utenti. Inoltre potremo beneficiare di un maggiore controllo sulle politiche relative alla scadenza di una sessione. Inoltre potremo sapere con semplicità quanti utenti stanno visitando il nostro sito, semplicemente contando i record della tabella delle sessioni.

- - - - - -

### php.ini e sessioni

Diverse sono le impostazioni presenti nel php.ini che influenzano le sessioni. Quasi tutte queste impostazioni comunque possono essere alterate direttamente dall'interno di uno script PHP grazie alla funzione [ini\_set()](http://it.php.net/manual/it/function.ini-set.php). Di seguito riportiamo quelle principali, per l'elenco completo consultare [questa pagina](http://it.php.net/manual/it/ref.session.php).

**session.name**

Imposta il nome di default della sessione, ovvero il nome del cookie o del parametro passato via URL impiegati per la propagazione del session ID

**session.save\_path**

Definisce la stringa passata al gestore del salvataggio delle sessioni. Se le sessioni vengono salvate su file di testo questa stringa rappresenta il path in cui salvare i files. Il suo valore di default è '/temp' e su Windows questo crea qualche problema ai neofiti poiché questa cartella difficilmente esiste. Questa impostazione può essere modificata all'interno di uno script PHP con l'istruzione session\_save\_path(). E' importante che questa cartella non sia leggibile da altri utenti del server poiché in essa potrebbero venire registrate informazioni sensibili.

**session.auto\_start**

Specifica se le sessioni devono avviarsi automaticamente all'inizio di ogni pagina. Il suo valore di default è 0 ovvero disattivata.

**session.gc\_probability - session.gc\_divisor**

specificano la probabilità che la routine di pulizia delle sessioni (garbage collection) venga avviata. Il loro valore di default è rispettivamente 1 e 100 realizzando una probabilità di 1/100 ovvero dell' 1%. E' consigliabile impostare un valore basso per evitare una eccessiva attività sul server.

**session.gc\_maxlifetime**

specifica il numero di secondi dopo i quali i dati scadono e possono essere cancellati dalla routine di pulizia. Se vengono usati dei file di testo per la memorizzazione dei dati, è importante che il filesistem tenga traccia della data di modifica del file stesso. Questo ha causato alcuni problemi con Windows 98 - Me ed alcune versioni di PHP

**session.use\_cookies**

specifica se usare i cookies per memorizzare sul client il session id. Questa impostazione è per default abilitata (1).

**session.use\_only\_cookies**

specifica se i cookies saranno l'unico mezzo da usare per memorizzare il sessioni id sul client. Questo parametro, introdotto con PHP 4.3.0, previene possibili problemi di sicurezza legati al passaggio del session id via URL.

**session.cookie\_lifetime**

Specifica per quanti secondi sarà considerato valido il cookie inviato al client. Se impostato su 0 significa che il cookie avrà validità fino alla chiusura del browser dell'utente. Si noti che quando questo paramentro è impostato ad un valore diverso da 0, la validità del cookie viene rinnovata ad ogni richiesta dell'utente.

**session.use\_trans\_sid**

abilita l'aggiunta automatica del session id in tutti gli URL presenti nelle pagine inviate al client. Si ricordi che la propagazione del session id via URL è maggiormente soggetta a problemi di sicurezza. Infatti un utente potrebbe memorizzare nei preferiti un indirizzo contenente un session id o peggio inviarlo a qualcuno.