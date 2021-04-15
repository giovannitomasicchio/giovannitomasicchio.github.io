---
title: "Model"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:18:53+0000
description: "Creare il model di una applicazione MVC con Zend Framework utilizzando Zend_Db_Table"
url: /corsi/corso-zend-framework/model/
categories:
  - Introduzione allo Zend Framework
  
---
Zend Framework non fornisce una classe Zend\_Model, dato che il model è la nostra logica di business ed è lasciata a noi la decisione di come farla funzionare. Ci sono molti componenti che possimamo usare, dipende dalle necessità. Un approccio consiste nell'avere una classe model per ciascuna entità della nostra applicazione e poi usere degli oggetti "mapper" per caricare e salvare le varie entità nel database. Questo approccio è documentato nella guida QuickStart a Zend Framework: [http://framework.zend.com/manual/en/learning.quickstart.create-model.html](http://framework.zend.com/manual/en/learning.quickstart.create-model.html.).

Per questo tutorial, andremo a creare un model che estende Zend\_Db\_Table e usa Zend\_Db\_Table\_Row. Zend Framework fornisce Zend\_Db\_Table che implementa il pattern Table Data Gateway che permette di interfacciarsi ai dati di una tabella del database. Dobbiamo essere consapevoli che il pattern Table Data Gateway può essere limitativo in un sistema più ampio. Potremmo avere anche la tentazione di mettere il codice di accesso al database in una action di un controller poiché questo viene esposto da Zend\_Db\_Table.

Zend\_Db\_Table\_Abstract è una classe astratta, dalla quale deriveremo la nostra classe specifica per gestire gli album. Non ha importanza come chiameremo la nostra classe, ma ha senso chiamarla con lo stesso nome della tabella del database.

Il nostro progetto dispone di un autoloader di default istanziato da Zend\_Application che mappa le classi di risorsa appartenenti ad un modulo nella directory dove esso è definito. Per la cartella principale application/ useremo il prefisso Application\_.

L'autoloader associa le risorse e le relative directory in questo modo:

 | Prefisso | Directory |
|---|---|
| Form | forms |
| Model | models |
| Model\_DbTable | models/DbTable |
| Model\_Mapper | models/mappers |
| Plugin | plugins |
| Service | services |
| View\_Filter | views/filters |
| View\_Helper | views/helper |

Poiché chiameremo il model con lo stesso nome della tabella del database "albums" ed useremo Zend\_Db\_Table, la nostra classe si chiamerà Application\_Model\_DbTable\_Albums, che verrà salvata in applications/models/DbTable/Albums.php.

Per indicare a Zend\_Db\_Table il nome della tabella che useremo, dobbiamo impostare un attributo protetto $\_name con il nome della tabella. Inoltre Zend\_Db\_Table assumerà che la nostra tabella possieda una chiave primaria di nome "id" di tipo auto-increment. Anche il nome di questa colonna può essere cambiato se necessario.

Possiamo usare il comando zf per farci fare parte del lavoro, quindi lanciamo il seguente comando da terminale:

 ```

zf create db-table Albums albums
```

Lo strumento a riga di comando ha creato adesso il file Albums.php nella cartella application/models/DbTable. All'interno di questo file c'è la classe Application\_Model\_DbTable\_Albums nella quale è impostato il nome della tabella del database con cui comunicherà.

Adesso dobbiamo aggiungere qualche funzionalità, modifichiamo quindi application/models/DbTable/Albums.php e aggiungiamo i metodi getAlbum(), addAlbum(), updateAlbum() e deleteAlbum() e ora dovrebbe assomigliare a qualcosa di simile a questo:

zf-tutorial/application/models/DbTable/Albums.php

 ```php
<?php
class Application_Model_DbTable_Albums extends Zend_Db_Table_Abstract
{
    protected $_name = 'albums';

    public function getAlbum($id)
    {
    $id = (int)$id;
    $row = $this->fetchRow('id = ' . $id);
    if (!$row) {
        throw new Exception("Could not find row $id");
    }
    return $row->toArray();
    }

    public function addAlbum($artist, $title)
    {
    $data = array(
        'artist' => $artist,
        'title' => $title,
    );
    $this->insert($data);
    }

    public function updateAlbum($id, $artist, $title)
    {
    $data = array(
        'artist' => $artist,
        'title' => $title,
    );
    $this->update($data, 'id = '. (int)$id);
    }

    public function deleteAlbum($id)
    {
    $this->delete('id =' . (int)$id);
    }
}
```

Abbiamo creato quattro metodi helper che la nostra applicazione userà per interfacciarsi alla tabella del database. getAlbum() recupera una singola riga sotto forma di array, addAlbum() crea una nuova riga nel database, updateAlbum() aggiorna la riga di un album e deleteAlbum() rimuove completamente una riga. Il codice di ogni metodo è auto esplicativo. Sebbene non necessario ai fini di questo tutorial, è anche possibile indicare a Zend\_Db\_Table le tabelle correlate in modo che possa trovare anche dati relazionati.
 Ora abbiamo bisogno di riempire il controller dei dati provenienti dal model affinché gli script di view li possano mostrare, ma prima di procedere dobbiamo capire come funziona il sistema di view di Zend Framework.