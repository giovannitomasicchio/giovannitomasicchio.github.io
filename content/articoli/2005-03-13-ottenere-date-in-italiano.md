---
title: "Ottenere date in italiano"
author: "Giovanni Tomasicchio"
type: article
date: 2005-03-13T15:42:14+0000
description: "Ottenere date in italiano negli script PHP"
url: /articoli/ottenere-date-in-italiano/
categories:
  - Articoli
tags:

  
---
 Spesso si ha la necessità di presentare le date in un formato più gradevole del solito gg/mm/hhhh. Se la funzione [date](http://it.php.net/manual/it/function.date.php) se la cava egregiamente per questo formato un po' spartano, non si può dire altrettanto quando si cerca di ottenere l'indicazione del mese o del giorno della settimana in lettere (ad es. domenica 13 marzo 2005) poiché il risultato sarebbe in inglese.

 PHP mette a disposizione la funzione [strftime](http://it.php.net/manual/it/function.strftime.php) che in coppia con la funzione [setlocale](http://it.php.net/manual/it/function.setlocale.php) permette di ottenere delle date che rispettino la lingua impostata. Purtroppo però l'indicazione della lingua da passare a setlocale non è valida per qualsiasi sistema operativo ma dipende da questo.

 Il seguente esempio è valido per un server Windows:

 ```
<pre class="brush: php">
<?php
setlocale(LC_TIME, 'ita');

echo strftime("%A %d %B %Y");
?>
```

 Invece per un server Linux (sperimentato su Fedora Core I):

 ```
<pre class="brush: php">
<?php
setlocale(LC_TIME, 'it_IT');

echo strftime("%A %d %B %Y");
?>
```

 I casi possibili ovviamente non si esauriscono solo a questi due appena visti, sono molte infatti le segnalazioni di insuccessi delle precedenti stringhe della lingua.

 Inoltre, anche se si avesse l'intera casistica a disposizione, rimarrebbe il problema di individuare correttamente il sistema operativo del server se vogliamo rendere i nostri script portabili.

 Una soluzione un po' artigianale potrebbe essere la seguente, che si basa sulla semplice traduzione dei giorni della settimana e dei mesi dalla loro indicazione numerica al relativo nome in lettere:

 ```
<pre class="brush: php">
<?php
$mesi = array(1=>'gennaio', 'febbraio', 'marzo', 'aprile',
                'maggio', 'giugno', 'luglio', 'agosto',
                'settembre', 'ottobre', 'novembre','dicembre');

$giorni = array('domenica','lunedì','marted','mercoledì',
                'giovedì','venerdì','sabato');

list($sett,$giorno,$mese,$anno) = explode('-',date('w-d-n-Y'));

echo $giorni[$sett],' ',$giorno,' ',$mesi[$mese],' ',$anno;
?>
```

 Non sarà la soluzione più elegante ma è garantita al 100%!

 Dalla versione 4.3.0 di PHP la funzione setlocale è diventata però più flessibile. Infatti ora è possibile passare diverse stringe per individuare la lingua desiderata. In altre parole è possibile fondere i due esempi iniziali nel modo seguente:

 ```
<pre class="brush: php">
<?php
setlocale(LC_TIME, 'ita', 'it_IT');

echo strftime("%A %d %B %Y");
?>
```