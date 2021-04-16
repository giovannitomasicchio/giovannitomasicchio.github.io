---
title: "Gli operatori"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:33:23+0000
description: "Gli operatori PHP"
url: /corsi/corso-php-base/gli-operatori/
weight: 10
categories:
  - Corso PHP base
  
---
La struttura if introdotta nella scorsa lezione ci ha portato a parlare di **operatori**. Cerchiamo adesso di approfondire l'argomento poiché il loro corretto utilizzo è fondamentale anche nei più semplici script PHP. Questa lezione potrebbe risultare particolarmente noiosa e per certi aspetti anche ostica. I concetti che vedremo però sono importanti anche per una corretta comprensione delle prossimi argomenti.

### Operatori aritmetici

Gli operatori aritmetici sono quelli che usiamo comunemente per effettuare le operazioni matematiche:

 | Operatore | Nome |
|---|---|
| + | Addizione |
| - | Sottrazione |
| \* | Moltiplicazione |
| / | Divisione |
| % | Modulo |

Niente di nuovo quindi, a parte forse l'operatore modulo (%) che restituisce il resto di una divisione. Vediamo un esempio

 ```php
<?php
$a = 5; $b = 2;

$somma = $a + $b; // 7
$differenza = $a - $b; // 3
$prodotto = $a * $b; // 10
$quoziente = $a / $b; // 2.5
$modulo = $a % $b; // 1
?>
```

### Operatori di assegnazione

Abbiamo già utilizzato l'operatore di assegnazione " = " per impostare il valore di una variabile. Citiamo qui anche le forme abbreviate (+=, -= e .=) che più spesso vengono usate. I commenti nel codice ne spiegano la valenza.

 ```php
<?php
$n = 5;
$testo = 'Ciao';

// forma abbreviata di $n = $n + 3
$n += 3;

// forma abbreviata di $n = $n - 2
$n -= 2;

// forma abbreviata di $testo = $testo . ' mondo!'
$testo .= ' mondo!'
?>
```

### Operatori di confronto

La seguente tabella riporta i principali operatori di confronto:

 | Operatore | Nome |
|---|---|
| == | uguale |
| != | diverso |
| &lt;&gt; | diverso |
| &lt; | minore |
| &gt; | maggiore |
| &lt;= | minore o uguale |
| &gt;= | maggiore o uguale |

Questi operatori sono molto importanti perché vengono usati sia nel costrutto if sia in altre strutture che vedremo in seguito. Effettuano il confronto tra due valori il cui esito verrà espresso attraverso una variabile booleana: se la relazione specificata (uguale, minore, maggiore, ecc.) viene rispettata allora il risultato del confronto sarà true, in caso contrario sarà false. Vediamone un impiego all'interno del costrutto if:

 ```php
<?php
$n = 5;

if($n < 0) {
   echo 'Il numero è negativo';
}

if($n > 0) {
   echo 'Il numero è positivo';
}
?>
```

In realtà ci sono altri 2 operatori di confronto (=== e !==) che operano, oltre che sul valore assunto dagli operandi, anche sul tipo di dato. Il loro utilizzo comunque è abbastanza sporadico ed indicato solo in contesti di programmazione avanzata.

### Operatori di incremento/decremento

Si tratta di due operatori (++ e --) che fanno aumentare o diminuire di una unità il valore di una variabile numerica. Sono quindi delle forme abbreviate per espressioni aritmetiche di uso comune.

 ```php
<?php
$n = 5; $m = 3;

// forma abbreviata di $n = $n + 1
$n++;

// forma abbreviata di $m = $m - 1
$m--;
?>
```

### Operatori logici

Nella seguente tabella sono elencati i 4 operatori logici, due dei quali (and e or) hanno anche un alias simbolico (&amp;&amp; e ||) con identiche funzionalità.

 | Operatore | Alias simbolico | Nome | Risultato |
|---|---|---|---|
| and | &amp;&amp; | And | TRUE se entrambi gli operandi sono TRUE. |
| or | \|\| | Or | TRUE se uno tra gli operandi è TRUE. |
| xor |  | Xor | TRUE se uno tra gli operandi è TRUE, ma non entrambi. |
| ! |  | Not | TRUE se l'operando è FALSE. |

Gli operatori and, or e xor si applicano ad una coppia di variabili booleane mentre l'operatore ! agisce su un unica variabile booleana. Gli operatori logici possano essere usati all'interno di espressioni più complesse, in cui gli operandi sono costituiti a loro volta da espressioni il cui risultato sarà una variabile booleana. Vediamo alcuni esempi:

 ```php
<?php
$denaro_disponibile = 500;
$prezzo = 1400;
$oggetto_disponibile = true;
$pagabile_a_rate = true;

if(!$oggetto_disponibile)
{
    echo "L'oggetto non è disponibile\n";
}

if($oggetto_disponibile and $pagabile_a_rate)
{
    echo "Acquista l'oggetto anche a rate!\n";
}

if($denaro_disponibile > $prezzo and $oggetto_disponibile)
{
    echo "Abbiamo verificato il tuo credito, puoi acquistare l'oggetto!\n";
}

// notare le parentesi tonde usate per specificare il
// corretto ordine con cui effettuare le espressioni
if($oggetto_disponibile && ($denaro_disponibile > $prezzo || $pagabile_a_rate))
{
    echo "Acquista l'oggetto, qualunque sia la tua disponibilità economica\n";
}
?>
```

Osservando il terzo ed il quarto if si intuisce che il PHP deve osservare un qualche tipo di priorità nella valutazione delle espressioni. Ad esempio l'espressione $denaro\_disponibile &gt; $prezzo and $oggetto\_disponibile prevede che dapprima venga valutato il confronto $denaro\_disponibile &gt; $prezzo e successivamente venga calcolata la and tra il risultato ottenuto e $oggetto\_disponibile. Questo perché l'operatore &gt; ha la precedenza sull'operatore and. Nel manuale ufficiale di PHP è possibile trovare l'elenco di tutti gli operatori [ordinati per precedenza](http://www.php.net/manual/it/language.operators.php). Nel caso in cui si voglia imporre un particolare ordine alla valutazione dei diversi operatori che compongono una espressione complessa, è possibile utilizzare delle parentesi tonde. Questo è il caso del quarto if.

Spesso gli operatori logici vengono applicati ad operandi non booleani. In questi casi il PHP, prima di effettuare l'operazione logica, trasforma automaticamente il valore assunto dagli operandi in un corrispondente valore booleano. Questa conversione, che prende il nome di cast o conversione di tipo, verrà approfondita nella prossima lezione.

### Operatori di stringa

L'unico operatore che si applica alle stringhe è il punto. Come abbiamo già visto nella Lezione 7 serve ad accodare due stringhe. Esempio:

$stringa4 = $stringa1 . $stringa2 . $stringa3;

### Operatore ternario (di confronto)

Un altro operatore che vale la pena menzionare è l'operatore ternario o di confronto ed ha una funzione simile al costrutto if - else e fa uso dei simboli ? e :. Il suo schema generale è:

(espressione1) ? (espressione2) : (espressione3);

Se l'espressione1 è true allora viene valutata l'espressione2, altrimenti sarà valutata l'espressione3. L'esempio seguente mostra un possibile uso di questo operatore:

 ```php
<?php
$voto = 7;

$giudizio = $voto < 6 ? 'negativo' : 'positivo';

// mostrerà la scritta "Il voto è positivo"
echo "Il voto è $giudizio";
?>
```

### Operatori bitwise

Per dovere di cronaca citiamo anche gli operatori bitwise &amp;, |, ^, ~, &lt;&lt; e &gt;&gt;. Si tratta di operatori che agiscono sui singoli bit che compongono le variabili a cui sono applicati. Per maggiori dettagli conviene consultare direttamente la [documentazione ufficiale](http://www.php.net/manual/it/language.operators.bitwise.php).