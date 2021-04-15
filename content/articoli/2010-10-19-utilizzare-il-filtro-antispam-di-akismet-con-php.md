---
title: "Utilizzare il filtro antispam di Akismet con PHP"
author: "Giovanni Tomasicchio"
type: article
date: 2010-10-19T20:34:51+0000
description: "Proteggere uno script PHP per l'invio dei commenti utilizzando il filtro antispam di Akismet"
url: /articoli/utilizzare-il-filtro-antispam-di-akismet-con-php/
categories:
  - Articoli
tags:
  - "Zend Framework"
  - "WordPress"
  - "email"
  - "UTF-8"
  - "XML"

  
---
 [Akismet](http://akismet.com/) è un servizio di filtraggio dello spam offerto dalla [Automattic](http://automattic.com/), l'azienda maggiormente impegnata nello sviluppo di Wordpress. In questo articolo vedremo come invocare Akismet nei nostri script PHP per ricevere una valutazione sulla bontà dei commenti inviati dagli utenti attraverso un classico form HTML.

 Il servizio di filtraggio di Akismet è gratuito per utilizzo personale, previa registrazione a [questo indirizzo](http://akismet.com/personal/). Completata la registrazione si riceverà via mail un codice personale che, come vedremo negli esempi tra breve, servirà per avere accesso al servizio.

 La comunicazione tra Akismet ed il nostro script PHP deve avvenire via rete, fortunatamente non dobbiamo preoccuparci dei dettagli di tale interazione poiché sono disponibili due classi PHP che ne nascondono la complessità.

 Iniziamo con la classe PHP5 Akismet, scaricabile a [questo indirizzo](http://www.achingbrain.net/stuff/php/akismet). Per vederla in azione prepariamo prima un form col quale un ipotetico utente può inviare un commento al nostro sito.

 **- form.htm**

 ```
<pre class="brush: php">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="it-it" lang="it-it" dir="ltr">
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
        <title>Test Akismet</title>
    </head>
    <body>
        <form method="post" action="form.php" >
            <fieldset>
                <legend>Invia un commento</legend>
                <label>Nome:<br />
                    <input type="text" name="nome" />
                </label>
                <br />
                <label>Email:<br />
                    <input type="text" name="email" />
                </label>
                <br />
                <label>Sito web:<br />
                    <input type="text" name="url" />
                </label>
                <br />
                <label>Commento:<br />
                    <textarea cols="20" rows="4" name="commento"></textarea>
                </label>
                <br />
                <input type="submit" name="invia" value="Invia" />
            </fieldset>
        </form>        
    </body>
</html>
```

 Nonostante il form sia molto semplice contiene i classici campi di un modulo di commenti: nome, email e URL dell’utente, testo del messaggio.

 Passiamo quindi a vedere come utilizzare la classe PHP5 Akismet.

 **- form.php**

 ```
<pre class="brush: php">
<?php
include 'Akismet.class.php';

// mettere qui il proprio codice Akismet
$codice_akismet = '************';

// impostare l'URL del proprio sito
$url_sito = 'http://www.miosito.it';

$akismet = new Akismet($url_sito, $codice_akismet);

$akismet->setCommentAuthor($_POST['nome']);
$akismet->setCommentAuthorEmail($_POST['email']);
$akismet->setCommentAuthorURL($_POST['url']);
$akismet->setCommentContent($_POST['commento']);
$akismet->setPermalink('http://www.miosito.it/articolo-da-commentare');

if ($akismet->isCommentSpam()) {
    echo 'Il commento non è stato approvato!';
} else {
    echo 'Il commento è stato approvato';
}

```

 Lo script PHP si apre con l’inclusione della classe Akismet.class.php, responsabile della comunicazione con il filtro antispam. Nella variabile $codice\_akismet viene conservato il codice personale di Akismet di cui abbiamo parlato all’inizio. Oltre al codice, per creare un oggetto Akismet è necessario passare al costruttore anche l’URL del nostro sito.

 Le successive istruzioni permettono di specificare i dati inseriti dall’utente nel form (nome, email e URL dell’utente e testo del commento) ed in più l’URL della pagina che riceve il commento (che quindi cambia da pagina a pagina!).

 Infine richiamando il metodo isCommentSpam() è possibile richiedere la valutazione del filtro antispam di Akismet sui dati precedentemente impostati. Se tale metodo restituisce *true* allora allora si trattava di spam.

 La classe PHP5 Akismet, oltre alla verifica dei commenti, permette anche di segnalare ad Akismet un messaggio di spam, semplicemente sostituendo nello script precedente la chiamata al metodo $akismet-&gt;isCommentSpam() con $akismet-&gt;submitSpam(). Se invece Akismet ha erroneamente valutato un commento come spam, possiamo segnalare il falso positivo utilizzando il metodo $akismet-&gt;submitHam().

 Nella prossima pagina vedremo come invocare il filtro antispam di Akismet utilizzando lo Zend Framework.

- - - - - -

 Coloro che utilizzano lo Zend Framework per realizzare il proprio sito, o che semplicemente hanno lo Zend Framework installato sul proprio server, possono evitare di scaricare la classe PHP5 Akismet utilizzata nell’esempio precedente ed impiegare la classe [Zend\_Service\_Akismet](http://framework.zend.com/manual/en/zend.service.akismet.html). Vediamo quindi come realizzare lo script PHP, lasciando il form HTML inalterato.

 **- form.php**

 ```
<pre class="brush: php">
<?php
set_include_path(get_include_path() . PATH_SEPARATOR . 'C:\Program Files (x86)\Zend\ZendServer\share\ZendFramework\library');

require_once 'Zend/Service/Akismet.php';

// mettere qui il proprio codice Akismet
$codice_akismet = '************';

// impostare l'URL del proprio sito
$url_sito = 'http://www.miosito.it';

$akismet = new Zend_Service_Akismet($codice_akismet, $url_sito);

$data = array(
    'user_ip' => $_SERVER['REMOTE_ADDR'],
    'user_agent' => $_SERVER['HTTP_USER_AGENT'],
    'referrer' => $_SERVER['HTTP_REFERER'],
    'permalink' => 'http://www.miosito.it/articolo-da-commentare',
    'comment_type' => 'comment',
    'comment_author' => $_POST['nome'],
    'comment_author_email' => $_POST['email'],
    'comment_author_url' => $_POST['url'],
    'comment_content' => $_POST['commento']
);

if ($akismet->isSpam($data)) {
    echo 'Il commento non è stato approvato!';
} else {
    echo 'Il commento è stato approvato';
}
```

 L’utilizzo di Zend\_Service\_Akismet non presenta particolari difficoltà. Similmente a quanto visto nella pagina precedente, lo script di esempio è diviso in tre sezioni:

1. inizializzazione dell’oggetto Zend\_Service\_Akismet, responsabile della comunicazione con il filtro antispam, attraverso l’impiego del codice Akismet e dell’URL del proprio sito;
2. raccolta delle informazioni riguardanti il commento da sottoporre al filtro;
3. interrogazione del servizio di Akismet e verifica della risposta ottenuta.
 
 Anche con Zend\_Service\_Akismet è possibile segnalare un messaggio di spam ad Akismet, utilizzando il metodo $akismet-&gt;submitSpam($data), oppure segnalare un falso positivo utilizzando $akismet-&gt;submitHam($data).