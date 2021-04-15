---
title: "true e false - le variabili booleane"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:32:49+0000
description: "Variabili booleane in PHP: true e false"
url: /corsi/corso-php-base/true-e-false-le-variabili-booleane/
categories:
  - Corso PHP base
  
---
 Dopo aver studiato gli array, sicuramente il tipo di dati più complesso ed articolato del PHP, analizziamo adesso quello più semplice: il tipo **booleano**. Non fatevi spaventare dalla parola, si tratta semplicemente di un tipo di dato che può assumere soltanto 2 valori: vero e falso.

 Capita spesso di dover memorizzare in una variabile se un determinato evento è accaduto o meno. Ad esempio se un utente ha effettuato il log-in, oppure se i dati che ha inserito sono corretti ecc.

 Il PHP ci viene incontro mettendoci a disposizione 2 valori particolari: **true** e **false** che significano appunto "vero" e "falso". Alcuni esempi chiariranno il concetto:

 ```php
$mi_piace_il_php = true; // altrimenti non avrei scritto questo corso
$php_difficile = false; // se credete sia difficile non conoscete il C++
$sono_ricco = false; // se lo fossi mi comprerei un'auto nuova
```

 True e false hanno quindi un significato ben preciso per il PHP, le variabili che contendono tali valori vengono dette booleane. L'importanza di tali variabili sarà chiarita nelle prossime lezioni, in particolare quando studieremo la struttura **if** (Lezione 10) e gli operatori logici (Lezione 11).