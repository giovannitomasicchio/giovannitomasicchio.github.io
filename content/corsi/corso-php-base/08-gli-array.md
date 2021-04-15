---
title: "Gli array"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:32:31+0000
description: "Gli Array in PHP"
url: /corsi/corso-php-base/gli-array/
categories:
  - Corso PHP base
  
---
Quando abbiamo parlato delle variabili abbiamo detto che queste sono come dei contenitori nei quali è possibile conservare un valore, ad esempio un numero o una stringa. In realtà il PHP permette di memorizzare in una variabile anche più di un valore. Questo tipo di variabile si chiama **array**. Ma qual'è lo scopo di tutto ciò?

Supponiamo di dover realizzare un sito che mostra le recensioni di alcuni film. Ogni film avrà un titolo, il nome del regista, i nomi degli attori principali ed una descrizione. Nella pagina principale del sito vogliamo mostrare le ultime 5 recensioni pubblicate. Lo script che andremo a realizzare avrà sicuramente delle variabili nelle quali metteremo queste informazioni. Se per ogni informazione dovessimo usare una variabile il nostro script inizierebbe a complicarsi notevolmente. Ad esempio dovremo avere le variabili $titolo\_film\_1, $titolo\_film\_2, $titolo\_film\_3, ecc, $regista\_film\_1, $regista\_film\_2, ecc.

Se invece riuscissimo a mettere tutte le informazioni di un film in un unica variabile la cosa si semplificherebbe notevolmente. Dovremo gestire infatti solo 5 variabili del tipo $film\_1, $film\_2, ecc. A fine lezione vedremo che anche una sola variabile è sufficiente a contenere tutte le recensioni!

C'è però un problema: se più informazioni vengono inserite in una variabile come facciamo ad accedere ai singoli dati? Ad un certo punto dovremo mostrare il titolo del film o il nome del regista, magari usando la funzione echo. Come faremo quindi a passare alla funzione echo solo una particolare stringa?

Queste variabili di tipo array possono gestire il loro contenuto in 2 modi diversi.

Le informazioni da conservare possono essere inserite nell'array attraverso la funzione **array()** semplicemente elencandole una alla volta:

 ```
<pre class="brush: php">
$test = array('ciao', 'prova', 123, 'php', 5.34);
```

L'array $test conterrà 5 valori (3 stringhe, un numero intero ed uno decimale). Questi valori potranno essere recuperati singolarmente usando semplicemente la loro posizione all'interno della lista, chiamata comunemente **indice**, specificata tra parentesi quadre:

 ```

$test[0] vale 'ciao'
$test[1] vale 'prova'
$test[2] vale 123
$test[3] vale 'php'
$test[4] vale 5.34
```

E importante notare subito che la prima posizione non è la numero 1 bensì la numero 0. Vediamo subito un esempio:

 ```
<pre class="brush: php">
<?php
$film = array('Via col Vento',1939,'Victor Fleming');

echo 'Titolo film: ' , $film[0];
echo '<br>';
echo 'Anno: ' , $film[1];
echo '<br>';
echo 'Regista: ' , $film[2];
?>
```

A volte però usare dei numeri (gli indici) per scegliere quale informazione prelevare da un array potrebbe essere scomodo. Immaginate la difficoltà che avremmo nel gestire un array contenente decine e decine di informazioni. Ma il PHP ci viene incontro dandoci la possibilità di usare un nome, chiamato **chiave**, da associare ad ogni valore inserito in un array. Si parla in questo caso di **array associativi**.

 ```
<pre class="brush: php">
<?php
$film = array('titolo' => 'Via col Vento', 'anno' => 1939,'regista' => 'Victor Fleming');

echo 'Titolo film: ' , $film['titolo'];
echo '<br>';
echo 'Anno: ' , $film['anno'];
echo '<br>';
echo 'Regista: ' , $film['regista'];
?>
```

Da notare i caratteri =&gt; usati nella costruzione dell'array per associare ad ogni chiave il suo valore. Per riferirci alle singole informazioni abbiamo usato proprio la relativa chiave, messa tra parentesi quadre. Questa volta la chiave è una stringa, quindi va posta tra apici.

Il risultato finale sarà lo stesso dell'esempio che usava gli indici numerici, ma in questo caso lo script risulta più chiaro.

Prima di proseguire con lo studio degli array dobbiamo introdurre la funzione **print\_r()**. Tale funzione ci permette di visualizzare in maniera immediata il contenuto e la struttura della variabile che gli passeremo. Sarà usata nei prossimi esempi.

PHP permette l'uso in uno stesso array sia di indici numerici che di coppie chiave =&gt; valore. Vediamo come.

 ```
<pre class="brush: php">
<?php
// definiamo un array $film
$film = array('Via col Vento', 'anno' => 1939,'Victor Fleming');

// visualizziamone la struttura con la funzione print_r
print_r($film);
?>
```

Questo sarà il risultato che otterremo. Interessante la funzione print\_r vero?

 ```

Array
(
   [0] => Via col Vento
   [anno] => 1939
   [1] => Victor Fleming
)
```

Notiamo come PHP abbia usato degli indici numerici (0 e 1) per tutti i valori per cui non è stata esplicitata una chiave. Un ultimo esempio chiarirà definitivamente il comportamento di PHP:

 ```
<pre class="brush: php">
<?php
$test = array('scheda',
              'test'=>'prova',
              5=>'full time',
              'luna',
              'nome'=>'Marco',
              1957,
              'Rossi',
              76=>'impiegato',
              234);

print_r($test);
?>
```

Ecco il risultato:

 ```

Array
(
   [0] => scheda
   [test] => prova
   [5] => full time
   [6] => luna
   [nome] => Marco
   [7] => 1957
   [8] => Rossi
   [76] => impiegato
   [77] => 234
)
```

Analizziamo nel dettaglio come il PHP ha costruito l'array. Il primo valore ('scheda') non ha una chiave, gli viene assegnato automaticamente l'indice 0. Il secondo è una coppia chiave =&gt; valore ('test'=&gt;'prova'), viene presa così com'è. Il terzo valore ('full time') ha una chiave numerica (5) che il PHP interpreta correttamente come indice numerico. Il quarto valore non ha una chiave, deve quindi ricevere da PHP un indice numerico, che sarà uguale all'ultimo assegnato (5) più uno (5 + 1 = 6). Segue un'altra coppia chiave =&gt; valore e successivamente 2 valori senza chiave. Per lo stesso criterio appena visto assumeranno un indice numerico crescente (7 e 8). Il valore seguente ('impiegato') specifica il suo indice numerico (76) che il PHP rispetterà mentre all'ultimo valore (234) il PHP assegna l'indice successivo (77).

Riepilogando: il PHP assegna automaticamente un indice numerico a tutti i valori sprovvisti di chiave. L'indice parte da 0 e va via via incrementandosi. Quando un valore è presente con un esplicito indice numerico, questo viene rispettato ed ai valori successivi, sprovvisti di indice, ne verrà assegnato uno appena maggiore. E' possibile quindi che si creino dei "buchi" all'interno degli indici numerici dell'array.

Possiamo dire quindi che tutti i valori di un array dispongono di una chiave che può essere una stringa oppure un numero. Quando ci riferiamo ad un elemento di un array dobbiamo quindi far riferimento alla relativa chiave (numerica o testuale) mettendola tra parentesi quadre. Ad esempio scriveremo $test\[76\] o $test\['nome'\].

Chiarito il meccanismo di assegnazione delle chiavi, possiamo ora vedere come aggiungere nuovi elementi all'interno di un array esistente:

 ```
<pre class="brush: php">
<?php
// avremmo potuto anche scrivere $test = array();
// per iniziare con un array completamente vuoto
$test = array('chiave'=>123,'prova',432);

// aggiungo una coppia chiave => valore
$test['colore'] = 'rosso';

// aggiungo un semplice valore che prenderà un indice numerico (2)
// notate le parentesi quadre senza indice o chiave
$test[] = 'impiegato';

// aggiungo un valore imponendo un indice (43)
$test[43] = 12000;

// il seguente valore avrà come indice 44
$test[] = 'casa';

print_r($test);
?>
```

Il risultato sarà:

 ```

Array
(
   [chiave] => 123
   [0] => prova
   [1] => 432
   [colore] => rosso
   [2] => impiegato
   [43] => 12000
   [44] => casa
)
```

La politica di assegnazione delle chiavi rimane quindi la stessa anche nel caso di inserimento di nuovi elementi all'interno di un array preesistente. Il PHP si limiterà a prendere atto delle nuove coppie chiave =&gt; valore, sia con chiavi numeriche (indici) sia con schiavi stringa. Nel caso non venga fornita una chiave, il nuovo valore assumerà un indice numerico di una unità superiore al più grande indice già presente nell'array.

Per concludere questa lezione accenniamo al fatto che gli elementi di un array possono essere di qualsiasi tipo: numeri, stringhe ma anche altri array. Vediamo un esempio proprio su quest'ultima possibilità:

 ```
<pre class="brush: php">
<?php
// definiamo un array $film
$film = array('titolo' => 'Via col Vento',
        'regista' => 'Victor Fleming',
        'attori' => array('Vivien Leigh', 'Clark Gable'));

// visualizziamone la struttura con la funzione print_r
print_r($film);

echo 'Scheda film: '
    ,'<br>titolo :' , $film['titolo']
    ,'<br>regista :' , $film['regista']
    ,'<br>protagonisti :' , $film['attori'][0] , ', ' , $film['attori'][1];
?>
```

Questo sarà il risultato:

 ```

Array
(
   [titolo] => Via col Vento
   [regista] => Victor Fleming
   [attori] => Array
   (
      [0] => Vivien Leigh
      [1] => Clark Gable
   )
)
```

Scheda film:

titolo :Via col Vento

regista :Victor Fleming

protagonisti :Vivien Leigh, Clark Gable

Notate l'utilizzo di $film\['attori'\]\[0\] e $film\['attori'\]\[1\] per riferirsi agli elementi dell'array 'attori' presente all'interno dell'array 'film'. Per riferirsi ad un elemento di un array multidimensionale, ovvero a più livelli, si usano tante coppie di parentesi quadre quanti sono i livelli di profondità. Si noti infine come il PHP, attraverso gli array, permette di realizzare complesse strutture ad albero, dotate cioè di diverse ramificazioni interne.

Grazie a questa notevole flessibilità degli array sarebbe possibile risolvere il problema introdotto all'inizio della lezione con un unico array che contenga tutte le recensioni, ciascuna costituita da un array di dati sul film. Scoprirete che tale approccio, unito alle tecniche di iterazione che vedremo nella Lezione 13, risulta essere estremamente efficace.

Anche per gli array vale lo stesso discorso fatto per le stringhe. Il PHP fornisce molte funzioni atte alla loro manipolazione. Per maggiori informazioni a riguardo basta consultare la sezione ad essi dedicata nel manuale di PHP.