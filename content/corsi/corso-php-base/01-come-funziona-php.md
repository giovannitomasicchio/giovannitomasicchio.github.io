---
title: "Cos'è il PHP? Come funziona?"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:26:13+0000
description: "Come funziona il PHP"
url: /corso-php-base/come-funziona-php/
weight: 20
categories:
  - Corso PHP base
  
---
Se state leggendo questa pagina probabilmente avete già un'idea di che cosa sia il PHP. Allora perché soffermarsi su questo argomento prima di passare ad esaminare codice PHP vero e proprio? Perché i più diffusi errori di programmazione di chi si avvicina al PHP dipendono spesso da una comprensione approssimata della natura, del funzionamento e degli scopi di questo linguaggio.

Niente paura, affronteremo il tutto cercando di non impantanarci in trattazioni teoriche, che in questo momento potrebbero ulteriormente confondere le idee. Iniziamo subito con un esempio: siete al vostro computer, che chiameremo anche **client**, connessi ad internet e dal vostro browser visualizzate la pagina web http://www.miosito.it/index.htm. Cosa è successo praticamente? Il browser ha chiesto di visionare la pagina index.htm al sito www.miosito.it ovvero ad un computer remoto, che chiameremo **server**. La richiesta è stata accolta da un programma, chiamato comunemente **server web**, che gira su quel computer e che ha risposto inviandoci il contenuto del file index.htm presente in una delle sue cartelle.

Come sono fatte queste pagine web? Probabilmente anche questo sapete, ma è meglio non dare niente per scontato. Ecco come potrebbe presentarsi una semplicissima pagina web, che dal server a seguito di una richiesta del browser viene inviata la client:

 ```php
<html>
<head>
<title>Pagina di prova</title>
</head>
<body>
<p>Sono le ore 20.24</p>
</body>
</html>
```

Quello che vedete qui sopra si chiama **codice HTML**, ed è un particolare "linguaggio" con cui è possibile spiegare ad un browser cosa deve visualizzare e come formattare tali contenuti. Il risultato di questa visualizzazione è il seguente:

Sono le ore 20.24

Come dite? Non sono le ore 20.24? Beh, non si può pretendere che il contenuto di un file HTML presente su un server cambi di minuto in minuto per fornirvi l'orario esatto. Questo è stato scritto nel file a suo tempo e questo vedete!

Dobbiamo quindi rassegnarci a realizzare pagine web tanto stupide da non capire che non sono le 20.24? No, dobbiamo semplicemente imparare ad usare il PHP.

Diamo un'occhiata al seguente codice, senza soffermarci sul suo contenuto (avremo modo in seguito di analizzarlo con calma) ma esclusivamente su ciò che il vostro browser visualizza:

 ```php
<html>
<head>
<title>Pagina di prova</title>
</head>
<body>
<p>Sono le ore <?php echo date("G.i"); ?></p>
</body>
</html>
```

Per vederne il risultato [cliccate qui](http://www.phpnews.it/corso/prova.php). Sorpresi? Questa volta l'orario è giusto! Ma come è stato possibile? Per scoprirlo vediamo quali sono state le singole fasi che hanno portato a questo risultato:

1. Cliccando sul link di prima (<http://www.phpnews.it/corso/prova.php>) avete richiesto di visionare la pagina prova.php che si trova nella cartella "corso" del server (computer remoto) che ospita il sito www.phpnews.it
2. Il programma che ha ricevuto questa richiesta, ovvero il server web (da non confondere con il server che è un computer) riceve questa richiesta.
3. Il server web capisce, guardando l'estensione ".php" del file richiesto, che si tratta di un file PHP ed invece di inviarci immediatamente il suo contenuto lo passa prima ad un ulteriore programma, il **motore PHP**.
4. Questo motore PHP individua ed esegue tutte le istruzioni PHP presenti nel file che gli è stato passato.
5. Il risultato di questa elaborazione del file PHP viene ripassato al server web.
6. Finalmente il server web può inviarci il file richiesto che verrà visualizzato nel browser.
 
Risulta adesso evidente che **ciò che arriva al nostro browser non è la pagina PHP presente sul server**. Otteniamo invece il risultato della sua elaborazione da parte del motore PHP. Spiegato quindi anche il mistero dell'orologio: il file PHP, al contrario del file HTML visto all'inizio, non contiene l'orario ma una istruzione PHP che una volta eseguita dal motore PHP, produce come risultato l'ora attuale. E poiché questa istruzione viene eseguita proprio nel momento in cui avete richiesto la pagina, allora riceverete l'ora esatta.

E se il server ha l'orologio che va 2 minuti in ritardo? Ovviamente l'orario che riceverete sarà 2 minuti in ritardo. Ecco cosa significa che il PHP è un **linguaggio Server Side**, ovvero lato server: **l'esecuzione delle istruzioni PHP avviene non sul client che visualizza le pagine, ma sul server che le conserva**. Tutto avviene lì, anzi il browser dell'utente non si accorge di nulla ed è convinto che ciò che riceve è un comunissimo file HTML da visualizzare. E non ha torto! Le istruzioni PHP presenti sono ormai state eseguite quando il file raggiunge il browser ed al loro posto è presente solo il risultato che hanno prodotto (pensate all'istruzione che generava l'orario corrente e che il motore PHP ha eseguito e sostituito con il suo risultato).

Uno script PHP è quindi un file che contiene, accanto ad eventuale codice HTML, delle istruzioni in linguaggio PHP. Queste istruzioni non sono altro che dei comandi che vengono eseguiti da quello che abbiamo chiamato motore PHP. Attraverso il PHP è possibile realizzare **pagine web dinamiche**, il cui contenuto cioè può variare a seconda di diversi fattori (come l'orologio dell'esempio precedente).