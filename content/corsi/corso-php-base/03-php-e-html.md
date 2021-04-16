---
title: "PHP e HTML"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:27:21+0000
description: "Relazione tra PHP e HTML"
url: /corsi/corso-php-base/php-e-html/
weight: 18
categories:
  - Corso PHP base
  
---
 Torniamo sul primo esempio di pagina PHP visto:

 ```php
<html>
<head>
<title>Pagina di prova</title>
</head>
<body>
<p>Sono le ore <?php echo date("G.i"); ?></p>
</body>
</html>
```

 La colorazione del precedente codice evidenzia come il normale codice HTML subisca ad un certo punto una interruzione, causata da quello che prende il nome di **tag di apertura PHP &lt;?php**. Similmente il codice HTML riprende subito dopo un'altra sequenza di caratteri **?&gt;** che costituiscono il **tag di chiusura di PHP**.

 Da questo semplice esempio possiamo capire tre cose molto importanti:

- Il PHP convive con l'HTML, nel senso che una pagina PHP è costituita il più delle volte da una fusione di HTML e istruzioni PHP.
- Le istruzioni PHP sono separate dal codice HTML attraverso due delimitatori, il tag di apertura &lt;?php ed il tag di chiusura ?&gt;. Provate a toglierli e vedete i risultati...
- Ogni istruzione PHP termina con un punto e virgola.
 
 A volte potrete trovare questo tag di apertura: **&lt;?**. Si tratta di una versione "ridotta", che non sempre funziona (dipende da alcune impostazioni del PHP) e che quindi è meglio evitare. Per completezza segnalo anche la possibilità, rara, di poter utilizzare dei tag del tipo &lt;% e %&gt;.

 Guardate adesso questo esempio di pagina PHP, salvatela in un file e visualizzatela:

 ```php
<?php echo date("G.i"); ?>
```

 Come era intuibile, continuiamo a vedere l'indicazione dell'ora corrente anche se abbiamo eliminato tutto l'HTML presente nel primo esempio. Il fatto è che i browser sono abbastanza clementi con le pagine web che vengono visualizzate anche se non rispettano la sintassi HTML (in questo caso completamente assente). Questa volta il risultato ottenuto è totalmente a carico delle istruzioni PHP. Anche se questa non è certo una buona norma da seguire, nei prossimi esempi eviteremo di riportare l'HTML che non è strettamente necessario. Potremo così ridurre la dimensione dei nostri script e focalizzare l'attenzione sulle istruzioni PHP.