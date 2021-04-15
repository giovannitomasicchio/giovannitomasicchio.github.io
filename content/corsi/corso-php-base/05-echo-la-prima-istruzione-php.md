---
title: "echo: la prima istruzione PHP"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:31:25+0000
description: "Mostrare del testo con PHP e la funzione echo"
url: /corsi/corso-php-base/echo-la-prima-istruzione-php/
categories:
  - Corso PHP base
  
---
 Adesso che abbiamo imparato come si inseriscono le istruzioni PHP all'interno del codice HTML, vediamo come si risolve uno dei più comuni compiti di uno script PHP: mostrare del testo all'utente. Il comando che useremo si chiama **echo**. Vediamo subito un esempio:

 ```php
<?php echo("Ciao Mondo !"); ?>
```

 Probabilmente vi sarete accorti che questo comando era già presente negli script visti nelle lezioni precedenti. Questa volta però la frase da visualizzare, più propriamente chiamata **stringa**, è racchiusa, oltre che dalle virgolette, anche dalle parentesi tonde. Niente panico! Il comando echo, infatti non è una normale funzione PHP (che richiederebbe l'uso delle parentesi) ma è un *costrutto*. Insieme a pochi altri costrutti del PHP, alcuni dei quali verranno mostrati nell'ultima lezione, echo può essere usato in entrambi i modi visti. Inoltre, se non vengono usate le parentesi, con echo è possibile concatenare più frasi usando semplicemente delle virgole:

 ```php
<?php
// con le parentesi
echo ("Ciao");
// senza le parentesi...
echo "Ciao";
// ...si possono accodare più frasi
echo "Ciao" , " a tutti." , "Come va?";
?>
```

 Mostriamo adesso diverse stringhe, una sotto l'altra:

 ```php
<?php
echo "Prima stringa, adesso andiamo a capo. <br>";
echo "Seconda riga .<br>";
echo "Ricordi l'HTML?";
?>
```

 Queste istruzioni ci permettono di fare un'importante considerazione. Siamo riusciti a mettere le precedenti stringhe su righe diverse grazie al **&lt;br&gt;**. Questo è un tag HTML che dice al browser di andare a capo. Morale della favola: qualsiasi cosa verrà prodotta dalle istruzioni PHP sarà visualizzata dal browser che si aspetta una pagina HTML. L'output di una pagina PHP deve quindi essere costituito da HTML. Non ne siete ancora convinti? Provate questo codice:

 ```php
<?php
echo "Normale<br>";
echo "<strong>Grassetto</strong><br>";
echo "<em>Corsivo</em>";
?>
```

 Tutto ciò che viene mostrato dal browser viene quindi interpretato come pagina HTML e come tale rappresentato. Quindi quando volete mostrare dei contenuti all'utente (una tabella, un elenco numerato, un'immagine, un link) dovete prima scoprire come si realizzano in HTML, e poi fare in modo che il risultato delle istruzioni PHP, ovvero il suo **output**, sia adeguato.

 Nelle prossime lezioni torneremo a parlare di stringhe, ma per approfondire l'argomento dobbiamo prima imparare ad usare le **variabili**.