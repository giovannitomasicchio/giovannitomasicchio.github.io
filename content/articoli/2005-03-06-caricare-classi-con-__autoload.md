---
title: "Caricare classi con __autoload()"
author: "Giovanni Tomasicchio"
type: article
date: 2005-03-06T00:19:09+0000
description: "Caricare automaticamente le classi in PHP con __autoload"
url: /articoli/caricare-classi-con-__autoload/
categories:
  - Articoli
tags:
  - "exception"
  - "XML"

  
---
 Quando un progetto PHP orientato agli oggetti inizia a crescere e le righe di codice aumentano a dismisura lo sviluppatore inizia a mettere le definizioni di ciascuna classe in files distinti, salvati magari in una cartella apposita. Questa è certamente una buona norma ma porta ad un proliferare di istruzioni di inclusione che spesso vengono poste all'inizio delle pagine. Alcune di queste inclusioni possono risultare inutili se caricano classi che verranno istanziate sono in particolari situazioni.

 PHP 5 offre una soluzione estremamente elegante ed efficace al problema su detto attraverso la funzione **[\_\_autoload()](http://www.php.net/manual/en/language.oop5.autoload.php)**. In pratica si tratta semplicemente di definire una funzione con il nome \_\_autoload che verrà chiamata ogni volta che si cerca di istanziare un oggetto da una classe non ancora definita.

 Se ad esempio nel nostro script è presente l'istruzione $oggetto = new mia\_classe(); ma la classe mia\_classe non è stata ancora definita, allora il PHP chiama (se presente) la funzione \_\_autoload() passandogli come parametro il nome della classe mia\_classe. Se questa funzione riesce a caricare la classe in questione allora lo script procede regolarmente, altrimenti viene lanciata una eccezione che però non può essere raccolta da un blocco catch data la gravità dell'errore.

 Vediamo un esempio:

 ```
<pre class="brush: php">
function __autoload($class_name)
{
   require $class_name . '.php';
}
```

 Con la funzione riportata qui sopra, ogni volta che si cercherà di istanziare una classe non ancora definita verrà caricato (se esiste) il file che porta proprio il nome della classe e che dovrà contenerne la definizione.

 Si può pensare di estendere questa funzione permettendole di caricare i files da cartelle diverse a seconda del nome della classe da istanziare. Ad esempio la seguente funzione cercherà la definizione di una ipotetica classe "common\_xml\_parser" nel file parser.php della cartella common/xml/

 ```
<pre class="brush: php">
function __autoload($class_name)
{
  $file_name = strreplace('_', '/', $class_name) . '.php';
  require $file_name;
}
```