---
title: Sull’organizzazione dei team di sviluppo
author: giovannitomasicchio
type: post
date: 2017-10-29T22:10:39+00:00
url: /organizzazione-dei-team-sviluppo/
thumbnail: /wp-content/uploads/2017/10/B0vO1pHIAAEhpMy.jpg
categories:
  - Senza categoria
tags:
  - Domain-driven design
  - Manifesto Agile

---
L&#8217;argomento in questione è di grande attualità in ufficio ed è per questo che mi sento in dovere di condividere in questo post la mia visione sull&#8217;organizzazione ideale dei team di sviluppo, frutto di un decennio di osservazioni delle dinamiche lavorative.

Il mio ragionamento parte dalla costatazione che la suddivisione dell&#8217;intero organico in diversi gruppi è necessaria ed inevitabile, e non per ragioni organizzative ma antropologiche. **I gruppi sociali si formano spontaneamente**, pertanto è meglio crearli secondo logiche di produttività. Inoltre, seguendo il celebre suggerimento &#8220;[two-pizza team][1]&#8221; di  [Jeff Bezos][2], **la dimensione dei team non deve superare le 6-12 unità**, altrimenti ne risente la comunicazione interna al gruppo e c&#8217;è il rischio che si creino spontaneamente dei sottogruppi che non comunicano tra loro.

Assodato che è necessario suddividere la software factory in team di piccole dimensioni, con quale criterio andrebbe fatto? Me ne vengono in mente diversi:

  1. per contesto tecnologico: sistemisti, database administrator, sviluppatori, analisti, ecc.;
  2. per tipologia di servizio erogato: team di progettazione e sviluppo di nuovi progetti, team di conduzione e manutenzione, team di industrializzazione, team di ricerca e sviluppo;
  3. per progetto: team per il progetto X, team per il progetto Y, ecc.;
  4. per prodotto: team per il prodotto A, team per il prodotto B, ecc;
  5. per settore di business: team banche, team finanza, team pubblica amministrazione, team energia, team media, ecc.

Vediamo cosa suggerisce il [Manifesto Agile][3] a tal proposito:

> Committenti e sviluppatori devono lavorare insieme quotidianamente per tutta la durata del progetto.

Salta subito all&#8217;occhio che **il rapporto quotidiano da costruire è tra il committente e gli sviluppatori**. Non si fa riferimento agli analisti o al project manager. Questa è una affermazione di enorme portata, che spazza via preconcetti ancestrali secondo cui lo sviluppatore deve rimanere relegato nella sua postazione di lavoro e deve ricevere indicazioni sul da farsi da parte di altri colleghi. Il modello 1 quindi non è compatibile con il Manifesto Agile, poiché determina la creazione di team (sviluppatori, sistemisti, DBA) che non avranno mai contatti con il committente.

Ma a cosa serve questo rapporto stretto tra cliente e sviluppatori? Che ripercussioni ha sull&#8217;organizzazione dei team?

### Documentazione e specifiche di sviluppo

La progettazione tradizionale, secondo il modello a cascata, prevede che un gruppo di analisti produca i documenti di specifiche, con un livello di dettaglio tale da permettere agli sviluppatori di realizzare il software in autonomia. Questo approccio parte dal presupposto, dimostratosi negli ultimi decenni piuttosto debole, che i requisiti software siano stabili nel tempo. Sappiamo bene quanto è oneroso realizzare questi documenti e, soprattutto, quanto costa mantenerli aggiornati. Presto o tardi i requisiti software cambiano e di conseguenza il software viene modificato, ma costa troppo aggiornare la documentazione che quindi diventa obsoleta e, in ultima istanza, inutile.

### Reattività del team

Un team di sviluppo a stretto contatto con il cliente riduce quindi la necessità di realizzare documentazione puntuale, intercetta più velocemente le variazioni dei requisiti e modifica conseguentemente il software. Riducendo i passaggi tra project manager, analisti e sviluppatori, si riducono le incomprensioni tipiche del telefono senza fili: &#8220;ma mi avevi detto di&#8230;&#8221;, &#8220;no, hai capito male, ti avevo detto di&#8230;&#8221;, ecc. ecc.

Questo non significa mandare in pensione PM e analisti, al contrario significa **restituire loro il tempo per dedicarsi a temi di portata più ampia e strategica**, spesso trascurati per gestire l&#8217;ordinaria evoluzione del software.

### Progettazione pragmatica

Gli sviluppatori conoscono la tecnologia, sanno cosa è possibile realizzare e cosa no, percepiscono istintivamente il costo di una funzionalità. Molti sviluppatori seguono le evoluzioni della tecnologia e le nuove soluzioni che è possibile offrire al cliente. Il loro contributo è certamente utile nelle fasi di ideazione ed evoluzione del software. Nonostante ciò, raramente sono coinvolti nel processo di analisi e definizione dei requisiti.

## Centralità del dominio

Quanto sinora detto però, per funzionare davvero, necessita di un ulteriore tassello: **lo sviluppatore deve conoscere bene il dominio del problema su cui opera**. E&#8217; la conoscenza del dominio che permette di fare a meno di documentazione puntuale, che consente di trasformare velocemente la variazione dei requisiti in modifiche al software, che permette di proporre nuove soluzioni tecnologiche in linea con le esigenze di business del cliente.

**La conoscenza del dominio** non si esaurisce con la conoscenza teorica del problema. Al contrario **richiede specifiche azioni**: osservazione del cliente che utilizza il software, utilizzo in prima persona del software che si sta realizzando e, se disponibile, utilizzo del precedente software che si va a sostituire.

Per queste ragioni, a mio avviso, anche il secondo modello di organizzazione dei team, quello che prevede la costituzione di gruppi distinti per la ricerca e sviluppo, per la realizzazione e per la conduzione del software, non è ottimale.

Anche il modello 3, che prevede di costituire team diversi per progetti diversi, non è ottimale se due o più progetti riguardano domini sovrapponibili. Stesse considerazioni si possono fare per il modello 4, che prevede team diversi per prodotti diversi. Se diversi prodotti, ad esempio uno legacy, uno corrente ed uno futuro, fanno riferimento allo stesso dominio, è bene che siano affidati allo stesso team.

Il modello 5, orientato ai settori di business, corre invece il rischio di abbracciare un ventaglio troppo ampio di scenari che, a ben vedere, possono essere suddivisi in contesti più circoscritti e, almeno in parte, indipendenti l&#8217;uno dall&#8217;altro. Un settore di business quindi richiederebbe un team di dimensioni troppo grandi.

## Il (mio) team ideale

Il mio team ideale? Ritagliato su uno o più [Bounded Contexts][4] del Domain-driven design, ovvero su uno o più contesti applicativi ben definiti, circoscritti. Il team deve seguire tutti i prodotti ed i progetti in cui compare il suo contesto applicativo.

Inoltre il team deve farsi carico di tutto il ciclo di vita del software legato al particolare dominio. E&#8217; responsabile quindi sia della progettazione dei nuovi applicativi, sia della manutenzione di quelli legacy. In questo modo il team, in fase di progettazione, è spronato a realizzare software che sarà facilmente manutenibile, visto che la manutenzione sarà a suo carico. Inoltre il team non rischia di &#8220;annoiarsi&#8221; nella manutenzione dei prodotti legacy poiché al contempo è chiamato a progettare le sue evoluzioni, utilizzando la conoscenza maturata sulle precedenti soluzioni.

Nel team devono essere presenti tutte le competenze necessarie e sufficienti alla progettazione, realizzazione e manutenzione del software: analisi, progettazione, sviluppo, amministrazione dei DB, ecc. In questo modo il team è autonomo e può procedere speditamente al raggiungimento dei propri obiettivi, non ostacolato dalla disponibilità di altre strutture aziendali. Non è un caso che questi stessi principi siano alla base delle architetture a [micro servizi][5].

Se la dimensione del team va oltre la dozzina di unità, ad esempio per la numerosità dei progetti o prodotti che deve seguire, è necessario verificare se il dominio su cui opera non possa essere ulteriormente scomposto in sotto-domini e quindi il team in diversi team più specializzati. Qualora non ci sia questa possibilità, si potrebbe pensare di definire all&#8217;interno del team dei sottogruppi, prestando attenzione a non ricadere nei modelli organizzativi precedentemente esaminati. Questi sottogruppi infatti devono avere vita breve, poiché finalizzati al raggiungimento di uno scopo ben preciso. Per dirlo con una similitudine informatica, il rapporto tra sottogruppi e team di appartenenza deve ricordare il rapporto tra i thread ed il processo che li ha generati.

Il team, essendo business-oriented, deve essere necessariamente longevo, con un basso turnover dei suoi componenti. In questo modo si evita di disperdere il know-how acquisito nel tempo e di effettuare inutili passaggi di consegne. Questo è un altro motivo per cui un team deve seguire tutto il ciclo di vita di un software, comprese le fasi meno eccitanti come la manutenzione.

Ritengo estremamente errato pensare di costituire un team ad hoc per la realizzazione di ciascun progetto, terminato il quale il team viene sciolto ed i suoi membri &#8220;liberati&#8221; per essere allocati altrove. Questo comportamento indurrebbe un atteggiamento di scarsa responsabilità nei confronti del proprio lavoro e un conseguente abbassamento della qualità di quanto realizzato.

### Responsabilità end-to-end

Un team specializzato su un particolare dominio, in cui tutti i membri hanno un contatto diretto e continuo con il cliente, agevola la maturazione, in ciascun componente del team, di un senso di responsabilità diretta con il cliente.

Infatti la creazione di gerarchie tra PM, responsabile di prodotto, analista, sviluppatore, assistente applicativo, sistemista, database administrator, ecc., genera inconsciamente un processo mentale di delega delle responsabilità al livello immediatamente superiore della gerarchia. Il lavoro di ciascun membro del team sembra destinato non al cliente, bensì ad un altro componente del team. Ne consegue che, in fondo, non è necessario che il lavoro fatto sia perfetto o completo, tanto verrà perfezionato o completato da qualche altro collega.

Dinamica simile si verifica quando il team non ha una adeguata conoscenza del dominio in cui opera. E&#8217; inevitabile cadere nella mortificazione del pensiero critico, nel tranello mentale del &#8220;dimmi quel che devo fare e io lo faccio&#8221;.

Conoscere il dominio su cui si sta operando, avere un contatto diretto con il cliente, porta lo sviluppatore a comprendere le conseguenze del suo operato. Ne consegue che lo sviluppatore può e deve opporsi alla realizzazione di qualcosa che, grazie alla sensibilità maturata, sa che non sarà gradita al cliente o all&#8217;utente finale. In questi senso mi sento di dire che **lo sviluppatore deve difendere il cliente**. Su questo tema consiglio la visione dell&#8217;intervento di Martin Fowler alla conferenza GOTO 2014 intitolato [Not Just Code Monkeys][6].

### Team leader sviluppatore

Se da un lato lo sviluppatore deve conoscere il dominio e deve avere contatti diretti con il cliente nei confronti del quale si deve sentire responsabile di quanto realizzato, dall&#8217;altro lato il team leader deve saper sviluppare software. Anche se non dedica tutta la giornata a scrivere codice, il team leader non deve avere alcuna difficoltà a farlo. Stessa cosa vale per gli analisti e gli architetti. Tutti i membri di un team di sviluppo devono saper scrivere codice, altrimenti si ricade nel modello di sviluppo a cascata in cui la netta suddivisione delle fasi di sviluppo si traduce in una netta suddivisione delle competenze, con tutte le ripercussioni negative descritte in precedenza.

## Servizi trasversali

Accanto ai team di sviluppo incentrati su specifici domini, in contesti aziendali di grandi dimensioni è bene che vengano definiti dei gruppi trasversali ai diversi team, anche questi costituiti da poche unità, con l&#8217;obiettivo di offrire servizi di consulenza tecnica ai team di sviluppo su specifiche problematiche: usabilità, interfaccia utente, tecnologie front-end, integrazioni, cloud, CI/CD, DB relazionali e NoSQL, ecc.

Questi gruppi trasversali devono essere composti da tecnici già impegnati nei team di dominio, pertanto non richiedono l&#8217;aumento dell&#8217;organico complessivo. Hanno inoltre la funzione di restituire alle questioni tecniche adeguata valenza strategica, messa apparentemente in discussione dalla centralità del dominio nei team di sviluppo. Questi gruppi trasversali devono quindi farsi carico di offrire le migliori soluzioni tecnologiche ai team di sviluppo, diffondere una cultura di innovazione e guidare l&#8217;avanzamento tecnologico dell&#8217;intera software factory.

Inoltre, la creazione di gruppi tecnici trasversali consente di avere dei team di sviluppo più piccoli, poiché non necessitano di avere in organico tutte le figure professionali in grado di coprire i numerosi aspetti tecnici, ma solo quelle sufficienti a garantire loro adeguata indipendenza dalle altre strutture aziendali. Inoltre i team di sviluppo non sono costretti a reinventare ogni volta la ruota, poiché mediante i gruppi trasversali, hanno accesso alle soluzioni individuate in precedenza in altri contesti.

In definitiva, i servizi offerti dai gruppi trasversali devono permettere ai team di sviluppo di concentrarsi maggiormente su ciò che deve conferire loro identità: il dominio.

 [1]: http://blog.idonethis.com/two-pizza-team/
 [2]: https://it.wikipedia.org/wiki/Jeff_Bezos
 [3]: http://agilemanifesto.org/iso/it/principles.html
 [4]: https://martinfowler.com/bliki/BoundedContext.html
 [5]: https://martinfowler.com/articles/microservices.html
 [6]: https://www.youtube.com/watch?v=4E3xfR6IBII