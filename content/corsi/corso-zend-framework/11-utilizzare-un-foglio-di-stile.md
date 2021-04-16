---
title: "Utilizzare un foglio di stile"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:24:10+0000
description: "Utilizzare un foglio di stile CSS in una applicazione MVC realizzata con lo Zend Framework"
url: /corsi/corso-zend-framework/utilizzare-un-foglio-di-stile/
weight: 6
categories:
  - Introduzione allo Zend Framework
  
---
Anche se questo è solo un tutorial, avremo bisogno di un file CSS per rendere la nostra applicazione un pochino più presentabile! Ciò ci crea un piccolo problema dato che non sappiamo come referenziare il file CSS poiché gli URL delle diverse pagine non puntano direttamente alla directory di root. Fortunatamente abbiamo a disposizione un view helper di nome baseUrl(). Questo helper raccoglie le informazioni di cui necessita dall'oggetto request e ci fornisce la parte di URL che non conosciamo. Possiamo adesso aggiungere il file CSS alla sezione &lt;head&gt; del file application/layouts/scripts/layouts.phtml e per farlo useremo un ulteriore helper, headLink():

zf-tutorial/application/layouts/scripts/layout.phtml

 ```php
...
<head>
    <?php echo $this->headMeta(); ?>
    <?php echo $this->headTitle(); ?>
    <?php echo $this->headLink()->prependStylesheet($this->baseUrl().'/css/site.css'); ?>
</head>
...
```

Usando il metodo prependStylesheet() di headLink(), abbiamo la possibilità di aggiungere file CSS aggiuntivi, e più specifici, a seconda dell'esecuzione degli script view del controller, aggiungendoli dopo site.css.

Infine abbiamo bisogno di un po' di stile CSS, creiamo quindi una directory css in public e aggiungiamo il file site.css con questo codice:

zf-tutorial/public/css/site.css

 ```css
body,html {
    margin: 0 5px;
    font-family: Verdana,sans-serif;
}
h1 {
    font-size: 1.4em;
    color: #008000;
}
a {
    color: #008000;
}

/* Table */
th {
    text-align: left;
}
td, th {
    padding-right: 5px;
}

/* style form */
form dt {
    width: 100px;
    display: block;
    float: left;
    clear: left;
}
form dd {
    margin-left: 0;
    float: left;
}
form #submitbutton {
    margin-left: 100px;
}
```

Questo dovrebbe rendere il tutto più carino.

Ora possiamo far pulizia nei quattro script action che sono già stati autogenerati, pronti per essere completati, procediamo quindi con lo svuotare i file index.phtml, add.phtml, edit.phtml e delete.phtml che, ricorderete, sono nella directory application/views/scripts/index.