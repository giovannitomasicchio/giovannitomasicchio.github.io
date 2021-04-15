---
title: "Cicli e iterazioni: while e do-while"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:34:27+0000
description: "Cicli e iterazioni negli script PHP, i costrutti WHILE e DO-WHILE"
url: /corsi/corso-php-base/cicli-e-iterazioni-while-e-do-while/
categories:
  - Corso PHP base
  
---
### while

A volte serve semplicemente ripetere un gruppo di istruzioni finché una determinata condizione è soddisfatta. Quando tale condizione non è più verificata le iterazioni devono essere interrotte. Questo è proprio quello che fa il ciclo **while** che si presenta con una sintassi del tipo:

 ```php
while(condizione)
{
   // istruzioni da iterare
}
```

dove l'espressione *condizione* viene valutata all'inizio di ogni iterazione. Se assume il valore *true* allora sarà eseguito il gruppo di istruzioni da iterare, altrimenti il ciclo verrà interrotto. Vediamo un esempio:

 ```php
<?php
$somma = 0;
while ($somma < 100)
{
    // creo un numero casuale compreso tra 0 e 10 e
    // lo aggiungo alla variabile $somma
    $somma += rand(0,10);
    
    // mostro la somma e vado a capo
    echo $somma , "<br>\n";
} 
?>
```

Il codice precedente crea una variabile `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$somma</span></span>` e la inizializza a 0. Ad ogni iterazione del ciclo while viene generato un numero casuale compreso tra 0 e 10 (`<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">rand</span><span style="color: rgb(0, 119, 0);">(</span><span style="color: rgb(0, 0, 187);">0</span><span style="color: rgb(0, 119, 0);">,</span><span style="color: rgb(0, 0, 187);">10</span><span style="color: rgb(0, 119, 0);">)</span></span>`) che viene sommato alla variabile `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$somma</span></span>`. Il ciclo continua fino a che la variabile `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$somma</span></span>` è inferiore a 100 (`<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$somma </span><span style="color: rgb(0, 119, 0);">< </span><span style="color: rgb(0, 0, 187);">100</span></span>`). Quando $somma è maggiore di 100 il ciclo viene interrotto.

Da questo esempio si evince che il ciclo while è particolarmente indicato quando il numero di iterazioni non è noto a priori ma dipende da una condizione da valutare volta per volta. La variabile `<span style="color: rgb(0, 0, 0);"><span style="color: rgb(0, 0, 187);">$somma</span></span>` viene incrementata con dei numeri casuali, quindi non c'è modo di sapere quando supererà il valore 100.

### do - while

Il ciclo **do-while** è una variante del ciclo while. Nel ciclo do-while infatti il controllo viene effettuato alla fine di ogni iterazione anziché all'inizio e questo fa si che la prima iterazione venga sempre eseguita. Si presenta con la seguente sintassi:

 ```php
do
{
   // istruzioni da iterare
}
while ( condizione)
```

Vediamo un esempio:

 ```php
<?php
do
{
    // creo un numero casuale compreso tra 0 e 10
    $numero = rand(0,10);

    // mostro il numero e vado a capo
    echo $numero , "<br>\n";
}
while ($numero < 9) // se $numero < 9 ripete le istruzioni 
?>
```

Ad ogni iterazione viene generato un numero casuale compreso tra 0 e 10. Se il numero generato è più piccolo di 9 il ciclo continua, altrimenti viene interrotto.

Anche il ciclo do-while, come il ciclo while, è quindi indicato quando il numero di iterazioni non è noto a priori (non sappiamo quando verrà generato un numero uguale o superiore a 9). La peculiarità del ciclo do-while sta nel controllo della condizione solo dopo la prima iterazione. Questo permette di valutare delle *condizioni* che fanno uso i dati (variabili) generati all'interno della prima iterazione. Nell'esempio precedente è infatti necessario generare il primo numero casuale per decidere se andare avanti con le iterazioni oppure no. E' quindi necessario eseguire almeno una volta il ciclo per poter valutare la *condizione* sulle iterazioni successive.