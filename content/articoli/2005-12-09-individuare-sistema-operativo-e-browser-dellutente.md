---
title: "Individuare sistema operativo e browser dell'utente"
author: "Giovanni Tomasicchio"
type: article
date: 2005-12-09T10:37:20+0000
description: "Script PHP per individuare sistema operativo e browser dell'utente"
url: /articoli/individuare-sistema-operativo-e-browser-dellutente/
categories:
  - Articoli
tags:

  
---
 Ogni qualvolta un utente visita una pagina del vostro sito, egli invia una richiesta in formato HTTP al server sul quale risiedono le vostre pagine. In questa stringa, oltre a trovarsi il nome della pagina richiesta dall'utente, ci sono molte informazioni sul suo computer, come il sistema operativo ed il browser utilizzato.

 Ecco un esempio di richiesta HTTP:

 GET /sito/index.php HTTP/1.1" 200 4615 "http://localhost/sito/index.php" "Mozilla/5.0 (Windows; U; Windows NT 5.1; it-IT; rv:1.7.8) Gecko/20050511 Firefox/1.0.4"

 Tra le varie informazioni contenute, quella che ci interessa è:

 Mozilla/5.0 (Windows; U; Windows NT 5.1; it-IT; rv:1.7.8) Gecko/20050511 Firefox/1.0.4

 Qui sono contenute informazioni sull'OS ed il browser dell'utente. Da una prima lettura balza subito all'occhio Firefox/1.0.4, che ci indica che il browser dell'utente è Firefox. Inoltre si nota la stringa Windows NT 5.1, un "altro nome" di Windows XP. Abbiamo dunque stabilito che questo utente usa Windows XP ed il browser Firefox.

 Ora è necessario creare una funzione che analizzi le stringhe e ricavi automaticamente i dati dell'utente. Per maggior chiarezza scriverò due funzioni diverse, una per il sistema operativo e una per il browser.

 La stringa che dovremo esaminare è contenuta nell'indice HTTP\_USER\_AGENT dell'array superglobale $\_SERVER, perciò potremo ottenerla in qualsiasi momento usando $\_SERVER\['HTTP\_USER\_AGENT'\].

 Sistema Operativo

 Girando nella rete, ho trovato la seguente tabella di riferimenti, che indica per ogni sistema operativo, che stringa c'è nella richiesta dell'utente:

 ```

Windows (generico)	Win
Windows XP		Windows NT 5.1
Windows 2000		Windows NT 5.0
Windows ME		Windows NT 4.90
Windows 95		Win95
Windows 98		Win98
Windows Net		Windows NT 5.2
Windows NT		WinNT4.0
Mac			Mac oppure PPC
Linux			Linux
FreeBSD			FreeBSD
SunOS			SunOS
Irix			Irix
BeOS			BeOS
OS/2			OS/2
AIX			AIX
```

 Naturalmente nulla vi vieta di cercare le stringhe relative agli altri sistemi operativi!

 Ecco quindi come potrebbe essere strutturata la funzione:

 ```
<pre class="brush: php">
<?php
function GetSistemaOperativo()
{
    $os = array(
        'Windows NT 5.1'  => 'Windows XP',
        'Windows NT 5.0'  => 'Windows 2000',
        'Windows NT 4.90' => 'Windows ME',
        'Win95'           => 'Windows 95',
        'Win98'           => 'Windows 98',
        'Windows NT 5.2'  => 'Windows NET',
        'WinNT4.0'        => 'Windows NT',
        'Mac'             => 'Mac',
        'PPC'             => 'Mac',
        'Linux'           => 'Linux',
        'FreeBSD'         => 'FreeBSD',
        'SunOS'           => 'SunOS',
        'Irix'            => 'Irix',
        'BeOS'            => 'BeOS',
        'OS/2'            => 'OS/2',
        'AIX'             => 'AIX',
    );

    foreach($os as $chiave => $valore)
    {
        if(strpos($_SERVER['HTTP_USER_AGENT'], $chiave))
        {
            return $valore;
        }
    }

    return 'Altro';
}
?> 
```

 Prima di tutto memorizziamo in un array i sistemi operativi. Ogni valore è un sistema operativo e l'indice relativo rappresenta la stringa che deve essere presente nella richiesta HTTP. Noterete che il valore Mac è presente due volte, prima con indice "Mac" e poi con indice "PPC". Questo perchè il sistema operativo sarà Mac in entrambi i casi.

 Se volete aggiungere altri sistemi operativi basta accodarli all'array $os.

 Dopo aver costruito l'array, questo viene analizzato in un ciclo foreach. Per ogni elemento, viene controllato, con la funzione strpos, se l'indice è presente in $\_SERVER\['HTTP\_USER\_AGENT'\]. In caso positivo ritornerà il valore di quell'elemento.

 Se non viene trovato nessun valore corrispondente, il ciclo foreach termina e la funzione ritorna la stringa "Altro", per indicare che non è stato identificato nessun sistema operativo.

 Browser

 Come vedrete, la funzione per ricavare il browser è molto simile alla precedente.

 ```
<pre class="brush: php">
<?php
function GetBrowser()
{
    $browser = array(
        'MSIE'      => 'Internet Explorer',
        'Firefox'   => 'FireFox',
        'Lynx'      => 'Lynx',
        'Opera'     => 'Opera',
        'WebTV'     => 'WebTV',
        'Konqueror' => 'Konqueror',
        'bot'       => 'Bot',
        'Google'    => 'Bot',
        'slurp'     => 'Bot',
        'scooter'   => 'Bot',
        'spider'    => 'Bot',
        'infoseek'  => 'Bot',
        'Nav'       => 'Netscape',
        'Gold'      => 'Netscape',
        'x11'       => 'Netscape',
        'Netscape'  => 'Netscape'
    );

    foreach($browser as $chiave => $valore)
    {
        if(strpos($_SERVER['HTTP_USER_AGENT'], $chiave ))
        {
            return $valore;
        }
    }

    return 'Altro';
}
?>
```

 La nostra funzione esaminerà la stringa verificando se in essa è contenuta una parola che identifica il browser. Per comodità, creiamo un array nel quale ogni valore è il nome di un browser, e il relativo indice è costituito dalla parola che lo identifica.

 Successivamente con un ciclo foreach esamino tutti i valori dell'array e, con la funzione strpos, verifico la presenza dell'indice nella stringa. Se l'indice è presente, restituisco il valore (ovvero il nome del browser). Se non viene trovato nessun browser, il ciclo foreach termina e la funzione restituisce "Altro".