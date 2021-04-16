---
title: Ereditarietà, solo per (sviluppatori) adulti
author: giovannitomasicchio
type: post
date: 2017-09-15T20:24:56+00:00
url: /ereditarieta-solo-sviluppatori-adulti/
thumbnail: /image/blog/2017/09/manette.jpg
categories:
  - Senza categoria
tags:
  - composizione
  - design pattern
  - ereditarietà
  - OOP

---
Visto l&#8217;interesse suscitato dal [precedente post][1] sulla relazione di ereditarietà, torno sull&#8217;argomento per mettere in risalto **ulteriori vincoli** da essa imposti.

Lo so, a volte **anche le costrizioni possono risultare piacevoli**&#8230; ed è proprio quello che accade con l&#8217;ereditarietà tra classi, certamente vincolante ma così immediata nel realizzare il riuso del codice. E allora, per allontanare questi pensieri impuri dai nostri progetti, recitiamo insieme la seguente preghiera tratta da [uno dei testi sacri][2] della programmazione ad oggetti:

<p style="text-align: center;">
  <em>Favor object composition over class inheritance</em>
</p>

Amen! Ma perché i sacerdoti della [Gang of four][3] ci invitano a **preferire la composizione rispetto all&#8217;ereditarietà tra classi**? Perché composizione ed ereditarietà sono entrambe una forma di riuso del codice ma l&#8217;ereditarietà risulta appunto più vincolante e problematica. Vediamo perché.

  1. Se la classe B estende la classe A, questa relazione è definita nel codice sorgente, pertanto **non può cambiare a runtime**. Al contrario se B usasse A, B potrebbe referenziare A mediante l&#8217;interfaccia I implementata da A. Pertanto a runtime al posto di A ci potrebbe essere un&#8217;altra classe di tipo***** I.
  2. Una modifica alla classe A estesa da B può avere **ripercussioni indesiderate** sul funzionamento di B. Infatti la classe B può accede ai dettagli implementativi di A, **violandone l&#8217;incapsulamento**. In questo caso si parla di riuso di tipo white-box per contrapporlo al riuso di tipo black-box della composizione che invece rispetta l&#8217;incapsulamento. Nel caso della composizione tra A che usa B mediante l&#8217;interfaccia I, solo una modifica ad I comporterebbe ripercussioni per A.

Dobbiamo quindi bandire l&#8217;ereditarietà tra classi dai nostri progetti? Assolutamente no, dobbiamo soltanto usarla con più consapevolezza, tenendo sempre presente che l&#8217;ereditarietà conferisce alla classe figlia le seguenti caratteristiche della classe padre:

  1. il tipo
  2. lo stato, ovvero gli attributi
  3. l&#8217;implementazione dei suoi metodi

Se non siamo interessati contemporaneamente ai suddetti 3 aspetti allora probabilmente è meglio adottare soluzioni alternative all&#8217;ereditarietà. Ad esempio se ci interessa solo il riuso dell&#8217;implementazione potremmo indirizzarci verso la composizione, oppure se ci interessa solo il tipo potremmo utilizzare l&#8217;ereditarietà delle interfacce o magari l&#8217;ereditarietà da una classe astratta, povera di implementazione.

\___\___

***** _ Il tipo di un oggetto è definito come l&#8217;insieme delle richieste a cui esso può rispondere, ovvero l&#8217;insieme delle firme dei metodi pubblici e quindi l&#8217;interfaccia._

 [1]: /ereditarieta-usarla-con-cautela/
 [2]: https://it.wikipedia.org/wiki/Design_Patterns
 [3]: https://it.wikipedia.org/wiki/Gang_of_four