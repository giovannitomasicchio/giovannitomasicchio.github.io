---
title: "Invio di email usando Zend_Mail"
author: "Mario Santagiuliana"
type: article
date: 2010-09-06T07:30:00+0000
description: "Inviare email con Zend_Mail proteggendo il form dallo spam con Zend_Service_ReCaptcha"
url: /articoli/invio-di-email-usando-zend_mail/
categories:
  - Articoli
tags:
  - "Zend Framework"
  - "CAPTCHA"
  - "email"
  - "SMTP"

  
---
 Questo articolo illustra come utilizzare lo [Zend Framework](http://framework.zend.com/) per realizzare un form di invio email, utile ad esempio agli utenti per contattare l'amministratore del sito oppure per segnalare una pagina del sito ad amici. Zend Framework fornisce tutti gli oggetti necessari allo scopo, oggetti che possono essere usati singolarmente, al di fuori di una applicazione MVC.
 L'uso di un framework non deve spaventare coloro che non si sentono degli esperti nella programmazione in PHP. Al contrario l'utilizzo degli oggetti forniti dal framework semplifica e velocizza la realizzazione dello script.

 Iniziamo con la creazione del form in HTML che permetterà di inviare i dati inseriti dall'utente tramite $\_POST allo script send.php:

 ```
<pre class="brush: php">
<form method="post" action="send.php" name="form">
<table border="0px">
<tr>
 <td style="text-align: right">
   Tuo Nome:
 </td>
 <td>
   <input type="text" name="name" size="40" maxlength="80" />
 </td>
</tr>
<tr>
 <td style="text-align: right">
   Tua Email:
 </td>
 <td>
   <input type="text" name="email" size="40" />
 </td>
</tr>
<tr>
 <td style="text-align: right">
   Oggetto:
 </td>
 <td>
   <input type="text" name="subject" size="40" maxlength="80" />
 </td>
</tr>
<tr>
 <td style="text-align: right">
   Testo:
 </td>
 <td>
   <textarea name="testo" cols="60" rows="10"></textarea>
 </td>
</tr>
<tr>
 <td>
 </td>
 <td style="text-align: right">
   <input type="submit" value="Invia" />
 </td>
</tr>
</table>
</form>

```

 Proseguiamo con la creazione dello script send.php. Per prima cosa è bene fare un controllo sull'array $\_POST. Se questo è vuoto è inutile proseguire poiché significa che l'utente non ha inviato il form, mostriamo quindi un messaggio di errore.

 Il passaggio successivo sarà richiamare l'oggetto [Zend\_Loader\_Autoloader](http://framework.zend.com/manual/en/zend.loader.html). Questa classe permette di caricare in automatico i vari oggetti del framework senza la necessità di dover includere i singoli script del framework contenenti le classi che ci interessano.

 Fatto ciò possiamo istanziare un oggetto [Zend\_Mail](http://framework.zend.com/manual/en/zend.mail.html) (referenziato dalla variabile $mail nello script) e comporre così l'email.

 La composizione di una email a partire dai campi del form creato precedentemente è davvero semplice ed intuitiva e uno sguardo allo script send.php lo dimostra chiaramente.

 Fatto ciò si deve spedire la posta. Prima però è necessario indicare a Zend\_Mail con quale "trasporter" inviare la propria email, ovvero quale sistema di invio impiegare. Di default l'invio di una email avviene tramite Zend\_Mail\_Transport\_Sendmail. Questo transporter utilizza il server sendmail impostato nella configurazione del php. Dunque per inviare l'email basterebbe utilizzare il metodo send di Zend\_Mail e l'email viene inviata.
 Supponiamo però di voler utilizzare un server SMTP. Per farlo ci viene in aiuto un oggetto molto interessante: Zend\_Mail\_Transport\_Smtp. Nello script send.php si può vedere come si impostano i parametri di connessione al server SMTP.
 Infine, tramite il metodo send di Zend\_Mail si invia l'email indicando il "transporter" creato:

 ```
<pre class="brush: php">
<?php
    // includo la cartella del framework
    // Attenzione, il path impostato è per server windows, se si è su un server linux il path dovrebbe essere del tipo: /directory/zendframework/library
    set_include_path(get_include_path().PATH_SEPARATOR."C:\Programmi\ZendFramework\library");
    
    /**
     * Se il form è stato riempito procedo con l'invio della email  
     */
    if($_POST){

	/**
	 * Richiamo il file contenente la mia classe Zend_Loader_Autoloader
	 *
	 * in questo modo posso caricare facilmente tutte le classi di
	 * Zend Framework agevolmente senza dover ogni volta fare un require
	 * come sto facendo ora
	 */
	require_once("Zend/Loader/Autoloader.php");
	
	$autoloader = Zend_Loader_Autoloader::getInstance();
	
	/**
	 * Istanzio il mio oggetto Zend_Mail
	 */
	$mail = new Zend_Mail();

	/**
	 *  Compongo la mia email 
	 *  impostando le varie parti dell'email
	 */
	$mail->addTo('mario@example.com', 'Mario Santagiuliana');
	
	$mail->setFrom($_POST['email'], $_POST['name']);
	$mail->setSubject($_POST['subject']);
	$mail->setBodyText($_POST['testo']);

	/**
	 *  Voglio usare il mio server smtp, imposto i
	 *  parametri di configurazione per la connessione smtp 
	 */
	$config = array('auth' => 'login',
		'username' => 'mario@example.com',
		'password' => 'la_mia_password',
		'port' => 25);
	/**
	 *  Istanzio l'oggetto Zend_Mail_Transport_Smtp indicandogli i
	 *  parametri di connessione e di accesso
	 */
	$transport = new Zend_Mail_Transport_Smtp('smtp.example.com', $config);
	
	/**
	 * Invio l'email tramite il mio server smtp appena configurato
	 * e stampo un messaggio di corretta esecuzione dello script
	 */
	$mail->send($transport);
	$messaggio = "Email inviata correttamente";
    } else {
	/**
	 * Imposto un messaggio generico di errore
	 */
	$messaggio = "Non hai riempito il form per il messaggio";
    }

    // Stampo il messaggio
    echo $messaggio;
?>
```

 Come si può vedere la creazione dello script, utilizzando le classi di Zend è oltremodo semplice e veloce.

- - - - - -

 Allo script appena visto però manca ancora qualche cosa. Cosa succede se l'utente inserisce un indirizzo email sbagliato? Come evitare un utilizzo improprio dello script, ad esempio per inviare spam?

 Nessun problema, Zend Framework fornisce un oggetto per controllare la validità di un indirizzo email: [Zend\_Validate\_EmailAddress](http://framework.zend.com/manual/en/zend.validate.set.html). Includiamo quindi questo controllo nello script precedente (referenziato da $validator) bloccando l'esecuzione dello script nel caso l'email fornita non sia valida.
 Invece per limitare l'uso improprio del form ed evitare lo spam, Zend Framework mette a disposizione due componenti: Zend\_Captcha e [Zend\_Service\_ReCaptcha](http://framework.zend.com/manual/en/zend.service.recaptcha.html). Nell'esempio che segue vedremo l'utilizzo di quest'ultimo. E' necessario essere registrati al servizio [ReCaptha](http://www.google.com/recaptcha) e possedere le relative chiavi per poterlo utilizzare.

 L'uso è molto semplice, basta istanziare l'oggetto e inserire il controllo a monte, subito dopo aver verificato che l'array POST abbia degli elementi al suo interno. Bisogna inoltre modificare anche il codice HTML del form per poter inserire al suo interno anche il codice di ReCaptcha.

 Invece di modificare il file HTML, stavolta lo includiamo direttamente all'interno dello script send.php. In questo modo avremo un unico script. Il form HTML subirà un piccolo mutamento in modo tale che i dati vengano inviati a se stesso (il campo action del tag form sarà pertanto vuoto).
 Cambieremo inoltre la logica dello script. Zend\_Service\_ReCaptcha tramite il metodo getHTML fornisce l'output del codice per ReCaptcha, sposteremo quindi l'inizializzazione di Zend\_Loader\_Autoloader al di fuori del controllo dell'array POST. Un'altra piccola modifica la facciamo sul messaggio di errore: se non viene compilato correttamente perché non far visualizzare nuovamente il form?

 Ecco dunque come si presenta lo script finale.

 ```
<pre class="brush: php">
<?php
    // includo la cartella del framework
    // Attenzione, il path impostato è per server windows, se si è su un server linux il path dovrebbe essere del tipo: /directory/zendframework/library
    set_include_path(get_include_path().PATH_SEPARATOR."C:\Programmi\ZendFramework\library");

    /**
     * Richiamo il file contenente la mia classe Zend_Loader_Autoloader
     *
     * in questo modo posso caricare facilmente tutte le classi di
     * Zend Framework agevolmente senza dover ogni volta fare un require
     * come sto facendo ora
     */
    require_once("Zend/Loader/Autoloader.php");
    
    $autoloader = Zend_Loader_Autoloader::getInstance();
    
    /**
     * Voglio essere sicuro che sia una persona a scrivermi e non una macchina
     * uso il servizo di ReCaptcha con Zend_Service_ReCaptcha
     */
    $recaptcha = new Zend_Service_ReCaptcha('inserire_la_chiave_pubblica', 'inserire_la_chiave_privata');
    // Abbellisco un po' l'output di recaptcha, la lingua italiana non è ancora disponibile
    $recaptcha->setOptions(array(
		'theme'=>'clean',
		'lang'=>'it',
		'tabindex'=>5
		)); 
	
    /**
     * Se il form è stato riempito procedo con l'invio della email  
     */
    if($_POST){
	// Controllo se il recaptcha inserito è corretto
	$result = $recaptcha->verify(
	    $_POST['recaptcha_challenge_field'],
	    $_POST['recaptcha_response_field']
	);
	// Se i codici inseriti dall'utente sono validi allora posso proseguire
	if($result->isValid()){
	    /**
	     * Controllo la validità dell'email, se è valida proseguo con l'Invio
	     * altrimenti stampo un messaggio di errore 
	     */
	    $validator = new Zend_Validate_EmailAddress(); // Istanzio l'oggetto

	    if ($validator->isValid($_POST['email'])) {
		/**
		 * Istanzio il mio oggetto Zend_Mail 
		 */
		$mail = new Zend_Mail();

		/**
		 *  Compongo la mia email 
		 *  impostando le varie parti dell'email
		 */
		$mail->addTo('mario@example.com', 'Mario Santagiuliana');
		
		$mail->setFrom($_POST['email'], $_POST['name']);
		$mail->setSubject($_POST['subject']);
		$mail->setBodyText($_POST['testo']);

		/**
		 *  Voglio usare il mio server smtp, imposto i
		 *  parametri di configurazione per la connessione smtp 
		 */
		$config = array('auth' => 'login',
			'username' => 'mario@example.com',
			'password' => 'la_mia_password',
			'port' => 25);
		/**
		 *  Istanzio l'oggetto Zend_Mail_Transport_Smtp indicandogli i
		 *  parametri di connessione e di accesso
		 */
		$transport = new Zend_Mail_Transport_Smtp('smtp.example.com', $config);
		
		/**
		 * Invio l'email tramite il mio server smtp appena configurato
		 * e stampo un messaggio di corretta esecuzione dello script
		 */
		$mail->send($transport);
		$messaggio = "Email inviata correttamente";
	    } else {
		$messaggio = "L'indirizzo email fornito non è valido";
	    }
	} else {
	    $messaggio = "Il codice captcha che hai inserito non è valido";
	}
    } else {
	/**
	 * Se non ho niente nell'array POST visualizzo il form HTML
	 * Un eventuale messaggio di errore lo stampo successivamente
	 */
	?>
	<form method="post" action="" name="form">
	<table border="0px">
	<tr>
	 <td style="text-align: right">
	   Tuo Nome:
	 </td>
	 <td>
	   <input type="text" name="name" size="40" maxlength="80" />
	 </td>
	</tr>
	<tr>
	 <td style="text-align: right">
	   Tua Email:
	 </td>
	 <td>
	   <input type="text" name="email" size="40" />
	 </td>
	</tr>
	<tr>
	 <td style="text-align: right">
	   Oggetto:
	 </td>
	 <td>
	   <input type="text" name="subject" size="40" maxlength="80" />
	 </td>
	</tr>
	<tr>
	 <td style="text-align: right">
	   Testo:
	 </td>
	 <td>
	   <textarea name="testo" cols="60" rows="10"></textarea>
	 </td>
	</tr>
	<tr>
	 <td>
	</table>
	<?php
	// Visualizzo il codice HTML di ReCaptcha
	echo $recaptcha->getHTML();
	?>
	 </td>
	 <td style="text-align: right">
	   <input type="submit" value="Invia" />
	 </td>
	</tr>
	</form>
	<?php
    }

    // Stampo il messaggio ma solo se esiste
    if($messaggio)
	echo $messaggio;
?>
```

 Come si può vedere, Zend Framework è un ottimo strumento anche per la creazione di piccoli script. Il framework fornisce molti elementi utili per creare in poco tempo script che non necessariamente devono risiedere all'interno di un'applicazione web basata sul paradigma MVC. Gli oggetti del framework, infatti, possono essere richiamati e inizializzati a piacere.
 Lo script che ho proposto può essere ulteriormente migliorato. Per il form HTML si potrebbe utilizzare [Zend\_Form](http://framework.zend.com/manual/en/zend.form.html) e tramite Zend\_Validate fare anche un'ulteriore serie di controlli sulla compilazione del form da parte dell'utente.
 Rimando dunque alla [documentazione ufficiale di Zend Framework](http://framework.zend.com/manual/en/manual.html) per scoprire altre opzioni ed utilizzi dei componenti citati.