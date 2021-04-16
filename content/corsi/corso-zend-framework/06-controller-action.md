---
title: "Controller e Action"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:15:05+0000
description: "Il ruolo dei controller e delle action in una applicazione Zend Framework"
url: /corsi/corso-zend-framework/controller-action/
weight: 11
categories:
  - Introduzione allo Zend Framework
  
---
Prima di procedere, è importante capire come lo Zend Framework si aspetta che siano organizzate le pagine. Ogni pagina dell'applicazione è detta "action" e più action vengono raggruppate all'interno di un controller. Per un URL del formato http://localhost/public/zf-tutorial/news/view, il controller è News e l'action è view. Ciò permette il raggruppamento delle action fra loro correlate. Per esempio, un controller News può avere delle action differenti per la lista, l'archivio e la visualizzazione delle news.

Il sistema MVC di Zend Framework supporta anche i moduli per il raggruppamento dei controller. L'applicazione che stiamo costruendo però non è così grande da necessitare di moduli differenti.

Di default i controller di Zend Framework riservano una specifica action di default chiamata index. Questa è l'action impiegata per gli URL simili al seguente: http://localhost/zf-tutorial/public/news/ per cui l'action index è esecuita dal controller News. C'è anche un controller di default chiamato index, così l'URL http://localhost/zf-tutorial/public chiamerà ed eseguirà l'action index del controller Index.

Dato che questo è solo un semplice tutorial, non faremo cose complicate come il login nell'applicazione! Per questo argomento sono disponibili dei [tutorial dedicati](http://akrabat.com/zend-auth-tutorial/) (o si può leggere [Zend Framework in Action](http://www.zendframeworkinaction.com/)).

Poiché ci saranno quattro pagine tutte relative agli album, le raggrupperemo in un unico controller realizzando quattro action. Useremo il controller di default e le quattro action saranno:

 | Pagina | Controller | Action |
|---|---|---|
| Home page | Index | index |
| Add new album | Index | add |
| Edit album | Index | edit |
| Delete album | Index | delete |

Per un sito più complicato, saranno necessari controller aggiuntivi e li si potrà raggruppare in moduli, se necessario.

###  Impostare i controller

Siamo ora pronti ad impostare il nostro controller. In Zend Framework, il controller è una classe che si deve chiamare {Nome controller}Controller. Da notare che {Nome controller} deve iniziare con la lettera maiuscola. Questa classe deve corrispondere ad un file di nome {Nome controller}Controller.php nella directory "controllers" di application/. Ogni action è un metodo di tipo public della classe controller che deve chiamarsi {nome action}Action. In questo caso {nome action} inizia con la lettera minuscola e deve essere scritto tutto in minuscolo. E' permesso creare nomi per controller e action che mischiano lettere maiuscole e minuscole ma prima bisogna conoscere e seguire delle speciali regole da approfondire nella documentazione ufficiale!

La nostra classe controller si chiama IndexController che è definita nel file application/controllers/IndexController.php creato automaticamente da Zend\_Tool. Inoltre contiene già il primo metodo action, indexAction(). Dobbiamo solamente aggiungere le altre action.

Per aggiungere altre action al controller si può usare il comando zf create action da terminale. Dunque basta aprire un terminale o il Prompt dei comandi, cambiare la directory in zf-tutorial/. Poi digitare questi comandi:

 ```

zf create action add Index
zf create action edit Index
zf create action delete Index
```

Questi comandi creano 3 nuovi metodi: addAction, editAction e deleteAction in IndexController ed inoltre creano gli script view appropriati che serviranno successivamente. Adesso abbiamo le nostre 4 action.

Gli URL di ogni action sono:

 | URL | metodo Action |
|---|---|
| http://localhost/zf-tutorial/public/ | IndexController::indexAction() |
| http://localhost/zf-tutorial/public/index/add | IndexController::addAction() |
| http://localhost/zf-tutorial/public/index/edit | IndexController::editAction() |
| http://localhost/zf-tutorial/public/index/delete | IndexController::deleteAction() |

Possiamo testare le 3 nuove action e dovremmo vedere un messaggio come questo:

 ```

View script for controller index and script/action name add
```

Se si ottiene un errore 404, allora non è stato configurato correttamente Apache con il modulo mod\_rewrite o non è stato impostato correttamente l'AllowOverride nel file di configurazione di Apache per utilizzare il file .htaccess presente nella cartella public/.

Se si sta sviluppando un sito in spazio "utente" (per es. http://localhost/~rob), allora si necessita di una riga di RewriteBase nel proprio .htaccess del tipo: RewriteBase /~rob/zf-tutorial/public/.