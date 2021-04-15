---
title: "Disabilitare il register_globals"
author: "Giovanni Tomasicchio"
type: article
date: 2005-12-01T17:36:48+0000
description: "Disabilitare il register_globals con PHP"
url: /articoli/disabilitare-il-register-globals/
categories:
  - Articoli
tags:
  - "Joomla!"
  - "sicurezza"
  - "cookie"

  
---
 Se nel php.ini è abilitata la direttiva **register\_globals** il PHP crea automaticamente, all'avvio di ogni script, delle variabili globali con i dati provenienti dall'URL, dai form, dai cookies, dalla sessione, dal server web e dal browser.

 Se inizialmente questa possibilità sembrò allettante poiché permetteva un immediato accesso a tutte queste informazioni, ben presto ci si accorse che costituiva un serio rischio per la sicurezza degli script. Così dalla versione 4.2 di PHP di default il register\_globals è disabilitato.

 Molti script tuttora circolanti sono stati realizzati per funzionare con il register\_globals ad On e questo ha spinto diversi fornitori di hosting a lasciare tale direttiva abilitata. Per ovviare a questo potenziale inconveniente ci sono diverse strade.

 La più semplice ed immediata consiste nel creare (o modificare) il file **.htaccess** nella cartella principale del nostro sito all'iterno del quale scriveremo:

 ```

php_flag register_globals Off
```

 Questa operazione ha effetti simili alla modifica diretta del php.ini A volte però non è possibile usare il file .htaccess, ad esempio se stiamo usando IIS come server web. In questi casi è possibile risolvere il problema con un semplice ma efficace script che di seguito vi riporto integralmente.

 ```
<pre class="brush: php">
<?php
/**
* @version $Id: globals.php-off 47 2005-09-15 02:55:27Z rhuk $
* @package Joomla
* @copyright Copyright (C) 2005 Open Source Matters. All rights reserved.
* @license http://www.gnu.org/copyleft/gpl.html GNU/GPL, see LICENSE.php
* Joomla! is free software and parts of it may contain or be derived from the
* GNU General Public License or other free or open source software licenses.
* See COPYRIGHT.php for copyright notices and details.
*/

/**
* Emulates register globals = off
*/
function unregister_globals () {
    $REQUEST = $_REQUEST;
    $GET = $_GET;
    $POST = $_POST;
    $COOKIE = $_COOKIE;
    if (isset ( $_SESSION )) {
        $SESSION = $_SESSION;
    }
    $FILES = $_FILES;
    $ENV = $_ENV;
    $SERVER = $_SERVER;
    foreach ($GLOBALS as $key => $value) {
        if ( $key != 'GLOBALS' ) {
            unset ( $GLOBALS [ $key ] );
        }
    }
    $_REQUEST = $REQUEST;
    $_GET = $GET;
    $_POST = $POST;
    $_COOKIE = $COOKIE;
    if (isset ( $SESSION )) {
        $_SESSION = $SESSION;
    }
    $_FILES = $FILES;
    $_ENV = $ENV;
    $_SERVER = $SERVER;
}
unregister_globals();
?>
```

 Come avrete notato leggendo l'intestazione si tratta di uno script preso dal famoso CMS [Joomla](http://www.joomla.org/). Il suo funzionamento è molto semplice: per prima cosa vengono salvati tutti gli array superglobali in diverse variabili locali, poi si procede alla cancellazione delle variabili globali ed infine vengono ripristinati gli array superglobali. Includendo questo file all'inizio di uno script sarà come lavorare con il register\_globals disabilitato.