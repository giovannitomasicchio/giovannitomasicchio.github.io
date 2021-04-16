---
title: "Variabili e costanti"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:31:48+0000
description: "Variabili e costanti in PHP"
url: /corsi/corso-php-base/variabili-e-costanti/
weight: 15
categories:
  - Corso PHP base
  
---
 Per capire il significato e l'utilità delle variabili risolviamo questo semplice problema di geometria: calcolare l'area di un rettangolo avente base uguale a 5 cm e l'altezza paria 3 cm. Svolgimento:

 base = 5

 altezza = 3

 area = base x altezza

 Adesso analizziamo il seguente script, che vi consiglio di provare sul vostro PC, e cerchiamo le analogie con il precedente problema:

 ```php
<?php
$base = 5;
$altezza = 3;
$area = $base * $altezza;
echo $area;
?>
```

 Se eseguiamo questo script ci verrà visualizzato il numero 15. Nulla di particolarmente interessante anche se da queste poche righe possiamo imparare parecchio.

- Come accade spesso nella soluzione di problemi matematici, anche in PHP si adoperano dei nomi (base, altezza, area) al posto dei valori delle grandezze in gioco. Questi nomi vengono chiamati **variabili** e servono da contenitori di valori che facilitano la risoluzione del problema. Le variabili si chiamano così perché il loro contenuto può essere modificato durante l'esecuzione del programma.
- Una variabile in PHP è costituita da un nome arbitrario preceduto dal simbolo **$** ed è buona norma che tale nome ci ricordi la natura dei dati contenuti.
- Il contenuto di una variabile, ovvero il suo valore, può essere inserito direttamente ($base = 5) o a seguito di una operazione, ad esempio una moltiplicazione ($area = $base \* $altezza).
- Non è necessario inizializzare o dichiarare una variabile prima di utilizzarla.
 
 Lo scopo delle variabili è quello di astrarre e generalizzare l'algoritmo, ovvero i passi necessaria alla soluzione di un problema. Nell'esempio precedente se le dimensioni del rettangolo dovessero cambiare non dovremo riscrivere lo script ma semplicemente aggiornare il valore delle variabili $base e $altezza. L'utilità delle variabili risulterà ancora più evidente quando dovremo gestire dei dati inviati dall'utente. In questi casi non potremo conoscere a priori tali valori e dovremo quindi lavorare esclusivamente con delle variabili.

 Ma le variabili in PHP non si limitano a contenere solo numeri. Il loro valore può essere ad esempio una stringa:

 ```php
<?php
$messaggio = "Questa è una stringa";
echo $messaggio;
?>
```

 E' interessante notare che stavolta la funzione echo non riceve la stringa da mostrare tra virgolette ma attraverso la variabile $messaggio. La funzione echo, come tutte le altre funzioni del PHP, lavora sul contenuto delle variabili che gli vengono passate.

 Osserviamo infine come non è stato necessario specificare il tipo di dato (stringa, numero, ecc.) che avremmo inserito in una variabile, come invece accade in altri linguaggi di programmazione, poiché questo viene riconosciuto al volo dal PHP. Per lo stesso motivo potremmo assegnare ad una variabile prima un valore numerico e successivamente una stringa.

###  Costanti

 Oltre alle variabili, il PHP mette a disposizione anche un altro tipo di contenitore di dati: le **costanti**. Queste non usano il simbolo $ all'inizio del loro nome e per assegnare loro un valore è necessario utilizzare un'apposita funzione dal nome **define**. Il seguente esempio definisce una costante di nome TASSO\_LIRA\_EURO a cui viene assegnato il valore 1936.27

 ```php
<?php
define("TASSO_LIRA_EURO", 1936.27);
echo TASSO_LIRA_EURO;
?>
```

 Le costanti devono questo nome al fatto che non possono essere modificate, al contrario di ciò che è possibile fare con le variabili.