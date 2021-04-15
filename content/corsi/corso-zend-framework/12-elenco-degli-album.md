---
title: "Elenco degli album"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:25:28+0000
description: "Visualizzazione dell'elenco dei record letti dal database in una applicazione MVC con Zend Framework"
url: /corsi/corso-zend-framework/elenco-degli-album/
categories:
  - Introduzione allo Zend Framework
  
---
Ora che si è impostata la configurazione, le informazioni per il database e lo scheletro delle viste, possiamo entrare nel cuore dell'applicazione e visualizzare gli album. Ciò avviene nella classe IndexController ed inizieremo con il visualizzare gli album in una tabella con il metodo indexAction():

zf-tutorial/application/controllers/IndexController.php

 ```php
...
function indexAction()
{
    $albums = new Application_Model_DbTable_Albums();
    $this->view->albums = $albums->fetchAll();
}
...
```

Istanziamo il nostro model basato sul pattern "table data gateway". Il metodo fetchAll() restituisce un oggetto Zend\_Db\_Table\_Rowset che ci permetterà di ciclare sulle righe ottenute all'interno dello script view dell'action.

Possiamo ora completare lo script view associato: index.phtml:

zf-tutorial/application/views/scripts/index/index.phtml

 ```php
<?php
    $this->title = "My Albums";
    $this->headTitle($this->title);
?>
<p><a href="<?php echo $this->url(array('controller'=>'index',
    'action'=>'add'));?>">Add new album</a></p>
<table>
<tr>
    <th>Title</th>
    <th>Artist</th>
    <th>&nbsp;</th>
</tr>
<?php foreach($this->albums as $album) : ?>
<tr>
    <td><?php echo $this->escape($album->title);?></td>
    <td><?php echo $this->escape($album->artist);?></td>
    <td>
    <a href="<?php echo $this->url(array('controller'=>'index',
        'action'=>'edit', 'id'=>$album->id));?>">Edit</a>
    <a href="<?php echo $this->url(array('controller'=>'index',
        'action'=>'delete', 'id'=>$album->id));?>">Delete</a>
    </td>
</tr>
<?php endforeach; ?>
</table>
```

La prima cosa che facciamo è impostare il titolo della pagina (usato nel layout) ed impostare il titolo per la sezione &lt;head&gt;, usando il view helper headTitle(), che verrà mostrato nella barra del titolo del browser. Creiamo poi un link per aggiungere un nuovo album. Il view helper url() fornito dal framework ci aiuta a creare i link con il corretto URL. Basta passergli in input un array di parametri e lui farà il resto.

Poi creiamo una tabella html in cui visualizzare per ciascun album il titolo, gli artisti ed i link per la modifica e cancellazione del record. Usiamo un classico ciclo foreach per iterare sulla lista degli album, impiegando la sintassi alternativa che fa uso del due punti e dell'endforeach, più semplice da leggere rispetto alle parentesi graffe. Ancora una volta usiamo il view helper url() per creare i link di modifica e cancellazione.

Se si apre http://localhost/zf-tutorial/public/ si dovrebbe adesso vedere un elenco di album, qualcosa del genere:

![Elenco degli album](/images/stories/Corsi/introduzione_zend_framework/my_albums.png)