---
title: Cos’è l’architettura di un software?
author: giovannitomasicchio
type: post
date: 2017-10-22T17:12:13+00:00
url: /cose-larchitettura-un-software/
thumbnail: /wp-content/uploads/2017/10/architettura.jpeg
categories:
  - Senza categoria
tags:
  - architettura
  - design

---
Prima di continuare a leggere questo post, provate a dare una risposta. Secondo vuoi cos&#8217;è l&#8217;architettura di un software?

Non si tratta di un discorso meramente teorico, finalizzato a trovare una definizione sterile. Al contrario, abbiamo bisogno di trovare una risposta perché da questa dipende la nostra capacità di **valutare la bontà di una architettura**. In altre parole abbiamo bisogno di sapere cos&#8217;è l&#8217;architettura di un software per avere un criterio che ci guidi nell&#8217;effettuare corrette **scelte architetturali**.

&#8220;Ma io non sono un architetto software, sono solo uno semplice sviluppatore!&#8221;. Mi sembra già di sentire qualcuno che, con questa motivazione, sta cercando di defilarsi dalla discussione. Questo &#8220;qualcuno&#8221;, magari inconsciamente, sta pensando che l&#8217;architettura del software sia qualcosa che riguarda i massimi sistemi, prerogativa di colleghi con i super poteri, che hanno smesso di sviluppare per dedicarsi allo sciamanesimo informatico. Riunitisi in stanze segrete, gli architetti cadono in trance ed elaborano progetti per la costruzione di [cattedrali][1] informatiche.

Se la pensate così, siete fuori strada. Vi anticipo che a mio avviso tutti gli sviluppatori, senza farci tanto caso, fanno continuamente scelte architetturali. Ma andiamo con ordine.

Se cercate sul web troverete diverse definizioni di architettura software, quasi tutte incentrate sulla scomposizione del sistema informatico in componenti e sulle reciproche interazioni. Piuttosto deludente, non trovate? Espresso così, il concetto di architettura risulta piuttosto **statico**, quasi a voler dire che, una volta definita, l&#8217;architettura non può cambiare. Inoltre non ci dà alcun indizio su come progettare una buona architettura. Spesso si utilizzano termini come &#8220;modulare&#8221;, &#8220;estensibile&#8221;, &#8220;scalabile&#8221;, per indicare delle caratteristiche di una buona architettura, che però non ci danno alcuna informazione sul vero metro di giudizio di un progetto: il **costo**.

A mio giudizio l&#8217;architettura di un software è semplicemente la somma delle scelte progettuali effettuate sugli aspetti non funzionali, ovvero non visibili all&#8217;utente finale. Mi riferisco in particolare alle scelte &#8220;importanti&#8221;, ovvero vincolanti per numerosi elementi del progetto e pertanto non semplici da cambiare, che indicano una precisa strada scelta tra le alternative disponibili. In questo senso l&#8217;architettura riguarda anche lo sviluppatore, anche se in contesti più circoscritti, quando si trova a dover scegliere tra implementazioni alternative di una particolare funzionalità.

A ben vedere non tutti gli elementi dell&#8217;architettura sono frutto di una valutazione e di una libera scelta. In alcuni casi infatti esiste una sola soluzione tecnologia che permetta di realizzare un particolare requisito. In questi casi non c&#8217;è alcuna scelta da fare. Ci sono inoltre elementi architetturali esplicitamente richiesti dal committente o, più raramente, anche dal PM. In questi casi è bene far mettere nero su bianco queste richieste, in modo che rientrino tra i requisiti software e rimangano distinti dalle scelte progettuali, di responsabilità del progettista.

## Design Stamina Hypothesis

Ma quale criterio utilizzare per fare scelte architetturali? Come anticipato, a mio avviso a guidare le nostre scelte deve essere il costo. Ma in che termini? Ci viene in soccorso, come al solito, il buon [Martin Fowler][2] che ha formulato quella che lui chiama la  [Design Stamina Hypothesis][3]. Secondo Fowler, nei contesti in cui la progettazione è assente (curva blu del grafico), si osserva una iniziale velocità nella realizzazione delle prime funzionalità che però, con l&#8217;aumentare del codice scritto, rallenta considerevolmente poiché diventa estremamente oneroso realizzare nuove funzionalità su una base di codice caotica. Al contrario, nei progetti che godono di una buona progettazione (curva rossa del grafico),  si osserva una costante accelerazione della capacità di sviluppare sempre nuove funzionalità.

{{< figure src="/wp-content/uploads/2017/10/designStaminaGraph.png" title="Martin Fowler - Design Stamina Hypothesis" >}}

Questa costatazione empirica, facilmente riscontrabile nei progetti reali, può essere utilizzata come criterio di valutazione delle scelte architetturali. L&#8217;ipotesi di Fowler infatti ci suggerisce che se dobbiamo effettuare una scelta architetturale, tra le diverse alternative possibili, dobbiamo scegliere quella che maggiormente velocizza lo sviluppo delle nuove funzionalità, rendendole quindi più economiche. Certo, scegliere ad esempio tra un DB commerciale come Oracle ed un DB opensource come PostgreSQL comporta di per se delle ricadute economiche sul progetto, ma **la velocità con cui un team di sviluppo realizza nuove funzionalità ha una valenza superiore, poiché al contempo economica e strategica**. Un team di sviluppo messo nelle condizioni di realizzare velocemente nuove funzionalità permette, ad esempio, di anticipare un concorrente, completare un progetto nei tempi imposti dal committente, preparare una demo in tempo utile, più in generale ridurre il [feature lead time][4].

Assodato quindi che l&#8217;ipotesi di Fowler ha conseguenze tutt&#8217;altro che banali, è necessario approfondire altri due concetti. Infatti abbiamo detto che la bontà di una scelta architetturale si valuta in base a quanto questa agevolerà la **realizzazione** delle **future** funzionalità. Ma quali attività ricadono nel concetto di realizzazione? Inoltre, quanto è remoto il futuro da prevedere?

## Costo per realizzare una nuova funzionalità

Per valutare l&#8217;impatto che avrà una scelta architetturale sulla realizzazione delle funzionalità del sistema, dobbiamo necessariamente individuare quali sono le attività che compongono l&#8217;intero processo di **progettazione, sviluppo e rilascio**. Spesso si tende a focalizzare tutta l&#8217;attenzione sulla fase di sviluppo software. Viene spontaneo infatti chiedersi come una scelta architetturale possa influenzare il codice sorgente. Niente di sbagliato, fintanto che la fase di progettazione e rilascio contribuiscono solo marginalmente al costo complessivo della realizzazione della funzionalità.

Ci sono però dei contesti, caratterizzati ad esempio da frequenti rilasci, da grandi team di sviluppo, dove la progettazione ed il rilascio hanno un peso preponderante sull&#8217;intero _effort_ di realizzazione. In questi casi la valutazione degli impatti di una scelta architetturale non può limitarsi ad analizzare le ripercussioni sul solo codice sorgente.

## Orizzonte temporale

Se è vero che la bontà di una scelta architetturale si valuta in base a quanto agevolerà la realizzazione delle future funzionalità, quanto lontano dobbiamo spingere la nostra previsione? Nell&#8217;ingegneria tradizionale, nonché nel modello di sviluppo del software [a cascata][5], la progettazione si deve concludere prima dell&#8217;avvio della attività di realizzazione: _plan your work, work your plan_. Questo approccio richiede quindi che tutte le scelte architetturali debbano essere effettuate prima dello sviluppo software. Di conseguenza l&#8217;orizzonte temporale da tener presente nella fase di progettazione è massimo, poiché deve contemplare le necessità dell&#8217;intera fase di sviluppo. Gli architetti software tendono persino ad andare oltre, e si sforzano di fare ipotesi sulle potenziali evoluzioni del sistema, tanto più improbabili quanto più collocabili in un futuro remoto. In pratica gli architetti si trasformano in profeti.

Questo **approccio predittivo** è stato fortemente messo in discussione negli anni, poiché basato implicitamente sulla stabilità dei requisiti software che si è dimostrata utopica. Le metodologie agili, come l&#8217;Extreme Programming, hanno invece puntato su un **approccio adattativo**, in grado di reagire alla variazione dei requisiti. Ne consegue che, nel contesto agile, l&#8217;orizzonte temporale da analizzare per valutare una scelta architetturale è più ristretto e le scelte architetturali non devono essere tutte prese prima di avviare lo sviluppo. Anzi, le stesse scelte architetturali possono essere oggetto di [refactoring][6] durante lo sviluppo software.

Come regola di buon senso, ritengo che lo sforzo predittivo connesso ad una scelta architetturale debba essere proporzionato al costo di un successivo ripensamento. Tanto più sarà oneroso modificare una decisione progettuale, tanto più è necessario prevedere i suoi effetti a lungo termine.

## Conclusioni

In questo post ho voluto condividere la mia visione dell&#8217;architettura di un software, certamente ispirata da grandi maestri. Secondo me l&#8217;architettura è un concetto tutt&#8217;altro che statico, composto da una miriade di scelte effettuate, lungo tutta la durata di un progetto, su aspetti invisibili all&#8217;utente finale ma che determinano il funzionamento del sistema. Obiettivo primario di queste scelte dovrebbe essere quello di velocizzare la realizzazione delle funzionalità di valore per l&#8217;utente. È necessario quindi analizzare le singole fasi di realizzazione di una funzionalità, per scoprire come velocizzarle, e prevedere, secondo un orizzonte temporale più o meno lungo, quali e quante funzionalità saranno impattate dalla particolare scelta architetturale.

Nel prossimo post proverò a fare qualche esempio di scelta architetturale pensata proprio per velocizzare la realizzazione del software.

 [1]: https://it.wikipedia.org/wiki/La_cattedrale_e_il_bazaar
 [2]: https://martinfowler.com/
 [3]: https://martinfowler.com/bliki/DesignStaminaHypothesis.html
 [4]: https://www.agilealliance.org/glossary/lead-time/
 [5]: https://it.wikipedia.org/wiki/Modello_a_cascata
 [6]: https://it.wikipedia.org/wiki/Refactoring