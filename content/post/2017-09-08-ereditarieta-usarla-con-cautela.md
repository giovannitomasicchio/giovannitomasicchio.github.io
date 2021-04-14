---
title: Ereditarietà, usarla con cautela
author: giovannitomasicchio
type: post
date: 2017-09-08T18:07:24+00:00
url: /ereditarieta-usarla-con-cautela/
thumbnail: /wp-content/uploads/2017/08/padre_figlio.png
categories:
  - Senza categoria
tags:
  - ereditarietà
  - OOP

---
In questo post voglio condividere alcune osservazioni sulle caratteristiche della relazione di ereditarietà nella programmazione object-oriented. Provo a farlo con un esempio.

Dobbiamo individuare il modello delle classi per una applicazione che gestisca l&#8217;archivio dei film di una videoteca. Per ciascun film dobbiamo memorizzare il regista e gli attori. Viene immediato individuare almeno tre classi: Film, Regista e Attore, collegate nel seguente modo:

<img loading="lazy" class="size-full wp-image-191 aligncenter" src="https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_1.png" alt="" width="336" height="167" srcset="https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_1.png 336w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_1-300x149.png 300w" sizes="(max-width: 336px) 100vw, 336px" /> 

Poiché dobbiamo realizzare delle funzionalità che operano sia sugli attori che sui registi, come ad esempio il motore di ricerca degli artisti, abbiamo bisogno del polimorfismo e quindi introduciamo una classe Artista. Infatti Attore IS-A Artista e anche Regista IS-A Artista, quindi possiamo modellare queste due relazioni con l&#8217;ereditarietà.

<img loading="lazy" class="size-full wp-image-192 aligncenter" src="https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_2.png" alt="" width="351" height="167" srcset="https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_2.png 351w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_2-300x143.png 300w" sizes="(max-width: 351px) 100vw, 351px" /> 

Tutto corretto vero? Si, fino a quando ci tocca censire un film di Woody Allen in cui il regista è anche un attore del film. Quindi Woody Allen è certamente un Artista, ma è contemporaneamente sia Regista che Attore. Siamo costretti quindi ad avere due oggetti, uno della classe Attore e uno della classe Regista, che in realtà fanno riferimento allo stesso soggetto. Questa duplicazione prima o poi produrrà delle anomalie. Infatti, se non sviluppassimo specifici workaround, Woody Allen comparirebbe due volte tra i risultati del motore di ricerca degli artisti.

## Entità vs ruoli

Che errore abbiamo commesso formulando il precedente modello? Abbiamo introdotto la relazione di ereditarietà per via della veridicità delle seguenti affermazioni:

  * Attore è un Artista;
  * Regista è un Artista;

ma abbiamo trascurato le seguenti implicazioni che l&#8217;ereditarietà comporta:

  * un Artista può essere o un Attore o un Regista;
  * un Artista di tipo Regista non può essere anche di tipo Attore, e viceversa;

ma queste conseguenze dell&#8217;ereditarietà non sono compatibili con le dinamiche dell&#8217;applicazione che stiamo progettando, come dimostrato dall&#8217;istanza &#8220;Woody Allen&#8221;.

Probabilmente a trarci in inganno è il modo utilizzato, nella lingua italiana, per indicare il ruolo ricoperto da un soggetto. Infatti diciamo che &#8220;il sig. Rossi **è un** impiegato&#8221; ma intendiamo dire &#8220;il sig. Rossi **ha un** impiego presso&#8230;&#8221;. E quando il sig. Rossi andrà in pensione viene spontaneo dire che &#8220;il sig. Rossi **è un** pensionato&#8221;, intendendo in realtà dire che &#8220;il sig. Rossi **ha una** pensione&#8221;. Abbiamo quindi la tendenza ad utilizzare il verbo &#8220;è un&#8221; con il significato di &#8220;ha un&#8221;, e di conseguenza commettiamo l&#8217;errore di utilizzare la relazione IS-A (l&#8217;ereditarietà) al posto della relazione HAS-A (associazione).

Il modello che abbiamo inizialmente pensato va evidentemente rivisto, ad esempio nel seguente modo: introduciamo una classe Ruolo, padre di Attore e Regista. Associamo quindi la classe Ruolo alla classe Artista. In questo modo un artista non è costretto ad **essere** alternativamente o un Attore o un Regista, bensì può **avere** il ruolo di attore e/o di regista.

<img loading="lazy" class="size-full wp-image-193 aligncenter" src="https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_3.png" alt="" width="579" height="167" srcset="https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_3.png 579w, https://www.giovannitomasicchio.it/wp-content/uploads/2017/08/modello_3-300x87.png 300w" sizes="(max-width: 579px) 100vw, 579px" /> 

In questo modo per Woody Allen avremo una sola istanza di Artista, associata a due istanze di Ruolo, una di tipo Attore e una di tipo Regista.

## Conclusioni

Come abbiamo visto nell&#8217;esempio, la relazione di ereditarietà impone forti vincoli al modello, ben più di quelli che il verbo &#8220;IS-A&#8221; ci suggerisce. Inoltre il verbo &#8220;IS-A&#8221; della relazione di ereditarietà non va confuso con l&#8217;affermazione &#8220;è un&#8221; della lingua italiana, spesso utilizzata per indicare una caratteristica, un ruolo.

Considerati i vincoli che impone, risulta quindi opportuno introdurre una relazione di ereditarietà in un modello ad oggetti solo quando sappiamo come sfruttarla, ad esempio tramite il polimorfismo, e non semplicemente perché risulta vera nel mondo reale, o peggio nella lingua italiana.