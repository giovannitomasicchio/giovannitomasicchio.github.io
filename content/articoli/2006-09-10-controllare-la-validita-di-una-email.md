---
title: "Controllare la validità di una email"
author: "Giovanni Tomasicchio"
type: article
date: 2006-09-10T12:53:06+0000
description: "Controllare con PHP la correttezza di un indirizzo email. Script per validare una mail."
url: /articoli/controllare-la-validita-di-una-email/
categories:
  - Articoli
tags:
  - "sicurezza"
  - "email"
  - "Regex"

  
---
 L'indirizzo email è uno tra i dati che più comunemente viene registrato da una applicazione web. E' importantissimo avere un modo per contattare e raggiungere i propri utenti ed una email è sicuramente il mezzo più veloce ed economico per farlo.

 Il valore di un indirizzo email quindi non può essere sciupato da un errore di input dell'utente che magari ha sbadatamente inserito in un form di registrazione una email scorretta. Ma altre motivazioni, ben più serie, richiedono un controllo attento di un indirizzo email fornito dall'utente. Diversi sono i possibili problemi di sicurezza che possono incorrere nell'uso di un indirizzo email opportunamente "manipolato" da un malintenzionato.

 Senza addentrarci nella problematica vediamo come poter eseguire dei minimi controlli su una stringa per verificare se essa realmente contiene un indirizzo email valido.

 ```
<pre class="brush: php">
<?php
function chkEmail($email)
{
	// elimino spazi, "a capo" e altro alle estremità della stringa
	$email = trim($email);

	// se la stringa è vuota sicuramente non è una mail
	if(!$email) {
		return false;
	}

	// controllo che ci sia una sola @ nella stringa
	$num_at = count(explode( '@', $email )) - 1;
	if($num_at != 1) {
		return false;
	}

	// controllo la presenza di ulteriori caratteri "pericolosi":
	if(strpos($email,';') || strpos($email,',') || strpos($email,' ')) {
		return false;
	}

	// la stringa rispetta il formato classico di una mail?
	if(!preg_match( '/^[\w\.\-]+@\w+[\w\.\-]*?\.\w{1,4}$/', $email)) {
		return false;
	}

	return true;
}

$email = 'test@dominio.it';

if(chkEmail($email)) {
	echo 'Indirizzo email corretto';
}
else {
	echo 'Indirizzo email errato';
}
?>
```

 Il precedente script PHP contiene la funzione chkEmail() che ha il compito di eseguire alcune verifiche sulla stringa passatagli come parametro. Ecco i test eseguiti:

- eliminati gli spazi alle estremità della stringa con trim() controllo che essa non sia vuota
- contando il numero di @ presenti nella stringa verifico che essa contiene una sola email
- cerco eventuali caratteri "pericolosi": punto e virgola, virgola, spazio
- con una espressione regolare controllo il formato dell'indirizzo email. Questo test è per certi versi ridondante e potrebbe portare in rari casi a dei falsi positivi.
 
 L'utilizzo della fuzione chkEmail(), mostrato nelle ultime righe dell'esempio, è molto semplice. Basta passare la stringa contenente l'indirizzo email e verificare il risultato ottenuto: *true* in caso di email corretta, *false* altrimenti.