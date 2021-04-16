---
title: "L'array $_GET - dati via URL"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:35:25+0000
description: "Leggere i dati presenti nell'URL (query string) con PHP attraverso l'array $_GET"
url: /corsi/corso-php-base/larray-$_get-dati-via-url/
weight: 4
categories:
  - Corso PHP base
  
---
 Probabilmente avrete già notato che alcuni link presenti nei siti web puntano ad indirizzi (URL) piuttosto complessi che accodano al nome della pagina da visualizzare anche una serie di parametri. Ad esempio il link http://www.phpnews.it/forum/index.php?board=1.0 porta al nostro forum PHP. E' evidente che questo URL si riferisce allo script "index.php" ma notiamo anche il testo "?board=1.0".

 Questa parte dell'indirizzo si chiamata **query string** ed ha lo scopo di conservare delle informazioni da passare allo script chiamato. Tali informazioni, separate dal resto dell'URL attraverso il punto interrogativo, sono presenti sotto forma di coppie chiave=valore. In questo caso quindi alla chiave "board" è associato il valore "1.0". Nell'eventualità si debbano inserire più coppie chiave=valore allora queste saranno separate dal carattere **&amp;**. Ad esempio:

 http://www.miosito.it/index.php?chiave1=valore1**&amp;**chiave2=valore2

 A seguito della richiesta della pagina "index.php" il PHP, prima di eseguirne le istruzioni, legge la query string, decodifica le informazioni in essa contenute e le rende disponibili allo script come elementi di un array dal nome **$\_GET**. La pagina "index.php" avrà quindi a disposizione due elementi nell'array $\_GET:

 $\_GET\['chiave1'\] di valore 'valore1'

 $\_GET\['chiave2'\] di valore 'valore2'

 Ciò che accade quindi è molto simile a quanto visto nella scorsa lezione a proposito dei dati inviati attraverso un form.

 Con il seguente esempio vedremo come usare la query string per mostrare il testo della notizia selezionata:

 ```php
<?php
if(!$_GET)
{
    echo 'Seleziona una notizia da leggere: <br><br>';
    echo '<a href="index.php?id_notizia=1">Notizia 1</a> <br>';
    echo '<a href="index.php?id_notizia=2">Notizia 2</a> <br>';
    echo '<a href="index.php?id_notizia=3">Notizia 3</a> <br>';
}
else
{
    $notizie = array(1=>'Questo è il testo della notizia numero 1',
                        'Questo è il testo della notizia numero 2',
                        'Questo è il testo della notizia numero 3');

    $id_notizia = $_GET['id_notizia'];

    echo 'Testo: <br>';
    echo $notizie[$id_notizia] . '<br><br>';
    echo '<a href="index.php">Torna all\'indice</a>';
}
?>
```

 Attenzione, è importante salvare questo script con il nome index.php.

 All'inizio dello script viene controllato se c'è qualcosa nell'array $\_GET. In caso negativo vengono mostrati 3 links, tutti indirizzati alla pagina index.php ma con il parametro id\_notizia nella query string che assume i valori 1, 2 e 3.

 Se invece l'array $\_GET non è vuoto significa che l'utente ha cliccato uno di questi link. Recuperiamo allora il valore del parametro id\_notizia dall'array $\_GET e lo usiamo per estrarre dall'array $notizie il testo richiesto.

 Quando si utilizza la query string per inviare delle informazioni ad una pagina PHP bisogna tenere presente alcune limitazioni. Infatti essa non può estendersi per più di 255 caratteri ed inoltre può contenere solo lettere, numeri ed il simbolo "\_". Per ovviare a questo inconveniente si usa la funzione urlencode che si occupa proprio di sostituire i caratteri illeciti con una loro rappresentazione alternativa.

 L'array $\_GET, come l'array $\_POST, è superglobale ovvero è visibile in qualsiasi punto del nostro script, anche all'interno di una funzione.

 Ricordiamo inoltre che la query string può essere facilmente modificata dall'utente, semplicemente agendo sull'indirizzo che appare nel browser. Pertanto è molto importante effettuare tutti i controlli necessari alla verifica dei valori contenuti in $\_GET prima che questi vengano effettivamente utilizzati nello script.