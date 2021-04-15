---
title: "Upload di un file con CURL"
author: "Giovanni Tomasicchio"
type: article
date: 2006-06-15T17:21:06+0000
description: "Script PHP per upload con CURL"
url: /articoli/upload-di-un-file-con-curl/
categories:
  - Articoli
tags:
  - "FTP"
  - "HTTPS"
  - "upload"

  
---
 **CURL** (Client URL Library) è una estensione di PHP che fornisce l'accesso alle funzioni della libreria libcurl. Queste funzioni permettono ad uno script di connettersi e comunicare con diversi tipi di server, adottando protocolli quali HTTP, HTTPS, FTP, TELNET, ecc.

 Per utilizzare CURL gli utenti Windows devono abilitare il caricamento dell'estensione **php\_curl.dll** agendo sul php.ini (basta eliminare il punto e virgola dalla riga *;extension=php\_curl.dll*).

 Numerosi sono i possibili utilizzi di CURL. Di seguito vedremo come sia possibile inviare un file dati.txt da uno script mittente.php ad un ulteriore script destinatario.php. La trasmissione avverrà attraverso il metodo POST dell'HTTP, pertanto sarà come effettuare l'upload di un file. Questa volta però il mittente non sarà il browser ma lo script mittente.php, grazie proprio alle funzioni CURL.

 Ecco il listato dello script mittente.php, abbondantemente commentato. Si noti come nella riga 4 per specificare il nome del file da trasmettere sia stato usato il carattere @. In questo modo CURL sa che il campo 'file\_dati' dell'ipotetico form che stiamo inviando non contiene la stringa "C:\\dati.txt" bensì il file C:\\dati.txt.

 ```
<pre class="brush: php">
<?php
// preparo l'array che conterrà i dati da inviare via POST
// in questo caso c'è solo il file da trasmettere
$dati_post['file_dati'] = "@C:\dati.txt";

// inizializzo la sessione CURL
$ch = curl_init();

// imposto l'URL dello script destinatario
curl_setopt($ch, CURLOPT_URL, "http://localhost/destinatario.php" );

// indico il tipo di comunicazione da effettuare (POST)
curl_setopt($ch, CURLOPT_POST, true );

// indico i dati da inviare attraverso POST
curl_setopt($ch, CURLOPT_POSTFIELDS, $dati_post);

// specifico che la funzione curl_exec dovrà restituire l'output
// prodotto dall'URL contattato (destinatario.php)
// invece di inviarlo direttamente al browser
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

// eseguo la connessione e l'invio dei dati e salvo in
// $postResult l'output prodotto dall'URL contattato
$postResult = curl_exec($ch);

// se ci sono stati degli errori mostro un messaggio esplicativo
if (curl_errno($ch)) {
	print curl_error($ch);
}

// chiudo la sessione CURL
curl_close($ch);

// mostro l'output prodotto da destinatario.php
echo $postResult;
?>
```

 destinatario.php è invece un semplicissimo script che si occupa esclusivamente di salvare il file ricevuto da mittente.php. In caso di utilizzo reale dello script sarebbe opportuno inserire una serie di controlli per verificare se il processo di upload è stato eseguito correttamente, verifiche non necessarie ai fini di questo esempio.

 ```
<pre class="brush: php">
<?php
// provo a salvare in "dati_ricevuti.txt" il file ricevuto
if (move_uploaded_file($_FILES['file_dati']['tmp_name'], 'dati_ricevuti.txt'))
{
	// se il salvataggio è andato a buon fine
	echo "Dati ricevuti con successo\n";
}
else
{
	// se c'è stato un probela
	echo "ERRORE! Problema nella ricezione dei dati";
}
?>
```

 Per concludere è importante ricordare che questo tipo di trasmissione è soggetto a i vincoli imposti dal server web ricevente, in particolare ai limiti di dimensione massima del file (direttiva *upload\_max\_filesize* del php.ini).