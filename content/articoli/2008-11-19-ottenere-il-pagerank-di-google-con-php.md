---
title: "Ottenere il pagerank di Google con PHP"
author: "Giovanni Tomasicchio"
type: article
date: 2008-11-19T20:20:38+0000
description: "Come calcolare il pagerank di Google con PHP"
url: /articoli/ottenere-il-pagerank-di-google-con-php/
categories:
  - Articoli
tags:

  
---
Dal sito Google Code è possibile [scaricare due classi](http://code.google.com/p/popstats/) per PHP 5 che permettono di ottenere il pagerank di un sito web, ovvero l'indice utilizzato da Google per valutare la popolarità di un sito internet.

Per provare queste classi basta salvare in una cartella i file [google\_pagerank.class.php](http://popstats.googlecode.com/svn/trunk/google_pagerank.class.php) e [cacher.class.php](http://popstats.googlecode.com/svn/trunk/cacher.class.php) e poi realizzare un semplice script come il seguente:

 ```
<pre class="brush: php">
<?php
include('google_pagerank.class.php');
$URLSito = 'http://www.repubblica.it';
$googlePageRank = new GooglePageRank($URLSito);
echo "Il pagerank di $URLSito è ", $googlePageRank->pagerank;
?>
```

Di default la classe GooglePageRank utilizza la classe Cacher per memorizzare in un file di testo il risultato di una interrogazione del pagerank per un particolare sito, in modo da evitare di doverla ripetere di frequente. La durata di un pagerank in cache è di 24 ore ma può essere modificata specificando il numero di secondi nel seguente modo:

 ```
<pre class="brush: php">
$durataCache = 60; // 60 secondi
$googlePageRank = new GooglePageRank($URLSito, $durataCache);
```