---
title: "Le funzioni personalizzate - I"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:35:43+0000
description: "Realizzare funzioni personalizzate in PHP"
url: /corsi/corso-php-base/le-funzioni-personalizzate-i/
categories:
  - Corso PHP base
  
---
Il PHP mette a disposizione dei nostri script una miriade di funzioni che possiamo impiegare nelle situazioni più disparate. Nonostante ciò ci si trova spesso nella necessità di dover risolvere determinati problemi personalmente, senza cioè poter contare su una funzione PHP già pronta.

Ad esempio se vogliamo mostrare un testo in grassetto o in corsivo non possiamo confidare su una funzione PHP ma dovremo inserire manualmente i tag HTML opportuni. In generale questo non è un problema ma lo può diventare se la pagina da realizzare richiede diverse stringhe in grassetto:

 ```
<pre class="brush: php">
<?php
$testo1 = 'Sito web';
$testo2 = 'URL';
$testo3 = 'Categoria';
$valore1 = 'Google';
$valore2 = 'www.google.it';
$valore3 = 'motore di ricerca';

echo "<b>$testo1</b>: <i>$valore1</i>";
echo '<br>';
echo "<b>$testo2</b>: <i>$valore2</i>";
echo '<br>';
echo "<b>$testo3</b>: <i>$valore3</i>";
?>
```

E' evidente che per ottenere il grassetto ed il corsivo abbiamo dovuto inserire i tab &lt;b&gt; e &lt;i&gt; ogni volta che ce n'era bisogno.

Il PHP ci permette di superare questo problema dandoci la possibilità di definire delle funzioni personalizzate, ovvero delle funzioni utilizzabili come le normali funzione PHP che però si comporteranno nel modo da noi specificato. Le funzioni personalizzate sono quindi un "pacchetto" di istruzioni a cui diamo un nome e che useremo per effettuare dei compiti particolari, spesso ripetitivi. Vediamo subito un esempio:

 ```
<pre class="brush: php">
<?php
// due stringhe da mostrare
$testo1 = 'Corso di PHP';
$testo2 = 'per principianti';

// chiamo la funzione grassetto
// come fosse una funzione nativa del PHP
grassetto($testo1);

// vado a capo
echo '<br>';

// chiamo la funzione grassetto
grassetto($testo2);

// definizione della funzione "grassetto"
function grassetto($stringa)
{
    echo "<b>$stringa</b>";
}
?>
```

Soffermiamoci sulla riga 17. La parola function serve ad avvisare il PHP che quella che segue è la definizione di una funzione personalizzata il cui nome sarà appunto "grassetto". Inoltre su questa riga abbiamo scritto tra parentesi tonde le variabili che questa funzione accetterà come parametri, in questo caso si tratta di un unico parametro. Seguono delle istruzioni raggruppate dalle parentesi graffe: si tratta della procedura che verrà messa in atto ad ogni chiamata della nostra funzione personalizzata "grassetto".

Ecco ciò che avviene quando invochiamo la funzione grassetto($testo1):

- Il PHP interrompe la normale esecuzione delle istruzioni e cerca la funzione grassetto.
- Preleva il contenuto della variabile $testo1 (il parametro passato alla funzione grassetto) e lo inserisce nella variabile $stringa (quella usata nella definizione della funzione).
- Vengono eseguite le istruzioni associate a tale funzione.
- Il PHP riprende l'esecuzione dello script interrotta al punto 1
 
E' evidente quindi che la variabile $stringa presente nella funzione è semplicemente un modo per riferirsi al primo parametro passato alla funzione. Tale variabile "esiste" solo all'interno della funzione e non se ne avrà più traccia una volta che la funzione sarà stata eseguita (maggiori dettagli nella prossima lezione).

Vediamo adesso un esempio che fa uso del comando return. Vogliamo realizzare un convertitore euro -&gt; lire.

 ```
<pre class="brush: php">
<?php
// 15 euro
$valuta = 15;

// chiamo la funzione converti. Questa volta dobbiamo usare una
// variabile per raccogliere il valore restituito dalla funzione!
$vecchio_conio = converti($valuta);
echo "$valuta euro equivalgono a $vecchio_conio lire";

// definizione della funzione "converti"
function converti($euro)
{
    // effettuo la conversione
    $lire = $euro * 1936.27;

    // restituisco il valore calcolato
    return $lire;
}
?>
```

Analizziamo la funzione "converti". Quando viene invocata riceve un parametro (la quantità di euro da convertire in lire) che viene memorizzata nella variabile $euro. La prima istruzione effettua la conversione vera e propria. Per far si che la funzione restituisca il risultato calcolato viene usato il comando return affiancato dalla variabile da restituire $lire.

Per utilizzare la funzione converti dovremo quindi specificare anche la variabile che raccoglierà il risultato restituito da tale funzione. Nel nostro esempio sarà la variabile $vecchio\_conio a conservare tale valore.

Anche se il comando return è in grado di restituire una sola variabile è possibile far si che una funzione restituisca anche più di un valore attraverso un semplice espediente. E' sufficiente infatti impacchettare tutti i valori da restituire all'interno di un array e poi usare il comando return proprio con questo array.