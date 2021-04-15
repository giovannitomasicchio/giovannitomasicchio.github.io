---
title: "Istruzioni \"casuali\""
author: "Giovanni Tomasicchio"
type: article
date: 2006-04-21T15:19:48+0000
description: "Script PHP per eseguire delle istruzioni ad intervalli casuali di tempo, secondo una determinata probabilità."
url: /articoli/istruzioni-casuali/
categories:
  - Articoli
tags:
  - "database"

  
---
 Supponiamo di voler cancellare periodicamente i file contenuti in una cartella del server, o eseguire saltuariamente determinate query. Una soluzione comunemente adottata è quella di affidare a cron (o programmi analoghi) l'esecuzione di particolari script ad intervalli regolari di tempo. Non sempre però si ha a disposizione cron e a volte non è neanche necessaria una programmazione rigorosa degli istanti di esecuzione. Si pensi ad una routine "spazzina", realizzata per eliminare vecchi record nel database. L'importante è che venga lanciata diverse volte al giorno (alla settimana o al mese), non importa quando.

 Una semplice tecnica per ottenere questo risultato consiste nell'associare una **probabilità di esecuzione** a determinate istruzioni presenti in una pagina comunemente visitata del nostro sito web. Per farlo basta utilizzare la funzione [mt\_rand](http://it.php.net/mt_rand) per generare un numero casuale tra 1 e 100 ed osservare che la probabilità che tale numero sia ad esempio minore o uguale a 20 è proprio pari al 20%. Il seguente esempio utilizza proprio questo principio:

 ```
<pre class="brush: php">
<?php
// imposto la probabilità dell'evento
$probabilità = 13; // 13%

if(mt_rand(1,100) <= $probabilità)
{
    // si è verificato l'evento
    echo 'Evento!';
}
?>
```

 Per verificare che la stringa "Evento!" viene mostrata con probabilità pari al 13% basta eseguire il prossimo script che si limita a ripetere la stessa tecnica per 100.000 volte e calcola la frequenza di successi dell'IF:

 ```
<pre class="brush: php">
<?php
// imposto la probabilità dell'evento
$probabilità = 13; // 13%

// il contatore dei successi
$successi = 0;

// ripeto 100.000 volte il test
for ($i=0 ; $i<100000 ; $i++)
{
    if( mt_rand(1,100) <= $probabilità)
    {
        // incremento il contatore
        $successi++;
    }
}
echo 'Percentuale di successi: ' , $successi/1000, '%';
?>
```

 Ovviamente non si otterrà come risultato esattamente 13% ma un valore molto prossimo.