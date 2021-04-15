---
title: "Zend Framework Tutorial - 1"
author: "Giovanni Tomasicchio"
type: article
date: 2007-07-01T22:39:41+0000
description: "Introduzione allo Zend Framework"
url: /articoli/zend-framework-tutorial-1/
categories:
  - Articoli
tags:
  - "database"
  - "Zend Framework"
  - "MySQL"
  - "mod_rewrite"
  - "PDO"
  - "SQL"
  - "upload"

  
---
###  Introduzione

 Lo [Zend Framework](http://framework.zend.com) è una completa collezione di classi da impiegare nella realizzazione di applicazioni PHP di media e grande complessità. Ci sono classi per tutte le esigenze quindi è inutile provare a farne un elenco dettagliato e descriverne il funzionamento. Il modo migliore per scoprire lo Zend Framework è invece realizzare con esso una piccola applicazione e quindi in questo tutorial vedremo come costruire un sito web in grado di mostrare delle news, gestite dinamicamente dall'utente, e conservate in un database MySQL. In home page mostreremo l'elenco delle news e attraverso dei link sarà possibile leggerne il testo completo, inserirne di nuove, modificale e cancellarle.

 Questo piccolo progetto ovviamente è solo un pretesto per iniziare ad usare lo Zend Framework e ci limiteremo ad impiegare solo alcune delle sue numerose funzionalità, quelle che ci permetteranno di realizzare velocemente l'applicazione e che probabilmente risulteranno più semplici per un novizio.

 Servirà avere Apache, PHP 5 e MySQL correttamente installati. PHP dovrà avere le estensioni PDO e PDO\_MYSQL caricate, e ovviamente avremo precedentemente scaricato lo Zend Framework e scompattato l'archivio compresso in una cartella sul nostro PC. Infine il mod\_rewrite ed i file .htaccess di Apache dovranno essere abilitati.

 Per i più curiosi e per chi non vuole attendere troppo, da [questo link](http://www.phpnews.it/download/ZF-tutorial.zip) potete scaricare il codice completo che compone il progetto, compreso il file SQL per creare il database. Se volete già provarlo vi anticipo che sarà necessario effettuare qualche modifica al file index.php affiché i dati in esso presenti siano corretti per il vostro PC (dati di accesso al DB, percorso della cartella dello Zend Framework). Avremo modo di affrontare con molta più calma tali configurazioni nel seguito del tutorial.

 In questo primo articolo studieremo l'architettura dell'applicazione, vedremo cioè quali file compongono il progetto e quali sono le responsabilità loro affidate.

- - - - - -

###  La gestione delle pagine con l'approccio MVC

 Il cuore di una applicazione web è la strategia adottata per generare le pagine dinamiche, ovvero il meccanismo che decide quali contenuti mostrare in base all'URL richiesto dall'utente. Usare lo Zend Framework non significa adottare necessariamente il suo sistema di gestione delle pagine web, anche se non farlo sarebbe veramente un peccato. In questo tutorial seguiremo quindi l'approccio proposto dal framework che si basa sul modello MVC, meccanismo alla base di molti framework, anche di altri linguaggi di programmazione.

 Lo scopo dell'architettura MVC è quello di rendere più semplice la realizzazione e la manutenzione del codice, attraverso una separazione netta delle diverse competenze in componenti distinti. Il modello MVC considera i **dati**, l'**interfaccia** e la **logica di funzionamento** i 3 aspetti fondamentali di qualsiasi elemento che interagisce con l'utente. Questi 3 aspetti sono affidati ad altrettanti componenti che prendono rispettivamente il nome di **Model**, **View** e **Controller**. Questi tre componenti interagiscono tra loro e, ciascuno secondo le proprie responsabilità, realizzano tutte le funzionalità di un elemento dell'applicazione. Ad esempio, una pagina di registrazione di un utente potrebbe essere realizzata con un Model che si occupa di memorizzare i dati dell'utente nel database, di una View che realizza il form di inserimento dei dati e mostra un messaggio di avvenuta iscrizione e di un Controller che coordina le diverse fasi della registrazione e determina l'ordine delle operazioni.

 Il modello MVC è molto diffuso nella realizzazione del software ma la sua implementazione cambia nei diversi contesti. Quindi, invece di annoiarci con una trattazione teorica, passiamo subito ad analizzare la sua realizzazione nello Zend Framework.

- - - - - -

###  L'MVC di Zend Framework

 Vogliamo realizzare un sito nel quale l'utente può vedere l'elenco delle news registrate, inserirne di nuove, modificare e leggere il testo completo di una notizia.

 Lo Zend Framework ci suggerisce di raggruppare tali funzionalità in modo da avere degli insiemi di "azioni" correlate. Nel nostro caso avremo l'insieme delle azioni che è possibile effettuare sulle notizie (visualizzazione, inserimento, modifica e cancellazione). In situazioni più complesse avremo sicuramente molti insiemi di funzionalità (gestione utente, gestione download/upload file, gestione archivio news, gestione commenti, ecc.).

 Per ciascun insieme di azioni dobbiamo creare una classe che deve implementare la logica di funzionamento delle singole funzionalità, ma non si deve occupare dell'interfaccia grafica (non deve contenere HTML) e non deve effettuare direttamente l'accesso ai dati necessari alla realizzazioni delle funzionalità (niente query quindi). Questa tipologia di classe costituisce il **Controller** citato nella pagina precedente e deve contenere un metodo (una funzione) per ciascuna azione di cui è responsabile. Nel nostro caso la classe che funge da Controller nelle notizie avrà 4 metodi: uno per la lettura di una singola notizia, uno per l'inserimento, uno per la modifica ed uno per la cancellazione di una notizia.

 Riepilogando, le singole funzionalità (dette "**Action**") del nostro sito web devono essere raggruppate in insiemi correlati e per ciascun insieme deve essere realizzata una classe (il Controller) che possiede un metodo per ogni Action da implementare.

 Accanto ai Controller necessari al funzionamento della nostra applicazione, individuati dall'analisi che abbiamo appena fatto, due ulteriori Controller devono essere sempre realizzati per gestire delle operazioni speciali. Un Controller di nome **IndexController** si deve occupare di realizzare la home page del sito attraverso un metodo di nome **indexAction** , infatti la home page è una funzionalità presente in tutti i siti. Nel nostro caso questo Controller mostrerà l'elenco delle news presenti nel database (ecco perché non abbiamo considerato tale funzionalità nel controler delle news!). Un secondo Controller di nome **ErrorController** invece deve gestisce le pagine di errore attraverso un metodo di nome **errorAction**, automaticamente interpellato dal framework ad esempio quando l'utente tenta di accedere ad una pagina inesistente, ovvero ad una "Action" non implementata dal nostro sito. Vedremo i dettagli di questi 2 Controller quando mostreremo il codice del progetto.

 L'IndexController può implementare anche altre Action per cui si potrebbe realizzare un sito web anche solo con questo Controller e con l'ErrorController. A dire il vero anche l'ErrorController potrebbe non essere realizzato, ma così facendo in caso di errore otterremo delle sgradevoli segnalazioni, simili ai fatal error di PHP.

 L'HTML necessario a ciascuna Action non deve trovarsi nel Controller ma in file separati, detti **template**. Tali file possono contenere codice PHP ma è buona norma che questo serva solo alla visualizzazione dei contenuti dinamici. Ciascuno di questi file è in generale associato ad una particolare Action e in questi casi lo Zend Framework, grazie ad alcune convenzioni sui nomi che vedremo in seguito, è in grado di individuarlo ed utilizzarlo automaticamente in base alla funzionalità richiesta dall'utente. L'esecuzione di questi file avviene all'interno della classe che realizza la **View** e che, al contrario dei Controller, non dobbiamo creare noi perché già presente nel framework. Analizzando il codice vedremo come la View offre molte funzionalità che ci semplificheranno il compito di realizzare i template HTML.

 Infine l'accesso ai dati, come premesso, non deve essere eseguito ne dal Controller ne dalla View, ma deve essere effettuato da classi specializzate dette **Model**. In questo tutorial per realizzare i Model useremo un approccio molto simile a quello di una comune applicazione web PHP/MySQL, ovvero attraverso delle query dirette sul database, ma è importante sapere che lo Zend Framework offre potenti alternative che però aggiungerebbero complessità a questo tutorial. Realizzeremo quindi una classe con diversi metodi necessari ad effettuare le operazioni sul database: lettura, inserimento ed aggiornamento delle news. Lo Zend Framework in questo caso non dà indicazione su come organizzare la classe.

- - - - - -

###  Il Front Controller

 Abbiamo visto come le differenti funzionalità presenti nel sito devono essere realizzare da uno o più Controller i quali si affidano a una o più View per la parte grafica e ad uno o più Model per l'accesso ai dati. Rimane da capire come questo meccanismo si leghi alla navigazione di un utente. In fondo un utente che visita un sito web si limita a richiedere una sequenza di pagine, ovvero di URL nei quali spesso è evidente il nome dello script richiesto ed eventuali parametri passati con la query string (http://www.miosito.it/mostra\_news.php?id=123).

 L'approccio MVC dello Zend Framework stravolge la naturale associazione tra URL e script PHP richiamato, in modo da utilizzare l'intero URL per capire quale Controller invocare e quale sua Action eseguire. Questo risultato è ottenuto facendo convogliare tutte le richieste dell'utente, ovvero tutti gli URL, ad un unico script PHP che funge da smistatore delle pagine. In pratica, indipendentemente dall'URL digitato dall'utente, viene sempre eseguito uno stesso file, nel nostro caso index.php, detto **file di bootstrap**. All'interno di index.php creiamo un oggetto smistatore che prende il nome di **Front Controller** il quale, attraverso l'URL richiesto, deduce quale Controller richiamare e quale Action eseguire.

 Qual'è il criterio seguito dal Front Controller per estrarre dall'URL il nome del Controller e della Action (ovvero il nome della classe e del suo metodo responsabile di una certa funzionalità)? Supponendo che l'URL di base della nostra applicazione sia http://www.miosito.it/cartella\_applicazione/ allora gli URL:

 http://www.miosito.it/cartella\_applicazione/notizie/new/

 http://www.miosito.it/cartella\_applicazione/notizie/edit/id/123/

 http://www.miosito.it/cartella\_applicazione/notizie/delete/id/123/

 http://www.miosito.it/cartella\_applicazione/notizie/view/id/123/

 fanno tutti riferimento al Controller (ovvero alla classe) **Notizie**Controller, e alle sue Action (ovvero i suoi metodi) di nome **new**Action, **edit**Action, **delete**Action, **view**Action. Inoltre gli ultimi 3 URL contengono anche un parametro "id" di valore 123.

 Quindi la struttura della classe NotizieController sarà la seguente (si noti come questo tipo di classi deve ereditare da **Zend\_Controller\_Action**, padre di tutti i Controller della nostra applicazione):

 ```php
<?php
class NewsController extends Zend_Controller_Action
{
    public function newAction() {
    }

    public function editAction() {
    }

    public function deleteAction() {
    }

    public function viewAction() {
    }
}
?>
```

 In generale gli URL quindi contengono:

 1) il nome del Controller

 2) il nome della Action

 3) eventuali parametri nella forma /nome1/valore1/nome2/valore2/...

 Cosa succede se manca l'indicazione della Action, come ad esempio nell'URL http://www.miosito.it/cartella\_applicazione/news ? Viene richiamata la Action di defult del Controller NotizieController, di nome "indexAction", se esiste, altrimenti la richiesta viene inoltrata all'ErrorController. E se addirittura manca anche il nome del Controller, come nel caso del semplice URL http://www.miosito.it/cartella\_applicazione/ ? Si tratta evidentemente della home page del sito quindi, come anticipato, viene richiamato l'IndexController e la sua action IndexAction. Da notare che si può accedere alla home page da diversi URL:

 http://www.miosito.it/cartella\_applicazione/

 http://www.miosito.it/cartella\_applicazione/index

 http://www.miosito.it/cartella\_applicazione/index/index

 infatti tutti quanti puntano alla indexAction dell'IndexController. L'ErrorController e la sua Action errorAction vengono richiamate automaticamente ogni qualvolta il front Controller non riesce ad individuare il Controller e la Action specificate dall'utente.

 Prima di concludere questa breve spiegazione tengo a precisare che il comportamento descritto è quello di default dello Zend Framework. Tutti gli aspetti analizzati e le logiche presentate sono però totalmente personalizzabili ma questo va ben oltre gli scopi del presente tutorial.