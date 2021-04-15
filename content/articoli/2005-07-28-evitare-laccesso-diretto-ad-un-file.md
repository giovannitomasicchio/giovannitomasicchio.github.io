---
title: "Evitare l'accesso diretto ad un file"
author: "Giovanni Tomasicchio"
type: article
date: 2005-07-28T08:18:52+0000
description: "Bloccare l'accesso diretto ad uno script PHP"
url: /articoli/evitare-laccesso-diretto-ad-un-file/
categories:
  - Articoli
tags:

  
---
 Quando si realizza uno script PHP che prevede l'inclusione di diversi files è conveniente evitare che questi possano essere richiamati direttamente dall'utente. Il modo più semplice per ottenere tale risultato consiste nel posizionare tutti gli script da includere al di fuori della root del server web, cioè esternamente alla sua cartella principale. E se ciò non fosse possibile?

 Non tutti i fornitori di hosting infatti permettono di gestire files al di fuori della root. In questi casi è possibile affidare al PHP il compito di controllare tali accessi diretti, semplicemente aggiungendo il seguente codice in testa a tutti gli script da proteggere:

 ```
<pre class="brush: php">
if(__FILE__ == $_SERVER['SCRIPT_FILENAME'])
{
    exit('Accesso non consentito') ;
}
```

 Il funzionamento è molto semplice: la variabile $\_SERVER\['SCRIPT\_FILENAME'\] contiene il nome dello script richiesto dall'utente mentre la costante \_\_FILE\_\_ è impostata automaticamente dal PHP con il nome del file corrente. Se questi due valori coincidono significa che l'utente sta cercando di accedere direttamente al file corrente e pertanto l'esecuzione dello script viene interrotta.

 Per chiarire ulteriormente questa tecnica consideriamo il seguente esempio: un file dal nome index.php include lo script config.php. Per evitare che quest'ultimo possa essere chiamato direttamente da un utente procediamo nel seguente modo:

 **- index.php**

 ```
<pre class="brush: php">
<?php
// includiamo il file di configurazione
include('config.php');

// il resto del file...
?>
```

 **- config.php**

 ```
<pre class="brush: php">
<?php
// evitiamo l'inclusione diretta
if(__FILE__ == $_SERVER['SCRIPT_FILENAME'])
{
    exit('Accesso non consentito') ;
}

// il resto del file...
?>
```

 Nel caso in cui si acceda direttamente al file config.php il valore di \_\_FILE\_\_ e di $\_SERVER\['SCRIPT\_FILENAME'\] coincideranno e pertanto l'esecuzione verrà terminata.