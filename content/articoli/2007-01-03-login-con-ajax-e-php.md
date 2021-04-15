---
title: "Login con AJAX e PHP"
author: "Giovanni Tomasicchio"
type: article
date: 2007-01-03T16:28:23+0000
description: "Realizzare un sistema di login con AJAX e PHP"
url: /articoli/login-con-ajax-e-php/
categories:
  - Articoli
tags:
  - "AJAX"
  - "Javascript"
  - "UTF-8"

  
---
###  Introduzione

 In un [precedente articolo](http://www.phpnews.it/articoli/php/ajax-in-pratica/) abbiamo visto una semplicissima implementazione della tecnologia AJAX con PHP, che faceva uso del metodo GET dell'HTTP per inviare delle informazioni dal browser al server in maniera asincrona.

 Con questo articolo invece ci occupiamo di inviare con AJAX ad uno script PHP i dati inseriti dall'utente all'interno di un comune form HTML. Per farlo ci serviremo di un semplice esempio, realizzeremo infatti un banale sistema di login.

 La nostra attenzione sarà interamente rivolta alle problematiche inerenti l'invio dei dati via POST con AJAX e la loro corretta ricezione con PHP, pertanto la logica di funzionamento del sistema di login sarà molto semplice.

 Divideremo la realizzazione di tale sistema in 3 fasi: realizzazione del form HTML, realizzazione dello script Javascript e dello script PHP.

- - - - - -

###  Il form per il log-in

 C'è veramente poco da dire sull'HTML necessario a realizzare il form per l'immissione di username e password. In realtà non è necessario neanche utilizzare il tag form, dato che i dati non saranno inviati attraverso il submit del form ma il POST sarà ?confezionato? ed inviato dall'oggetto request di Javascript.

 Tutto il Javascript necessario all'interazione con il server è contenuto nel file login.js incluso nella pagina alla riga 5.

 Si noti l'evento onClick associato al bottone e responsabile dell'avvio della procedura di invio dati con AJAX.

 **login.htm**

 ```
<pre class="brush: xml">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
<title>Login</title>
<script type="text/javascript" language="javascript" src="login.js"></script>
</head>
<body>
<fieldset style="width:250px">
<legend>log-in</legend>
<p>
  <label>username
  <input name="username" type="text" id="username" />
  </label>
</p>
<p>
  <label>password
  <input name="password" type="password" id="password" />
  </label>
</p>
<p>
  <input name="Button" type="button" onClick="javascript:invia_dati();" value="Button" />
</p>
</fieldset>
</body>
</html>
```

- - - - - -

###  Il Javascript per AJAX

 Tutto il codice Javascript necessario all'interazione asincrona con il server, conservato nel file login.js, è racchiuso in 3 funzioni:

- crea\_http\_req() si occupa di creare http\_req, l'oggetto request responsabile della comunicazione con il server. Questa funzione tenta diverse strategie per istanziare tale oggetto, in modo da risultare efficace con la maggior parte dei browser che supportano AJAX.
- invia\_dati() configura e utilizza http\_req per inviare username e password via POST al server. Questi dati vengono impacchettati nella stringa dati\_post, come una sequenza di coppie chiave=valore separati dal carattere &amp;. Si noti come i valori vengono preventivamente codificati con la funzione **encodeURIComponent**, in modo che non presentino caratteri illeciti per un POST.
- gestisci\_risposta() infine intercetta la risposta proveniente dal server e, a seconda del risultato restituito, mostra un messaggio all'utente attraverso alert().
 
 **login.js**

 ```
<pre class="brush: javascript">
// crea l'oggetto per la comunicazione AJAX con il server
// compatibile con tutti i browser che supportano AJAX
function crea_http_req() {
	var req = false;
	if (typeof XMLHttpRequest != "undefined")
		req = new XMLHttpRequest();
	if (!req && typeof ActiveXObject != "undefined") {
		try {
			req=new ActiveXObject("Msxml2.XMLHTTP");
		} catch (e1) {
			try {
				req=new ActiveXObject("Microsoft.XMLHTTP");
			} catch (e2) {
				try {
					req=new ActiveXObject("Msxml2.XMLHTTP.4.0");
				} catch (e3) {
					req=null;
				}
			}
		}
	}

	if(!req && window.createRequest)
		req = window.createRequest();

	if (!req) alert("Il browser non supporta AJAX");

	return req;
}

// l'oggetto per comunicare con il server
var http_req = crea_http_req();

// invia i dati del form al server
function invia_dati() {
	var dati_post = "username=" +
					encodeURIComponent( document.getElementById("username").value ) +
					"&password=" +
					encodeURIComponent( document.getElementById("password").value );

	http_req.onreadystatechange = gestisci_risposta;
	http_req.open('POST', 'login.php', true);
	http_req.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
	http_req.setRequestHeader("Content-length", dati_post.length);
	http_req.setRequestHeader("Connection", "close");
	http_req.send(dati_post);
}

// recupero e gestisco la risposta inviata dal server
function gestisci_risposta() {
	if(http_req.readyState == 4) {
		var esito = http_req.responseText;
		switch (esito) {
		  case '1':
			alert('username non presente nel sistema');
		  break;

		  case '2':
			alert('password errata');
		  break;

		  case '3':
			alert('username o password non inserite');
		  break;

		  case '4':
			alert('login effettuato correttamente');
		  break;

		  default:
			alert('Risposta del server non riconosciuta: ' + esito);
		}
	}
}
```

 Le righe 41-46 sono responsabili della configurazione dell'oggetto http\_req in modo che venga effettuato un POST con AJAX. Da notare l'indicazione del Content-Type e del Content-length nelle intestazioni inviate con la richiesta al server.

- - - - - -

###  Il PHP per AJAX

 Lo script PHP che si occupa di ricevere il form inviato via AJAX e un comune script per la gestione del POST. La logica di funzionamento è molto semplice ma è necessario adottare alcuni accorgimenti per gestire correttamente le codifiche dei caratteri. Ecco i passaggi essenziali:

- username e password vengono estratti dall'array $\_POST, privati di eventuali spazi alle estremità e salvati nelle variabili $username e $password.
- Se nel php.ini è abilitato il magic\_quotes, vengono eliminati gli eventuali backslash inseriti automaticamente da PHP.
- Poiché username e password sono stati inviati dal client via AJAX, questi sono codificati in UTF8. E' necessario pertanto convertirli in ISO-8859-1 con la funzione **utf8\_decode()**.
- username e password possono finalmente essere passati alla funzione login() che, dopo una banale verifica per confronto con username "ivan" e password "segreto", restituisce un codice numerico che identifica l'esito dell'operazione.
- Il risultato del controllo deve essere inviato al client, ma non prima che questo venga convertito in UTF8, codifica utilizzata da AJAX sul browser, attraverso la funzione **utf8\_encode()**.
- Infine il codice con l'esito dell'operazione viene inviato al browser, con una semplicissima echo.
 
 **login.php**

 ```
<pre class="brush: php">
<?php
// prelevo username e password dal POST,
// eliminando eventuali spazi alle estremità delle stringhe
$username = trim($_POST['username']);
$password = trim($_POST['password']);

// elimino eventuali backslash inseriti automaticamente da PHP
if(get_magic_quotes_gpc())
{
	$username = stripslashes($username);
	$password = stripslashes($password);
}

// Converto in ISO-8859-1 i caratteri provenienti
// dal client, codificati di default in UTF8
$username = utf8_decode($username);
$password = utf8_decode($password);

// provo ad effettuare il login con i dati recuperati dal form
$esito = login($_POST['username'],$_POST['password']);

// converto in UTF8 la risposta restituita dalla funzione login()
$esito = utf8_encode($esito);

// invio la risposta al client
echo $esito;

// funzione per la verifica dei dati di login
function login($username, $password)
{
	$username_corretto = "ivan";
	$password_corretta = "segreto";

	if(!$username || !$password) {
		return 3;
	}

	if($username != $username_corretto) {
		return 1;
	}

	if($username == $username_corretto && $password != $password_corretta) {
		return 2;
	}

	if($username == $username_corretto && $password == $password_corretta) {
		return 4;
	}
}
?>
```

- - - - - -

###  Conclusioni

 Come premesso, in questo articolo ci siamo concentrati sugli aspetti strettamente inerenti l'nvio di un form attraverso POST con AJAX e PHP. Abbiamo trascurato pertanto tutte le altre problematiche che ruotano attorno all'esempio del sistema di login e più in generale dell'utilizzo di AJAX all'interno di applicazioni web.

 In particolare ci siamo soffermati sull'utilizzo del metodo POST con AJAX e sull'importanza di una corretta gestione del set di caratteri e della codifica impiegati nella comunicazione asincrona tra browser e server. Sul fronte Javascript abbiamo visto come i dati del form vanno impacchettati in una stringa e codificati con la funzione encodeURIComponent. Lo script PHP invece, non supportando nativamente l'UTF8, necessita delle funzioni utf8\_decode() e utf8\_encode() per lavorare internamente con il classico set di caratteri ISO-8859-1.