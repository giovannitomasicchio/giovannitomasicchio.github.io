---
title: Il ruolo del dominio nello sviluppo di applicazioni
author: giovannitomasicchio
type: post
date: 2017-10-15T08:51:39+00:00
url: /ruolo-del-dominio-nello-sviluppo-applicazioni/
thumbnail: /wp-content/uploads/2017/10/Knowledge-Sharing.jpg
categories:
  - Senza categoria
tags:
  - Domain-driven design
  - Manifesto Agile

---
A scuola abbiamo imparato che per risolvere un problema è utile creare un **modello**, ovvero una astrazione semplificata della realtà in esame, che contempli solo gli elementi necessari alla descrizione del problema in questione. Ristretto il cerchio alle poche **entità** che costituiscono il modello individuato, sarà più facile individuare le logiche che ne governano il funzionamento e quindi risolvere il problema.

**Ma è proprio necessario che il modello attorno al quale realizziamo una applicazione debba essere costruito sul dominio del problema?**

Il dubbio mi è venuto osservando che ci sono contesti in cui questo approccio non viene rispettato. Ad esempio in matematica ci sono problemi che vengono risolti trasformando (secondo metodologie inventate da Fourier, Laplace e altri grandi matematici) il dominio reale in un dominio &#8220;sintetico&#8221; ad esso correlato nel quale è più semplice trovare una soluzione. Senza andare troppo lontano, anche nel mondo della programmazione funzionale la tendenza è quella di astrarre il problema e ridurlo ad una sequenza di operazioni sui dati, così lontane dal dominio del problema.

## Domain-driven design

<figure id="attachment_493" aria-describedby="caption-attachment-493" style="width: 140px" class="wp-caption alignright"><img loading="lazy" class=" wp-image-493" src="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/eric_evans-300x300.jpg" alt="" width="150" height="150" srcset="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/eric_evans-300x300.jpg 300w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/eric_evans-150x150.jpg 150w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/eric_evans.jpg 400w" sizes="(max-width: 150px) 100vw, 150px" /><figcaption id="caption-attachment-493" class="wp-caption-text">Eric Evans</figcaption></figure>

Così ho iniziato a fare una ricerca, e ovviamente sono partito dal lavoro di [Eric Evans][1], ovvero dal [Domain-driven design (DDD)][2], un approccio allo sviluppo del software che si può riassumere nei seguenti punti:

  * prestare massima attenzione agli **aspetti fondamentali del dominio** e delle sue logiche;
  * realizzare uno o più **modelli sulla base del dominio del problema**, assieme agli esperti di dominio, finalizzati a risolvere i problemi più complessi;
  * scrivere software a partire da questi modelli che devono quindi essere **esplicitamente visibili nel codice**;
  * utilizzare uno **linguaggio condiviso tra tutti gli stakeholder**, tecnici e non, (ubiquitous language) valido all&#8217;interno di ciascun particolare contesto (bounded context).

Non credo sia necessario entrare nei dettagli del DDD, è evidente che per Evans tutto il processo di sviluppo del software va costruito strategicamente sul dominio. L&#8217;autore descrive il suo modello nel libro [Domain-Driven Design, Tackling Complexity in the Heart of Software][3], nella cui prefazione, curata da [Martin Fowler][4], si pone l&#8217;accento sul rapporto tra i tecnici e gli esperti di dominio:

> The greatest value of a domain model is that it provides ubiquitous language that ties domain experts and technologists together.

Il dominio quindi non solo per comprendere e risolvere il problema ma come punto di incontro tra tecnici ed esperti di dominio, tra sviluppatori e committenti. Il dominio quindi nel DDD assume una valenza più ampia rispetto al ruolo giocato da un semplice modello.

## Manifesto Agile

<figure id="attachment_494" aria-describedby="caption-attachment-494" style="width: 140px" class="wp-caption alignright"><img loading="lazy" class=" wp-image-494" src="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/martin_fowler.jpg" alt="" width="150" height="150" srcset="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/martin_fowler.jpg 280w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/martin_fowler-150x150.jpg 150w" sizes="(max-width: 150px) 100vw, 150px" /><figcaption id="caption-attachment-494" class="wp-caption-text">Martin Fowler</figcaption></figure>

Fowler è uno dei 17 firmatari del [Manifesto Agile][5], pertanto sono andato a verificare se questi concetti fossero in qualche modo presenti tra i 12 principi del manifesto, ed ho trovato il seguente:

> Committenti e sviluppatori devono lavorare insieme quotidianamente per tutta la durata del progetto.

Come possono lavorare insieme quotidianamente gli sviluppatori ed il committente se parlano una lingua diversa? E&#8217; evidente che l&#8217;_ubiquitous language_ del DDD è uno strumento adatto ad **accorciare le distanze tra i tecnici ed il committente**, obiettivo evidentemente auspicato anche dal Manifesto Agile.

Sullo stesso tema, Fowler sottolinea l&#8217;importanza della **[Customer Affinity][6]**, ovvero &#8220;l&#8217;interesse e la vicinanza che gli sviluppatori dimostrano nei confronti dei problemi di business che il software risolve e delle persone che lavorano in quel business&#8221;.

<figure id="attachment_497" aria-describedby="caption-attachment-497" style="width: 140px" class="wp-caption alignleft"><img loading="lazy" class=" wp-image-497" src="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/kent.jpg" alt="" width="150" height="150" srcset="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/kent.jpg 300w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/kent-150x150.jpg 150w" sizes="(max-width: 150px) 100vw, 150px" /><figcaption id="caption-attachment-497" class="wp-caption-text">Kent Beck</figcaption></figure>

Fowler afferma che il suo apprezzamento nei confronti dell&#8217;[Extreme Programming][7] (XP) e delle altre metodologie agili deriva principalmente dall&#8217;importanza che queste attribuiscono alla customer affinity. Egli ricorda che, in occasione degli incontri che portarono alla definizione del Manifesto Agile, [Kent Beck][8], presentando l&#8217;XP, preferì evidenziare non gli aspetti più tecnici della sua metodologia ma il suo desiderio di **modificare la natura dei rapporti tra committenti e sviluppatori**.

## Performance e adattabilità al cambiamento

A questo punto della mia ricerca, il dominio sembra essere il Santo Graal dello sviluppo moderno. Un dubbio ancora mi rimane: scrivendo codice che trasuda dominio da tutte le righe e che pertanto tende ad essere prolisso, **non corriamo il rischio di mortificare le performance?** In fondo il software è costituito da istruzioni rivolte ad una macchina mentre il dominio è un concetto legato al committente e al suo business. Istruire la macchina sul da farsi utilizzando direttamente la logica del dominio può avere delle ripercussioni di natura tecnica?

<figure id="attachment_495" aria-describedby="caption-attachment-495" style="width: 140px" class="wp-caption alignright"><img loading="lazy" class=" wp-image-495" src="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/carlo_pescio.jpg" alt="" width="150" height="149" srcset="https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/carlo_pescio.jpg 247w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/10/carlo_pescio-150x150.jpg 150w" sizes="(max-width: 150px) 100vw, 150px" /><figcaption id="caption-attachment-495" class="wp-caption-text">Carlo Pescio</figcaption></figure>

La risposta l&#8217;ho trovata in un recente lavoro di [Carlo Pescio][9], dal titolo [Programming as if the Domain (and Performance) Mattered][10]. Nell&#8217;articolo viene dimostrato con rigore scientifico come le **migliori performance** non si ottengono mediante lo sfruttamento delle caratteristiche di un linguaggio di programmazione, bensì attraverso una **migliore conoscenza del dominio del problema**.

La dimostrazione viene condotta considerando due differenti implementazioni, una di tipo funzionale, in Haskell, orientata all&#8217;implementazione di un algoritmo, e l&#8217;altra in C#, secondo un modello ad oggetti basato sul dominio. Pescio dimostra come, contrariamente a quanto si possa ipotizzare, l&#8217;implementazione in C# risulta più performante. Inoltre la soluzione basata sul dominio si dimostra anche molto più **adattabile ai cambiamenti** del problema iniziale rispetto all&#8217;approccio algoritmico. Il dominio quindi come uno **strumento _agile_**.

## Conclusioni

La centralità del dominio, così come emerge da tutte le fonti che ho consultato in questa mia ricerca, mi porta ad inevitabili riflessioni sugli effetti di pratiche che invece sembrano ignorarne la valenza. Mi riferisco ad esempio ad un sostenuto turnover all&#8217;interno del team di sviluppo e ai passaggi di interi progetti da un team uscente verso un nuovo team. Sono evenienze non così rare, motivate spesso da interessi economici, che temo possano avere conseguenze nel lungo periodo tutt&#8217;altro che economiche.

 [1]: https://twitter.com/ericevans0
 [2]: http://www.mokabyte.it/2008/11/domaindriven-1/
 [3]: https://www.amazon.it/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215
 [4]: https://martinfowler.com/
 [5]: http://agilemanifesto.org/iso/it/manifesto.html
 [6]: https://martinfowler.com/bliki/CustomerAffinity.html
 [7]: https://it.wikipedia.org/wiki/Extreme_programming
 [8]: https://it.wikipedia.org/wiki/Kent_Beck
 [9]: http://www.eptacom.net
 [10]: https://drive.google.com/file/d/0B59Tysg-nEQZSXRqVjJmQjZyVXc/view