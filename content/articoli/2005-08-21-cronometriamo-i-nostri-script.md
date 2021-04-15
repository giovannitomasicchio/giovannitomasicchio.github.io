---
title: "Cronometriamo i nostri script"
author: "Giovanni Tomasicchio"
type: article
date: 2005-08-21T12:32:55+0000
description: "Misurare il tempo di esecuzione di uno script PHP"
url: /articoli/cronometriamo-i-nostri-script/
categories:
  - Articoli
tags:

  
---
 Sicuramente vi sarà capitato di vedere in un sito, magari in fondo ad una pagina, l'indicazione del tempo di esecuzione dello script che l'ha generata. Questa informazione, mostrata a volte solo per motivi estetici, può invece essere un buon indicatore dello stato di salute del nostro server o della pesantezza dei nostri script. Cronometrare uno script è molto semplice, vediamo come fare.

 Tutta la procedura si basa sulla funzione [microtime()](http://it2.php.net/microtime) che ci fornisce l'istante corrente (UNIX timestamp) con una precisione al microsecondo. Purtroppo però microtime restituisce tale istante sotto forma di una stringa del tipo "microsecondi secondi", che quindi non può essere usata immediatamente per effettuare calcoli.

 Conviene quindi realizzare una funzione che estragga i secondi ed i microsecondi dall'output di microtime e li restituisca sotto forma di numero con la virgola (float). Si potrebbe procedere in questo modo:

 ```php
function microtime_float()
{
    list($usec, $sec) = explode(" ", microtime());
    return ((float)$usec + (float)$sec);
}
```

 Risolto questo problema, per calcolare il tempo di esecuzione non ci resta che memorizzare l'istante di inizio e fine dello script e farne la differenza. Ecco un esempio:

 ```php
<?php
// restituisce l'istante corrente
function microtime_float()
{
    list($usec, $sec) = explode(" ", microtime());
    return ((float)$usec + (float)$sec);
}

// memorizzo l'istante di inizio dello script
$inizio = microtime_float();

// -----------------------------------
// inizia lo script vero e proprio ...
// ... una pausa di un secondo
sleep(1);
// fine dello script da cronometrare
// -----------------------------------

// memorizzo l'istante di fine dello script
$fine = microtime_float();

// il tempo impiegato dallo script si calcola come
// differenza dell'istante di fine meno quello di inizio
$tempo_impiegato = $fine - $inizio;

// formatto il $tempo_impiegato
$tempo = number_format($tempo_impiegato,5,',','.');

echo "Tempo impiegato dallo script: $tempo secondi";
?>
```

 Con la funzione [number\_format](http://it2.php.net/manual/it/function.number-format.php) abbiamo impostato il numero dei decimali da mostrare ed il carattere (la virgola) per separarli dalle unità.

 Con PHP 5 l'output della funzione microtime è stato rivisto ed ora, passando semplicemente come parametro il valore true, restituisce l'istante corrente come un numero decimale e non come una stringa. In questo modo la funzione mostrata precedentemente non serve più. Vediamo quindi come cronometrare uno script in PHP 5.

 ```php
<?php
// memorizzo l'istante di inizio dello script
$inizio = microtime(true);

// -----------------------------------
// inizia lo script vero e proprio ...
// ... una pausa di un secondo
sleep(1);
// fine dello script da cronometrare
// -----------------------------------

// memorizzo l'istante di fine dello script
$fine = microtime(true);

// il tempo impiegato dallo script si calcola come
// differenza dell'istante di fine meno quello di inizio
$tempo_impiegato = $fine - $inizio;

// formatto il $tempo_impiegato
$tempo = number_format($tempo_impiegato,5,',','.');

echo "Tempo impiegato dallo script: $tempo secondi";
?>
```

 Utilizzando questo approccio è possibile calcolare anche degli intertempi, cioè i tempi di esecuzione di singole parti dei nostri script. Ad esempio potremmo calcolare il tempo necessario all'esecuzione delle query, per confrontarlo con il tempo totale o per ottimizzare le query più complesse.