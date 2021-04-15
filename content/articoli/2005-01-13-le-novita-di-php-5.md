---
title: "Le novità di PHP 5"
author: "Giovanni Tomasicchio"
type: article
date: 2005-01-13T22:43:13+0000
description: "Le novità di PHP 5"
url: /articoli/le-novita-di-php-5/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "Java"
  - "DOM"
  - "exception"
  - "mysqli"
  - "SimpleXML"
  - "SPL"
  - "SQL"
  - "SQLite"
  - "web service"
  - "XML"
  - "XSL"
  - "XSLT"

  
---
 Niente panico! Nonostante il numero e la valenza delle novità introdotte con PHP 5 sia impressionante, questa nuova versione non stravolge il modo di lavorare con PHP. Tutto ciò che avete imparato a fare con PHP 4 rimane valido, salvo qualche piccola eccezione, anche per PHP 5.

 Perché allora si sente parlare di compatibilità degli script con PHP 5? Uno dei principali motivi per cui alcune applicazioni hanno problemi a girare con la nuova versione di PHP sta nella rinnovata gestione degli oggetti e nel modo in cui vengono passati come parametri alle funzioni. Fortunatamente le correzioni che è necessario apportare non comportano, nella maggior parte dei casi, la completa riprogettazione degli script.

 Ma andiamo con ordine. Le novità introdotte da PHP 5 possono essere raggruppate principalmte in due gruppi:

- **evoluzione del linguaggio**
   PHP 5 vede la maturazione del suo modello ad oggetti, l'introduzione della gestione degli errori attraverso il paradigma try/throw/catch ed altre interessanti caratteristiche.
- **miglioramento delle estensioni**
   La nuova release aggiorna le estensioni SAX, DOM e XSLT ed introduce le nuove SimpleXML e SOAP. Rinnova l'estensione per l'accesso a MySQL e supporta SQLite
 
 Le nuove caratteristiche di PHP 5 rafforzano i punti deboli e colmano alcune lacune di PHP 4, decretandone una sensibile maturazione. PHP 5 si presenta così più appetibile al mercato enterprise ed è ormai in grado di affrontare sfide sempre più impegnative.

 In questo articolo accenneremo solamente ad alcune delle svariate innovazioni e migliorie che PHP 5 porta con se, con il semplice scopo di accrescere la curiosità su questa release ma senza la pretesa di risultare esaustivi.

- - - - - -

###  Nuovo modello a oggetti

 PHP nasce come linguaggio procedurale, basato cioè su una struttura che prevede un programma principale e diverse funzioni da esso richiamate. Le versioni 3 e 4 di PHP introducono le classi ma queste si limitano ad aggregare metodi e proprietà e supportano solo alcune caratteristiche della programmazione ad oggetti (come l'ereditarietà). Inoltre lo Zend Engine (il cuore di PHP 3 e 4, ovvero il sistema che fa girare i nostri script) considera gli oggetti come tipi primitivi (stringhe, interi, ecc.). Questo approccio comporta una anomalia durante le assegnazioni di oggetti. Infatti gli oggetti, quando usati come argomento di funzioni e metodi, vengono passati per valore e non per riferimento, ovvero vengono copiati contrariamente a ciò che avviene nei più popolari linguaggi orientati agli oggetti.

 PHP 5 vede l'avvento dello Zend Engine II che, tra l'altro, riscrive l'intera infrastruttura alla base del modello ad oggetti introducendo gli *object handles* (una specie di puntatori, trasparenti al programmatore) che risolvono l'anomalia prima citata. Inoltre, ispirandosi principalmente al Java, PHP 5 introduce una grande quantità di nuovi strumenti per potenziare il suo modello ad oggetti. Vediamo quali sono le principali novità.

\- Introduzione dei modificatori di accesso **public**, **private** e **protected** sia per metodi che per proprietà con cui regolamentare l'accesso ad un oggetto. Le variabili membro e i metodi dichiarati public sono sempre accessibili, quelli dichiarati protected possono essere usati solo dagli oggetti istanziati dalla classe di appartenenza o da una che la eredita, quelli dichiarati private solo dagli oggetti istanzaiti dalla classe di appartenenza.

 ```php
class classe_test
{
	public $var1; /* proprietà pubblico */

	private $var2; /* proprietà privata :-) */

	protected $var3; /* proprietà protetta */

	public function function1() {
		/* metodo pubblico */
	}

	private function function2() {
	}

	protected function function3() {
		/* metodo protetto */
	}
}
```

\- Nuovo nome univoco per il metodo costruttore **\_\_construct()** di una classe. E' comunque ancora possibile usare il nome della classe di appartenenza.

 ```php
class classe_test
{
	function __construct() {
		/* costruttore della classe */
	}

	/* altri metodi */
}
```

\- Introdotto un metodo distruttore dal nome unico **\_\_destructor()**

 ```php
class classe_test
{
	function __destruct() {
		/* distruttore della classe */
	}

	/* altri metodi */
}
```

\- Introduzione delle **interfacce**. Grazie alle interfacce viene superato il vincolo della ereditarietà da una singola classe infatti una classe può implementare più interfacce.

 ```php
interface interfaccia_1
{
	/* metodi astratti */
	function funzione1();
}

interface interfaccia_2
{
	/* metodi astratti */
	function funzione2();
}

/* classe che implementa 2 interfacce */
class classe_test implements interfaccia_1, interfaccia_2
{
	function funzione1() {
		/* implementazione */
	}

	function funzione2() {
		/* implementazione */
	}
}
```

\- Introduzione dell'operatore **instance of** per verificare la classe di un oggetto

 ```php
if ($oggetto instance of utente) {
	echo'$ottetto è una istanza della classe utente';
}
```

\- Possibilità di dichiarare un **metodo** come **final** affinché non venga effettuato l'overload per esso dalle classe che lo ereditano

 ```php
class mia_classe
{
	final function mia_funzione() {
		/* questo metodo non potrà essere sovrascritto
		    dalle classi che lo ereditano*/
	}
}
```

\- Possibilità di dichiarare **classi** come **final** affinché non possano essere ereditate

 ```php
final class FinalClass
{
	/* ... */
}
```

\- Introduzione del metodo **\_\_clone()** che, se implementato, verrà richiamato durante il processo di copia di un oggetto. Può essere utilizzato per personalizzare la procedura di copia di un oggetto. Nel seguente esempio un oggetto che gestisce la connessione ad un database, quando viene clonato, conserva tutte le variabili membro originarie ma incrementa il suo id

 ```php
<?php
class database
{
	public $user = 'nome_utente';
	public $password = 'mia_password';
	public $id = 0;

	function __clone() {
		$this->id = $this->id + 1;
	}
}

$datalink_1 = new database();

$datalink_2 = clone $datalink_1;

echo 'user: '. $datalink_1->user ."n";
echo 'password: '. $datalink_1->password."n";
echo 'id: '. $datalink_1->id."n";

echo 'user: '. $datalink_2->user ."n";
echo 'password: '. $datalink_2->password."n";
echo 'id: '. $datalink_2->id."n";
?>

Risultato:
user: nome_utente
password: mia_password
id: 0
user: nome_utente
password: mia_password
id: 1
```

\- Possibilità di dichiarare delle costanti all'interno di una classe attraverso l'uso di **const**

 ```php
class mia_classe
{
	const PI_GRECO = 3.14;
}
```

\- **Proprietà statiche** attraverso l'uso di static

 ```php
class settings
{
	static $max_users = 5;
}

echo settings::$max_users;
```

\- **Metodi statici** che è possibile richiamare al di fuori di un oggetto

 ```php
class lib
{
	static function get_time() {
		return time();
	}
}

echo lib::get_time();
```

\- **Classi astratte** che non possono essere istanziate direttamente ma solo ereditate

 ```php
abstract class classe_base {
	/* ... */
}
```

\- **Metodi astratti** per i quali la definizione è lasciata alla classe che li eredita. Ovviamente una classe che contiene un metodo astratto deve essere dichiarata come abstract.

 ```php
abstract class classe_base {
	abstract function metodo_astratto();
}
```

\- **Type hinting**: possibilità di dichiarare la classe degli oggetti passati come parametri alle funzioni.

 ```php
class utente {/* ... */}

$oggetto = new utente();

function log_in(utente $oggetto) {
	/* se $oggetto non è di tipo "utente" verrà generato un errore */
}
```

\- **Deferenziazione** degli oggetti prodotti dai metodi.

 ```php
$oggetto->oggetto_restituito()->metodo();
```

\- Introduzione della funzione **\_\_autoload()** che viene automaticamente chiamato qualora si stia cercando di istanziare una classe non ancora definita.

 ```php
mia_classe.class.php 
<?php
echo 'caricata classe mia_classe';
class mia_classe {}
?>

test.php
<?php
function __autoload($nome_classe) {
include_once($nome_classe . '.class.php');
}

$oggetto = new mia_classe();
?>

Risultato:
caricata classe mia_classe
```

- - - - - -

###  Il paradigma try-throw-catch

 Con PHP 4 risulta difficile realizzare una netta separazione tra il codice di gestione delle eccezioni e le istruzioni della nostra applicazione. Spesso infatti si è costretti, dopo ogni operazione effettuata, a controllare il risultato restituito per verificarne l'esito.

 Anche se è possibile definire delle procedure di gestione globale degli errori (attraverso l'istruzione set\_error\_handler) queste non potranno mai essere sufficientemente specializzate per ogni singola categoria di eccezioni e quindi non potranno intervenire e rispondere in maniera adeguata.

 PHP 5 risolve questo problema introducendo il paradigma try-throw-catch, ovvero una struttura di controllo simile al meccanismo if-else ma progettato appositamente per la gestione delle eccezioni. Per chiarirne il funzionamento vediamo il seguente esempio:

 ```php
<?php 
try {
	$a = -1; // valore inaspettato
	if ( $a < 0 )
	throw new Exception('$a è negativo');

	// il codice che segue una eccezione non viene eseguito
	echo 'Questo testo non verrà mai visualizzato';
}
catch (Exception $e) {
	echo "Eccezione intercettata: ", $e, "n";
}
?>

Risultato:
Eccezione intercettata: exception 'Exception' with message '$a è negativo' in pathnomefile:riga
Stack trace:
#0 {main}
```

 Nel blocco **try** una istruzione lancia (**throw**) una eccezione ovvero istanzia un oggetto della classe Exception, definita all'interno dello stesso PHP 5 e perciò sempre disponibile. La gestione di questo oggetto, e quindi dell'eccezione, non viene implementata immediatamente ma rimandata al blocco **catch**.

 E' anche possibile definire delle classi personalizzate per la gestione delle eccezioni, estendendo la classe Exception. In questo modo è possibile avere più blocchi catch che possano distinguere tra i vari tipi di eccezione avvenuta reagendo in maniera più specializzata.

 La maggior parte delle funzioni interne di PHP non lancia delle eccezioni, comunque le nuove estensioni ne fanno uso. Per intercettare eccezioni che non sono state catturate da nessun blocco catch prima che termini lo script è possibile usare la funzione set\_exception\_handler().

- - - - - -

###  Standard PHP Library - Iteratori

 Parlando del paradigma try-throw-catch abbiamo visto come una eccezione venga rappresentata da un oggetto istanziato della classe Exception definita all'interno di PHP 5. La classe Exception infatti fa parte della Standard PHP Library (SPL) che consiste, come spiega il [manuale](http://it.php.net/spl), in un insieme di interfacce e classi realizzate per risolvere problemi standard. Questa libreria fa parte del core di PHP 5 ed è quindi sempre disponibile e non necessita di installazione.

 La SPL è una vera novità nel panorama PHP che ci aveva abituato a veder reinventare la ruota ad ogni nuovo progetto. Attraverso l'uso della SPL è possibile standardizzare l'approccio a determinate problematiche e questo comporta anche un più facile riutilizzo del codice tra diversi progetti. La Standard PHP Library è solo agli inizi infatti, oltre al problema delle eccezioni, risolve solo l'implementazione di iteratori. Purtroppo il manuale di PHP fornisce ancora poche informazioni circa l'uso della SPL, per maggiori informazioni è possibile comunque consultare la [documentazione](http://www.php.net/~helly/php/ext/spl/) prodotta dal suo ideatore [Marcus Boerger](http://marcus-boerger.de/)

 Lo script che segue elenca i file presenti in una cartella. Ad un ciclo foreach viene passato un oggetto ottenuto come istanza della classe ElencaFile. Questa classe estende l'iteratore DirectIterator appartenente alla SPL.

 ```php
<?php 
class ElencaFile extends DirectoryIterator
{
	function current()
	{
		return parent::getFileName();
	}

	function valid()
	{
		if ( parent::valid() )
		{
			if ( !parent::isFile() )
			{
				parent::next();
				return $this->valid();
			}
			return TRUE;
		}
		return FALSE;
	}

	function rewind()
	{
		parent::rewind();
	}
}

$cartella_corrente = new ElencaFile('./');

foreach ($cartella_corrente as $file)
{
	echo $file.'<br>';
}
?>
```

 PHP 5 è in grado di applicare un ciclo foreach() anche ad oggetti che non estendono iteratori. In questo caso l'iterazione verrà effettuata sulle proprietà pubbliche dell'oggetto.

 ```php
<?php 
class database
{
	public $user = 'nome_utente';
	protected $password = 'mia_password';
	private $id = 0;
}

$data = new database();

foreach ($data as $variabile => $contenuto)
{
	echo $variabile . ' = ' . $contenuto . "n";

}
?>

Risultato:
user = nome_utente
```

- - - - - -

###  Altre novità del linguaggio

 **Foreach con riferimenti**

 La struttura di controllo *foreach* è forse una delle più amate e più utilizzate dagli sviluppatori PHP grazie alla facilità con cui riesce ad attraversare un array. Come abbiamo già visto parlando degli iteratori, PHP 5 accresce le capacità di foreach rendendolo capace di accettare come argomento anche un oggetto.

 Ma le novità per questa struttura di controllo non finiscono qui. Con PHP 4 il ciclo foreach mostrava una limitazione: lavorando su una copia dell' array, qualsiasi modifica ad esso effettuata non ha un effetto persistente. Per modificare un array è quindi necessario utilizzare altri costrutti di PHP, come ad esempio i cicli for.

 PHP 5 supera questa limitazione permettendo l'uso di array passati per indirizzo attraverso l'uso di **&amp;**. L'esempio che segue mostra come, attraverso l'uso di tale tecnica, sia possibile impiegare questa struttura di controllo per modificare il contenuto di un array.

 ```php
<?php 
$vettore = array(1,2,3,4);

echo "Vettore originale: n";
print_r($vettore);

foreach ($vettore as &$valore)
{
	$valore = $valore * 2;
}

echo "Vettore dopo il foreach con &: n";
print_r($vettore);
?>

Risultato:
Vettore originale:
Array
(
	[0] => 1
	[1] => 2
	[2] => 3
	[3] => 4
)
Vettore dopo il foreach con &:
Array
(
	[0] => 2
	[1] => 4
	[2] => 6
	[3] => 8
)
```

 **Valori di default per parametri passati a funzioni per riferimento**

 PHP 5 permette di specificare dei valori di default anche per i parametri delle funzioni passati per riferimento:

 ```php
<?php 
function grassetto(& $stringa = null)
{
	if ($stringa === null)
	echo 'Nessuna stringa passata!!!';
	else
	$stringa = "<b>$stringa</b>";
}

$testo = 'Testo di prova';
echo $testo ."n";

grassetto($testo);
echo $testo ."n";

grassetto();
?>

Risultato:
Testo di prova
<b>Testo di prova</b>
Nessuna stringa passata!!!
```

- - - - - -

###  XML and Web Services

 PHP 4, per il trattamento di documenti XML, si appoggia su tre librerie sviluppate da terzi: il parser SAX [Expat](http://expat.sourceforge.net/), il parser DOM [libxml](http://www.xmlsoft.org/) e la libreria [Sablotron](http://www.gingerall.com/charlie/ga/xml/p_sab.xml?s=org) per le funzioni XSLT (XSL Transformation). Di queste tre solo il parser SAX può essere considerato affidabile al 100%, infatti l'estensione XSLT ha sofferto di alcuni problemi di incompatibilità tra le diverse piattaforme mentre il parser DOM è tuttora sperimentale ed è afflitto da diversi bachi.

 L'uso di tre distinte librerie ha comportato un notevole dispendio di energie agli sviluppatori di PHP che per la versione 5 hanno deciso di abbandonare Expat e Sablotron rimpiazzandoli con la libreria **libxml** che, come abbiamo detto, era già usata per il parser DOM.

 Questa potrebbe sembrare una scelta quanto meno strana, dati i noti problemi che questa libreria ha arrecato in passato. In realtà a ben vedere questi problemi non erano legati direttamente a libxml quanto piuttosto al suo utilizzo in PHP come parser DOM. Libxml risulta infatti il parser DOM più veloce in circolazione ed inoltre è in grado di soddisfare le più svariate necessità.

 Nonostante il nuovo parser DOM di PHP 5 sia ancora soggetto a sviluppo, le sue funzioni principali possono essere considerate stabili. Da sottolineare la sua capacità di maneggiare, anche se ancora con qualche imperfezione, i documenti HTML.

 Probabilmente però la più importante novità riguardante il trattamento di documenti XML introdotta da PHP 5 è la nuova estensione **SimpleXML**. Si tratta di un parser XML in parte simile al parser DOM poiché costruisce, a partire dagli elementi del documento XML, una struttura dati ad albero ma si differenzia da questo perché questo albero è costituito da tipi di dati nativi di PHP facilitandone così l'accesso. L'esempio che segue ne è una dimostrazione.

 ```php
<?php 
$xml_string = "<?xml version='1.0' standalone='yes'?>
<utenti>
	<utente>
		<nome>Mario</nome>
		<cognome>Rossi</cognome>
		<telefono type=\"casa\">123456789</telefono>
		<telefono type=\"mobile\">987654321</telefono>
	</utente>
</utenti>";

$xml = simplexml_load_string($xml_string);
print_r($xml)
?>

Risultato:
SimpleXMLElement Object
(
	[utente] => SimpleXMLElement Object
	(
		[nome] => Mario
		[cognome] => Rossi
		[telefono] => Array
		(
			[0] => 123456789
			[1] => 987654321
		)
	)
)
```

 Nel caso SimpleXML non sia in grado di effettuare particolari operazioni (in realtà anche il semplice esempio precedente mostra alcune limitazioni di SimpleXML) è sempre possibile importare il tutto in un albero DOM attraverso dom\_import\_simplexml() e, una volta effettuate le operazioni, reimportarlo con simplexml\_import\_dom()

 PHP 5 introduce inoltre una nuova estensione per il supporto di **SOAP** scritta in C che, rispetto alle precedenti soluzioni utilizzate da PHP 4 quasi tutte scritte in PHP, assicura maggiori prestazioni. Questa estensione SOAP implementa la maggior parte del protocollo SOAP 1.2 ed è tuttora in fase di intenso sviluppo ma ha tutti i presupposti per poter gareggiare in un prossimo futuro con le implementazioni JAVA e .NET.

- - - - - -

###  MySQLi

 MySQLi è la rinnovata estensione per la connessione a MySQL che permette di accedere alle nuove funzionalità introdotte da MySQL 4.1 e successivi. Diverse le nuove features tra cui:

- supporto al più efficiente protocollo binario introdotto con MySQL 4.1
- prepared statements
- bounding parameter
- supporto alla replicazione
 
 MySQLi inoltre permette, accanto ad un tradizionale paradigma procedurale, un approccio ad oggetti all'interazione con MySQL, come mostrato nel seguente esempio.

 ```php
<?php 
/* Connessione al server MySQL */
$mysqli = new mysqli('host', 'nome_utente', 'password', 'nome_db');

if (mysqli_connect_errno()) {
	printf("Non riesco a connettermi a MySQL. Errore: %sn", mysqli_connect_error());
	exit;
}

/* Invio una query al server */
if ($result = $mysqli->query('SELECT campo FROM tabella')) {

	/* Effettuo la fetch dei risultati */
	while( $row = $result->fetch_assoc() ){
		echo $row['campo'];
	}

	/* Cancello il result set e libero la memoria */
	$result->close();
}

/* Chiudo la connessione */
$mysqli->close();
?>
```

 MySQLi non supporta, diversamente dalla precedente versione, la connessione automatica e non usa il link di default al database se viene usato l'approccio procedurale.

###  SQLite

 Anche se il supporto a [SQLite](http://www.sqlite.org/) è stato già introdotto nelle ultime versioni di PHP 4.3.x, PHP 5 ne trae maggior beneficio grazie al supporto degli iteratori. SQLite è un database embedded, ovvero una libreria SQL che non necessita di un database server per il suo funzionamento e, contrariamente a quanto il nome suggerisce, è ricca di funzionalità avanzate come le viste, le transazioni e le sub-query. Nel seguente esempio viene mostrato come sia semplice con SQLite creare un database, inserire e recuperare dati da esso.

 ```php
<?php 
if ($db = sqlite_open('sqlite_db', 0666, $sqliteerror))
{
	sqlite_query($db, 'CREATE TABLE tabella (colonna varchar(15))');
	sqlite_query($db, "INSERT INTO tabella VALUES ('valore')");
	$result = sqlite_query($db, 'SELECT colonna FROM tabella');
	var_dump(sqlite_fetch_array($result));
}
else
{
	die($sqliteerror);
}
?>

Risultato:
array(2) {
	[0]=>
	string(6) "valore"
	["colonna"]=>
	string(6) "valore"
}
```

###  Tidy

 PHP 5 dispone anche dell'estensione [Tidy](http://tidy.sf.net/) che permette di effettuare diverse operazioni su documenti HTLM come il parsing, la diagnosi, la pulizia (mostrata nel seguente esempio) e la riparazione. Tidy supporta le eccezioni e può essere usata con un approccio sia procedurale che ad oggetti.

 ```php
<?php 
$tidy = tidy_parse_string("<B>Questa</I> è una <U>prova</B>");
tidy_clean_repair($tidy);
echo $tidy;
?>

Risultato:


<head>
<title></title>
</head>

<b>Questa</b> è una <u>prova</u> 
```

- - - - - -

###  Altre novità di PHP 5

 L'avvento dello Zend Engie II porta con se diversi benefici, molti dei quali trasparenti allo sviluppatore ma non per questo meno importanti. Tra questi una rinnovata e più performante gestione della memoria e un miglior supporto per gli ambienti multi-threaded.

 Notevolmente migliorata è anche l'interazione con i diversi componenti di Java, COM/DCOM e .NET che con PHP 5 vengono visti come oggetti nativi. Introdotta anche una estensione che permette di chiamare script Perl e usare oggetti e funzionalità native di questo linguaggio direttamente da PHP.

 Altra novità di PHP 5 è il supporto alla [riflessione](http://it.php.net/manual/it/language.oop5.reflection.php) (o introspezione) ovvero la capacità di ottenere informazioni sullo script durante la sua esecuzione in modo da poter effettuare un reverse-engineering di classi, metodi, interfacce ed estensioni.

 Nonostante sia evidente lo sforzo fatto per garantire la retrocompatibilità di PHP 5, questa release apporta alcune modifiche che potrebbero pregiudicare il funzionamento di script realizzati con la precedente versione. Oltre al già citato passaggio degli oggetti per indirizzo e non più per valore, PHP 5 presenta alcune incompatibilità. Maggiori informazioni a tal proposito si possono trovare nella [documentazione ufficiale](http://www.php.net/manual/en/migration5.incompatible.php).