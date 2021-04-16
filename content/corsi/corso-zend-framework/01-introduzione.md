---
title: "Introduzione"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T15:37:01+0000
description: "Presentazione del corso sullo Zend Framework e preparazione dell'ambiente di lavoro"
url: /corsi/corso-zend-framework/introduzione/
weight: 16
categories:
  - Introduzione allo Zend Framework
  
---
Questo corso è tratto dal tutorial [Getting Started with Zend Framework](http://akrabat.com/wp-content/uploads/Getting-Started-with-Zend-Framework.pdf) di Rob Allen, versione 1.7.5. La traduzione in italiano è a cura di [Mario Santagiuliana](autore/mario-santagiuliana/).

E' possibile scaricare tutti i sorgenti dell'applicazione di esempio a [questo indirizzo](http://akrabat.com/zend-framework-tutorial/).

 

L'obbiettivo di questa guida è fornire un'introduzione all'uso dello Zend Framework attraverso la creazione di una semplice applicazione per la gestione di un database adottando il paradigma Model-View-Controller (MVC).

**Attenzione**: questa guida è stata verificata con la version 1.10.1 di Zend Framework. Molto probabilmente funzionerà anche con le versioni successive della serie 1.x ma non con le versioni precedenti alla 1.10.1.

 

###  Requisiti

Zend Framework necessita di:

- PHP 5.2.4 (o superiore)
- Un web server che supporti il mod\_rewrite di apache o una funzionalità simile.
 
###  Presupposti previsti dalla guida

Stiamo assumendo di utilizzare PHP 5.2.4 o superiore con un server web Apache. L'installazione di Apache deve avere l'estensione mod\_rewrite installata e configurata. Dobbiamo assicurarci inoltre che Apache supporti i file **.htaccess**. Possiamo farlo modificando l'impostazione:

 ```

AllowOverride None
```

in:

 ```

AllowOverride All
```

nel file di configurazione httpd.conf. Consultare la documentazione della propria distribuzione per maggiori dettagli. Se non impostiamo correttamente il mod\_rewrite e l'.htaccess non saremo in grado di navigare in nessuna pagina se non nella home page.

###  Procurarsi il framework

Zend Framework può essere scaricato da <http://framework.zend.com/download/latest> in formato .zip o .tar.gz. La versione "Minimal" è quella di cui abbiamo bisogno.