---
title: High-Performance Java Persistence – recensione
author: giovannitomasicchio
type: post
date: 2017-11-18T11:19:45+00:00
url: /high-performance-java-persistence-recensione/
thumbnail: /wp-content/uploads/2017/11/High-Performance-Java-Persistence.jpg
categories:
  - Senza categoria
tags:
  - Hibernate
  - JDBC
  - JOOQ
  - JPA
  - recensione

---
Ho appena finito di leggere, anzi, di divorare, il libro &#8220;[High-Performance Java Persistence][1]&#8221; di [Vlad Mihalcea][2]. Molte recensioni positive mi hanno spinto ad acquistarlo, nella versione cartacea attraverso [Amazon][3], ma non mi aspettavo un testo al contempo così pragmatico, accessibile, dettagliato, scientifico ed in certi punti anche illuminante.

L&#8217;autore, che tra l&#8217;altro è uno degli **sviluppatori di Hibernate**, in questo testo analizza i molteplici fattori che influenzano le performance del layer di persistenza dei dati in una applicazione Java. Per farlo suddivide la trattazione in tre sezioni. La prima è dedicata allo standard **JDBC** e contiene approfondimenti, tra l&#8217;altro, sui pool di connessione, sull&#8217;esecuzione di statement SQL in modalità batch e sulla gestione delle transazioni.

La seconda parte è dedicata allo standard **JPA** e ad Hibernate e segue un approccio piuttosto originale nella trattazione dei diversi aspetti di queste tecnologie. Infatti, l&#8217;autore non si limita a descrivere il significato delle diverse annotazioni e funzionalità. Al contrario, tutti gli aspetti e le alternative possibili vendono valutate in funzione degli statement SQL che il provider JPA invierà al DB.

La terza parte, la più breve, è dedicata a [**JOOQ**][4], uno strumento per la generazione di statement SQL a partire da una semplice sintassi Java. Questo testo mi ha permesso di apprezzare la valenza ed inquadrare il contesto di applicazione di questo ottimo layer che confesso di aver ingiustamente trascurato.

Lungo tutta la trattazione, l&#8217;autore aiuta il lettore a non perde mai di vista due aspetti: la valenza, in termini di performance misurate in contesti reali, dell&#8217;argomento di volta in volta tratto, nonché il diverso comportamento dei diversi RDBMS, in particolare di Oracle, Microsoft SQL Server, PostgreSQL e MySQL.

Un&#8217;altra caratteristica che ho molto apprezzato del testo è la disinvoltura con cui l&#8217;autore spiega il perché di determinati comportamenti dei sistemi analizzati, scendendo sino ai dettagli più tecnici e nascosti, senza però rendere criptica la trattazione e ostica la lettura. Il teso infatti, nonostante le sue 468 pagine, è scritto in un inglese semplice e scorrevole, e si presta ad una agevola lettura, sempre che il lettore abbia già un minimo di basi sugli argomenti trattati.

Non si tratta quindi di un testo per principianti ma piuttosto di un libro per chi già lavora con Java ed i database SQL e vuole maturare una maggiore consapevolezza sugli argomenti trattati.

&nbsp;

 [1]: https://vladmihalcea.com/books/high-performance-java-persistence/
 [2]: https://vladmihalcea.com/about/
 [3]: https://www.amazon.it/High-Performance-Java-Persistence-Vlad-Mihalcea/dp/973022823X/
 [4]: https://www.jooq.org/