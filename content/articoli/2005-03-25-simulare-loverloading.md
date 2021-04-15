---
title: "Simulare l'overloading"
author: "Giovanni Tomasicchio"
type: article
date: 2005-03-25T17:23:45+0000
description: "Simulare l'overloading negli script PHP"
url: /articoli/simulare-loverloading/
categories:
  - Articoli
tags:

  
---
 Il PHP, essendo un linguaggio di scripting, concede allo sviluppatore una notevole libertà nella gestione e nell'utilizzo delle variabili. Contrariamente a quanto avviene in altri linguaggi, in PHP non è necessario definire una variabile e dichiarare il tipo prima di usarla. Questa agevolazione però ha anche degli svantaggi, ad esempio non è possibile realizzare in maniera nativa il sovraccarico (overloading) delle funzioni.

 Con PHP 5 però è possibile simulare l'overloading dei metodi di un oggetto attraverso l'uso del metodo speciale \_\_call().

 Se in una classe è definito questo metodo, esso sarà richiamato ogni volta che verrà invocata l'esecuzione di un metodo non definito. \_\_call() quindi è un metodo che intercetta tutte le chiamate ai metodi di una classe che non vanno a buon fine.Il PHP passa a \_\_call() il nome del metodo chiamato ed un array contenente i parametri passati.

 L'overloading di un metodo può essere quindi realizzato attraverso una sorta di smistatore di chiamate da realizzare all'interno di \_\_call(). Questo smistatore deve essere in grado di riconoscere il tipo e il numero dei parametri passati e deve lanciare la routine più appropriata alla loro gestione.

 L'esempio che segue realizza l'overloading del metodo somma() al quale si potranno passare sia interi,sui quali verrà effettuata una somma algebrica, sia stringhe, che verranno invece concatenate. Potranno essere passati un numero qualsiasi di parametri, anche nessuno.

 ```php
<?php
class sommatore
{
    function __call($m, $a)
    {
        if($m == 'somma')
        {
            $num_par = count($a);
            if(!$num_par)
            {
                echo 'Nessun parametro passato',"\n\n";
            }
            else
            {
                if($this->sono_interi($a))
                {
                    echo 'Tipo parametri passati: interi (',implode(', ',$a),")\n";
                    echo 'risultato: ',array_reduce($a,array($this,'somma_numeri')),"\n\n";
                }
                else
                {
                    echo 'Tipo parametri passati: stringhe e/o interi (',implode(', ',$a),")\n";
                    echo 'risultato: ',array_reduce($a,array($this,'somma_stringhe')),"\n\n";
                }
            }
        }
        else
        echo "Il metodo '$m' non è definito \n\n";
    }

    function somma_numeri($v, $w)
    {
        $v += $w;
        return $v;
    }

    function somma_stringhe($v, $w)
    {
        $v .= $w;
        return $v;
    }

    function sono_interi($a)
    {
        foreach ($a as $value) {
            if (!is_int($value)) return false;
        }
        return true;
    }
}

$mioSommatore = new sommatore();
$mioSommatore->somma();
$mioSommatore->somma(1);
$mioSommatore->somma(3,5);
$mioSommatore->somma('Concatena',' stringa');
$mioSommatore->ciccio();
?>
```

 Se lanciamo lo script otterremo il seguente risultato che evidenzia come, attraverso questa tecnica, siamo in grado di realizzare dei metodi il cui comportamento è funzione del numero e del tipo di parametri passati.

 ```

Nessun parametro passato

Tipo parametri passati: interi (1)
risultato: 1

Tipo parametri passati: interi (3, 5)
risultato: 8

Tipo parametri passati: stringhe e/o interi (Concatena, stringa)
risultato: Concatena stringa

Il metodo 'ciccio' non è definito
```