---
title: "Le stringhe"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:32:16+0000
description: "Le stringhe in PHP"
url: /corsi/corso-php-base/le-stringhe/
categories:
  - Corso PHP base
  
---
 Il PHP è un linguaggio progettato e realizzato per il web. Non sorprende quindi che risulti veramente potente nella gestione dei testi: una pagina web non è forse un testo contenente HTML?

 E' per questo motivo che vale la pena ora soffermarci su come PHP gestisce le stringhe. Iniziamo con un esempio:

 ```php
<?php
// stringa definita con i singoli apici
$stringa1 = 'Ciao a tutti';

// visualizziamo la stringa
echo $stringa1;

// vado a capo
echo '<br>';

//stringa definita con i doppi apici
$stringa2 = "Ciao a tutti";

// visualizziamo la seconda stringa
echo $stringa2;
?>
```

 Salviamo questo script in un file PHP e lanciamolo dal browser.

 Da questo esempio sembrerebbe che, sia che usiamo i **singoli apici** sia che usiamo i **doppi apici** per delimitare una stringa, il risultato sia identico. Ciò è vero però solo per questo semplice script. Il successivo ci mostrerà invece le prime differenze tra le due tecniche:

 ```php
<?php
// stringa che cercherò di includere
$domanda = 'vi piace il PHP?';

// stringa definita con i singoli apici
$stringa1 = 'Ciao a tutti, $domanda';

// visualizziamo la stringa
echo $stringa1;

// vado a capo
echo '<br>';

//stringa definita con i doppi apici
$stringa2 = "Ciao a tutti, $domanda";

// visualizziamo la seconda stringa
echo $stringa2;
?>
```

 Salvate anche questo script in un file e visualizzatelo dal browser. Dovreste ottenere il seguente risultato:

 Ciao a tutti, $domanda

 Ciao a tutti, vi piace il PHP?

 Ecco la prima importante differenza tra le stringhe con i singoli apici e quelle con i doppi apici: nel primo caso il PHP non analizza il contenuto della stringa e quindi non nota al suo interno la variabile $domanda che viene considerata come un normale testo. La funzione echo la riporta quindi così come è stata inserita nella stringa.

 Invece nel caso in cui siano stati usati i doppi apici per delimitare la stringa, il PHP legge il contenuto e se trova una variabile la sostituisce con il suo valore. La funzione echo quindi non troverà nella stringa da mostrare il nome della variabile bensì il suo contenuto.

 Quindi il PHP quando incontra una stringa delimitata da doppi apici interpreta il suo contenuto. Oltre a sostituire le variabili con il loro contenuto è anche in grado di riconoscere delle sequenze di caratteri speciali, detti **caratteri di escape**, e sostituirle con speciali formattazioni. Il seguente esempio chiarirà la questione:

 ```php
<?php
// una stringa con "strane" sequenze di caratteri
$stringa = " cane \t gatto \t topo \n cavallo \t mucca \t pecora \n rana \t serpente \t pesce";

// visualizziamo la stringa
echo $stringa;
?>
```

 Se lanciamo questo script noteremo come i caratteri **\\t** e **\\n** sono scomparsi. Per capire che fine hanno fatto basta chiedere al browser di mostrarci il codice HTML (per IE: tasto destro del mouse -&gt; HTML, per Firefox: tasto destro del mouse -&gt; Visualizza sorgente pagina). Vedremo che al posto di ogni \\t è stata inserita una tabulazione mentre dove erano i \\n il testo va a capo. Ecco perché il browser non ci ha mostrato tali caratteri, l'HTML infatti non prende in considerazione le tabulazioni e gli "a capo". Questo tipo di simboli sarà utile quando dovrete formattare il contenuto di un file di testo o di una email.

 Affrontiamo ora un altro problema: come facciamo ad inserire un apice all'interno di una stringa delimitata da singoli apici o un doppio apice in una stringa delimitata da doppi apici. Per evitare che questi caratteri vengano interpretati come "fine stringa" si fanno precedere dal simbolo \\. Vediamo un esempio.

 ```php
<?php
// stringa definita con i singoli apici
$stringa1 = 'Il romanzo \'I promessi Sposi\' fu scritto dal "Manzoni"';

// visualizziamo la stringa
echo $stringa1;

// vado a capo
echo '<br>';

//stringa definita con i doppi apici
$stringa2 = "Il romanzo \"I promessi Sposi\" fu scritto dal 'Manzoni'";

// visualizziamo la seconda stringa
echo $stringa2;
?>
```

 Questo esempio mostra anche che i singoli apici all'interno della prima stringa e i doppi apici all'interno della seconda stringa, non potendo essere confusi con dei delimitatori, possono essere inseriti senza problemi.

 E se volessimo unire due o più stringhe, accodandole una all'altra? Basta usare un punto!

 ```php
<?php
// tre stringhe
$stringa1 = 'Ciao';
$stringa2 = ' a ';
$stringa3 = 'tutti';

// unisco le variabili contenenti le stringhe
$stringa4 = $stringa1 . $stringa2 . $stringa3;

// visualizziamo la stringa complessiva
echo $stringa4;

// vado a capo
echo '<br>';

// unisco e mostro le stringhe direttamente
echo ('Ciao' . ' a ' . 'tutti');
?>
```

 L'esempio mostra come, attraverso l'uso del punto, sia possibile accodare più stringhe in un unico testo. Il punto può essere usato sia direttamente sulle stringhe delimitate da apici (singoli o doppi) sia sulle variabili che contengono stringhe. Il risultato sarà equivalente.

 PHP mette a disposizione molte funzioni per la manipolazione delle stringhe. A tal proposito conviene dare uno sguardo alla sezione del manuale ufficiale di PHP dedicata proprio alle [stringhe](http://www.php.net/manual/it/ref.strings.php).