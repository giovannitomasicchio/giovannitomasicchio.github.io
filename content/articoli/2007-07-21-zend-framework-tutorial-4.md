---
title: "Zend Framework Tutorial - 4"
author: "Giovanni Tomasicchio"
type: article
date: 2007-07-21T09:28:44+0000
description: "I Controller dello Zend Framework"
url: /articoli/zend-framework-tutorial-4/
categories:
  - Articoli
tags:
  - "database"
  - "Zend Framework"
  - "CSS"
  - "XML"

  
---
###  IndexController

 Dopo aver analizzato la classe Model che gestisce tutta l'interazione con il database, possiamo finalmente iniziare a vedere i Controller della nostra applicazione ed i template HTML associati. Abbiamo detto che i Controller sono classi costituite da Action, metodi che vengono automaticamente richiamati dal Front Controller per svolgere una particolare funzionalità del sito, associata ad un URL richiesto dall'utente. Cosa ci aspettiamo di trovare quindi in una Action?

 Generalmente una Action preleva eventuali dati presenti nella richiesta effettuata dall'utente (come i dati GET e POST), richiama uno o più Model per interagire col database e mostra il risultato del suo operato caricando (implicitamente o esplicitamente) un template a cui passa i dati dinamici da visualizzare. Vediamo come queste operazioni vengono eseguite nell'IndexController, responsabile della home page del sito. Di seguito viene riportato il codice completo.

 ```
<pre class="brush: php">
class IndexController extends Zend_Controller_Action
{
    public function indexAction()
    {
        $this->view->titoloPagina = 'Ultime news';

        $fm = $this->_helper->getHelper('FlashMessenger');
        $this->view->messaggi = $fm->getMessages();

        $news = new News();

        $this->view->news = $news->getAllNews();
    }
}
```

 Analizziamo il codice riga per riga:

- riga 5: la stringa 'Ultime news' viene assegnata alla variabile titoloPagina dell'oggetto View. Questo oggetto, che ricordiamo si occupa della visualizzazione dei template HTML, viene automaticamente creato dallo Zend Framework ed è accessibile dal Controller attraverso la sua variabile $this-&gt;view. In questo modo il passaggio dei dati dal Controller alla View è semplicissimo, basta infatti assegnare i dati ad una variabile dell'oggetto $this-&gt;view, ad esempio $this-&gt;view-&gt;nomeVariabile = $valore. Il template HTML che verrà poi richiamato da View vedrà tutte le variabili della View come proprie, quindi per recuperare tale valore accederà a $this-&gt;nomeVariabile.
- riga 7: viene creato un oggetto **FlashMessenger**, un componente di supporto (Helper) del Controller nel quale è possibile conservare uno o più messaggi da mostrare all'utente nelle successive pagine del sito. Questi messaggi vengono salvati automaticamente in sessione quindi il FlashMessenger può essere utilizzato da una Action che sta per effettuare il redirect ad un'altra pagina e vuole che questa mostri un messaggio. Nella nostra applicazione questi messaggi vengono generati durante la gestione delle news (inserimento, modifica, cancellazione) e vengono visualizzati nella home page.
- riga 8: l'array dei messaggi conservati da precedenti Action nel FlashMessenger viene assegnato alla variabile $this-&gt;view-&gt;messaggi in modo che il template possa visualizzarli.
- riga 10: viene istanziato il Model delle News.
- riga 12: assegnamo alla variabile $this-&gt;view-&gt;news tutte le news che ci restituisce il Model in modo che il template possa visualizzarle.
 
 Come avrete notato, in indexAction non si fa riferimento a nessun template da mostrare. Questo perché lo Zend Framework di default, dopo aver richiamato una Action, carica automaticamente il template associato attraverso l'oggetto View. In questo caso, poiché la Action si chiama **index**Action ed appartiene al controller **Index**Controller allora verrà richiamato il template view/scripts/**index**/**index**.phtml.

- - - - - -

###  template, header e footer

 Di seguito viene mostrato il contenuto del file index.phtml. Si tratta del template HTML associato alla home page del nostro sito, caricato automaticamente al termine dell'esecuzione di indexAction dell'IndexController. Sono presenti diverse istruzioni PHP ma tutte finalizzate a mostrare il contenuto dinamico, generalmente passato alla View dal Controller. In particolare l'indexController fornisce alla View l'array dei messaggi conservati nel FlashMessenger e l'array delle news estratto dal DB. Questi dati sono accessibili nel template attraverso $this-&gt;messaggi e $this-&gt;news.

 Il template utilizza anche 2 importanti funzioni della View:

- **$this-&gt;render('nome\_template.phtml')** serve ad includere nell'attuale template un ulteriore template, operazione analoga al classico include() di PHP. In questo modo vengono caricati header.phtml e footer.phtml, i 2 template che costituiscono l'intestazione ed il piè di pagina di tutte le pagine del nostro sito.
- **$this-&gt;escape($stringa)** serve a convertire i caratteri illeciti per una pagina HTML in entità HTML, come ad esempio i caratteri &lt; e &gt;. L'utilizzo di $this-&gt;escape al posto del classico html\_special\_chars è da preferire poiché il suo comportamento si adegua in maniera trasparente al set di caratteri impostato nella View.
 
 **index.phtml**

 ```
<pre class="brush: php">
<?php echo $this->render('header.phtml'); ?>
<p class="errore"><?php echo $this->escape($this->messaggi[0])?></p>
<a href="<?php echo BASE_URL?>news/new/">Inserisci una news</a>
<?php if ($this->news): ?>
    <?php foreach ($this->news as $news): ?>
        <div class="news">
            <h2><a href="<?php echo BASE_URL?>news/view/id/<?php echo $this->escape($news['news_id']) ?>/"><?php echo $this->escape($news['titolo']) ?></a></h2>
            <div class="autore">di <?php echo $this->escape($news['autore']) ?> | <?php echo $this->escape($news['data']) ?></div>
            <div class="modifica">
                <a href="<?php echo BASE_URL?>news/edit/id/<?php echo $this->escape($news['news_id']) ?>/">Modifica</a>
                -
                <a href="<?php echo BASE_URL?>news/delete/id/<?php echo $this->escape($news['news_id']) ?>/">Cancella</a>
            </div>
            <p class="testo"><?php echo nl2br($this->escape($news['testo_intro'])) ?></p>
        </div>
    <?php endforeach; ?>
<?php else: ?>
    <p>Nessuna news registrata.</p>
<?php endif; ?>
<?php echo $this->render('footer.phtml'); ?>
```

 Si noti come nel precedente template vengono costruiti i link che permettono all'utente di visualizzare, inserire, modificare o cancellare una news. Viene utilizzata la costante BASE\_URL come percorso comune a tutti gli URL, al quale viene accodato il nome del Controller (news) e della Action (new, view, edit e delete), secondo le convenzioni viste nei precedenti articoli, e se necessario un parametro "id" per identificare la news in questione. Il valore di tale parametro è fornito dal database e conservato nella variabile $news\['news\_id'\].

 Ecco il codice contenuto nei template che costituiscono header e footer di tutte le pagine della nostra applicazione web. Si notino i tag &lt;title&gt; e &lt;h1&gt; valorizzati dal contenuto di $this-&gt;titoloPagina.

 **header.phtml**

 ```
<pre class="brush: php">

<html xmlns="http://www.w3.org/1999/xhtml" lang="it" xml:lang="it">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
    <title><?php echo $this->escape($this->titoloPagina)?></title>
    <link rel="stylesheet" type="text/css" media="screen, handheld, print" href="<?php echo BASE_URL?>main.css" />
</head>
<body>
    <div id="page">
        <h1><?php echo $this->escape($this->titoloPagina)?></h1>
```

 **footer.phtml**

 ```
<pre class="brush: php">
</div>
    </body>
</html>
```

- - - - - -

###  ErrorController

 ErrorController è il Controller che intercetta (tra le altre cose) eventuali errori del Front Controller, come ad esempio gli errori di "pagina non trovata". La sua struttura e davvero molto semplice, in pratica si limita a dare un titolo esplicativo alla pagina. Il messaggio vero e proprio è invece conservato direttamente nel template associato (error.phtml), richiamato automaticamente grazie alla convenzione sui nomi dei file.

 ```
<pre class="brush: php">
<?php
class ErrorController extends Zend_Controller_Action
{
    public function errorAction()
    {
        $this->view->titoloPagina = 'Pagina non trovata';
    }
}
?>
```

 **error.phtml**

 ```
<pre class="brush: php">
<?php echo $this->render('header.phtml'); ?>
<div class="testo">Attensione! La pagina che stai cercando non esiste oppure è stata spostata.</div>
<br />
<a href="<?php echo BASE_URL?>">&lt;&lt; Home page</a>
<?php echo $this->render('footer.phtml'); ?>
```

 Nel prossimo ed ultimo articolo vedremo il funzionamento del NewsController, cuore della nostra applicazione realizzata con lo Zend Framework.