---
title: "Modificare un album"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:28:40+0000
description: "Update di un record del database in una applicazione MVC realizzata con Zend Framework"
url: /corsi/corso-zend-framework/modificare-un-album/
categories:
  - Introduzione allo Zend Framework
  
---
La modifica di un album è quasi identica all'inserimento, il codice è molto simile:

zf-tutorial/application/controllers/IndexController.php

 ```php
 ...
 function editAction()
 {
    $form = new Application_Form_Album();
    $form->submit->setLabel('Save');
    $this->view->form = $form;

    if ($this->getRequest()->isPost()) {
        $formData = $this->getRequest()->getPost();
        if ($form->isValid($formData)) {
            $id = (int)$form->getValue('id');
            $artist = $form->getValue('artist');
            $title = $form->getValue('title');
            $albums = new Application_Model_DbTable_Albums();
            $albums->updateAlbum($id, $artist, $title);
       
            $this->_helper->redirector('index');
        } else {
            $form->populate($formData);
        }
    } else {
        $id = $this->_getParam('id', 0);
        if ($id > 0) {
            $albums = new Application_Model_DbTable_Albums();
            $form->populate($albums->getAlbum($id));
        }
    }
 }
 ...
```

Vediamo le differenze rispetto all'inserimento di un album. Prima di tutto, quando si visualizza il form all'utente abbiamo bisogno di recuperare il titolo e l'artista dell'album dal database per poi popolare gli elementi del form con questi dati. Ciò avviene alla fine del metodo:

 ```php
$id = $this->_getParam('id', 0);
if ($id > 0) {
    $albums = new Application_Model_DbTable_Albums();
    $form->populate($albums->getAlbum($id));
}
```

Notiamo che ciò viene eseguito se non c'è stata una richiesta via POST, dato che il POST implica che è stato riempito il form e che lo si vuole processare. Per la visualizzazione iniziale del form, recuperiamo l'id dalla request usanto il metodo \_getParam(). Poi usiamo il model per recuperare la riga corrispondente nel database e popolare direttamente il form con i dati. (Adesso sarà chiaro perchè il metodo getAlbum() nel model restituisce un array!)

Dopo aver validato il form, abbiamo bisogno di salvare i dati nel record corretto del database. Lo facciamo usando il nostro metodo updateAlbum() del model:

 ```php
$id = $form->getValue('id');
$artist = $form->getValue('artist');
$title = $form->getValue('title');
$albums = new Application_Model_DbTable_Albums();
$albums->updateAlbum($id, $artist, $title);
```

Il template dello script di view è lo stesso di add.phtml:

zf-tutorial/application/views/scripts/index/edit.phtml

 ```php
<?php
$this->title = "Edit album";
$this->headTitle($this->title);

echo $this->form ;
?>
```

Adesso dovremmo essere in grado di modificare gli album.