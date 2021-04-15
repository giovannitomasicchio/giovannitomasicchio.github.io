---
title: "Cloaking con PHP"
author: "Giovanni Tomasicchio"
type: article
date: 2007-01-13T22:08:35+0000
description: "Creare uno script PHP che effettua il cloaking"
url: /articoli/cloaking-con-php/
categories:
  - Articoli
tags:
  - "SEO"

  
---
 Il **cloaking** è una pratica messa in atto da molti SEO (search engine optimizers) per migliorare la posizione di un certo sito nelle SERP (i risultati) di un motore di ricerca, in riferimento a certe keywords. La parola "cloaking" significa mascheramento e ben descrive quello che il processo in analisi mira a compiere.

 In pratica uno script server side (scritto in PHP o altro linguaggio) si occupa di svolgere le seguenti azioni:

- rilevare la natura dell'user agent (un identificativo del software) che richiede la pagina;
- restituire dei risultati "personalizzati" a seconda dell'esito della rilevazione svolta al punto precedente.
 
 Ora ci si può chiedere perchè un webmaster dovrebbe praticare questa "personalizzazione" dei contenuti veicolati dal proprio sito. Chi ha esperienza come SEO sa bene che i contenuti allettanti per gli utenti in carne ed ossa sono ben diversi dai contenuti che un motore di ricerca vorrebbe trovare per etichettare un sito come "interessante", in riferimento ad una certa keyword. Ne consegue che la personalizzazione dei contenuti risulta in molti casi un'arma vincente per ottenere risultati eccellenti, benché non completamente lecita.

 Va infatti ricordato come la pratica qui esposta sia contro le policy dei motori di ricerca, i quali pretenderebbero, legittimamente o meno, che i propri spider avessero accesso agli stessi contenuti destinati agli utenti reali. Il cloaking potrebbe portare infatti ad una disarmonia tra la qualità degli argomenti "percepita" dal motore di ricerca e l'effettivo valore attribuito alla risorsa dagli utenti dello stesso motore.

 Al di la delle considerazioni etiche sull'attività di SEO e al di la di quali siano le caratteristiche dei contenuti che garantiscono un buon ranking nelle SERP dei motori, di seguito viene mostrato un semplice script PHP per praticare il cloaking.

 ```
<pre class="brush: php">
<?php
$pagina_ottimizzata_per_gg = <<<EOF
codice html pagina sovraottimizzata
EOF;

$pagina_per_utenti = <<<EOF
codice html pagina per utenti reali
EOF;

$us_agent = $_SERVER['HTTP_USER_AGENT'];

$gg_agent_string = "Googlebot";

if(strstr($us_agent, $gg_agent_string)) {
   echo $pagina_ottimizzata_per_gg;
}
else {
   echo $pagina_per_utenti;
}
?>
```

 Come vedete la variabile $pagina\_ottimizzata\_per\_gg conterrà il codice html dei contenuti che si vogliono restituire allo spider (in questo caso Googlebot - lo spider di Google). La variabile $pagina\_per\_utenti conterrà invece i contenuti reali destinati agli utenti. La variabile $us\_agent raccoglie infine il valore della variabile del server relativa all'user agent richiedente la pagina. [Ecco qui](http://www.pgts.com.au/pgtsj/pgtsj0208d.html) una lista delle stringhe presenti nell'header della richiesta HTTP mandate dai vari spider presenti sull'attuale panorama dei motori di ricerca. Come vedete Googlebot manda la stringa:

 Googlebot/2.1 (+http://www.googlebot.com/bot.html)

 Di conseguenza con l'istruzione:

 ```
<pre class="brush: php">
strstr($us_agent, $gg_agent_string)
```

 riusciamo a controllare se nella stringa relativa allo user agent è presente la parola Google. Se il controllo ha esito positivo questo significa necessariamente che ci troviamo di fronte allo spider di Google. Come vedete lo script restituisce in questo caso la variabile $pagina\_ottimizzata\_per\_gg. In tutti gli altri casi lo script restituisce come risultato il codice html con i contenuti dedicati agli utenti. Appare chiaro che il processo di verifica dello user agent potrebbe essere esteso in modo da verificare se altri spider richiedono la pagine, in modo da inviare contenuti specifici per il particolare motore di ricerca.