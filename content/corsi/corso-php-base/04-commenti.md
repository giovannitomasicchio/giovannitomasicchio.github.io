---
title: "Commenti?"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:27:44+0000
description: "Come inserire dei commenti in uno script PHP"
url: /corsi/corso-php-base/commenti/
categories:
  - Corso PHP base
  
---
 Appena avrete preso un po' di confidenza con il PHP inizierete a scrivere programmi che diventeranno sempre più potenti ed utili ma purtroppo anche più lunghi e complessi. Vi accorgerete che scrivere script di centinaia o addirittura migliaia di righe non sarà poi così difficile. Il difficile sarà invece non perdersi tra tutte quelle righe di codice e ricordare esattamente quale sia il loro scopo.

 Per semplificare la lettura e la comprensione dei programmi PHP possiamo però utilizzare i **commenti**, ovvero delle frasi da inserire all'interno dei nostri script il cui scopo è semplicemente quello di chiarire il significato del codice vero e proprio. Queste frasi sono ovviamente del tutto arbitrarie, sono degli appunti che il programmatore lascia principalmente per agevolare la comprensione del listato.

 Ovviamente il PHP dovrà ignorare queste frasi e perciò dovremo rispettare una certa sintassi nell'inserimento dei commenti. Ci sono tre modi per inserire un commento:

- farlo precedere dai caratteri **//**. Questo tipo di commenti non potrà occupare più di una riga, ovvero la frase non potrà andare a capo;
- delimitare il commento ponendo i caratteri **/\*** all'inizio e **\*/** alla fine. In questo caso il commento potrà estendersi su più righe;
- farlo precedere dal carattere **\#**. Anche questo commento non può andare a capo. E' usato più di rado.
 
 Nel seguente script verranno presentati le diverse modalità di inserimento di commenti:

 ```
<pre class="brush: php">
<?php

// questo è un commento
echo "Ciao Mondo !";
echo "Ciao Mondo !"; // altro commento

/* questo è un commento
un po' più lungo,
che può stare su più righe */
echo "Ciao Mondo !";

# stile di commento più raro
echo "Ciao Mondo !"; ?>
```

 Molti degli script che useremo per questo corso contengono dei commenti che ne semplificheranno la comprensione e pertanto vi esortiamo a non trascurarli!