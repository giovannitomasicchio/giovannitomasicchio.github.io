---
title: "Layout"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:22:22+0000
description: "Utilizzare Zend_Layout per centralizzare la gestione del codice HTML comune alle pagine di una applicazione MVC realizzata con lo Zend Framework"
url: /corsi/corso-zend-framework/layout/
categories:
  - Introduzione allo Zend Framework
  
---
E' facile capire che ci sarà molto codice HTML comune nelle nostre view, almeno per le sezioni header e footer e magari per una o due barre laterali. Questo è un problema molto comune ed il componente Zend\_Layout è progettato per risolverlo. Zend\_Layout ci consente di spostare tutti gli elementi comuni come header, footer e altri in uno script view di layout che a sua volta includerà il codice specifico per l'azione che verrà eseguita.

Il posto dove, di default, metteremo i nostri layouts è application/layouts/ e c'è una risorsa apposita per Zend\_Application che configurerà Zend\_Layout per noi. Useremo Zend\_Tool per creare il view script per il layout ed aggiorare il file application.ini. Ancora una volta, da un terminale o dal Prompt dei comandi, digitiamo il seguente comando nella directory zf-tutorial.

 ```

zf enable layout
```

Zend\_Tool adesso ha creato una cartella application/layouts/scripts e posizionato al suo interno uno script view chiamato layout.phtml. Inoltre ha aggiornato application.ini e aggiunto la linea resources.layout.layoutPath = APPLICATION\_PATH "/layouts/scripts/" nella sezione \[production\].

Alla fine del ciclo di esecuzione (ciclo di dispatch), quando l'esecuzione del metodo action del controller si è conclusa, Zend\_Layout elaborerà il nostro layout. Zend\_Tool mette a disposizione un file di layout davvero basilare che si limita a mostrare il contenuto dello script view dell'action. Noi lo integreremo con il codice HTML necessario al nostro sito web. Apriamo layouts.phtml e sostituiamo il codice con:

zf-tutorial/application/layouts/scripts/layout.phtml

 ```
<pre class="brush: php">
<?php
$this->headMeta()->appendHttpEquiv('Content-Type', 'text/html;charset=utf-8');
$this->headTitle()->setSeparator(' - ');
$this->headTitle('Zend Framework Tutorial');

echo $this->doctype(); ?>
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
<head>
    <?php echo $this->headMeta(); ?>
    <?php echo $this->headTitle(); ?>
</head>
<body>
<div id="content">
    <h1><?php echo $this->escape($this->title); ?></h1>
    <?php echo $this->layout()->content; ?>
</div>
</body>
</html>
```

Il file di layout contiene il codice HTML "esterno" che è piuttosto standard. Dato che si tratta di un normale file PHP, possiamo usare il PHP al suo interno. E' diponibile la variabile $this che contiene una istanza dell'oggetto view creato nella fase di bootstrap. Possiamo usarla per recuperare i dati che sono stati assegnati alla view ed inoltre chiamare i suoi metodi. I metodi (detti view helpers) ritornano una stringa che possiamo stampare con un echo.

Prima di tutto, configureremo alcuni view helpers per la sezione head della pagina web per poi stampare il doctype corretto. All'interno di &lt;body&gt;, creeremo un div con un &lt;h1&gt; che conterrà il titolo. Per ottenere lo script view dell'action corrente che dobbiamo visualizzare, stamperemo con un echo il contenuto restituito dal view helper layout(): echo $this-&gt;layout()-&gt;content; che farà questa cosa per noi. Ciò significa che gli script view delle action vengono elaborati prima dello script view di layout.

E' necessario impostare il doctype delle pagine web prima di creare qualsiasi script view. Questo perchè gli script view delle varie action devono sapere quale doctype è impostato dato che vengono eseguiti prima del layout. Questo è estremamente importante, ad esempio, per il componente Zend\_Form.

Per impostare il doctype basta aggiungere una lina ad application.ini nella sezione \[production\]:

 ```

resources.view.doctype = "XHTML1_STRICT"
```

Il view helper doctype() darà come output il corretto doctype ed i componenti come Zend\_Form genereranno il codice HTML compatibile.