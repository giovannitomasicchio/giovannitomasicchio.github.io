---
title: "Cicli e iterazioni: break e continue"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:34:49+0000
description: "Cicli e iterazioni negli script PHP, BREAK e CONTINUE"
url: /corsi/corso-php-base/cicli-e-iterazioni-break-e-continue/
categories:
  - Corso PHP base
  
---
### break

Abbiamo visto che ogni struttura per la realizzazione di cicli (for, foreach, while, do-while) utilizza una condizione per decidere se continuare le iterazioni o interrompersi. PHP comunque mette a disposizione il comando **break** per effettuare una terminazione prematura di queste esecuzioni:

 ```php
<?php
$colori = array('bianco','nero','rosso','verde','blu','giallo');
$trovato = false;
$colore_cercato = 'verde';

foreach ($colori as $colore)
{
    // se trovo il colore cercato...
    if($colore == $colore_cercato)
    {
        // memorizzo il successo della ricerca ed arresto il ciclo
        $trovato = true;
        break;
    }
}

if($trovato)
    echo "Il colore '$colore_cercato' è nell'array";
else
    echo "Il colore '$colore_cercato' non è stato trovato nell'array";
?>
```

L'esempio precedente scorre gli elementi dell'array $colori attraverso un foreach alla ricerca di un particolare valore. Appena questo valore viene trovato è inutile procedere con le iterazioni. E' conveniente quindi interrompere l'esecuzione del ciclo e questo viene fatto proprio attraverso l'uso del comando break.

Ci sono casi in cui più cicli vengono annidati, l'uno dentro l'altro, per risolvere problemi più complessi. Il seguente esempio è molto simile al precedente ma in questo caso la struttura dell'array $colori è più complessa. $colori infatti è composto a sua volta da tre array ognuno dei quali contiene 2 colori. Per scorrere questo array usiamo un ciclo for ed al suo interno un ciclo foreach nel quale viene effettuata la ricerca vera e propria. Non appena viene trovato il valore cercato è inutile procedere con le iterazioni sia del ciclo foreach che del ciclo for più esterno. Useremo allora il comando break 2 proprio per specificare che i cicli da interrompere sono 2. Se avessimo usato solo il comando break avremmo interrotto solo il ciclo foreach, ovvero il più interno.

 ```php
<?php
$colori = array( array('bianco','nero'),
array('rosso','verde'),
array('blu','giallo')); $trovato = false; $colore_cercato = 'verde';

for ($i = 0; $i < 3 ; $i++)
{
    foreach ($colori[$i] as $colore)
    {
        // se trovo il colore cercato...
        if($colore == $colore_cercato)
        {
            // memorizzo il successo della ricerca ed arresto tutti i cicli
            $trovato = true;
            break 2;
        }
    }
}

if($trovato)
    echo "Il colore '$colore_cercato' è nell'array";
else
    echo "Il colore '$colore_cercato' non è stato trovato nell'array";
?>
```

Come già detto nella Lezione 10 il comando break è usato anche nel costrutto switch per bloccare l'esecuzione delle istruzioni successive.

### continue

Anche il comando **continue** serve ad alterare le normali iterazioni di un ciclo for, foreach, while e do-while. In particolare continue fa si che non vengano eseguite le successive istruzioni del ciclo ma che si passi direttamente alle prime istruzioni della prossima iterazione. Il seguente esempio effettua la somma di tutti i numeri pari presenti in un array. Quando si incontra un numero dispari il comando continue permette di saltarne la somma e di procedere con il prossimo numero.

 ```php
<?php
$somma = 0;
$numeri = array(3,2,5,6,8,12,43,23,66,11,32);
foreach ($numeri as $numero)
{
    // se il numero è dispari ...
    if($numero % 2 == 1)
      continue;

    // $somma = $somma + $numero
    $somma += $numero;
}

echo "La somma dei numeri pari è $somma";
?> 
```

Anche per il comando continue come per il comando break, in caso di cicli annidati è possibile specificare il numero di cicli da interrompere e da riprendere dall'iterazione successiva.