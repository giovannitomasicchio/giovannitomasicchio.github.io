---
title: "Inserire un nuovo album"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:27:27+0000
description: "Inserimento di un nuovo record nel database con Zend Framework"
url: /corsi/corso-zend-framework/inserire-un-nuovo-album/
weight: 4
categories:
  - Introduzione allo Zend Framework
  
---
Creiamo adesso la funzione per aggingere un nuovo album. Ci sono due cose da fare:

- visualizzare un form all'utente affinché possa inserire i dati
- processare l'invio del form e salvare i dati nel database
 
Usiamo Zend\_Form per fare il tutto. Il componente Zend\_Form permette di creare un form e validare i dati inseriti. Creiamo una nuova classe Form\_Album che estende Zend\_Form per definire il nostro form. Come una risorsa dell'applicazione, la classe viene salvata nel file Album.php nella directory forms. Iniziamo usando lo strumento da riga di comando zf per creare il file corretto:

 ```

zf create form Album
```

Questo crea il file Album.php in application/forms che include un metodo init() dove si può impostare il form e aggiungere gli elementi di cui abbiamo bisogno. Modifichiamo application/forms/Album.php, cancelliamo i commenti in init() e aggiungiamo quanto segue:

zf-tutorial/application/forms/Album.php

 ```php
<?php
class Application_Form_Album extends Zend_Form
{
    public function init()
    {
    $this->setName('album');

    $id = new Zend_Form_Element_Hidden('id');
    $id->addFilter('Int');

    $artist = new Zend_Form_Element_Text('artist');
    $artist->setLabel('Artist')
        ->setRequired(true)
        ->addFilter('StripTags')
        ->addFilter('StringTrim')
        ->addValidator('NotEmpty');

    $title = new Zend_Form_Element_Text('title');
    $title->setLabel('Title')
        ->setRequired(true)
        ->addFilter('StripTags')
        ->addFilter('StringTrim')
        ->addValidator('NotEmpty');

    $submit = new Zend_Form_Element_Submit('submit');
    $submit->setAttrib('id', 'submitbutton');

    $this->addElements(array($id, $artist, $title, $submit));
    }
}
```

Con il metodo init() di Application\_Form\_Album, creiamo quattro elementi per l'id, l'artista, il titolo ed il bottone di submit. Per ogni elemento impostiamo vari attributi, inclusi le label da visualizzare. Per id, vogliamo assicurarci che sia inserito solo un numero intero per prevenire potenziali attacchi di tipo SQL injection. Il filtro Int farà questo per noi.

Per gli elementi di tipo testo, aggiungiamo due filtri, StripTags e StringTrim per rimuovere codice HTML e spazi bianchi inutili. Li impostiamo inoltre come obbligatori (required) e aggiungiamo un controllo (validator) di tipo NotEmpty per assicurarci che l'utente inserisca le informazioni necessarie. (Il controllo NotEmpty non è tecnicamente necessario dato che è automatiamente aggiunto dal sistema quando setRequired() è impostato a 'true'; è stato inserito solo per mostrare come aggiungere un validator.)

Abbiamo ora bisogno di visualizzare il form e processare l'invio dei dati. Ciò avviene all'interno di addAction() dell'IndexController:

zf-tutorial/application/controllers/IndexController.php

 ```php
 ...
 function addAction()
 {
    $form = new Application_Form_Album();
    $form->submit->setLabel('Add');
    $this->view->form = $form;
    if ($this->getRequest()->isPost()) {
        $formData = $this->getRequest()->getPost();
        if ($form->isValid($formData)) {
        $artist = $form->getValue('artist');
        $title = $form->getValue('title');
        $albums = new Application_Model_DbTable_Albums();
        $albums->addAlbum($artist, $title);

        $this->_helper->redirector('index');
        } else {
        $form->populate($formData);
        }
    }
 }
 ...
```

Esaminiamo il codice più in dettaglio:

 ```php
$form = new Application_Form_Album();
$form->submit->setLabel('Add');
$this->view->form = $form;
```

Istanziamo il nostro Form\_Album, impostiamo la label per il bottone submit ad "Add" e poi assegniamo il tutto all'oggetto view per il rendering.

 ```php
if ($this->getRequest()->isPost()) {
    $formData = $this->getRequest()->getPost();
    if ($form->isValid($formData)) {
```

Se il metodo isPost() dell'oggetto request restituisce true, allora il form è stato inviato e si possono recuperare i dati del form con getPost() e di conseguenza controllare la validità dello stesso usando la funzione isValid().

 ```php
$artist = $form->getValue('artist');
$title = $form->getValue('title');
$albums = new Application_Model_DbTable_Albums();
$albums->addAlbum($artist, $title);
```

Se il form è valido, si può istanziare il model Application\_Model\_DbTable\_Albums e usare addAlbum(), creato precedentemente, per inserire un nuovo record nel database.

 ```php
$this->_helper->redirector('index');
```

Dopo aver salvato il nuovo album, si procede con un redirect usando l'action helper Redirector per ritornare all'action index (ovvero per tornare alla home page).

 ```php
} else {
    $form->populate($formData);
}
```

Se i dati del form non sono validi, allora popoliamo il form con i dati che l'utente ha inserito e ri-visualizziamo il form.

Adesso dobbiamo creare il form nello script view add.phtml:

zf-tutorial/application/views/scripts/index/add.phtml

 ```php
<?php
$this->title = "Add new album";
$this->headTitle($this->title);
echo $this->form ;
?>
```

Come possiamo vedere, mostrare un form è davvero facile - basta semplicemente stamparlo con un echo poiché il form sa esattamente come deve mostrarsi. Adesso dovremmo essere in grado di usare il link "Add new album" presente nella home page dell'applicazione per aggiungere un nuovo album.