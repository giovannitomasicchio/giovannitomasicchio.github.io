---
title: "Scrivere codice indipendente dal \"Magic Quotes\""
author: "Giovanni Tomasicchio"
type: article
date: 2005-03-05T08:34:52+0000
description: "Script PHP indipendenti dal Magic Quotes"
url: /articoli/scrivere-codice-indipendente-dal-magic-quotes/
categories:
  - Articoli
tags:
  - "cookie"

  
---
 Uno dei problemi che affligge i programmatori PHP è quello di cercare di scrivere codice che funzioni su qualunque server venga fatto girare. La maggior parte degli hoster infatti non permette di modificare le impostazioni del php.ini e questo significa che i nostri script spesso si trovano a lavorare in un ambiente molto diverso da quello nel quale li abbiamo realizzati.

 Un modo per minimizzare i possibili problemi che potrebbero crearsi è quello di inserire delle routine, all'inizio della nostra pagina PHP, che testino l'ambiente di lavoro (i valori impostati nel php.ini) e attuino eventuali contromisure per "standardizzarlo"

 Il [magic\_quotes\_gpc](http://www.php.net/manual/it/function.get-magic-quotes-gpc.php) è una delle direttive del php.ini che più spesso crea problemi. Se impostata su "On" (valore di default) il PHP provvederà automaticamente ad aggiungere degli slashes a tutti i dati provenienti da GET, POST e COOKIE. Altrimenti questi dati rimarranno inalterati.

 E' ovvio quindi che se vogliamo realizzare uno script che funzioni indipendentemente dal valore di questa direttiva dovremo preventivamente o togliere tutti gli slashes o aggiungerli ai dati GET, POST e COOKIE.

 Il seguente codice ad esempio si occupa di aggiungere gli slashes nel caso in cui nel php.ini la direttiva magic\_quotes\_gpc sia impostata ad "Off":

 ```
<pre class="brush: php">
// se nel php.ini non è abilitato il "magic_quotes_gpc"
if (!get_magic_quotes_gpc())
{
    // funzione ricorsiva per l'aggiunta degli slashes ad un array
    function magicSlashes($element)
    {
        if (is_array($element))
            return array_map("magicSlashes", $element);
        else
            return addslashes($element);
    }

    // Aggiungo gli slashes a tutti i dati GET/POST/COOKIE
    if (isset ($_GET)     && count($_GET))    $_GET    = array_map("magicSlashes", $_GET);
    if (isset ($_POST)    && count($_POST))   $_POST   = array_map("magicSlashes", $_POST);
    if (isset ($_COOKIES) && count($_COOKIES))$_COOKIE = array_map("magicSlashes", $_COOKIE);
}
```

 Se invece preferiamo lavorare senza gli slashes, indipendentemente dalle impostazioni del php.ini possiamo far precedere i nostri script dal seguente codice:

 ```
<pre class="brush: php">
// se nel php.ini è abilitato il "magic_quotes_gpc"
if (get_magic_quotes_gpc())
{
    // funzione ricorsiva per l'eliminazione degli slashes ad un array
    function magicSlashes($element)
    {
        if (is_array($element))
            return array_map("magicSlashes", $element);
        else
            return stripslashes($element);
    }

    // Rimuovo gli slashes a tutti i dati GET/POST/COOKIE
    if (isset ($_GET)     && count($_GET))    $_GET    = array_map("magicSlashes", $_GET);
    if (isset ($_POST)    && count($_POST))   $_POST   = array_map("magicSlashes", $_POST);
    if (isset ($_COOKIES) && count($_COOKIES))$_COOKIE = array_map("magicSlashes", $_COOKIE);
}
```

 Come avrete notato, l'algoritmo prevede la chiamata ricorsiva di una funzione per attraversare gli array, qualsiasi sia la loro profondità.

 La funzione get\_magic\_quotes\_gpc controlla il valore della direttiva magic\_quotes\_gpc nel php.ini, a seconda della sua impostazione gli array $\_GET, $\_POST e $\_COOKIE, se esistono e contengono dati, vengono "mappati", attraverso la funzione [array\_map](http://www.php.net/manual/it/function.array-map.php), sulla nostra funzione magicSlashes che provvede, ricorsivamente, ad aggiungere (primo esempio) o a togliere (secondo esempio) gli slashes.