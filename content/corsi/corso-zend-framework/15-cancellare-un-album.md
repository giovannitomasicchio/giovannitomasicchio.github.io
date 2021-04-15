---
title: "Cancellare un album"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:30:26+0000
description: "cancellazione di un record dal database in una applicazione MVC realizzata con lo Zend Framework"
url: /corsi/corso-zend-framework/cancellare-un-album/
categories:
  - Introduzione allo Zend Framework
  
---
Per completare la nostra applicazione, dobbiamo aggiungere la cancellazione. Abbiamo un link "Delete" accanto ad ogni album nel nostro elenco e l'approccio più semplice sarebbe quello di fare una cancellazione diretta quando si fa click sul link. Ciò sarebbe sbagliato. Ricordando le specifiche dell'HTTP, non dovremmo effettuare un'operazione irreversible attraverso una richiesta GET, bensì attraverso una richiesta di tipo POST.

Quando un utente clicca su "Delete" dobbiamo mostrare un form di conferma e se poi si fa click su "yes" si procede con la cancellazione. Dato che il form è semplice, metteremo il codice direttamente nella nostra view (Zend\_Form è, dopo tutto, uno strumento opzionale!).

Iniziamo con il codice della action in IndexController::deleteAction():

zf-tutorial/application/controllers/IndexController.php

 ```php
 ...
 public function deleteAction()
 {
    if ($this->getRequest()->isPost()) {
        $del = $this->getRequest()->getPost('del');
        if ($del == 'Yes') {
        $id = $this->getRequest()->getPost('id');
        $albums = new Application_Model_DbTable_Albums();
        $albums->deleteAlbum($id);
        }
        $this->_helper->redirector('index');
    } else {
        $id = $this->_getParam('id', 0);
        $albums = new Application_Model_DbTable_Albums();
        $this->view->album = $albums->getAlbum($id);
    }
 }
 ...
```

Come per l'inserimento e la modifica, useremo il metodo della request isPost() per determinare se dobbiamo far vedere il form di conferma o se dobbiamo procedere con la cancellazione. Useremo il model Application\_Model\_DbTable\_Albums per eliminare la riga usando il metodo deleteAlbum(). Se la richiesta non è un POST, allora leggeremo il parametro id e recupereremo dal database il record corrispondente da assegnare alla view.

Lo script view è un semplice form:

zf-tutorial/application/views/scripts/index/delete.phtml

 ```php
<?php
$this->title = "Delete album";
$this->headTitle($this->title);
?>
<p>Are you sure that you want to delete
    '<?php echo $this->escape($this->album['title']); ?>' by
    '<?php echo $this->escape($this->album['artist']); ?>'?
</p>
<form action="<?php echo $this->url(array('action'=>'delete')); ?>" method="post">
<div>
    <input type="hidden" name="id" value="<?php echo $this->album['id']; ?>" />
    <input type="submit" name="del" value="Yes" />
    <input type="submit" name="del" value="No" />
</div>
</form>
```

In questo script mostriamo un messaggio di conferma all'utente e poi un form con i bottoni "yes" e "no". Nell'action controlliamo nello specifico il valore "Yes" per effettuare la cancellazione.

Questo è tutto, adesso abbiamo a disposizione un'applicazione completa e funzionante.