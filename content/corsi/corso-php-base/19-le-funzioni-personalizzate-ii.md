---
title: "Le funzioni personalizzate - II"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:36:03+0000
description: "Concetti avanzati sui parametri delle funzioni personalizzate di PHP: Parametri di default, passaggio per rifermentodati, visibilità dei parametri, variagili globali."
url: /corsi/corso-php-base/le-funzioni-personalizzate-ii/
categories:
  - Corso PHP base
  
---
###  parametri di default

 Spesso capita di realizzare delle funzioni che accettano uno o più parametri ma alcuni di questi assumono quasi sempre lo stesso valore. Il PHP ci permette di assegnare ad essi un valore di *default* (predefinito) che verrà utilizzato ogni qualvolta tale parametro non sarà specificato nella chiamata della funzione.

 Supponiamo ad esempio di dover realizzare una funzione che crei una descrizione di un film. Passeremo come parametri il titolo del film, il regista, l'anno e una indicazione 'bianco e nero' o 'a colori'. Poiché supponiamo che la maggior parte dei film da descrivere sarà a colori allora faremo in modo che questa sia l'opzione di default. Vediamo come realizzare tale funzione.

 ```
<pre class="brush: php">
<?php
// il terzo parametro ha un valore di default
function descrivi_film($titolo, $regista, $bn_colori = 'colori')
{
    echo "Titolo: $titolo <br>";
    echo "Regista: $regista <br>";
    echo "B/N - colori: $bn_colori <br>";
}

// chiamo la funzione passando 2 soli parametri
// il terzo è sottointeso
descrivi_film('Il malato immaginario','Tonino Cervi');

echo '<br>';
// chiamo la funzione passando tutti e 3 i parametri
descrivi_film('I soliti ignoti','Mario Monicelli','bianco e nero');
?>
```

 Nella definizione della funzione "descrivi\_film" abbiamo assegnato un valore al parametro $bn\_colori. Si tratta proprio del valore di default. La funzione è stata chiamata 2 volte, la prima volta senza specificare un valore per il parametro $bn\_colori che così ha assunto il valore di default 'colori'. La seconda volta invece impostando tale parametro ad un preciso valore.

###  parametri per riferimento

 Partiamo subito con un esempio: una funzione che calcola il quadrato di un numero.

 ```
<pre class="brush: php">
<?php
function quadrato($a)
{
    $a = $a * $a;
}

$numero = 2;
quadrato($numero);
echo $numero;
?>
```

 Se proviamo a lanciare questo script otterremo come risultato il numero 2 anzichè 4. Perché? Analizziamo le varie fasi del programma:

- La variabile $numero assume il valore 2.
- Viene lanciata la funzione quadrato
- Questa funzione riceve il valore contenuto nella variabile $numero
- Tale valore viene copiato in una nuova variabile: $a
- Viene calcolato il quadrato di $a e salvato sempre nella variabile $a
- Termina la funzione: $a viene distrutta!
- Il programma principale riprende con l'istruzione echo $numero che mostrerà ovviamente il valore 2.
 
 E' quindi chiaro che non possiamo alterare all'interno di una funzione i parametri passati sperando che queste modifiche abbiano effetto anche sulle relative variabili del programma principale. Questo perché i parametri durante il passaggio vengono semplicemente copiati. E' possibile però modificare questo comportamento attraverso l'uso del carattere **&amp;** (passaggio di variabile per riferimento, già incontrato nei cicli foreach) . Modifichiamo l'esempio precedente:

 ```
<pre class="brush: php">
<?php
// il parametro viene passato "per riferimento"

function quadrato(&$a)
{
    $a = $a * $a;
}
$numero = 2;
quadrato($numero);
echo $numero;
?>
```

 Questa volta la variabile $a sarà un alias ovvero un "soprannome" della variabile $numero. $a e $numero sono la stessa variabile, chiamata in 2 modi diversi! Infatti questa volta la funzione "quadrato" non riceve una semplice copia del valore della variabile $numero bensì la variabile stessa: invece di passare una copia del contenuto abbiamo inviato il contenitore stesso. Tutte le modifiche effettuate ad $a ovviamente saranno quindi fatte a $numero. Al termine della funzione il "soprannome" $a viene distrutto ma il suo contenuto è comunque ancora accessibile attraverso $numero. Questa volta lo script darà come risultato 4.

###  Visibilità delle variabili

 Negli esempi precedenti abbiamo visto come la variabile $a, utilizzata per conservare il parametro passato alla funzione, veniva distrutta al termine dell'esecuzione della funzione. In realtà questa è la sorte di tutte le variabili che eventualmente verranno create all'interno di una funzione, dette anche **variabili locali**, che pertanto saranno "invisibili" all'esterno.

 Un fenomeno analogo avviene per le variabili dichiarate all'esterno delle funzioni, nel programma principale, che sono dette anche **variabili globali**. Queste saranno "invisibili" all'interno delle varie funzioni. Il seguente esempio dimostra quanto detto.

 ```
<pre class="brush: php">
<?php
function test()
{
    $b = 1;

    if(isset($a))
        echo 'Vedo la variabile $a <br>';
    else
        echo 'Non vedo la variabile $a <br>';
}

// programma principale
$a = 1;
test();

if(isset($b))
    echo 'Vedo la variabile $b';
else
    echo 'Non vedo la variabile $b';
?>
```

 Se eseguiamo questo script ci rendiamo conto che la variabile $a, definita nel programma principale, non è visibile nella funzione e la variabile $b, definita nella funzione, non è visibile nel programma principale.

 Questa gestione della visibilità delle variabili serve a garantire una certa separazione dei diversi ambiti di elaborazione (programma principale - funzioni) ed è per questo che le funzioni usano delle tecniche particolari (parametri, return) per prendere o restituire dati da o verso l'esterno.

 A volte però, magari per evitare di usare lunghe liste di parametri o per non complicare troppo i valori restituiti da return, si preferisce alterare le normali regole di visibilità delle variabili attraverso l'uso del comando **global**.

 ```
<pre class="brush: php">
<?php
function test()
{
    // rendo globali le variabili $a e $b
    global $a, $b;

    $b = 1;

    if(isset($a))
        echo 'Vedo la variabile $a <br>';
    else
        echo 'Non vedo la variabile $a <br>';
}

// programma principale
$a = 1;
test();

if(isset($b))
    echo 'Vedo la variabile $b';
else
    echo 'Non vedo la variabile $b';
?>
```

 Questo script è identico al precedente tranne che per l'aggiunta del comando global $a, $b; che ha reso tali variabili globali e visibili sia all'interno della funzione che nel programma principale.