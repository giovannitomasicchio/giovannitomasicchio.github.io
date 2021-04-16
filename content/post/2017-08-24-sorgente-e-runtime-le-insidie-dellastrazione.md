---
title: Sorgente e runtime, le insidie dell’astrazione
author: giovannitomasicchio
type: post
date: 2017-08-24T13:35:17+00:00
url: /sorgente-e-runtime-le-insidie-dellastrazione/
thumbnail: /image/blog/2017/08/astrazione-1.jpg
categories:
  - Senza categoria
tags:
  - astrazione
  - ETL
  - eventual consistency
  - Integrazione
  - RESTful
  - scalabilità
  - teorema CAP
  - test di carico

---
Quando sviluppiamo tendiamo a **trascurare gli aspetti legati al momento dell&#8217;esecuzione del codice**, al così detto runtime. Questo capita maggiormente quando si ha a che fare con i linguaggi di alto livello, magari a macchina virtuale come il Java che &#8220;gira&#8221; all&#8217;interno di una [JVM][1] o il C# eseguito da una [CLR][2], ma anche con i linguaggi di scripting come il PHP interpretato ed eseguito dallo [Zend Engine][3]. Questi ambienti di runtime infatti operano dietro le quinte, ad esempio gestendo in maniera trasparente l&#8217;allocazione dinamica della memoria.

Ad allontanare ulteriormente il codice che scriviamo nei sorgenti da ciò che viene realmente eseguito a runtime ci sono altri ingombranti layer software come il **framework** impiegato, l&#8217;**application server** e lo stesso **sistema operativo, **virtualizzazioni e container Docker compresi. I più insidiosi a mio avviso sono i framework web, sia quelli lato server ([Spring][4], [ASP.NET MVC][5], [Zend][6], [Symfony][7]),  sia quelli lato client ([Angular][8], [React][9]), che per semplificare il compito dello sviluppatore nascondono la reale natura del web (fatto principalmente di HTTP, HTML e javascript)

In generale non penso che questo **livello di astrazione** sia un problema, anzi, è la soluzione alla sempre crescente complessità dei sistemi che siamo chiamati a realizzare. Oggi possiamo contare su hardware in grado di digerire anche le applicazioni più pesanti e su reti sempre più veloci. Godiamoci quindi l&#8217;ebrezza, **scrivendo solo poche righe di codice**, di estrarre da database, elaborare e trasferire tonnellate di dati tra due applicazioni web su rete geografica via servizi RESTful, magari in JSON o XML, il tutto in frazioni di secondo.

Ma non è tutto oro quel che luccica! Ci sono alcuni aspetti, legati al runtime, che l&#8217;astrazione del codice sorgente non ci deve far perdere di vista. Cito solo qualche esempio, se ne avete altri potete magari riportarli nei commenti (grazie!).

## Scalabilità

_&#8230;quando le risorse iniziano a scarseggiare._

Partendo dal presupposto che le micro-ottimizzazioni del codice hanno senso solo in rare circostanze, dobbiamo comunque prestare attenzione al consumo delle risorse (CPU, RAM, storage, banda, database, I/O sul filesystem, ecc.) in certi contesti, in particolare quelli che richiedono **scalabilità**.

Se ad esempio realizziamo una funzionalità web che alloca nella sessione utente un misero megabyte di RAM, se utilizzata da mille utenti tale consumo lievita ad un giga. In Java o C# non è così immediato capire, a partire dal sorgete, quanta memoria dinamica verrà allocata dall&#8217;ambiente di runtime. Ancora più difficile capire se, e quando, verrà liberata questa memoria. Possono venirci in soccorso i **test di carico** (JMeter), ovvero quei test che simulano l&#8217;utilizzo del nostro software da parte di un certo numero di utenti. Monitorando il comportamento del server in termini di risorse utilizzate potremo verificare se la nostra applicazione scala bene in funzione del numero di utenti.

Un&#8217;altra particolare tipologia di applicativi che merita attenzione è quella dei processi di **[ETL][10]**, ovvero quella in cui si leggono, elaborano e scrivono grosse quantità di dati. Dal punto di vista del sorgente non c&#8217;è differenza tra le seguenti implementazioni:

  1. leggere tutto, elaborare tutto, scrivere tutto;
  2. leggere una porzione di dati, elaborare quanto letto, scrivere quanto elaborato, ripetere dall&#8217;inizio fino allo smaltimento di tutti i dati.

E&#8217; evidente che la prima implementazione è la più immediata da realizzare ma non scala bene con il crescere del volume dei dati da trattare.

## Integrazione

_&#8230;le distanze contano._

Se stiamo usando un moderno framework web, dal punto di vista del codice sorgente, non c&#8217;è differenza tra chiamare un comune metodo di una qualunque classe ed invocare un servizio RESTful o SOAP. Scambiare informazioni tra una applicazione Android e un servizio RESTful esposto su un server remoto è questione di semplici chiamate a metodo, se visto con gli occhi del programmatore.

Ma sappiamo bene che in realtà, a runtime, la cosa è molto più complessa. Il framework infatti, in maniera del tutto trasparente e automatica, si occupa di effettuare il marshalling/unmarshalling dei dati e la trasmissione di questi via rete. E poi ci sono le incognite sulla disponibilità e raggiungibilità del sistema da invocare. Quindi, oltre a dover considerare il diverso &#8220;peso&#8221; di una chiamata a sistema remoto, dobbiamo:

  1. gestire i problemi di lentezza o indisponibilità dei sistemi remoti in ciascuna invocazione effettuata;
  2. progettare l&#8217;intera applicazione affinché sia minimizzato l&#8217;impatto dell&#8217;indisponibilità dei sistemi remoti;
  3. fare attenzione alle transazioni che coinvolgono sistemi remoti, per via dei vincoli esposti nel [teorema CAP][11] e affidarsi a soluzioni di tipo [Eventual consistency][12].

Su questo ultimo punto cercherò di tornare in un [prossimo post][13].

## Conclusioni

Quelli che ho citato sono solo alcuni degli scenari in cui l&#8217;astrazione dei linguaggi di alto livello e dei framework di sviluppo può far sottovalutare la reale complessità sottostante e portare a prendere delle decisioni sbagliate in fase di progettazione e sviluppo di una funzionalità. Il mio consiglio è quindi quello di non perdere mai di vista il &#8220;runtime&#8221; poiché, in fin dei conti, il software non è altro che l&#8217;insieme delle istruzioni che l&#8217;hardware dovrà eseguire. Dobbiamo quindi progettare l&#8217;architettura dei sorgenti ma anche l&#8217;architettura del runtime, considerando la reciproca influenza.

 [1]: https://it.wikipedia.org/wiki/Macchina_virtuale_Java
 [2]: https://it.wikipedia.org/wiki/Common_Language_Runtime
 [3]: http://www.zend.com/en/products/zend_server#engine
 [4]: https://spring.io/
 [5]: https://www.asp.net/mvc
 [6]: https://framework.zend.com/
 [7]: https://symfony.com/
 [8]: https://angular.io/
 [9]: https://facebook.github.io/react/
 [10]: https://it.wikipedia.org/wiki/Extract,_transform,_load
 [11]: https://it.wikipedia.org/wiki/Teorema_CAP
 [12]: https://en.wikipedia.org/wiki/Eventual_consistency
 [13]: https://www.giovannitomasicchio.it/cap-acid-base-eventual-consistency/