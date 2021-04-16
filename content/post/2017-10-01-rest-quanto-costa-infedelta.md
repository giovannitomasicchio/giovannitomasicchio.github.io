---
title: REST, quanto costa l’infedeltà?
author: giovannitomasicchio
type: post
date: 2017-10-01T21:39:22+00:00
url: /rest-quanto-costa-infedelta/
thumbnail: /image/blog/2017/10/infedelta.jpg
categories:
  - Senza categoria
tags:
  - HTTP
  - REST
  - RESTful
  - URI

---
In questo ultimo post sul modello REST voglio provare a rispondere ad una semplice domanda: **perché dovremmo rimanere fedeli al [modello REST][1] quando realizziamo applicazioni web**?

Per farlo dobbiamo partire dagli obiettivi che questa architettura si pone:

  1. scalabilità geografica;
  2. adozione di interfacce generiche (es.: HTTP, URI, ecc.) condivise tra tutti i client e server;
  3. deploy indipendente dei componenti (es.: siti web, web app, browser, motori di ricerca, ecc.);
  4. adozione di middleware per ridurre la latenza delle interazioni (es.: proxy web con funzionalità di cache).

Avrete certamente riconosciuto temi importanti che si incrociano quotidianamente nello sviluppo di applicazioni web, d&#8217;altronde il Web non è altro che l&#8217;implementazione del modello REST su scala mondiale. Eppure nello sviluppo web non sempre si adottano soluzioni in linea con questa architettura.

Realizzare applicazioni web senza rispettare il modello REST non è necessariamente un errore ma generalmente porta a risultati che non beneficiano delle caratteristiche precedentemente elencate. Provo quindi a fare qualche esempio di mancato rispetto del modello REST nelle applicazioni web, per analizzarne di volta in volta le conseguenze. Per individuare questi casi basta cercare i contesti in cui vengono utilizzati, ma non rispettati, gli standard HTTP e URI* che sono alla base dell&#8217;adozione del modello REST nel Web.

## Single-page application

Le [single-page application][2] sono quelle applicazioni web che espongono tutte le loro funzionalità su di un singolo URI. Queste applicazioni non rispettano il principio REST secondo cui ad un identificativo deve essere associata una specifica risorsa, pertanto **non rispettano una dell&#8217;interfacce generiche del Web, gli URI**. Ne consegue che **i client di queste applicazioni**, come i browser, i motori di ricerca e i sistemi di analisi del traffico, **non funzioneranno correttamente**. Infatti:

  * La cronologia del browser rimane ferma all&#8217;unico URL utilizzato dall&#8217;applicazione e di conseguenza le frecce &#8220;avanti&#8221; e &#8220;indietro&#8221; del browser non funzionano come dovrebbero. Per la stessa ragione anche i &#8220;preferiti&#8221; del browser sono piuttosto inefficaci. È necessario adottare specifiche strategie ([Fragment identifier][3] o [History API][4]) per aggirare il problema.
  * I motori di ricerca non riescono a navigare all&#8217;interno dell&#8217;applicazione e ad indicizzare le diverse risorse poiché queste vengono servite dallo stesso URL.
  * I servizi di analisi del traffico web hanno difficoltà a distinguere le visite effettuate alle diverse sezioni dell&#8217;applicazione.

## Submit ripetuto dei form

Vi è mai capitato di utilizzare la freccia &#8220;indietro&#8221; del browser ed imbattervi in quell&#8217;avviso che vi chiede conferma sull&#8217;invio del form? Ciò succede quando **l&#8217;applicazione web non rispetta il significato dei verbi, o metodi, dell&#8217;HTTP**.

Secondo le specifiche di questo protocollo, il metodo GET deve essere utilizzato esclusivamente per recuperare una risorsa dal server, quindi soltanto nei link delle pagine HTML e nei form di ricerca. Il metodo POST invece deve essere utilizzato per richiedere al server di accettare l&#8217;entità (i dati) presenti nella request come subordinati rispetto a quelli associati all&#8217;URL. In risposta il server dovrebbe limitarsi ad indicare l&#8217;esito della richiesta, ad esempio 201 Created, ed un URL su cui proseguire la navigazione mediante una nuova richiesta GET.

Spesso invece le applicazioni web rispondono ad una richiesta POST con una pagina HTML. Il browser la aggiunge alla sua history, associandola alla request necessaria al suo ottenimento. Queste request dovrebbero essere tutte di tipo GET, quindi ripetibili senza ripercussioni per il server, ma l&#8217;applicazione web non sta rispettando il protocollo. Alla pressione del tasto &#8220;indietro&#8221;, **il browser ci chiede conferma prima di ripetere il POST, poiché questo tipo di richiesta potrebbe modificare lo stato del server**. Il mancato rispetto del protocollo HTTP può esporre quindi la nostra applicazione ad indesiderate ed involontarie modifiche ai dati, semplicemente navigando la history del browser.

L&#8217;approccio più corretto che una web app dovrebbe seguire va sotto il nome di [pattern PRG][5] (Post, Redirect, Get) secondo cui il server, a fronte di una richiesta di tipo POST, deve rispondere con un redirect all&#8217;URL contenente la successiva risorsa da restituire al browser. Questa verrà recuperata con una ulteriore request, questa volta di tipo GET.

## POST nei form di ricerca

I form HTML impiegati per la ricerca all&#8217;interno di archivi dovrebbero sempre utilizzare il metodo GET. Infatti questi form servono a recuperare risorse dal server (GET), non a modificare lo stato del server (POST). A volte invece questi form utilizzano erroneamente delle request di tipo POST a cui il server risponde direttamente con la pagina web contenente i risultati. Oltre ai problemi di submit ripetuto precedentemente descritti, **questo errore impedisce ai sistemi di cache** (browser, proxy cache) **di funzionare** poiché lo standard prevede che la response di un POST non venga memorizzata in cache. Inoltre **le pagine dei risultati non saranno indicizzabili** dai motori di ricerca poiché, come le single page application, su di un unico URL vengono mostrati risultati di ricerche aventi diversi criteri.

## Servizi SOAP

I servizi SOAP su HTTP sono un caso eclatante di **utilizzo &#8220;abusivo&#8221; degli URI e dell&#8217;HTTP**, anzi sono in contrapposizione con il modello REST. Infatti su di un unico URL vengono esposte diverse risorse e funzionalità. Inoltre l&#8217;HTTP viene utilizzato solamente come &#8220;lascia passare&#8221; verso i vari apparati di sicurezza delle reti, generalmente configurati per consentire le trasmissioni effettuate con questo protocollo. I messaggi XML dei servizi SOAP contengono tutte le informazioni necessarie all&#8217;interazione client-server. Non serve quindi utilizzare l&#8217;URI come sistema di identificazione delle risorse, né l&#8217;HTTP per specificare cosa chiede il client e l&#8217;esito di tale richiesta.

Banalizzando potremmo dire che l&#8217;interfaccia di un qualsiasi sito o applicazione web è identica: URI e HTTP. Ed infatti un qualunque client, come un browser, è in grado di interagire correttamente con qualsiasi sito o applicazione web. Invece l&#8217;interfaccia dei servizi SOAP è sempre diversa e non è possibile realizzare un client che funzioni con tutti i servizi SOAP. Lo sviluppo di un client SOAP non può prescindere dalla conoscenza dell&#8217;interfaccia del particolare servizio SOAP con cui dovrà interagire. Anzi, molto spesso si realizza un servizio SOAP conoscendo già il sistema che dovrà invocarlo.

Ne consegue che i tradizionali sistemi di navigazione ed indicizzazione del Web non funzionano coi i servizi SOAP che invece ricorrono a specifiche soluzioni, come i [WSDL][6] e i registri [UDDI][7]. Inoltre in SOAP l&#8217;utilizzo di interfacce specifiche impedisce anche l&#8217;impiego di tradizionali sistemi intermediari, come proxy web con funzione di cache o di controllo degli accessi, poiché questi non saprebbero come interpretare i messaggi SOAP. Anche in questo caso ci si affida a specifiche soluzioni come, ad esempio, gli [ESB][8].

I servizi RESTful su HTTP si affidano invece a questo protocollo e agli URI per adottare una interfaccia standard tra client e server. Di conseguenza si prestano meglio all&#8217;utilizzo &#8220;world wide&#8221;, sotto forma di [API][9] a disposizione di un generico sistema client, non noto al server. Se ben realizzato, per navigare all&#8217;interno delle risorse esposte da un servizio RESTful è sufficiente conoscere l&#8217;URL principale dal quale si dovrebbero ricevere informazioni sulle altre risorse (URL) disponibili, un po&#8217; come accade con la home page di un sito web che permette di accedere, mediante link, a tutte le ulteriori pagine del sito.

I servizi RESTful quindi non sono né migliori né peggiori dei servizi SOAP. I primi fanno uso di interfacce standard orientate alle risorse, i secondi invece utilizzano interfacce specifiche, orientate per lo più all&#8217;esecuzione di procedure remote ([RPC][10]), di derivazione [RMI][11] e [CORBA][12].

## Conclusioni

Dopo questa lunga dissertazione provo a condensare il tutto con tre concetti che, a mio avviso, dovrebbero essere ben chiari a ciascuno sviluppatore web.

  1. Sviluppare applicazioni web significa costruire software utilizzando degli standard (URI, HTTP, HTML, ecc.) che pertanto devono essere sufficientemente conosciuti.
  2. Questi standard costituiscono le interfacce generiche utilizzate da tutti i componenti della rete (server, browser, motori di ricerca, proxy, ecc.). Se questi standard non vengono rispettati dal nostro applicativo, qualche componente smetterà di funzionare correttamente.
  3. Gli standard del Web si ispirano al modello REST per raggiungere gli obiettivi che questa architettura si pone. Rispettare questi standard consente alle nostre applicazioni di raggiungere gli stessi obiettivi senza dover introdurre specifiche soluzioni.

\___\___

* A ben vedere neanche gli standard URI e HTTP rispettano perfettamente il modello REST. Infatti in un URL è possibile inserire lo username e la password del client per accedere a risorse protette da meccanismi di sicurezza. Ma gli identificativi delle risorse REST devono contenere solo informazioni relative al server, non al client. Anche i cookie dell&#8217;HTTP non rispettano il modello REST, infatti contengono dati associati non ad un singolo URL ma a tutti gli URL di un determinato percorso. Questi dati poi non sono semanticamente strutturati e pertanto risultano potenzialmente pericolosi. Con essi infatti è possibile tracciare la navigazione di un utente.

 [1]: /rest-applicazione-al-web-ed-ai-servizi-restful/
 [2]: http://itsnat.sourceforge.net/php/spim/spi_manifesto_en.php
 [3]: https://en.wikipedia.org/wiki/Fragment_identifier "Fragment identifier"
 [4]: https://developer.mozilla.org/en-US/docs/Web/API/History_API "Comparison of layout engines (HTML5)"
 [5]: https://en.wikipedia.org/wiki/Post/Redirect/Get
 [6]: https://it.wikipedia.org/wiki/Web_Services_Description_Language
 [7]: https://it.wikipedia.org/wiki/Universal_Description_Discovery_and_Integration
 [8]: https://it.wikipedia.org/wiki/Enterprise_Service_Bus
 [9]: https://it.wikipedia.org/wiki/Application_programming_interface
 [10]: https://it.wikipedia.org/wiki/Chiamata_di_procedura_remota
 [11]: https://it.wikipedia.org/wiki/Remote_Method_Invocation
 [12]: https://it.wikipedia.org/wiki/Common_Object_Request_Broker_Architecture