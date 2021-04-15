---
title: "Parsing di feed RSS con simpleXML"
author: "Giovanni Tomasicchio"
type: article
date: 2006-12-16T12:45:08+0000
description: "Leggere un feed RSS con PHP utilizzando SimpleXML"
url: /articoli/parsing-di-feed-rss-con-simplexml/
categories:
  - Articoli
tags:
  - "RSS"
  - "DOM"
  - "email"
  - "SimpleXML"
  - "XML"

  
---
###  I vantaggi della libreria simpleXML

 Prima che fosse introdotta la libreria simpleXML, apparsa con la versione 5 di PHP, l'attività di parsing (lettura) ed utilizzo dei dati contenuti nei documenti XML era affidata esclusivamente alle librerie SAX e DOM, sicuramente potenti ma non di immediato impiego. Questo nuovo strumento, come il nome suggerisce, semplifica notevolmente la vita degli sviluppatori interessati all'interazione tra PHP e XML.

 Grazie a SimpleXML è possibile ottenere con estrema facilità una gerarchia di oggetti che rispecchiano la struttura originale del documento XML di cui si sta effettuando l'analisi. L'uso delle proprietà e dei metodi di tali oggetti permette poi l'accesso ai contenuti dei tag del documento XML, garantendone un'immediata gestione e riutilizzo per i più svariati scopi.

 In questo articolo vedremo in particolare come sia possibile usare simpleXML per realizzare semplici applicazioni PHP in grado di leggere documenti RSS (i ben noti feed sono infatti dei documenti XML) per poi creare, sulla base dei dati estratti, pagine dinamiche che ne riportano i contenuti.

- - - - - -

###  Usare simpleXML

 Iniziamo subito a vedere come effettuare il parsing di un documento XML con simpleXML. Ecco l'unica istruzione necessaria:

 ```
<pre class="brush: php">
$documento = simplexml_load_file("file_esempio.xml");
```

 Estremamente semplice non trovate? E' interessante notare che simpleXML con questa funzione non si è limitato ad una semplice lettura del file indicato ma ha già creato la gerarchia di oggetti rappresentanti la struttura del documento XML.

 Vedremo fra un po' come accedere a questa struttura. Nel frattempo è interessante notare che il parametro passato alla funzione simplexml\_load\_file() può anche essere, e in effetti lo sarà nel nostro esempio, un indirizzo (URL) di un documento XML remoto. Questo significa che uno script, realizzato in locale o alloggiato su un sito internet, è in grado di leggere ed utilizzare documenti XML generati dal altri siti internet, come nel caso dei feed RSS.

 Entriamo ora nel vivo della questione e vediamo come accedere alla gerarchia di oggetti restituita dalla funzione simplexml\_load\_file(). Va subito osservato come tutta la gerarchia è contenuta nella variabile restituita da tale funzione, che nel nostro caso si chiama appunto $documento. Tale variabile risulta essere un oggetto corrispondente all'elemento radice del file XML. Dentro tale oggetto ci si può muove agilmente, grazie alla seguente sintassi:

 ```
<pre class="brush: php">
$documento->nome_elementi_figli;
```

 simplexml\_load\_file() restituisce quindi un oggetto che rappresenta l'elemento radice del documento XML, al cui interno verranno innestati un insieme di ulteriori oggetti, ciascuno dei quali rappresenta un nodo figlio. Il nome dei nodi figli diverrà il nome degli oggetti che li rappresentano. Se più nodi figlio posseggono lo stesso nome allora simpleXML genera un array di oggetti corrispondenti. Per chiarire le cose facciamo un esempio, riferendoci al seguente semplice documento chiamato "esempio.xml":

 ```
<pre class="brush: xml">
<radice>
   <item>primo elemento</item>
   <item>secondo elemento</item>
   <item>terzo elemento</item>
</radice>
```

 Attraverso le seguenti istruzioni possiamo recuperare l'array contenente i tre oggetti che rappresentano i 3 elementi figli &lt;item&gt; presenti in esempio.xml e annidati in &lt;radice&gt;.

 ```
<pre class="brush: php">
$documento = simplexml_load_file("esempio.xml");
$array_items = $documento->item;
```

 Infine, con un semplice ciclo foreach, possiamo stampare il contenuto dei tre oggetti &lt;item&gt;. Ecco il codice necessario:

 ```
<pre class="brush: php">
foreach($array_items as $item)
{
   echo $item . "<br />";
}
```

 Nel caso in cui i tag &lt;item&gt; del nostro file "esempio.xml" contenessero ulteriori tag figli annidati, essi avrebbero potuto essere recuperati con una sintassi del tutto simile a quella appena vista. Complichiamo un po' il nostro file "esempio.xml" e vediamo come trattarlo al fine di recuperare i nuovi tag inseriti:

 ```
<pre class="brush: xml">
<radice>
   <item>
      <title>primo elemento</title>
      <autore>pinco</autore>
   </item>
   <item>
      <title>secondo elemento</title>
      <autore>pallino</autore>
   </item>
   <item>
      <title>terzo elemento</title>
      <autore>pallone</autore>
   </item>
</radice>
```

 A questo punto se vogliamo recuperare il contenuto degli elementi figli dei tag &lt;item&gt; presenti nel documento, non dobbiamo far altro che adoperare il seguente ciclo foreach:

 ```
<pre class="brush: php">
$documento = simplexml_load_file("esempio.xml");
$array_items = $documento->item;
foreach($array_items as $item)
{
   echo $item->title . "<br />";
   echo $item->autore . "<br />";
}
```

 Come potete notare abbiamo adoperato la sintassi per il recupero degli elementi già introdotta in precedenza. Va osservato che quando un tag (nel nostro caso &lt;item&gt;) contiene elementi figli con nome distinto (nel nostro caso &lt;title&gt; o &lt;autore&gt;) non è necessario adoperare un ciclo foreach per accedervi. Basta infatti usare la sintassi:

 ```
<pre class="brush: php">
echo $item->title;
echo $item->autore;
```

 Verrà selezionato automaticamente l'unico nodo figlio "title" e "autore". I nodi figli &lt;title&gt; e &lt;autore&gt;, seppur presenti più volte nel documento XML, sono però unici nel nodo "item" di appartenenza.

- - - - - -

###  Uno script per leggere RSS 2.0

 Di seguito trovate un'applicazione di tutto quello che abbiamo spiegato fino ad ora. E' uno script che pubblicherà i feed RSS (versione 2.0) forniti da un sito web il cui indirizzo dovrà essere specificato nella configurazione dello script stesso, nella variabile $url\_feed. Considerata l'estrema variabilità dei tag usati in un documento RSS si è pensato di preparare uno script in grado di analizzare la fonte (feed) in questione e stampare, tra i tag contemplati nella [specifica RSS 2.0](http://rss.specifiche.it/2.0/), quelli effettivamente impiegati nel particolare documento RSS che si sta elaborando.

 In linea di principio un documento RSS è composto da una serie di tag &lt;channel&gt; (di solito uno), ciascuno contiene due tipologie di dati:

- un insieme di tag descriventi il canale (link al sito, autore, lingua, copyright, ecc.);
- una sequenza di tag &lt;item&gt; contenenti i contenuti veri e propri, insieme ad ulteriori informazioni ad essi associate (autore dell'item, data di pubblicazione dell'item, link alla pagina del sito in cui è pubblicato l'item, ecc.).
 
 Si tenga presente che i tag che un &lt;channel&gt; deve necessariamente contenere sono solo 3 e sono decisamente i più informativi: &lt;title&gt;, &lt;link&gt; e &lt;description&gt;. La lista di tag &lt;item&gt; comunemente presente in &lt;channel&gt; invece non è obbligatoria (benché auspicata). Un tag &lt;channel&gt; senza alcun tag &lt;item&gt; al suo interno sarebbe un pò come una emittente radio senza nessuna trasmissione all'attivo: non molto entusiasmante insomma!

 Lo script proposto preleva tutti e tre i tag obbligatori di &lt;channel&gt; per poi mostrarli nella pagina generata. Esso pubblicherà anche tutti gli altri tag possibili per &lt;channel&gt;, allorché essi fosserò effettivamente presenti nel documento RSS.

 Per quanto riguarda gli elementi &lt;item&gt; non ci sono invece tag annidati obbligatori (anche se viene consigliato di specificare, come per channel, un title, un link e un description). Lo script quindi valuta di volta in volta la presenza dei tag in questione e li trasforma in elementi di pagina HTML nel caso in cui essi sono effettivamente contenuti nei tag &lt;item&gt; presenti nel documento in analisi.

 Ecco lo script e alcuni commenti per una sua più chiara comprensione:

 ```
<pre class="brush: php">
<?php
// indirizzo remoto del feed
$url_feed = "http://www.phpnews.it/index2.php?option=com_rss&feed=RSS2.0&no_html=1";

// array contenente i tag della specifica RSS 2.0 relativi a channel - commentare gli elementi che non si desidera processare
$tags_channel = array("title" => "Titolo feed",
"link" => "URL del sito che distribuisce il canale",
"description" => "Descrizione del canale",
"language" => "Lingua del canale",
"copyright" => "Info copyright",
"managingEditor" => "Email del responsabile editoriale",
"webMaster" => "Email del responsabile del sito",
"pubDate" => "Data creazione del canale",
"lastBuildDate" => "Data ultimo aggiornamento del canale",
"category" => "Alcuni argomenti su cui verte il canale",
"generator" => "Software con cui viene generato il canale",
"docs" => "URL della documentazione sul formato del feed",
"cloud" => "Servizio web che supporti rssCloud per questo canale",
"ttl" => "Minuti prima di eseguire un aggiornamento del canale",
"rating" => "Il PICS per la votazione del canale",
"textInput" => "Specifica un campo input che può essere mostrato con il canale",
"skipHours" => "Info per gli aggregatori indicante quando inibire la lettura",
"skipDays" => "Info per gli aggregatori indicante in che giorni inibire la lettura");

// array contenente i tag della specifica RSS 2.0 relativi a item - commentare gli elementi che non si desidera processare
$tags_item = array("title" =>  "Titolo item",
"link" => "URL dell'item",
"description" => "Sintesi dell'item",
"author" => "Autore",
"category" => "Categoria dell'item",
"comments" => "URL della pagina contenente i commenti all'item",
"enclosure" => "Descrive un oggetto multimediale allegato all'item",
"guid" => "Stringa che identifica univocamente l'item",
"pubDate" => "Data di pubblicazione dell'item",
"source" => "Il canale RSS dal quale proviene l'item");


// file RSS che si desidera elaborare
$document = simplexml_load_file($url_feed);

// stampa dei tag presenti in channel
foreach($document->channel as $channel)
{
	foreach($tags_channel as $key_ch => $desc_ch)
	{
		// verifica la presenza del tag nella descrizione del canale - se esiste lo stampa
		if($channel->$key_ch)
		{
			echo "<p><b>" . $desc_ch . ":</b> " . htmlentities(utf8_decode($channel->$key_ch)) . "</p>\n";
		}
	}

	echo "<hr />";
	echo "<hr />";

	// stampa degli item e dei tag in essi annidati
	foreach($channel->item as $item)
	{
		foreach($tags_item as $key_it => $desc_it)
		{
			if($item->$key_it)
			{
				// verifica la presenza del tag nell'item - se esiste lo stampa
				echo "<p><b>" . $desc_it . ":</b> " . htmlentities(utf8_decode($item->$key_it)) . "</p>\n";
			}
		}

		echo "<hr />";
	}
}
?>
```

 Per configurare lo script basta specificare in $url\_feed l'URL remoto dell'RSS a cui si intende accedere. Lo script crea poi 2 array ($tags\_channel e $tags\_item) contenenti i nomi dei tag previsti della specifica RSS 2.0, associando a ciascuno una breve descrizione del loro significato logico da adoperare nella fase di conversione in HTML del feed. Il primo array descrive i possibili tag annidati in &lt;channel&gt; mentre il secondo quelli annidati in &lt;item&gt;.

 Caricato il documento RSS con la funzione simplexml\_load\_file, lo script passa poi a verificare la presenza nel documento dei diversi tag e, nel caso in cui essi siano effettivamente presenti, li stampa avendo cura di fare alcune codifiche sui caratteri (utf8\_decode()) ed convertendo i tag HTML (htmlentities()).

 Se si ritiene che le informazioni presenti in un particolare feed siano troppo dettagliate, basta commentare le parti superflue dei due array associativi. Questo permetterà di ignorare i tag RSS che non volete siano riconosciuti e stampati dallo script.

 Le tecniche di lettura e trasformazione dei documenti XML viste sopra rimangono valide a patto che i documenti su cui viene eseguito il parsing siano completamente aderenti al protocollo XML (o RSS nel caso dello script presentato). Documenti che non sono ben formati o che usano nomi anomali per i tag componenti i documenti possono generare errori nello script e/o risultati inattesi. Conviene quindi verificare preventivamente la validità dei feed a cui si è interessati, adoperando magari uno dei molti servizi presenti in rete. [Eccone uno](http://validator.w3.org/feed/).