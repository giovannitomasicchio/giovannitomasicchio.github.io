---
title: "Combattere lo spam con tecniche CAPTCHA"
author: "Giovanni Tomasicchio"
type: article
date: 2006-04-21T22:07:51+0000
description: "Creare immagini CAPTCHA con PHP"
url: /articoli/combattere-lo-spam-con-tecniche-captcha/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "sicurezza"
  - "CAPTCHA"
  - "cookie"

  
---
 Dopo mille peripezie e notti insonni siamo riusciti a realizzare un guestbook o un sistema di commenti per il nostro sito web. Finalmente gli utenti hanno la possibilità di interagire con il sito, lasciare la loro "impronta", sentirsi più coinvolti. Neanche il tempo di godersi la nuova creazione che subito un altro problema sembra vanificare tutti i nostri sforzi: lo spam!!!

 E' vero, ogni tanto qualche buontempone si diverte ad "abusare" delle funzionalità offerte dai nostri script ma avevamo pensato anche a questo e dal nostro pannello di amministrazione facciamo pulizia degli interventi indesiderati. Alcuni però si ripetono costantemente e si distinguono dagli altri perché contengono pubblicità a prodotti o servizi di siti web stranieri.

 Spesso questi messaggi non vengono inseriti da utenti in carne ed ossa ma da sistemi automatizzati che si collegano con il nostro server per riempirlo di spam. Cancellare questi messaggi serve a poco, sono destinati a ripresentarsi. Ma ci si può difendere da questo tipo di attacchi?

 Alan Turing, uno dei padri fondatori della computer scienze, nel lontano 1950 affrontò il problema della creazione di test che potessero essere superati facilmente da un uomo ma non da una macchina. Proprio da questi studi prende il nome la tecnica CAPTCHA (*Completely Automated Public Turing Test to Tell Computers and Humans Apart*) comunemente usata nei siti web per verificare se l'interlocutore del sito web sia un individuo o un software.

 Una classica implementazione delle tecniche CAPTCHA si basa sulla realizzazione di immagini contenenti caratteri che l'utente deve leggere e riportare correttamente in una casella di testo. L'operazione non può essere effettuata da un software di OCR poiché i caratteri mostrati sono deformati o contengono qualche altra forma di disturbo.

 In questo articolo vedremo dapprima come sia semplice creare un sistema automatico per l'inserimento di dati in un form HTML, verrà poi illustrata la realizzazione di un semplice ma efficace procedura di protezione CAPTCHA che potete vedere in azione nelle pagine di PHPnews.it.

- - - - - -

####  Come viene generato lo spam?

 Per convincersi della facilità con cui è possibile "inquinare" un form HTML con dati inseriti attraverso un sistema automatico proponiamo uno script che fa uso delle funzioni CURL per connettersi ad un server ed inviare dei dati via POST. Ovviamente si tratta solo di un esempio senza troppe pretese ma sufficiente a comprendere il meccanismo di generazione automatica dello spam.

 Supponiamo quindi che il form bersaglio sia generato dal seguente script.

 **post.php**

 ```php
<form id="form1" name="form1" method="post" action="">
<p>titolo
<input type="text" name="titolo" />
</p>
<p>testo
<input type="text" name="testo" />
</p>
<p>
<input type="submit" name="Submit" value="invia" />
</p>
</form>
<?php
if(!$_POST) exit();

$handle = fopen('testo.txt', 'a');
fwrite($handle, 'Titolo: '.$_POST['titolo'].' testo: '.$_POST['testo']);
fclose($handle);
?>
```

 Lo script genera il form e salva in un file di testo (testo.txt) i dati che l'utente o il sistema automatico inviano via POST.

 Il seguente listato invece serve ad "attaccare" il form bersaglio:

 ```php
<?php
$post_data = array();

$post_data['titolo'] = "titolo test";
$post_data['testo'] = "contenuto";

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, "http://localhost/post.php" );
curl_setopt($ch, CURLOPT_POST, 1 );
curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
$postResult = curl_exec($ch);

if (curl_errno($ch)) {
	print curl_error($ch);
}
curl_close($ch);
print $postResult;
?>
```

 Per eseguire il precedente script è necessario abilitare il caricamento dell'estensione curl e modificare l'URL impostato con la direttiva CURLOPT\_URL in modo che punti al form bersaglio. Per verificare il funzionamento dello *spammer*  basta leggere il contenuto del file testo.txt generato da post.php.

 Ora non dovrebbero esserci più dubbi sulla semplicità con cui è possibile subissare un sito web di spam. E' quindi il caso di correre ai ripari...

- - - - - -

####  funzionamento della protezione CAPTCHA

 L'approccio impiegato per realizzare la protezione CAPTCHA non fa uso ne di cookie ne delle sessioni, in modo da interferire il meno possibile nel funzionamento degli script preesistenti. Serve solo avere a disposizione una tabella nel DB MySQL da impiegare per conservare i pochi dati necessari. Per crearla è sufficiente eseguire questa query:

 ```sql
CREATE TABLE CAPTCHA ( 
	CAPTCHA_id char(32) NOT NULL default '', 
	code char(3) NOT NULL default '', 
	`timestamp` int(11) NOT NULL default '0', 
	PRIMARY KEY (CAPTCHA_id) 
) 
```

 Il principio di funzionamento si basa sulla creazione di un codice casuale di 3 caratteri (code), che viene mostrato all'utente sotto forma di immagine distorta, e di un identificativo alfanumerico di 32 caratteri (CAPTCHA\_id) associato ad esso. Codice ed identificativo vengono memorizzati nella tabella del database, insieme all'indicazione dell'istante di creazione (timestamp). L'utente interpreta i caratteri disegnati nell'immagine che gli viene mostrata e li riporta in un campo input di un form. Un campo nascosto conserva l'identificativo del codice da riconoscere. Una volta inviato il form è sufficiente verificare che la coppia identificativo/codice sia presente nel database.

 Tale procedura si basa su tre script:

- form.php, che si limita a creare il form ed a generare un identificativo casuale.
- CAPTCHA.php, che prende l'identificativo generato, ne associa un codice ti tre lettere casuali, memorizza la coppia identificativo/codice nel database e genera l'immagine da interpretare.
- post.php, che preleva identificativo e codice inviati dall'utente e li confronta con i dati presenti nel database.
 
 L'aver memorizzato il timestamp permette di implementare un sistema di "scadenza" delle immagini ed inoltre facilita le operazioni di pulizia della tabella.

- - - - - -

####  Creazione del form

 Di seguito viene riportato il codice dello script form.php. In situazioni di utilizzo concreto di tale tecnica il form sarà ovviamente più complesso, dovendo contenere tutti gli elementi che si vogliono proteggere (input, select, textbox, ecc.).

 Si noti come l'unica vera operazione eseguita è la creazione del $CAPTCHA\_id casuale, necessaria poiché questo identificativo deve essere presente nel campo nascosto del form ed inoltre deve essere passato allo script CAPTCHA.php responsabile della creazione dell'immagine. Osservando il tag &lt;img&gt; si nota come l'immagine non è costituita da un file vero e proprio presente sul server ma viene generata "al volo" dallo script CAPTCHA.php.

 ```php
<?php
// creo un identificativo da associare al codice di sicurezza
$CAPTCHA_id = md5(microtime().mt_rand().'super1segreto2segretissimo3');

// creo un codice di controllo per la verifica dell'identificativo
$sale = "frase super segreta da cambiare a discrezione.";
$chk = md5($sale.$CAPTCHA_id);
?>

<form id="form1" name="form1" method="post" action="post.php">
Inserisci i caratteri
<img src="CAPTCHA.php?id=<?php echo "$CAPTCHA_id&chk=$chk"?>" width="110" height="70" />
qui:
<input type="text" name="CAPTCHA_code" />
<input name="CAPTCHA_id" type="hidden" id="CAPTCHA_id" value="<?php echo $CAPTCHA_id?>" />
<input type="submit" name="Submit" value="Invia" />
</form>
```

 La stringa $chk è un codice di controllo, costruito per proteggere lo script CAPTCHA.php da chiamate illecite. Viene costruito anteponendo una stringa arbitraria contenuta in $sale alla variabile $CAPTCHA\_id e calcolando l'md5 del risultato.

- - - - - -

####  Creazione dell'immagine

 Vediamo ora il funzionamento dello script CAPTCHA.php, che assolve diversi importanti compiti del nostro sistema di protezione antispam.

 Per prima cosa lo script verifica che l'identificativo passato via GET sia presente e valido. Viene ricalcolato il codice di controllo e confrontato con quello ricevuto via GET. Si procede quindi con la creazione dell'immagine. Viene allocato un colore per lo sfondo e tre colori per le lettere che vengono estratte casualmente dalla stringa $caratteri. Si noti come in tale stringa manca lo zero, per evitare confusioni con la lettera o. La funzione imagecolorallocatealpha permette di impostare la trasparenza delle lettere.

 Il ciclo for genera le tre lettere e le disegna nell'immagine. La distorsione è realizzata facendo sovrapporre le lettere e conferendo loro una piccola rotazione casuale di +/- 5°. Si noti che si è specificato l'utilizzo di un determinato font (VeraMono.ttf) da inserire nella stessa cartella dello script.

 Creata l'immagine si procede alla memorizzazione dei dati nel database. La query per l'inserimento dei dati non usa una INSERT ma una REPLACE per evitare problemi nel caso lo script CAPTCHA.php venga richiamato più volte con lo stesso identificativo. Segue una procedura che con probabilità impostata da $probabilità provvede a cancellare i record più vecchi di $durata\_CAPTCHA secondi.

 Infine l'immagine viene inviata al browser insieme ad una intestazione (header) che ne specifica il tipo MIME.

 ```php
<?php
// controllo se è stato fornito l'identificativo del codice CAPTCHA
if(!isset($_GET['id'])) exit();

// controllo la validità dell'identificativo
$CAPTCHA_id = $_GET['id'];
if(!preg_match("/^[a-f0-9]{32}$/",$CAPTCHA_id)) exit();

// ricreo il codice di controllo e lo confronto con quello passato via GET
$sale = "frase super segreta da cambiare a discrezione.";
$chk = md5($sale . $CAPTCHA_id);
if($chk != $_GET['chk']) exit();

// creo l'immagine
// ---------------------------------------------------------------

// le dimensioni dell'immagine
$size_x = 110;
$size_y = 70;

$img = imagecreatetruecolor($size_x,$size_y);

// alloco un colore per lo sfondo
$backgroung = imagecolorallocate($img,255, 255, 255);

// alloco 3 colori per le 3 lettere da decifrare
// utilizzo il canale alpha per impostare la trasparenza
$color[] = imagecolorallocatealpha($img,110,110,110,60);
$color[] = imagecolorallocatealpha($img,3,187,63,60);
$color[] = imagecolorallocatealpha($img,255,0,0,70);

// mischio l'ordine dei colori
shuffle($color);

// coloro lo sfondo creando un rettangolo
imagefilledrectangle($img,0,0,$size_x-1,$size_y-1,$backgroung);

// i caratteri da utilizzare per il codice di sicurezza
$caratteri = "ABCDEFGHIJKLMNOPQRSTUVWXYZ123456789";

// inizializzo la variabile che conterrà il codice
$codice = '';

// per ciascuno dei 3 caratteri
for ($i=0 ; $i < 3 ; $i++)
{
	// estraggo un carattere a caso
	$codice .= $caratteri{rand(0,34)};

	// disegno il carattere
	imagettftext(
			$img,
			50,
			-5+rand(0,10), // rotazione casuale
			($i+0.3)*24,
			60,
			$color[$i],
			'./VeraMono.ttf',
			$codice{$i});
}

// inserisco identificativo e codice nel DB
// ---------------------------------------------------------------
mysql_connect('localhost','root','secret');
mysql_select_db('CAPTCHA');

mysql_query("REPLACE INTO CAPTCHA (CAPTCHA_id,code,`timestamp`)
values ('$CAPTCHA_id','$codice',UNIX_TIMESTAMP())");

// con prob. impostata da $probabilità cancello i record più vecchi
$probabilità = 5; // 5%
if(mt_rand(1,100) <= $probabilità)
{
	$durata_CAPTCHA = 60*60;
	mysql_query("DELETE FROM CAPTCHA WHERE `timestamp`<(UNIX_TIMESTAMP()-$durata_CAPTCHA)");
}

// invio l'immagine al browser
// ---------------------------------------------------------------
header("Content-type: image/png");
imagepng($img);
?>
```

- - - - - -

####  Gestione del POST

 Lo script post.php completa il nostro sistema di protezione CAPTCHA. Dapprima viene verificata la presenza e la validità dell'identificativo e del codice inviati via POST. Il codice viene poi convertito in maiuscolo ed eventuali zeri vengono trasformati in caratteri "O". Una query di selezione verifica se i dati ricevuti sono presenti nella tabella del database, ignorando però quelli "scaduti".

 Se la query restituisce un risultato allora la verifica ha avuto successo. Indipendentemente dall'esito si procede poi alla cancellazione sia dei vecchi record presenti nel database, sia del record contenente l'identificativo inviato dall'utente.

 ```php
<?php
// dopo quanto tempo i dati nella tabella del DB "scadono"?
$durata_CAPTCHA = 60*60; // secondi

// ci sono tutti i dati?
if(!isset($_POST['CAPTCHA_code']) OR !isset($_POST['CAPTCHA_id'])) exit;

$CAPTCHA_code = $_POST['CAPTCHA_code'];
$CAPTCHA_id = $_POST['CAPTCHA_id'];

// controlla la validità del $CAPTCHA_id
if(!preg_match("/^[a-f0-9]{32}$/",$CAPTCHA_id))
{
	// se il $CAPTCHA_id non è valido termino la procedura
	echo '$CAPTCHA_id non valido';
}
else
{
	// controlla la validità del $CAPTCHA_code
	if (!preg_match("/^[a-zA-Z0-9]{3}$/", $CAPTCHA_code))
	{
		// se il $CAPTCHA_code non è valido non faccio la ricerca nel DB
		echo '$CAPTCHA_code non valido';
	}
	else
	{
		// $CAPTCHA_id e $CAPTCHA_code sono ben formattai, posso fare la verifica

		// rendo tutte le lettere del $CAPTCHA_code maiuscole e
		// sostituisco gli zeri con le O
		$CAPTCHA_code = strtoupper($CAPTCHA_code);
		$CAPTCHA_code = str_replace('0','O',$CAPTCHA_code);

		mysql_connect('localhost','root','secret') or die(mysql_error());
		mysql_select_db('CAPTCHA') or die(mysql_error());

		// nella verifica non considero dati più vecchi di $durata_CAPTCHA secondi
		$res = mysql_query("SELECT code FROM CAPTCHA
							WHERE
							CAPTCHA_id = '$CAPTCHA_id' AND
							code = '$CAPTCHA_code' AND
							`timestamp` > (UNIX_TIMESTAMP() - $durata_CAPTCHA)")
							or die (mysql_error());
		if(mysql_num_rows($res) != 1)
		{
			// verifica fallita
			echo '$CAPTCHA_code errato';
		}
		else
		{
			// OK!!!
			echo '$CAPTCHA_code corretto!';
		}
	}

	// cancello i vecchi codici e quello corrente
	mysql_query("DELETE FROM CAPTCHA
				WHERE CAPTCHA_id = '$CAPTCHA_id' OR
				`timestamp` < (UNIX_TIMESTAMP() - $durata_CAPTCHA)");
}
?>
```

- - - - - -

####  Considerazioni e conclusioni

 Nella realizzazione dei tre script sono state adottate alcune tecniche ed accorgimenti per aumentare il livello di sicurezza del sistema CAPTCHA:

- Per l'identificativo associato al codice CAPTCHA si è fatto uso di una stringa casuale impossibile da intuire o prevedere. Se un utente legge tale stringa non può risalire agli altri identificativi presenti nel database.
- L'identificativo è protetto da un codice di controllo che solo lo script form.php può generare correttamente. Questo codice permette allo script CAPTCHA.php di verificare se è stato richiamato realmente da form.php. Un utente non può quindi lanciare più volte CAPTCHA.php sperando che la tabella del database si riempia di record.
- CAPTCHA.php provvede anche a ripulire la tabella dai vecchi record attraverso un sistema che interviene con una determinata probabilità.
- L'immagine non viene salvata in un file ma inviata "al volo" al browser, quindi la ripetuta esecuzione dello script CAPTCHA.php non rischia di intasare il server di immagini. Inoltre non è necessario prevedere un sistema per la cancellazione delle vecchie immagini.
- Indipendentemente dall'esito del controllo del codice inserito, il record presente nel DB viene cancellato sventando così qualsiasi tentativo di attacco "brute force".
 
 Il sistema CAPTCHA presentato in questo articolo può essere certamente migliorato, utilizzando ad esempio tecniche più raffinate per la creazione dell'immagine distorta. Si è dimostrato comunque efficace nel bloccare lo spam dei commenti che quotidianamente si verificava nelle pagine di PHPnews.it, il tutto a costo zero considerato che non richiede manutenzione e non necessità di particolari risorse.