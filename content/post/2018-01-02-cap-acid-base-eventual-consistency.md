---
title: CAP, ACID, BASE e Eventual Consistency
author: giovannitomasicchio
type: post
date: 2018-01-02T15:50:26+00:00
url: /cap-acid-base-eventual-consistency/
thumbnail: /image/blog/2018/01/chimica.jpg
toc: false
categories:
  - Senza categoria
tags:
  - architettura
  - astrazione
  - domain event
  - Domain-driven design
  - eventual consistency
  - Integrazione
  - microservizi
  - nosql
  - RESTful
  - scalabilità
  - teorema CAP

---
Per chi è abituato a lavorare con sistemi centralizzati, costituiti ad esempio da una applicazione monolitica dotata di un unico database relazionale, la realizzazione di un sistema distribuito si presenta ricca di insidie. Ne facevo cenno già in un [precedente post][1] in cui cercavo di far luce sui problemi che l&#8217;astrazione offerta dai moderni strumenti riesce a nascondere ma non a risolvere del tutto. Uno di questi problemi è legato al concetto di **consistenza dei dati** sparsi in un sistema distribuito.

In questo post proverò ad analizzare i differenti significati che il termine &#8220;consistenza&#8221; assume nei diversi contesti e come la consistenza sia un valida chiave di lettura per l&#8217;analisi delle varie strategie di integrazione tra i nodi di un sistema distribuito.

## Teorema CAP

Il **Teorema CAP**, formulato da Eric Brewer nel 1998, afferma che se i dati di un sistema sono distribuiti tra i nodi di una rete, solo due delle seguenti caratteristiche possono contemporaneamente essere garantite:

  * **Consistency**: le operazioni di lettura restituiscono il dato aggiornato, ovvero proveniente dall&#8217;ultima scrittura dello stesso, oppure un messaggio di errore.
  * **Availability**: l&#8217;accesso ai dati è sempre garantito, non si ricevono mai messaggi di errore, ma i dati restituiti non sono necessariamente consistenti, ovvero non coincidono con quelli utilizzati nell&#8217;ultima operazione di scrittura degli stessi.
  * **Partition tolerance**: il sistema continua a funzionare nonostante alcuni messaggi vengano persi o subiscano rallentamenti nelle comunicazioni tra i nodi della rete.

Questo teorema viene spesso utilizzato per analizzare le dinamiche dei sistemi informativi che possiedono due o più archivi di dati posti su nodi distinti della rete. Rientrano in questa categoria:

  * i sistemi informativi cooperanti, ovvero gli insiemi di sistemi, ciascun dotato della propria base dati, che comunicano tra loro per l&#8217;accesso a dati e a funzionalità reciprocamente esposte;
  * i sistemi informativi dotati di due o più basi dati distinte, come i sistemi che possiedono al contempo database SQL, NoSQL, sistemi di cache (es.: [Redis][2]) e di ricerca (es.: [ElasticSearch][3]); in questo caso di parla di [Polyglot Persistence][4];
  * i sistemi informativi che fanno uso di basi dati distribuite (es.: Apache HBase, Apache Cassandra, ecc.).

Questi sistemi, avendo distribuito i dati su più nodi della rete, richiedono necessariamente la _partition tolerance_ del teorema CAP. Ne consegue che nell&#8217;acceso ai dati distribuiti, e quindi nella progettazione dei meccanismi di integrazione, non si può confidare contemporaneamente sulle caratteristiche di disponibilità e consistenza, definite nel teorema CAP, dei dati richiesti.

Esistono certamente molti modi per realizzare integrazione tra sistemi informativi, finalizzati all&#8217;accesso ai reciproci dati e servizi, ma a ben vedere possono essere racchiusi in due macro categorie: quelli che puntano alla consistenza dei dati e quelli che, al contrario, puntano alla loro disponibilità.

## Integrazioni per l&#8217;accesso ai dati aggiornati

Rientrano in questa categoria tutte quelle tecniche di tipo RPC (remote procedure call) che si sforzano di riprodurre, nell&#8217;accesso ad un sistema remoto, approcci simili a quelli utilizzati per la chiamata di una procedura locale. Il pattern RPC, con la sua variante ad oggetti RMI (remote method Invocation), è alla base di molteplici standard e di particolari implementazioni: CORBA, Java RMI, Microsoft DCOM, Windows Communication Foundation, XML-RPC, SOAP e tanti altri.

Questo tipo di integrazioni **permette di accedere a dati consistenti, ovvero più aggiornati, del sistema remoto**, a patto che questo sia in funzione e raggiungibile, altrimenti l&#8217;integrazione fallisce. Pertanto, quando si sviluppano integrazioni di questo tipo, è necessario prevedere dei meccanismi di gestione dell&#8217;indisponibilità del sistema remoto. Ci si può limitare a mostrare un messaggio esplicativo all&#8217;utente, del tipo &#8220;sistema non disponibile, riprova più tardi&#8221;. Oppure si possono implementare meccanismi che ripetono l&#8217;invocazione remota fino alla sua esecuzione corretta. E&#8217; anche possibile realizzare complessi meccanismi di _fall back_, alternativi al sistema di tipo RPC, simili a quelli descritti nel prossimo paragrafo, da utilizzare fintanto che il sistema remoto non ritorna disponibile.

A prima vista simili alle tecniche RPC, i servizi RESTful, a ben vedere, sono una tipologia di integrazione a se stante. Se da un lato condividono con lo stile RPC l&#8217;approccio &#8220;on line&#8221; delle operazioni, basato su di un insieme di interazioni dirette e in tempo reale con il sistema remoto, che garantiscono l&#8217;accesso ai dati consistenti del sistema remoto, dall&#8217;altro utilizzano un modello di integrazione incentrato sull&#8217;interazione con lo stato del sistema remoto mediante sue rappresentazioni. I servizi RESTful infatti non espongono generiche procedure ma specifiche operazioni, quelle dell&#8217;HTTP, per interagire con lo stato del sistema. Questo è costituito dall&#8217;insieme delle sue risorse, individuate dagli URI, ed è possibile interagire con esse mediante sue rappresentazioni codificate in differenti modi (es.: JSON, XML, ecc.).

### CAP Consistency vs ACID Consistency

E&#8217; importante non confondere il significato di consistenza del Teorema CAP con quello previsto dalle caratteristiche ACID delle transazioni di un database. Il Teorema CAP parla di consistenza in termini di accesso al valore più aggiornato di un certo dato, ovvero quello utilizzato nell&#8217;ultima scrittura dello stesso. Nelle transazioni invece la consistenza fa riferimento al rispetto dei vincoli di integrità, prima e dopo l&#8217;esecuzione di una transazione. In altre parole, una transazione porta il database da uno stato consistente ad un nuovo stato consistente e non ci sono mai momenti in cui i dati del database non rispettano i vincoli di integrità.

La consistenza del Teorema CAP è quindi una caratteristica che riguarda le **operazioni di lettura** dei dati distribuiti mentre la consistenza ACID è una proprietà delle **operazioni di scrittura** (inserimento, modifica o cancellazione) dei dati. E&#8217; evidente quindi che, anche se parliamo di consistenza, **sia le integrazioni di tipo RPC che quelle RESTful non garantiscono la complessiva integrità dei dati presenti sui diversi nodi**, propria di un sistema transazionale. Infatti queste tecniche non prevedono un sistema universalmente applicabile di gestione della transazione distribuita.

## Integrazioni per la disponibilità dei dati

Rilassando il requisito della consistenza CAP, ovvero rinunciando all&#8217;accesso alla versione più aggiornata di un dato remoto, è possibile realizzare meccanismi di accesso ai dati remoti basati sullo scambio di messaggi. Il sistema su cui si verifica un evento provvede a notificarlo ai sistemi cooperanti, o mediante interazioni dirette con gli stessi, o più di frequente affidando il messaggio ad appositi sistemi che fungono da mediatori (code di messaggi, meccanismi di publish/subscribe, pattern pipes and filters, ecc.). I sistemi che ricevono questi messaggi provvedono a recepire nella propria base dati locale quanto accaduto nel sistema remoto.

Le integrazioni basate su messaggi permettono quindi di recepire localmente un dato remoto. I dati locali, se da un lato sono certamente disponibili, dall&#8217;altro non sono necessariamente i più aggiornati per via dell&#8217;inevitabile ritardo con cui si propagano i messaggi di aggiornamento tra i sistemi cooperanti.

A ben vedere però, le integrazioni basate su messaggi non rinunciano completamente alla consistenza CAP dei dati tra i sistemi. Possono infatti contare sua una particolare forma di consistenza, di tipo asintotica, a regime, che prende il nome di **eventual consistency**.

### Eventual Consistency e transazioni BASE

Il fluire dei messaggi di notifica tra i sistemi cooperanti permette infatti, anche se non in maniera istantanea, di mantenere allineate le diverse basi dati. Questo meccanismo, molto meno rigoroso di quello previsto dalle transazioni ACID, permette di realizzare il modello transazionale noto con l&#8217;acronimo BASE, che sta per **B**asically **A**vailable, **S**oft state, **E**ventually consistent.

L&#8217;eventual consistency quindi non è una garanzia di consistenza ma solo una caratteristica del sistema di integrazione, non promette una istantanea e complessiva consistenza dei dati distribuiti ma solo una tendenza delle basi dati ad allinearsi, senza però poter garantire l&#8217;istante in cui ciò accadrà.

L&#8217;eventual consistency, se da un lato risulta più debole sia della consistenza del teorema CAP che della consistenza delle transazioni ACID, dall&#8217;altro, a regime e con un certo grado di imperfezione, tende a raggiungerle entrambe. Infatti la ricezione dei messaggi dai sistemi cooperanti permette sia di replicare in locale i dati aggiornati di pertinenza dei sistemi remoti, sia di adeguare i dati di pertinenza del sistema locale al fine di soddisfare i vincoli di integrità complessivi del sistema distribuito.

## Partizionamento dei dati, come e perché

Da quanto detto sinora, è chiaro che l&#8217;integrazione perfetta non esiste e che è necessario scegliere il giusto compromesso tra consistenza e disponibilità del teorema CAP che più si addice alla particolare situazione.

Non potendo annullarli, dobbiamo quindi minimizzare gli effetti collaterali della strategia di integrazione impiegata, e per farlo è estremamente importante che la suddivisione dei dati tra i nodi del sistema distribuito avvenga in maniera oculata, cercando di rispettare la **naturale attitudine delle informazioni ad aggregarsi**.

I dati infatti si presentano spesso in strutture complesse, frutto dell&#8217;aggregazione di elementi più semplici: una fattura è composta dall&#8217;aggregazione delle informazioni del fornitore, del cliente, dell&#8217;elenco dei beni oggetto del contratto con le relative quantità. Se i dati presenti in questi [aggregati][5] vengono distribuiti su sistemi indipendenti tra loro, le problematiche relative alla loro disponibilità e consistenza potrebbero non essere sufficientemente controllabili dalle strategie di integrazione.

Una regola empirica per capire se un insieme di dati costituisce un aggregato da preservare dal partizionamento è la seguente: se un insieme di dati è oggetto di modifica all&#8217;interno di una stessa transazione allora si tratta di un aggregato che è meglio non partizionare.

Altre forme naturali di partizionamento sono quelle che sfruttano l&#8217;aggregazione dei dati per pertinenza territoriale (es.: suddivisione dei dati per regioni geografiche) e temporale (es.: suddivisione dei dati per annualità).

In molti casi l&#8217;integrazione tra sistemi viene progettata successivamente alla realizzazione dei sistemi interessati (anche i sistemi informativi tendono naturalmente ad aggregarsi!). In questi casi poco o nulla si può fare in merito al criterio di suddivisione dei dati tra i sistemi cooperanti ma fortunatamente i dati appartengono generalmente a &#8220;domini&#8221; disgiunti e pertanto non si verificano criticità.

In altre situazioni invece la distribuzione dei dati è una scelta progettuale, dettata da diverse motivazioni:

  * utilizzo di differenti tecnologie di archiviazione dei dati, dedicate a specifici compiti: RDBMS per le operazioni transazionale, database NoSQL per le query e l&#8217;analisi dei dati (pattern [CQRS][6]), ecc.;
  * scomposizione di un sistema complesso in [microservizi][7], realizzabili e manutenibili da team distinti ed indipendenti;
  * realizzazione di architetture scalabili, ad esempio in contesti Big Data;
  * realizzazione di architetture in grado di continuare a funzionare, seppur parzialmente, anche in caso di indisponibilità di alcuni sistemi.

Di volta in volta, per l&#8217;individuazione della migliore strategia di integrazione, si dovrà tener conto certamente degli aspetti tecnici ma soprattutto delle **logiche di business**. Quali sono le funzionalità che, cascasse il mondo, devono essere sempre disponibili, anche a costo di lavorare con dati non aggiornati? Quali sono invece le operazione che il sistema deve effettuare solo ed esclusivamente su dati consistenti?

 [1]: https://www.giovannitomasicchio.it/sorgente-e-runtime-le-insidie-dellastrazione/
 [2]: https://redis.io/
 [3]: https://www.elastic.co/products/elasticsearch
 [4]: http://www.sleberknight.com/blog/sleberkn/entry/polyglot_persistence
 [5]: https://martinfowler.com/bliki/DDD_Aggregate.html
 [6]: https://martinfowler.com/bliki/CQRS.html
 [7]: https://martinfowler.com/articles/microservices.html