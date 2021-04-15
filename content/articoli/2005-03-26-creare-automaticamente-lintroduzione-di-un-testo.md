---
title: "Creare automaticamente l'introduzione di un testo"
author: "Giovanni Tomasicchio"
type: article
date: 2005-03-26T16:13:56+0000
description: "Script PHP per creare l'introduzione di un testo"
url: /articoli/creare-automaticamente-lintroduzione-di-un-testo/
categories:
  - Articoli
tags:
  - "Regex"

  
---
 Spesso i siti web che contengono notizie, articoli e documenti di varia natura mostrano nella pagina principale una breve introduzione di questi contenuti che rimanda all'articolo completo. In molti casi questa introduzione viene scritta dallo stesso autore oppure è ottenuta prendendo i primi caratteri dell'articolo completo. Vediamo come realizzare questa tecnica cercando di renderla più raffinata.

 Il nostro scopo è quello di individuare il punto in cui è più opportuno tagliare il testo dell'articolo così da ottenere una introduzione. Per fare ciò cerchiamo tutte le interruzioni di frase, individuabili dalla presenza di un punto e di uno spazio, segno di tabulazione o carattere di fine riga.

 Supponendo di voler generare introduzioni con una determinata lunghezza media, cerchiamo quindi l'interruzione di frase più prossima a questa lunghezza e ne memorizziamo la posizione. Fatto ciò non ci resta che estrarre una sottostringa dalla stringa originale fermandoci alla posizione appena calcolata.

 L'esempio che segue realizza la procedura descritta:

 ```
<pre class="brush: php">
<?php
$frase = "Questa è un articolo molto lungo.\nUna frase\n".
            "a cavallo. Voglio riuscire a estrapolarne una introduzione.\n".
            "Deve essere lunga circa 150 caratteri. ".
            "Frase uno.\n Frase 2. Frase 3.";

echo creaIntro($frase, 150);

function creaIntro($frase,$numero_di_caratteri)
{
    // cerco i punti in cui terminano le frasi
    preg_match_all("/(\.\s)+/",$frase,$out,PREG_OFFSET_CAPTURE);

    // cerco l'interruzione più vicina a $numero_di_caratteri
    $minima_distanza = strlen($frase);
    $offset_minima_distanza = false;

    foreach ($out[0] as $offset) {
        $distanza = abs($numero_di_caratteri - $offset[1]);
        if($distanza < $minima_distanza) {
            $minima_distanza = $distanza;
            $offset_minima_distanza = $offset[1];
        }
    }

    if($offset_minima_distanza) {
        // restituisco l'introduzione some sottostringa
        // di lunghezza $offset_minima_distanza + 1
        echo substr($frase, 0, $offset_minima_distanza + 1);
    } else {
        // se non ho trovato una interruzione restituisco tutta la frase
        echo $frase;
    }
}
?>
```

 Poiché la procedura non è velocissima a causa dell'espressione regolare si potrebbe pensare di memorizzare la variabile $offset\_minima\_distanza ovvero il punto in cui viene effettuato il "taglio" dell'introduzione in modo da non doverlo ricalcolare ad ogni nuova visualizzazione.