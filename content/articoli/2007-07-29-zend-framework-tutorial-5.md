---
title: "Zend Framework Tutorial - 5"
author: "Giovanni Tomasicchio"
type: article
date: 2007-07-29T10:49:26+0000
description: "Realizzare un gestore di news con lo Zend Framework"
url: /articoli/zend-framework-tutorial-5/
categories:
  - Articoli
tags:
  - "database"
  - "Zend Framework"
  - "PDF"
  - "Java"
  - "CSS"
  - "email"
  - "exception"
  - "web service"

  
---
###  NewsController

 Siamo finalmente arrivati all'ultima parte di questa serie di articoli dedicati alla realizzazione di una applicazione con lo Zend Framework. Dobbiamo analizzare il funzionamento del NewsController, il cuore della nostro sito web, che si occupa della visualizzazione, inserimento, modifica e cancellazione delle news. Di seguito viene riportata la struttura del Controller che sarà analizzata in dettaglio nelle prossime pagine.

 ```
<pre class="brush: php">
class NewsController extends Zend_Controller_Action
{
    protected $_flashMessenger = null;

    public function init() {
        // istruzioni...
    }

    public function viewAction() {
        // istruzioni...
    }

    public function deleteAction() {
        // istruzioni...
    }

    public function newAction() {
        // istruzioni...
    }

    public function editAction() {
        // istruzioni...
    }
}
```

 Nel codice sono evidenti le quattro Action per la gestione delle news (viewAction, deleteAction, newAction, editAction). Inoltre si può notare l'attributo $\_flashMessenger che ci serve per conservare un riferimento all'helper FlashMessenger, descritto nel precedente articolo. Poiché il FlashMessenger viene utilizzato in quasi tutte le Action, è conveniente recuperarlo e conservarlo in un attributo (una variabile) del Controller. Dove vanno poste le istruzioni per recuperare il FlashMessenger indipendentemente dalla Action chiamata? Nel metodo **init()**, di seguito riportato:

 ```
<pre class="brush: php">
public function init()
{
    $this->_flashMessenger = $this->_helper->getHelper('FlashMessenger');

    if($this->_hasParam('annulla')) {
        $this->_redirect('/');
    }
}
```

 Il metodo init() è una particolare funzione che è possibile inserire in un Controller. Se presente, viene eseguito prima di qualsiasi Action richiesta dall'utente, quindi risulta utile quando vogliamo lanciare delle istruzioni comuni a tutte le Action, senza doverle ripetere in ciascuna di esse. Nel nostro caso il metodo init() è stato utilizzato proprio per conservare un riferimento al FlashMessenger nell'attributo $this-&gt;\_flashMessenger.

 Inoltre due Action del Controller (quelle che gestiscono l'inserimento e la modifica di una news) prevedono che l'operazione possa essere annullata attraverso la pressione di un tasto "annulla", presente nel form. Anche la gestione di questo comportamento comune quindi può essere "centralizzata" nel metodo init() in modo da non doverla ripetere (ricopiare) nelle due Action.

 Per sapere se è presente un parametro tra i dati inviati con il form utilizziamo l'istruzione **$this-&gt;\_hasParam('nome\_parametro')**. Nel nostro caso il pulsante di annullamento è un classico bottone di submit e ha per nome 'annulla'. Per effettuare il redirect utilizziamo invece **$this-&gt;\_redirect('/URL/di/destinazione')**. Corrisponde al classico header('location: /URL/di/destinazione');

- - - - - -

###  viewAction

 Se l'utente clicca sul titolo di una news in home page, il sito mostra una pagina contenente il testo completo della news selezionata. Per identificare univocamente la news di interesse abbiamo creato il link associato nella forma /news/view/id/news\_id dove news\_id è il valore della chiave primaria della tabella delle news. Come si può vedere dal codice del metodo viewAction, per recuperare questo valore usiamo la funzione **$this-&gt;\_getParam('id')** che ricorda il metodo $this-&gt;\_hasParam visto nella pagina precedente.

 In realtà il metodo $this-&gt;\_getParam('nome\_parametro') è una scorciatoia che equivale al più prolisso **$this-&gt;getRequest()-&gt;getParam('nome\_parametro')**. Cosa fa **$this-&gt;getRequest()**? Ci restituisce l'**oggetto Request**, costruito dallo Zend Framework per contenere tutti i dati associati alla richiesta corrente dell'utente: il nome del Controller, il nome della Action, le coppie nome/valore presenti nell'URL e tutti gli eventuali parametri contenuti in $\_GET, $\_POST, $\_COOKIE, $\_SERVER e $\_ENV. In poche parole tutti i dati provenienti dall'estero, dall'utente, vengono racchiusi automaticamente dallo Zend Framework nell'oggetto Request (similmente a ciò che avviene in una servlet del Java). Tutti questi dati sono nella forma chiave=&gt;valore quindi potenzialmente si sovrappongono l'uno con l'altro (a parità di chiave) secondo l'ordine in cui sono stati elencati, similmente a quello che avviene nell'array $\_REQUEST. Se però si vuole recuperare esattamente un particolare parametro, ad esempio proveniente dall'array $\_POST, allora si può usare **$request-&gt;getPost('nome\_parametro')** dove $request è ovviamente l'oggetto Request ottenuto nel Controller attraverso $this-&gt;getRequest().

 E' evidente che lo Zend Framework vuole che lo sviluppatore non acceda direttamente alle variabili superglobali ma che lo faccia attraverso l'oggetto Request. In questo modo tutti i dati provenienti dall'esterno sono raggruppati in un unico oggetto e sono accessibili tutti allo stesso modo, indipendentemente dalla loro particolare provenienza. Inoltre così si evita di modificare involontariamente uno di questi dati (if ($\_POST\['parametro'\] = 'valore') al posto di if ($\_POST\['parametro'\] == 'valore')).

 E' bene precisare comunque che l'oggetto Request non filtra in alcun modo i dati in esso conservati, operazione affidata ad altri componenti del framework che in questo tutorial non abbiamo considerato.

 Chiarito le modalità di accesso ai dati provenienti dall'esterno vediamo il codice dell'Action che mostra una news all'utente:

 ```
<pre class="brush: php">
public function viewAction()
{
    $news = new News();

    $newsId = $this->_getParam('id');
    $news = $news->getNewsById($newsId);

    if(!$news) {
        throw new Zend_Controller_Action_Exception;
    }

    $this->view->titoloPagina = $news['titolo'];
    $this->view->news = $news;
}
```

 Tutte le istruzioni dovrebbero ormai essere chiare, tranne che per l'eccezione sollevata nel caso in cui il metodo getNewsById() del Model non restituisce alcun risultato. Se questo avviene significa che l'id della news recuperato dall'URL non corrisponde ad una news memorizzata nel database. Questo problema viene gestito simulando un errore di "pagina non trovata". L'eccezione **Zend\_Controller\_Action\_Exception** interrompe l'esecuzione di viewAction() e risale fino al Front Controller (o meglio fino al dispatcher) che reagisce richiamando l'ErrorController che a sua volta mostrata una pagina di errore. L'ErrorController quindi, oltre ad essere richiamato automaticamente dal framework, può essere invocato in maniera programmatica da qualunque altro Controller, nel modo appena visto.

 Terminata l'esecuzione della viewAction, per le convenzioni sui nomi dei template, viene automaticamente caricato il template /views/scripts/news/view.phtml, riportato di seguito.

 ```
<pre class="brush: php">
<?php echo $this->render('header.phtml'); ?>
<div class="news">
    <div class="autore">di <?php echo $this->escape($this->news['autore']) ?> | <?php echo $this->escape($this->news['data']) ?></div>
    <div class="modifica">
        <a href="<?php echo BASE_URL?>news/edit/id/<?php echo $this->escape($this->news['news_id']) ?>/">Modifica</a>
        -
        <a href="<?php echo BASE_URL?>news/delete/id/<?php echo $this->escape($this->news['news_id']) ?>/">Cancella</a>
    </div>
    <div class="testo">
        <?php echo nl2br($this->escape($this->news['testo_intro'])) ?>
        <br />
        <?php echo nl2br($this->escape($this->news['testo_completo'])) ?>
    </div>
</div>
<a href="<?php echo BASE_URL?>">&lt;&lt; Indietro</a>
<?php echo $this->render('footer.phtml'); ?>
```

- - - - - -

###  deleteAction

 L'Action che effettua la cancellazione di una news ha una struttura molto semplice. Istanzia il Model delle News, recupera dall'URL l'id della news da cancellare, lo passa al Model per l'effettiva cancellazione dal database, memorizza nel flashMessenger un messaggio di avvenuta cancellazione ed infine effettua il redirect alla home page del sito.

 Ovviamente la presenza del redirect inibisce il caricamento automatico di un template al termine della Acion.

 ```
<pre class="brush: php">
public function deleteAction()
{
    $news = new News();
    $newsId = $this->_getParam('id');
    $news->deleteNews($newsId);
    $this->_flashMessenger->addMessage("Cancellazione della news avvenuta con successo");
    $this->_redirect('/');
}
```

- - - - - -

###  newAction

 La Action che si occupa di inserire una nuova news nel database ha una struttura particolare, che rende le poche righe di codice che la costituiscono davvero efficienti. Infatti newAction deve mostrare il form di inserimento, inserire la news se tutti i dati inviati sono corretti e mostrare nuovamente il form con i dati precedentemente inseriti se questi non sono corretti. Analizziamone il funzionamento.

 In qualunque situazione ci troviamo assegnamo il titolo alla pagina e specifichiamo il nome del template da mostrare (righe 3 e 6). Infatti il template usato per inserire o modificare una news è unico, quindi non potrà rispettare la convenzione sui nomi e di conseguenza non verrebbe caricato automaticamente. La riga 6 serve proprio a specificare un template diverso da quello che l'oggetto **viewRenderer**, responsabile del caricamento automatico dei template, si sarebbe aspettato.

 Se è stato effettuato l'invio del form (**$request-&gt;isPost()** in riga 9) allora dobbiamo provare ad inserire la nuova news nel database. Recuperiamo quindi i dati inseriti dall'utente con $request-&gt;getPost('nome\_parametro') ed invochiamo il metodo insertUpdateNews del Model $news (riga 19). Se tutto va bene memorizziamo un messaggio di successo nel flashMessenger ed effettuiamo il redirect alla home page. Se invece il Model $news solleva un'eccezione a causa di un problema con i dati inseriti dall'utente, allora mostro un messaggio di errore (riga 23) insieme ai dati inviati dall'utente (righe 24-27)

 ```
<pre class="brush: php">
public function newAction()
{
    $this->view->titoloPagina = 'Inserisci una news';

    // Utilizza 'news/news-form.phtml' invece di  'news/new.phtml'
    $this->_helper->viewRenderer->setScriptAction('news-form');

    $request = $this->getRequest();
    if($request->isPost())
    {
        $news = new News;

        $autore        = $request->getPost('autore');
        $titolo        = $request->getPost('titolo');
        $testoIntro    = $request->getPost('testo_intro');
        $testoCompleto = $request->getPost('testo_completo');

        try {
            $news->insertUpdateNews(false,$autore,$titolo,$testoIntro,$testoCompleto);
            $this->_flashMessenger->addMessage("News '$titolo' memorizzata con successo");
            $this->_redirect('/');
        } catch (Zend_Exception $e) {
            $this->view->errorMsg = $e->getMessage();
            $this->view->news = array('autore' => $autore,
            'titolo'        => $titolo,
            'testo_intro'   => $testoIntro,
            'testoCompleto' => $testoCompleto);
        }
    }
}
```

 Di seguito è riportato il codice del template utilizzato per l'inserimento e la modifica di una news. Si noti l'utilizzo dei metodi **$this-&gt;formText** e **$this-&gt;formTextarea**, appartenenti alla categoria dei **View Helpers**, che servono a creare dinamicamente i campi di inserimento dati del form. Questi metodi accettano tre parametri: il nome del controllo (l'attributo name), il contenuto del controllo (l'attributo value, che non necessita di escape!) e un array contenente una o più coppie "nome\_attributo"=&gt;"valore". Nel nostro esempio questo array viene utilizzato per specificare una classe CSS.

 **news-form.phtml**

 ```
<pre class="brush: php">
<?php echo $this->render('header.phtml'); ?>
<p class="errore"><?php echo $this->escape($this->errorMsg)?></p>
<form action="" method="post">
    <fieldset>
    <legend>Dati della news</legend>
        <p><label>Autore*:<br />
            <?php echo $this->formText('autore', $this->news['autore'], array('class'=>'inputForm')) ?>
        </label></p>
        <p><label>Titolo*:<br />
            <?php echo $this->formText('titolo', $this->news['titolo'], array('class'=>'inputForm')) ?>
        </label></p>
        <p><label>Testo introduttivo*:<br />
            <?php echo $this->formTextarea('testo_intro', $this->news['testo_intro'],array('rows' => 5,'cols'=>50)) ?>
        </label></p>
        <p><label>Testo completo:<br />
            <?php echo $this->formTextarea('testo_completo', $this->news['testo_completo'],array('rows' => 5,'cols'=>50)) ?>
        </label></p>
        <p>* campo obbligatorio</p>
        <div class="buttons">
            <input type="submit" name="annulla" value="Annulla" /><input type="submit" name="invia" value="Invia" />
        </div>
    </fieldset>
</form>
<?php echo $this->render('footer.phtml'); ?>
```

- - - - - -

###  editAction

 La Action per la modifica di una news precedentemente inserita dall'utente ha una struttura molto simile a quella per l'inserimento vista nella precedente pagina. La principale differenza consiste nel fatto che se la pagina viene mostrata per la prima volta non basta caricare il template del form di inserimento/modifica ma bisogna anche recuperare i dati della news dal database e passarli al template. Queste operazioni vengono realizzate alla fine dello script, nel blocco "else" (righe 34-38). Si noti come anche in questo caso viene effettuato un controllo sulla news recuperata dal database. Infatti poiché l'id della news proviene dall'utente, questo potrebbe esser manomesso e quindi non associato ad una news realmente presente nel database. In questo caso viene sollevata una eccezzione che si traduce, come visto nella viewAction, in un messaggio di pagina non trovata.

 ```
<pre class="brush: php">
public function editAction()
{
    $this->view->titoloPagina = 'Modifica news';

    // Utilizza il template 'news/news-form.phtml' al posto di  'news/edit.phtml'
    $this->_helper->viewRenderer->setScriptAction('news-form');

    $news = new News;

    $request = $this->getRequest();
    $newsId  = $request->getParam('id');
    if($request->isPost())
    {
        $autore        = $request->getPost('autore');
        $titolo        = $request->getPost('titolo');
        $testoIntro    = $request->getPost('testo_intro');
        $testoCompleto = $request->getPost('testo_completo');

        try {
            $news->insertUpdateNews($newsId,$autore,$titolo,$testoIntro,$testoCompleto);
            $this->_flashMessenger->addMessage("News '$titolo' modificata con successo");
            $this->_redirect('/');
        } catch (Zend_Exception $e) {
            $this->view->errorMsg = $e->getMessage();
            $this->view->news = array($autore,$titolo,$testoIntro,$testoCompleto);
            $this->view->news = array('autore' => $autore,
            'titolo'        => $titolo,
            'testo_intro'   => $testoIntro,
            'testoCompleto' => $testoCompleto);
        }
    }
    else
    {
        $news = $news->getNewsById($newsId);
        if(!$news) {
            throw new Zend_Controller_Action_Exception;
        }
        $this->view->news = $news;
    }
}
```

- - - - - -

###  Conclusioni

 Si conclude qui il nostro breve viaggio all'interno dello Zend Framework, un potente strumento che esalta le caratteristiche di PHP e lo rende adeguato alla realizzazione di applicazioni complesse senza dover rinunciare alla semplicità a cui siamo abituati. Forse chi non ha mai utilizzato framework così ambiziosi si sarà trovato in difficoltà nel seguire il percorso si qui fatto ma con un po' di pratica tutto risulterà più immediato.

 Vale la pena ricordare che i componenti utilizzati nella nostra applicazione di esempio sono solo una minima parte di quelli offerti dallo Zend Framework ma, a differenza degli altri, sono quelli che ne costituiscono l'architettura e che quindi sono il fondamento di qualunque applicazione. Data la natura del framework, tutti i componenti che non abbiamo citato (autenticazione, autorizzazione, pdf, email, internazionalizzazione, web services, ecc.) potranno essere studiati separatamente ed impiegati all'occorrenza, senza che il cuore dell'applicazione realizzata venga stravolto.

 Anche se sono in cantiere ulteriori articoli sullo Zend Framework, da questo momento è d'obbligo far riferimento alla documentazione ufficiale per approfondire ed ampliare la propria conoscenza di questo ottimo strumento.