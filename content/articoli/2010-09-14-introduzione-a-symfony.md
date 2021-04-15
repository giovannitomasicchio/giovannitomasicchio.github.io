---
title: "Introduzione a symfony"
author: "Massimiliano Arione"
type: article
date: 2010-09-14T12:48:19+0000
description: "Introduzione al framewrok Symfony "
url: /articoli/introduzione-a-symfony/
categories:
  - Articoli
tags:
  - "database"
  - "sicurezza"
  - "Symfony"
  - "email"

  
---
 La facilità di approccio a PHP è, da sempre, croce e delizia di questo linguaggio. Essa infatti costituisce un grosso punto di forza, capace di attrarre numerosi potenziali sviluppatori, ma allo stesso tempo fa sì che molti di questi sviluppatori restino bloccati nelle paludi del cosiddetto *spaghetti code*, il codice procedurale scritto un po' come viene.

 Pochi, o comunque una minoranza, sono ancora quelli che si avventurano nel mondo della programmazione a oggetti, su cui ormai PHP sta spingendo da diverse versioni, ancora percepito come qualcosa di misterioso e complicato. Con [symfony](http://www.symfony-project.org/) questa avventura si può rivelare più facile del previsto e può regalare notevoli soddisfazioni, facendo riscoprire il piacere dello sviluppo, che alla fine dovrebbe essere il motore di ogni progetto.

 Symfony si presenta appunto come un framework sviluppato interamente a oggetti, che sfrutta le pratiche migliori della programmazione, quei **pattern** che dovrebbero essere alla base di ogni attività di sviluppo, in ogni linguaggio. Quante volte, nella nostra carriera di sviluppatori alle prime armi, abbiamo cercato di implementare un form per il login degli utenti? Una sessione? Un elenco di righe estratte da un database? Tutte attività che ovviamente erano già state implementate da moltissimi altri programmatori, centinaia di altre volte, in una specie di collettiva fatica di Sisifo. Uno dei principi fondamentali di symfony è "non reinventare la ruota ogni volta". Questo non solo si può riferire al framework stesso, ma anche alla pratica quotidiana nel suo utilizzo, quindi al codice che noi stessi produrremo.

- - - - - -

 Il primo vantaggio di symfony consiste dunque nel trovare già implementate una serie di **soluzioni di uso comune**: form, autenticazioni, accesso al DB, escape del testo, traduzioni, invio di email, solo per citare alcuni degli aspetti più comuni. Sviluppare con symfony assicura quindi non solo la rapidità nel ritrovare già pronte le soluzioni per i problemi più comuni, ma anche la sicurezza nel sapere che tali soluzioni sono già testate e funzionanti.

 Il secondo e forse più importante vantaggio è che lo sforzo iniziale per comprendere symfony consentirà di modificare radicalmente quell'approccio procedurale a cui si accennava inizialmente, consentendo una piena comprensione della programmazione a oggetti, con tutti i suoi benefici. Per esempio, il paradigma **MVC** (Modello-Vista-Controllore) implementato da symfony consente di separare nettamente la logica (il Modello) dai template (la Vista), con un il livello del Controllore a fungere da collante tra i primi due.

 Vediamo ora una sintesi degli altri pregi, non meno importanti, di symfony:

- **stabilità**: la politica di rilasci di symfony è molto stretta e prevede che le nuove caratteristiche siano implementate esclusivamente nelle versioni maggiori. Le versioni minori sono finalizzate esclusivamente alla chiusura dei bug. Ogni versione maggiore ha un periodo di supporto. L'attuale versione, la 1.4, sarà supportata fino al 2012.
- **modularità**: symfony è stato realizzato con una serie di classi disaccoppiate (per cui teoricamente è anche possibile utilizzare solo una o più parti del framework) e questo consente di manutenere più facilmente i vari componenti; inoltre prevede una struttura delle applicazioni (realizzate con symfony) basata essa stessa su moduli.
- **estensibilità**: un repository di plugin, alcuni sviluppati e mantenuti dal team di sviluppo di symfony, la maggior parte invece creati dalla comunità, implementano diverse funzionalità che sono di interesse comune, ma non sono comprese nel framework stesso.
- **documentazione**: la documentazione di symfony è molto estesa e dettagliata. Il motto del suo autore è "se una funzionalità non è documentata, non esiste". Al momento esistono cinque guide complete, pubblicate anche in forma cartacea, che coprono diversi aspetti sia teorici che pratici. Tutta la documentazione è disponibile anche in italiano.
- **community**: una grande comunità internazionale di sviluppatori contribuisce quotidianamente tramite segnalazione di bug, partecipazione a forum e mailing list, convention e quant'altro. La comunità italiana è abbastanza sviluppata e cura un [blog monotematico](http://www.symfony.it) e una [mailing list](http://groups.google.com/group/symfony-it).
 
- - - - - -

 Visto che un esempio vale più di mille parole, vedremo ora come symfony gestisce lo scaffolding, ovvero la generazione delle classi del modello a partire da un semplice schema. Supponiamo di dover sviluppare un forum, avremo uno schema come quello seguente:

 **User**:
 id: ~
 name: { type: varchar, required: true }
 password: { type: varchar, required: true }

 **Forum**:
 id: ~
 title: { type: varchar, required: true }
 is\_open: { type: boolean, default: true }

 **Topic**:
 id: ~
 forum\_id: ~
 user\_id: ~
 title: { type: varchar, required: true }

 **Message**: 
 id: ~
 topic\_id: ~
 user\_id: ~
 title: { type: varchar, required: true }
 text: { type: longvarchar, required: true }

 Anche senza conoscere la sintassi specifica di questo schema, scritto in [YAML](http://www.symfony-project.org/reference/1_4/it/02-YAML), la composizione delle entità e delle relazioni dovrebbe essere facilmente intuibile da chiunque: ci sono dei forum, in cui vengono scritti dei topic, che sono composti da messaggi, che sono scritti da utenti. Per i campi le cui caratteristiche sono omesse (quelli seguiti dal segno di tilde), symfony provvederà a un calcolo automatico (per esempio, i campi "id" sono degli interi con auto-increment, i campi come "forum\_id" sono chiavi esterne)

 Con un paio di comandi eseguiti da terminale, symfony genererà, partendo da tale schema:

- tutte le **tabelle** necessarie nel database
- tutte le **classi** necessarie per inserire dati nelle tabelle, estrarre dati, aggiornarli e cancellarli
- tutti i **form** relativi, con i campi necessari (ad esempio un radio per i campi booleani, un input select per le chiavi esterne, con i valori estratti dalla tabella relativa)
 
 Con un altro comando è possibile generare un'**intera interfaccia di amministrazione**, basata sulle classi e sui form di cui sopra. In questo modo non ci sarà alcun bisogno di perder tempo in quei compiti ripetitivi e noiosi, quali scrivere le query di base per le esigenze più comuni relative all'interazione col database. Tuttavia, sarà sempre possibile modificare le classi generate automaticamente, per aggiungere tutti quei metodi necessari alla personalizzazione. In caso di modifica "in corsa" al database, sarà sufficiente modificare lo schema e rigenerare le classi. Le personalizzazione già fatte non saranno sovrascritte. Come accennato inizialmente, questo consente di concentrarsi sui problemi e sulle logiche specifiche dell'applicazione da implementare.

 Il suddetto esempio ovviamente non esaurisce tutte le potenzialità di symfony ma permette di intuire come sia sufficiente un minimo sforzo per ottenere fin da subito risultati considerevoli. Per approfondire l'utilizzo di symfony basta consutare l'estesa documentazione disponibile su [www.symfony-project.org](http://www.symfony-project.org/).