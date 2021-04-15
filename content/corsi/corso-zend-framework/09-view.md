---
title: "View"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:20:41+0000
description: "Usare Zend_View per gestire le view di una applicazione MVC con lo Zend Framework"
url: /corsi/corso-zend-framework/view/
categories:
  - Introduzione allo Zend Framework
  
---
Il componente view di Zend Framework si chiama, senza troppa fantasia, Zend\_View. Questo componente ci permetterà di separare il codice che visualizza la pagina dal codice dei metodi action.

L'uso base di Zend\_View è:

 ```
<pre class="brush: php">
$view = new Zend_View();
$view->setScriptPath('/path/to/scripts');
echo $view->render('script.php');
```

E' molto facile constatare che, se mettessimo questo codice direttamente in ogni nostra action, dovremmo ripetere questo noioso codice architetturale privo di qualsiasi interesse per la action. Inizializzaremo invece la view altrove e poi avremo accesso al nostro oggetto view, già inizializzato, da ogni nostra action. Zend Framework fornisce un Action Helper di nome ViewRenderer. Esso si occupa di inizializzare un attributo del controller ($this-&gt;view) assegnandoli un oggetto view ed inoltre esegue uno script view dopo che l'action è stata processata.

Per l'esecuzione, ViewRenderer imposta l'oggetto Zend\_View affinché guardi in views/scripts/{nome controller} per gli script view che devono essere eseguiti e (di default, almeno) esegue lo script che ha lo stesso nome dell'action, con estensione phtml. Dunque, lo script view che verrà eseguito è views/scripts/{nome controller}/{nome action}.phtml ed l'output prodotto è accodato al corpo dell'oggetto Response. L'oggetto Response è usato per raccogliere tutti gli header HTTP, il contenuto della pagina web e le eccezioni generate dal sistema MVC. Il front controller quindi, alla fine del ciclo di elaborazione della richiesta (dispatch), invierà automaticamente gli header, seguiti dal contenuto della pagina web.

Tutto ciò viene impostato per noi da Zend\_Tool quando creiamo un progetto e aggiungiamo i controller e le azioni usando i comandi zf create controller e zf create action.