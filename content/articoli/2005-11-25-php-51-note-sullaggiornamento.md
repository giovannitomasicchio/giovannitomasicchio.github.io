---
title: "PHP 5.1 - Note sull'aggiornamento"
author: "Giovanni Tomasicchio"
type: article
date: 2005-11-25T22:50:36+0000
description: "Le novità di PHP 5.1"
url: /articoli/php-51-note-sullaggiornamento/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "namespace"
  - "Oracle"
  - "sicurezza"
  - "mysqli"
  - "PDO"
  - "SPL"
  - "SQLite"

  
---
 L'uscita di PHP 5.1 segna una piccola svolta nel percorso di PHP, non solo per le nuove funzionalità introdotte ma anche per alcune variazioni del comportamento del PHP Engine, il cuore di PHP, e per la riorganizzazione di diverse estensioni.

 Per aiutare lo sviluppatore ad orientarsi nei cambiamenti introdotti e rendere il passaggio alla nuova release meno traumatico, sul sito ufficiale di PHP è stata pubblicata una [guida all'aggiornamento](http://www.php.net/README_UPGRADE_51.php) di cui riporto la traduzione.

1. [Variazioni nella gestione dei riferimenti](#cirh)
  1. [Panoramica](#overview)
  2. [Codice che funzionava sotto PHP 4.3, ma che ora fallisce](#cirh2)
  3. [Codice che era valido sotto PHP 4.3, ma che ora produce errori](#cirh3)
  4. [Codice che fallisce sotto PHP 4.3, ma ora funziona](#cirh4)
  5. [Codice che 'avrebbe dovuto funzionare' sotto PHP 5.0](#cirh5)
  6. [Avvisi che vanno e vengono](#cirh6)
2. [Lettura \[\]](#reading)
3. [instanceof, is\_a(), is\_subclass\_of(), catch](#is_a)
4. [Valori interi nei parametri di funzione](#int)
5. [Metodi privati astratti](#abstract)
6. [Modificatori di accesso nelle interfacce](#ami)
7. [Cambiamenti nelle regole di ereditarietà](#cir)
8. [Costanti di classe](#cc)
9. [Estensioni](#extensions)
  1. [Estensioni che sono scomparse dal nucleo di PHP](#extensions1)
  2. [Costanti di classe nelle nuove estensioni di PHP 5.1](#extensions2)
10. [Supporto per date e tempo](#date)
11. [Variazioni nel supporto ai database](#db)
  1. [Panoramica su PDO](#db1)
  2. [Cambiamenti nel supporto a MySQL](#db2)
  3. [Variazioni nel supporto a SQLite](#db3)
12. [Ulteriori informazioni sulla migrazione](#misc)
13. [Controllo degli errori E\_STRICT](#estrict)
 
##  <a id="cirh" name="cirh">1. Variazioni nella gestione dei riferimenti</a>

###  <a id="overview" name="overview">1a. Panoramica</a>

 Dal punto di vista del programmatore PHP, il cambiamento che più probabilmente influenzerà il vecchio codice riguarda il modo in cui vengono gestiti i riferimenti in tutte le versioni successive alla 4.4.0.
 Fino alla versione 4.3 inclusa, era possibile inviare, assegnare o restituire variabili tramite riferimento, quando invece dovevano essere restituite per valore, come nel caso di una costante, di un valore temporaneo (ad es. il risultato di un'espressione), o del risultato di una funzione che avrebbe dovuto essere restituito per valore, come nel seguente script:

 ```php
<?php 
$foo = "123";

function return_value() {
	global $foo;
	return $foo;
}

$bar = &return_value();
?>
```

 Sebbene questo codice normalmente fornisce i risultati attesi sotto PHP 4.3, in generale il risultato è indefinito. Il Motore Zend non potrebbe agire correttamente su questi valori trattandoli come riferimenti. Questo baco poteva ed ha effettivamente condotto a vari problemi di corruzione della memoria difficili da riprodurre, soprattutto nel caso di grossi script.
 In PHP 4.4.0, 5.0.4 ed in tutte le versioni successive, il Motore è stato corretto in modo da riconoscere quando l'operatore di riferimento viene usato su di un valore che non dovrebbe essere referenziato. In questi casi viene utilizzato il valore effettivo, ed è visualizzato un messaggio di avvertimento. Il messaggio prende la forma di un E\_NOTICE in PHP 4.4.0 e successivi, e di un E\_STRICT in PHP 5.0.4 e successivi.
 Il codice che potenzialmente poteva produrre corruzione di memoria non può più farlo. Comunque qualche vecchio script potrebbe di conseguenza funzionare in modo diverso da quello atteso.

###  <a id="cirh2" name="cirh2">1b. Codice che funzionava sotto PHP 4.3, ma che ora fallisce</a>

 ```php
<?php 
function func(&$arraykey) {
	return $arraykey; // function returns by value!
}

$array = array('a', 'b', 'c');
foreach (array_keys($array) as $key) {
	$y = &func($array[$key]);
	$z[] =& $y;
}

var_dump($z);
?>
```

 Facendo girare lo script precedente sotto qualsiasi versione di PHP che precede la correzione della gestione dei riferimenti, si produrrebbe questo output:

 ```

array(3) {
   [0]=>
   &string(1) "a"
   [1]=>
   &string(1) "b"
   [2]=>
   &string(1) "c"
 }
```

 Con la correzione della gestione dei riferimenti, lo stesso codice produrrebbe questo risultato:

 ```

array(3) {
   [0]=>
   &string(1) "c"
   [1]=>
   &string(1) "c"
   [2]=>
   &string(1) "c"
 }
```

 Questo accade perché, seguendo i cambiamenti, func() assegna per valore. Il valore di $y è ri-assegnato ed il collegamento per riferimento è preservato da $z. Prima della correzione, il valore era assegnato per riferimento, portando $y ad essere ri-collegato ad ogni assegnazione. Il tentativo di legare ad un valore temporaneo attraverso il riferimento era la causa della corruzione della memoria.
 Quel codice può essere fatto funzionare in modo identico sia nelle versione di PHP precedenti che in quelle successive alla correzione della gestione dei riferimenti. La firma di func() può essere modificata in modo da restituire tramite riferimento, oppure l'assegnazione per riferimento può essere rimossa dal risultato di func().

 ```php
<?php 
function func() {
	return 'function return';
}

$x = 'original value';
$y =& $x;
$y = &func();
echo $x;
?>
```

 In PHP 4.3 $x sarebbe 'original value', mentre dopo le variazioni sarebbe 'function return', ricordate che dove la funzione non restituisce il valore tramite riferimento, l'assegnazione del riferimento è convertita in una regolare assegnazione. Anche questa volta è possibile allineare il comportamento dello script, forzando func() a restituire il risultato tramite riferimento oppure eliminando l'assegnazione tramite riferimento.

###  <a id="cirh3" name="cirh3">1c. Codice che era valido sotto PHP 4.3, ma che ora produce errori</a>

 ```php
<?php 
class Foo {

	function getThis() {
		return $this;
	}

	function destroyThis() {
		$bar =& $this->getThis();
	}
}

$bar = new Foo();
$bar->destroyThis();
var_dump($bar);
?>
```

 In PHP 5.0.3, $bar vale NULL invece di essere un oggetto. Ciò succede perché getThis() restituisce un risultato tramite valore, ma il valore nell'esempio è assegnato tramite riferimento. Sebbene adesso funzioni nel modo atteso, questo è in realtà un codice invalido che lancia un E\_NOTICE sotto PHP 4.4 o un E\_STRICT sotto PHP 5.0.4 e successivi.

###  <a id="cirh4" name="cirh4">1d. Codice che fallisce sotto PHP 4.3, ma ora funziona</a>

 ```php
<?php 
function &f() {
	$x = "foo";
	var_dump($x);
	print "$x\n";
	return($a);
}

for ($i = 0; $i < 3; $i++) {
	$h = &f();
}
?>
```

 In PHP 4.3 la terza chiamata a var\_dump produce NULL, ciò è dovuto alla corruzione della memoria causata dalla restituzione di un valore non inizializzato tramite riferimento. Questo è un codice valido in PHP 5.0.4 e successivi, ma produce errori nelle precedenti versioni di PHP.

 ```php
<?php 
$arr = array('a1' => array('alfa' => 'ok')); 
$arr =& $arr['a1']; 
echo '-'.$arr['alfa']."-\n"; 
?>
```

 Prima di PHP 5.0.5 non era possibile assegnare un array tramite riferimento in questo modo. Ora è possibile.

###  <a id="cirh5" name="cirh5">1e. Codice che 'avrebbe dovuto funzionare' sotto PHP 5.0</a>

 Ci sono un paio di bachi riportati sotto PHP 5.0 prima della correzione della gestione dei riferimenti che adesso 'funzionano'. Comunque, in entrambi i casi vengono lanciati degli errori da PHP 5.1, poiché il codice non è comunque corretto. Restituire valori tramite riferimento utilizzando self:: in generale ora funziona ma produce un avviso E\_STRICT, e sebbene il risultato può variare effettuando assegnazioni per riferimento ad un oggetto sovraccaricato, otterrete ancora un E\_ERROR quando lo provate, anche quando l'assegnazione stessa sembra funzionare.

###  <a id="cirh6" name="cirh6">1f. Avvisi che vanno e vengono</a>

 ```php
<?php 
function & foo() {
	$var = 'ok';
	return $var;
}

function & bar() {
	return foo();
}

$a =& bar();
echo "$a\n";
?>
```

 Chiamate annidate a funzioni che restituiscono valori tramite riferimento sono valide sotto PHP 4.3 e PHP 5.1 ma lanciano un ingiustificato E\_NOTICE o E\_STRICT con le altre versioni.

##  <a id="reading" name="reading">2. Lettura \[\]</a>

 ```php
<?php 
class XmlTest {

	function test_ref(&$test) {
		$test = "ok";
	}

	function test($test) { }

	function run() {
		$ar = array();
		$this->test_ref($ar[]);
		var_dump($ar);
		$this->test($ar[]);
	}
}

$o = new XmlTest();
$o->run();
?> 
```

 Questo esempio avrebbe dovuto lanciare sempre un E\_ERROR visto che \[\] non può essere utilizzato per leggere in PHP. Questo è un codice invalido da PHP 4.4.2 e PHP 5.0.5 in poi.

##  <a id="is_a" name="is_a">3. instanceof, is\_a(), is\_subclass\_of(), catch </a>

 In PHP 5.0, is\_a() è stato deprecato e rimpiazzato dall'operatore 'instanceof'. C'erano alcune questioni aperte dall'iniziale implementazione di 'instanceof', che dipendevano dalla ricerca delle classi mancanti da parte di \_\_autoload(). Se la classe non fosse stata presente, 'instanceof' avrebbe lanciato un E\_ERROR fatale dovuto al tentativo fallito di \_\_autoload() nel trovare tale classe. Lo stesso accadeva per l'operatore 'catch' e la funzione is\_subclass\_of(), per lo stesso motivo.
 Nessuna di queste funzioni o operatori chiama \_\_autoload() in PHP 5.1, e l'utilizzo di class\_exists() come espediente nel codice scritto per PHP 5.0, benché non crei alcun problema, non è più necessario.

##  <a id="int" name="int">4. Valori interi nei parametri di funzione</a>

 Con l'avvento di PHP 5.0, è stato introdotto nelle API un nuovo sistema di analisi dei parametri che viene utilizzato da un gran numero di funzioni PHP. In tutte le versioni tra la 5.0 e la 5.1, il trattamento dei valori interi era molto rigoroso, tanto che avrebbe rigettato valori numerici non correttamente formattati quando una funzione PHP si aspettava un intero. Queste verifiche ora sono state rese meno rigorose in modo da supportare stringhe numeriche non ben-formattate come " 123" e "123 ", e non produrranno più il fallimento come succedeva con PHP 5.0. Comunque, per promuovere la sicurezza del codice e la validazione dell'input, le funzioni PHP produrranno un E\_NOTICE quando tali stringhe sono passate come interi.

##  <a id="abstract" name="abstract">5. Metodi privati astratti</a>

 I metodi privati astratti erano supportati tra PHP 5.0.0 e PHP 5.0.4, ma sono stati successivamente aboliti a causa della mutua esclusività dei comportamenti 'privato' e 'astratto'.

##  <a id="ami" name="ami">6. Modificatori di accesso nelle interfacce</a>

 Sotto PHP 5.0, le dichiarazioni delle funzioni nelle interfacce erano trattate nello stesso modo delle dichiarazioni di funzioni nelle classi. Ciò fino all'Ottobre 2004, quando nella dichiarazione delle funzioni di interfaccia è stato autorizzato solo il modificatore di accesso 'public'. Dall'Aprile 2005 , prima dell'uscita di PH 5.0b1 , anche il modificatore 'static' è stato autorizzato. Comunque, i modificatori 'protected' e 'private' non lanceranno un E\_ERROR, come farà invece il modificatore 'abstract'. Si noti che questa variazione non dovrebbe interessare il vostro codice esistente, poiché nessuno di questi modificatori ha senso nel contesto delle interfacce.

##  <a id="cir" name="cir">7. Cambiamenti nelle regole di ereditarietà</a>

 Sotto PHP 5.0 era possibile avere una dichiarazione di funzione in una classe derivata che non combaciava con la dichiarazione della stessa funzione nella classe base, ad es.

 ```php
class Base {
	function &return_by_ref() {
		$r = 1;
		return $r;
	}
}

class Derived extends Base {
	function return_by_ref() {
		return 1;
	}
}
```

 Questo codice causerà la generazione di un errore E\_STRICT in PHP 5.1.

##  <a id="cc" name="cc">8. Costanti di classe</a>

 Sotto PHP 5.0 il seguente codice era valido:

 ```php
<?php 
class test {
	const foobar = 'foo';
	const foobar = 'bar';
}
?> 
```

 Sotto PHP 5.1, la ri-definizione di una costante di classe lancerà un fatale E\_ERROR.

##  <a id="extensions" name="extensions">9. Estensioni</a>

###  <a id="extensions1" name="extensions1">9a. Estensioni che sono scomparse dal nucleo di PHP</a>

 Una delle prime cose che probabilmente noterete scaricando PHP 5.1 è che molte delle estensioni sono scomparse. Le estensioni ancora attivamente sviluppate sono disponibili in PECL (PHP Extension Community Library), all'indirizzo [http://pecl.php.net](http://pecl.php.net/). I binari per Windows vengono regolarmente compilati, ed è possibile procurarsi i binari delle estensioni PECL compilati per PHP 5.1 da [http://pecl4win.php.net/list.php/5\_1](http://pecl4win.php.net/list.php/5_1).

 ```

Estensioni          Alternativa/stato
=========           ========================
ext/cpdf            pecl/pdflib
ext/dbx             pecl/dbx
ext/dio             pecl/dio
ext/fam             not actively maintained
ext/ingres_ii       pecl/ingres
ext/ircg            not actively maintained
ext/mcve            pecl/mcve
ext/mnogosearch     not actively maintained
ext/oracle          ext/oci8 or ext/pdo_oci
ext/ovrimos         not actively maintained
ext/pfpro           not actively maintained - alternatives at
                    http://pecl.php.net/packages.php?catpid=18&catname=Payment
ext/w32api          pecl/ffi
ext/yp              not actively maintained
sapi/activescript   http://pecl4win.php.net/ext.php/php5activescript.dll (PECL package)
                    or pecl/activescript (CVS)
```

 I moduli in PECL che non sono attivamente sviluppati (non sono stati supportati per un certo tempo, non c'è attualmente un attivo lavoro di manutenzione su di esse, e non hanno nessuna versione di package PECL), sono ancora disponibili in CVS su <http://cvs.php.net/pecl/>. Comunque i moduli non rilasciati da PHP sono per loro natura non supportati, ed i risultati potranno essere diversi quando cercate di installarli o usarli.

###  <a id="extensions2" name="extensions2">9b. Costanti di classe nelle nuove estensioni di PHP 5.1</a>

 Le API del Motore Zend permettono agli sviluppatori di dichiarare costanti nelle classi in estensioni orientate agli oggetti. Nuove estensioni scritte per PHP 5.1, incluse SPL, PDO, ext/XMLReader ed ext/date, hanno le loro costanti nel formato

 PDO::CLASS\_CONSTANT

 piuttosto che nel formato C

 PDO\_CLASS\_CONSTANT

 in modo da minimizzare la confusione del namespace globale in PHP.

 Si noti che la nuova classe Date esiste al momento solamente per permettere all'estensione 'core' delle date di aderire alla convenzione qui sopra, sebbene siano state pianificate per il futuro ulteriori funzionalità per la classe.

##  <a id="date" name="date">10. Supporto per date e tempo </a>

 Il supporto a date/time è stato completamente riscritto in PHP 5.1, che non usa più le impostazioni del sistema per conoscere il fuso orario nelle operazioni. Verranno invece impiegati, nel seguente ordine:

- il fuso orario settato dalla funzione date\_default\_timezone\_set() (se impiegata)
- la variabile d'ambiente TZ (se non è vuota)
- l'opzione date.timezone nel php.ini (se impostata)
- supposizione "magica" (se il sistema operativo la supporta)
- se nessuna delle opzioni precedenti ha successo, UTC
 
 Per assicurare accuratezza (ed evitare avvisi E\_STRICT), è necessario definire il proprio fuso orario nel php.ini usando il seguente formato:
 date.timezone = Europe/London
 I fusi orari supportati sono elencati, in questo formato nel manuale PHP su <http://www.php.net/manual/en/timezones.php>.

##  <a id="db" name="db">11. Variazioni nel supporto ai database</a>

###  <a id="db1" name="db1">11a. Panoramica su PDO </a>

 PHP Data Objects (PDO) è stato introdotto in PHP 5.0 come una estensione PECL, ed è diventato parte della distribuzione di PHP in PHP 5.1 L'estensione PDO offre una interfaccia omogenea per l'accesso ai database, ed è usata al fianco di specifici driver PDO. Ogni driver può anche avere specifiche funzioni di gestione dei database, ma le funzionalità di base quali l'invio delle queries ed il prelevamento dei risultati sono coperte dalle funzioni di PDO, attraverso l'utilizzo del driver richiamati nel PDO::\_\_construct().
 Si noti che l'estensione PDO, ed i suoi driver, sono da compilare preferibilmente come estensioni condivise. Ciò permetterà semplici aggiornamenti da PECL, senza costringervi a ricompilare tutto il PHP.
 Con la versione PHP 5.1, PDO è più che pronto per un test diffuso e può essere adottato in molte situazioni. Comunque, è importante capire che PDO ed i suoi driver sono relativamente giovani e possono essere deficitari di specifiche caratteristiche di un database; valutate PDO con cautela prima di usarlo in nuovi progetti.
 Il vecchio codice conterà sulle pre-esistenti estensioni per database, che sono ancora supportate.
 Ci sono informazioni più approfondite sull'estensione PDO nel manuale su <http://www.php.net/manual/ref.pdo.php>.

###  <a id="db2" name="db2">11b. Cambiamenti nel supporto a MySQL</a>

 In PHP 4 era incorporato il supporto a MySQL 3. Con la versione 5.0 di PHP c'erano due estensioni di MySQL, chiamate 'mysql' e 'mysqli', progettate per supportare rispettivamente MySQL &lt; 4.1 e MySQL 4.1 e successivi. Con l'introduzione di PDO, che fornisce un'interfaccia molto veloce a tutte le API dei database supportati da PHP, il driver PDO\_MySQL può supportare tutte le versioni correnti (MySQL 3, 4 e 5) nel codice PHP scritto per PDO, a seconda della versione della libreria MySQL usata durante la compilazione. Le estensioni più vecchie di MySQL rimangono in vigore per ragioni di retro-compatibilità, ma non sono abilitate di default.

###  <a id="db3" name="db3">11c. Variazioni nel supporto a SQLite</a>

 In PHP 5.0 era fornito il supporto a SQLite 2 attraverso l'estensione sqlite, che era disponibile anche come estensione PECL in PHP 4.3 e PHP 4.4. Con l'introduzione di PDO, l'estensione sqlite raddoppia per agire come driver 'sqlite2' per PDO; per questo motivo l'estensione sqlite in PHP 5.1 ha una dipendenza verso l'estensione PDO.
 PHP 5.1 porta con se diverse interfacce alternative a sqlite:
 l'estensione sqlite fornisce la classica API procedurale e ad oggetti che potete aver usato nelle versioni precedenti di PHP. Inoltre fornisce il driver PDO 'sqlite2', che vi permette di accedere ai precedenti database SQLite 2 utilizzando le API PDO.
 PDO\_SQLITE fornisce il driver per la versione 3 di 'sqlite'. La versione 3 di SQLite è notevolmente superiore alla versione 2, ma i formati dei file delle due versioni non sono compatibili.
 Se il vostro progetto basato su SQLite è già scritto e funziona con le precedenti versioni di PHP, allora potete continuare ad usare ext/sqlite senza problemi, ma avrete bisogni di abilitare esplicitamente sia PDO che sqlite. I nuovi progetti dovrebbero usare PDO ed il driver 'sqlite' (versione 3), visto che è più veloce rispetto a SQLite 2, ha un migliorato bloccaggio concorrente e supporta sia i prepared statements che i campi binari in maniera nativa.
 Dovrete abilitare PDO ad usare l'estensione SQLite. Se volete compilare l'estensione PDO come un estensione condivisa, anche l'estensione SQLite deve essere compilata nello stesso modo. Vale la stessa regola per ogni estensione che fornisce un driver PDO.

##  <a id="misc" name="misc">12. Ulteriori informazioni sulla migrazione</a>

 Per informazioni generali circa la migrazione da PHP 4 a PHP 5, prego riferirsi alla sezione pertinente nel manuale PHP su <http://www.php.net/manual/migration5.php>.

##  <a id="estrict" name="estrict">13. Controllo degli errori E\_STRICT</a>

 Se avete un singolo script da controllare, potete raccogliere gli errori E\_STRICT utilizzando la linea di comando di PHP:

 php -d error\_reporting=4095 -l script\_to\_check.php

 Per progetti più ampi, lo script qui sotto garantirà lo stesso risultato:

 ```bash
#!/bin/sh

directory=$1

shift

# These extensions are checked
extensions="php inc"

check_file ()
{
  echo -ne "Doing PHP syntax check on $1 ..."

  # Options:
  ERRORS=`/www/php/bin/php -d display_errors=1 -d html_errors=0 -d error_prepend_string=" " -d error_append_string=" " -d error_reporting=4095 -l $1 | grep -v "No syntax errors detected"`

  if test -z "$ERRORS"; then
    echo -ne "OK."
  else
    echo -e "Errors found!\n$ERRORS"
  fi

  echo
}

# loop over remaining file args
for FILE in "$@" ; do
  for ext in $extensions; do
     if echo $FILE | grep "\.$ext$" > /dev/null; then
       if test -f $FILE; then
         check_file "$FILE"
       fi
     fi
  done
done
```