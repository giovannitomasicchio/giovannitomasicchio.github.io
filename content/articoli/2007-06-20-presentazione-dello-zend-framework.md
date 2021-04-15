---
title: "Presentazione dello Zend Framework"
author: "Giovanni Tomasicchio"
type: article
date: 2007-06-20T16:12:21+0000
description: "Una panoramica sullo Zend Framework. Presentazione dei principali componenti dello Zend Framework."
url: /articoli/presentazione-dello-zend-framework/
categories:
  - Articoli
tags:
  - "database"
  - "Joomla!"
  - "Zend Framework"
  - "MySQL"
  - "PDF"
  - "Java"
  - "Smarty"
  - "Oracle"
  - "sicurezza"
  - "RSS"
  - "ORM"
  - "AJAX"
  - "email"
  - "JSON"
  - "OOP"
  - "web service"

  
---
###  Introduzione

 Se avete maturato un po' di esperienza in PHP sapete bene come gran parte del codice di un nuovo lavoro può essere ereditato dai precedenti progetti: le funzioni per collegarsi al database, quelle per gestire i template, quelle per l'autenticazione dell'utente, ecc.. Accanto al codice da voi prodotto, forse usate anche diverse librerie di codice realizzate da terzi: Smarty per gestire i template HTML, FPDF per creare PDF, PHPMailer per inviare email complesse, ADODb per interagire con i diversi database, la miriade di classi PEAR, ecc.. La mole di codice PHP oggi a nostra disposizione è davvero cospicua, e ciò ci assicura una certa libertà nell'organizzazione delle applicazioni.

 Ma questo scenario non è privo di problemi. Infatti l'integrazione del nostro codice con quello delle diverse librerie non è sempre immediata. Le dipendenze ed i requisiti dei componenti non sono omogenei ed inoltre capita che alcune librerie non vengano più sviluppate e trovare supporto diventa difficile. Altre volte la documentazione è scarsa oppure non in linea con le nuove versioni del codice.

 Per risolvere questo caos negli ultimi anni sono nati i cosiddetti framework, collezioni organiche di codice PHP da utilizzare per costruire applicazioni, disponibili già da lungo tempo per altri linguaggi (.NET e JAVA ad esempio). Alcuni di questi framework sono progetti originali, altri prendono spunto da framework di successo di altre tecnologie. La maggior parte però viene portata avanti da piccoli gruppi di sviluppatori, con il supporto di comunità più o meno grandi e, nonostante alcuni abbiano raggiunto una buona diffusione, nessuno di questi si può considerare oggi uno standard.

 In questo contesto nasce lo [Zend Framework](http://framework.zend.com), un progetto dell'azienda leader nel panorama PHP, che vuole mettere ordine nella realizzazione di programmi PHP, fornendo strumenti in grado di soddisfare le esigenze comuni e risolvere i problemi che quotidianamente uno sviluppatore deve affrontare.

- - - - - -

###  PHP Collaboration project

 Anche se PHP ha conquistato un'enorme diffusione, negli ultimi anni si è rafforzata l'idea che mancasse ancora qualcosa per rendere questo linguaggio adatto anche agli **ambienti enterprise** (grosse aziende coinvolte in progetti complessi). Se guardiamo alle piattaforme concorrenti ci accorgiamo che, accanto alle differenze dei linguaggi, PHP non possiede una raccolta di codice ufficiale, universalmente riconosciuta. Il JAVA ad esempio dispone delle classi del JDK, della piattaforma J2EE, dei framework Struts, JSF, ecc. I linguaggi della piattaforma .NET possono contare sull'enorme quantità di componenti racchiusa nel .NET framework. Per non parlare del successo di Ruby ottenuto soprattuto grazie al framework Rails. E' vero che anche per PHP, come anticipato, sono stati realizzati diversi framework, ma nulla di paragonabile alle soluzioni disponibili alle tecnologie concorrenti.

 Ma c'è ancora un altro aspetto che, secondo l'analisi fatta da Zend, limita la diffusione di PHP negli ambienti enterprise: la mancanza di un ambiente di sviluppo evoluto in grado realmente di ridurre i tempi di realizzazione delle applicazioni. Chi ha utilizzato il Visual Studio, Eclipse o Netbeans sa di cosa stiamo parlando.

 La Zend nell'ottobre del 2005 ha deciso pertanto di avviare un progetto in collaborazione con diversi giganti dell'informatica (IBM, Intel, Oracle, MySQL, Amazon, eBay, Paypal, Joomla, solo per citarne alcuni) per dare al PHP proprio ciò che a suo parere ancora gli manca. Tale progetto, denominato **PHP Collaboration Project**, si concretizza in tre principali obiettivi:

- realizzare un Framework opensource con la stessa filosofia alla base di PHP: semplicità, versatilità e potenza
- realizzare un ambiente di sviluppo opensource per PHP, basato sulla piattaforma Eclipse.
- realizzare un sito web attorno al quale aggregare la comunità di sviluppatori PHP
 
 Di tutti e tre gli obbiettivi oggi è disponibile un risultato tangibile. Lo Zend Framework raggiungerà la versione 1.0.0 stabile fra pochi giorni ed è già pronto per l'uso. IBM e Google lo stanno già impiegando nella realizzazione di applicazioni web 2.0. L'ambiente di sviluppo Eclipse PDT (PHP Development Tool) è un promettente IDE, gratuito, e dotato di numerose funzionalità. Non ha ancora raggiunto una versione stabile, prevista per settembre 2007, ma può essere già utilizzato, scaricandolo dal sito <http://www.zend.com/pdt>. Infine il sito web [devzone.zend.com](http://devzone.zend.com), sebbene ancora giovane, è sicuramente un progetto promettente e presto vedrà importanti aggiornamenti, in occasione dell'uscita del framework.

- - - - - -

###  Caratteristiche e finalità

 Cos'è dunque lo Zend Framework? In breve si tratta di un insieme di classi che hanno lo scopo di semplificare ed accelerare la realizzazione delle applicazioni, garantendo sicurezza, scalabilità e robustezza. Lo Zend Framework vuole fornire soluzioni ai problemi ricorrenti in modo che gli sviluppatori possano concentrarsi sulle reali peculiarità del loro progetto.

 Il framework si pone degli obiettivi ambiziosi che, se raggiunti appieno, lo renderebbero unico nel panorama dei framework per PHP:

- I suoi diversi componenti devono essere semplici da usare, in modo da rispettare la filosofia che ha decretato il successo del PHP.
- L'intero framework non deve esser specializzato per qualche particolare compito ma deve poter essere impiegato in tutti i contesti, persino nella realizzazione di applicazioni a riga di comando.
- Non deve imporre scelte stringenti o pesanti vincoli allo sviluppatore ma deve lasciar ampio spazio alla personalizzazione di tutto il suo funzionamento.
- Deve essere completamente modulare, i suoi componenti devono poter essere utilizzati separatamente l'uno dall'altro ed in congiunzione con librerie esterne.
- La documentazione deve essere completa e costantemente aggiornata.
- Il codice che lo costituisce deve essere scritto in PHP 5 e deve essere compatibile con il più stringente livello di segnalazione errori E\_STRICT.
- Deve essere interamente realizzato da zero e non deve avere alcuna dipendenza con librerie esterne.
- Anche se è il frutto della collaborazione dei più esperti sviluppatori PHP, deve rimanere aperto alle proposte della comunità di sviluppatori PHP.
 
 I suddetti obiettivi non sono certo di facile realizzazione ma, all'alba dell'uscita della versione 1.0, è difficile trovarne uno che non sia stato, almeno in parte, raggiunto.

- - - - - -

###  I componenti dello Zend Framework

 Come accennato, lo Zend Framework si compone di una collezione di classi, quindi è realizzato secondo lo stile di programmazione ad oggetti. Questo è uno dei motivi per cui PHP 4 non viene supportato, a causa del suo ridotto supporto al paradigma OOP. Le classi sono organizzate per gruppi funzionali e costituiscono i componenti del framework. Lo sviluppatore però non è costretto ad usare necessariamente tutti questi componenti poiché sono indipendenti l'uno dall'altro anche se ottimizzati per interagire tra loro.

 I principali componenti del framework sono:

- **Model-View-Controller (MVC)** - il motore che realizza l'architettura dell'applicazione e la generazione delle pagine web. Si basa sullo schema MVC e sull'utilizzo di un front controller per lo smistamento delle richieste utente.
- **Core Infrastructure** - componenti per la gestione della configurazione dell'applicazione, per la gestione dei log, della cache, per la validazione dell'input dell'utente, il caricamento automatico delle classi, ecc..
- **Databases** - classi che gestiscono l'accesso e l'utilizzo dei database ed implementano la mappatura degli oggetti in tabelle (ORM)
- **Authentication &amp; Authorization** - gestiscono il riconoscimento dell'utente, la gestione dei diritti di accesso e le sessioni
- **Internationalization (i18n) &amp; Localization (l10n)** - componenti che si occupano di semplificare la realizzazione di una applicazione multilingua
- **Mail, Formats, &amp; Search** - permettono l'invio di email complesse, la produzione di PDF, la realizzazione di motori di ricerca e l'utilizzo di file JSON
- **Web &amp; Web Services** ? consentono di realizzare client e server per i web services. Permettono inoltre di gestire feed RSS, effettuare chiamate XMLRPC e Rest
 
 Inutile affrontare in dettaglio ciascuno dei suddetti componenti. Le funzionalità implementate sono numerosissime, altamente configurabili ed estendibili.

- - - - - -

###  Conclusioni

 Difficile poter effettuare delle valutazioni su un progetto di così ampio respiro quando mancano ancora pochi giorni al rilascio della sua prima release stabile. Nonostante risulta evidente che lo Zend Framework è già uno strumento completo, la sua diffusione dipenderà da molti fattori, sia oggettivi che soggettivi, poiché si sa, il programmatore PHP è abituato alla semplicità ed alla versatilità del linguaggio e tali peculiarità vengono spesso limitate dall'uso dei framework.

 Gli sviluppatori dello Zend Framework hanno comunque dimostrato molta attenzione ai suggerimenti offerti dalla comunità PHP durante il processo di sviluppo, apportando cambiamenti sostanziali al codice se necessario.

 La roadmap del framework comunque non lascia spazio a dubbi su quanto la Zend stia investendo in questo progetto. Nelle prossime versioni infatti vedremo, accanto all'evoluzione degli attuali componenti, anche nuove classi per utilizzare AJAX, per interfacciarsi con i servizi web più diffusi ed per aumentare il numero di formati di file manipolabili.