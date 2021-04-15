---
title: "AJAX in pratica"
author: "Giovanni Tomasicchio"
type: article
date: 2005-07-29T18:55:45+0000
description: "Creazione di uno script PHP che utilizza AJAX"
url: /articoli/ajax-in-pratica/
categories:
  - Articoli
tags:
  - "AJAX"
  - "CSS"
  - "DOM"
  - "Javascript"
  - "XML"

  
---
 Il termine **AJAX** sta per Asynchronous JavaScript and XML ed indica una tecnica per la realizzazione di applicazioni web estremamente interattive. Si basa sulla possibilità di modificare l'HTML o il CSS di una pagina web attraverso l'uso di Javascript (DHTML) a seguito di una interrogazione del server web.

 Una tradizionale applicazione web prevede la costruzione di una pagina a seguito di ogni interrogazione del web server: l'utente richiede una pagina ed il server la invia al browser. L'interazione utente &lt;-&gt; server risulta quindi sincrona. In una applicazione web che fa uso di tecniche AJAX invece è possibile effettuare una richiesta al server ed utilizzare le informazioni ricevute per modificare la pagina web mostrata dal browser senza doverla ricaricare completamente.

 Questa dinamica si basa comunemente sui seguenti eventi:

 1) L'utente interagisce con la pagina web, magari cliccando su di un link

 2) Un Javascript raccoglie questa richiesta creando un oggetto di tipo XMLHttpRequest che viene fornito direttamente dal browser.

 3) Attraverso questo oggetto il Javascript invia una richiesta al server remoto, usando il suo metodo "send".

 4) Il server web riceve ed elabora tale richiesta, magari attraverso un normale script PHP o più in generale attraverso un qualsiasi linguaggio di programmazione server-side.

 5) La risposta del server web viene intercettata dal Javascript e non comporta quindi il completo aggiornamento della pagina mostrata dal browser.

 6) Il Javascript estrapola i dati presenti nella risposta intercettata e modifica solo determinate porzioni di HTML della pagina web.

 Anche se a prima vista questa tecnica può sembrare alquanto complessa, in realtà si riduce all'uso di un po' di Javascript e PHP. Ed è proprio il papà del PHP, [Rasmus Lerdorf](http://lerdorf.com/bio.php), a sottolineare come non sia difficile implementare AJAX su una pagina web.

 In un [messaggio](http://marc.theaimsgroup.com/?l=php-general&m=112198633625636&w=2) dal titolo "Rasmus' 30 second AJAX Tutorial" sulla mailing list [php-general](http://www.php.net/mailing-lists.php), Rasmus Lerdorf presenta un breve tutorial su AJAX che vale la pena leggere e che riportiamo nella prossima pagina. Abbiamo preventivamente contattato Rasmus che con un lapidario "Sure, go ahead." ci ha concordato il permesso di pubblicare il suo tutorial.

- - - - - -

 Ecco il breve ma efficace tutorial di Rasmus Lerdorf su AJAX:

 Trovo che AJAX sia un po' una montatura. Molte persone hanno usato tecniche simili, prima che queste fossero chiamate "AJAX". E non si tratta di cose complicate come molti fanno credere. Ecco un semplice esempio tratto da una mia applicazione. Iniziamo con il Javascript:

 ```
<pre class="brush: javascript">
function createRequestObject() {
	var ro;
	var browser = navigator.appName;
	if(browser == "Microsoft Internet Explorer"){
		ro = new ActiveXObject("Microsoft.XMLHTTP");
	}else{
		ro = new XMLHttpRequest();
	}
	return ro;
}

var http = createRequestObject();

function sndReq(action) {
	http.open('get', 'rpc.php?action='+action);
	http.onreadystatechange = handleResponse;
	http.send(null);
}

function handleResponse() {
	if(http.readyState == 4){
		var response = http.responseText;
		var update = new Array();

		if(response.indexOf('|' != -1)) {
			update = response.split('|');
			document.getElementById(update[0]).innerHTML = update[1];
		}
	}
}
```

 Questo script definisce una funzione per la creazione di un oggetto *Request*, una funzione per l'invio di tale richiesta ed una funzione per la gestione della risposta. Per essere usato, questo script va inserito all'interno di una pagina web.
 Per effettuare una richiesta al backend possiamo collegare tale script ad un evento "onClick" oppure direttamente all'attributo *href* di un link, nel seguente modo:

 ```
<pre class="brush: xml">
<a href="javascript:sndReq('foo')">[foo]</a>
```

 Questo fa si che quando un utente clicca sul link venga inviata una richiesta alla pagina rpc.php?action=foo

 Nel file rpc.php potremmo inserire qualcosa di simile:

 ```
<pre class="brush: php">
switch($_REQUEST['action']) { 
	case 'foo': 
	/* do something */ 
	echo "foo|foo done"; 
	break; 

... 
}
```

 Adesso osserviamo la funzione *handleResponse*. Essa effettua il parsing della stringa "foo|foo done" e la divide in 2 parti in modo da usare il testo che precede il carattere '|' come l'id di un elemento dom della pagina, ed il testo successivo al carattere '|' come il nuovo HTML da inserire in tale elemento dom. Ciò significa che se abbiamo nella pagina un tag div come il seguente:

 ```
<pre class="brush: xml">
```

 ```
<pre class="brush: xml">
	
```

 

 una volta cliccato sul link di prima, tale tag cambierà in:

 ```
<pre class="brush: xml">
```

 ```
<pre class="brush: xml">
	
foo done 
```

 

 Questo è tutto. Tutto il resto sono solo evoluzioni di tale tecnica che sostituiscono la semplice risposta "id|text" con un formato XML più elaborato ed inoltre rendono la richiesta molto più complessa. Prima di installare alla cieca qualche voluminosa libreria "AJAX", bisognerebbe provare a realizzare tali funzionalità da soli in modo da comprenderne esattamente il funzionamento e aumentarne la complessità solo se necessario. Spesso infatti non serve più di quanto appena mostrato.

 Per estendere questo approccio all'invio nella richiesta di più parametri si potrebbe semplicemente procedere così:

 ```
<pre class="brush: javascript">
function sndReqArg(action,arg) { 
	http.open('get', 'rpc.php?action='+action+'&arg='+arg); 
	http.onreadystatechange = handleResponse; 
	http.send(null); 
}
```

 Anche la funzione handleResponse può essere modificata affinché faccia qualcosa di più interessante di modificare il contenuto di un tag div.

- - - - - -

 Come sottolineato da Rasmus Lerdorf, il principio di funzionamento di AJAX non è di recente invenzione anche se stentano a diffondersi tali tecniche nelle comuni applicazioni web. Forse è proprio Google, con il suo servizio di posta Gmail, a costituire uno degli esempi meglio riusciti in questo campo.

 I benefici sono molteplici. A cominciare dalla notevole interattività che è possibile realizzare e che permette di portare a pieno titolo il pattern MVC (Model-View-Controller) nelle applicazioni web. Inoltre la possibilità di scambiare col server solo le informazioni veramente necessarie comporta un notevole risparmio di banda ed una conseguente riduzione dei tempi di risposta (non serve più ridisegnare l'intera pagina ad ogni interazione)

 Non mancano però i lati negativi, primo fra tutti la compatibilità tra i vari browser anche se le più recenti versioni non sembrano avere problemi.

 AJAX è sicuramente una tecnica da provare, permette infatti di superare molti dei limiti di una comune applicazione web e conferisce ad essa un livello di interattività ed tocco di professionalità senza pari.