---
title: "Introduzione all'utilizzo di MongoDB in PHP"
author: "Giuseppe Maggi"
type: article
date: 2012-11-16T12:44:31+0000
description: "Utilizzare il database orientato ai documenti MongoDB nelle applicazioni PHP"
url: /articoli/introduzione-all'utilizzo-di-mongodb-in-php/
categories:
  - Articoli
tags:
  - "database"
  - "MongoDB"

  
---
Quasi ogni applicazione deve gestisce un archivio dati. Lo può fare in svariati modi ma il più comune è l'utilizzo di un database relazionale gestito tramite qualche noto DBMS: Oracle, MySQL, SQL Server, PostgreSQL, etc..

Parlare di database relazionali richiama subito alla memoria alcuni concetti fondamentali quali tabelle, relazioni, linguaggio SQL, ecc.. Essenzialmente un database relazionale può essere visto come un sistema strutturato per la conservazione ed il reperimento di dati, memorizzati all'interno di tabelle collegate tra loro da relazioni. Se predisposta bene, questa “infrastruttura” ci permette, attraverso il linguaggio SQL, di svolgere operazioni di aggregazione e filtraggio dei dati, anche molto complesse.

L'informatica attuale, fortemente condizionata dal mondo dei servizi Internet e dei social network, è chiamata a prendersi cura di moli sempre più imponenti di informazioni, collezionate con grande velocità da milioni di utenti. Ciò che spesso **manca** a questi dati raccolti è la possibilità di essere inquadrati in **una struttura**.

Nel mondo “relazionale” la strutturazione dei dati, definita in fase di progettazione delle tabelle, ha un ruolo dominante ed è l'elemento che prepara la strada all'azione del linguaggio SQL. Da alcuni anni però si stanno diffondendo soluzioni che impiegano un diverso approccio alla gestione della persistenza dei dati, finalizzato alla concentrazione di **dati non strutturati** che, pertanto, non possono fare uso del linguaggio SQL.

Questi prodotti, detti NoSQL, oggi costituiscono già un universo variegato. Il modo più pratico per approcciarne lo studio è raggrupparli per categorie.

Le principali, almeno come numero di prodotti che ne fanno parte, sono:

- document-oriented, tra cui MongoDB, oggetto di questo articolo;
- a grafo;
- chiave/valore archiviato su disco;
- ad oggetti.
 
MongoDB è uno dei più famosi database basati sui documenti, viene attualmente utilizzato in sistemi di produzione e deve la sua precoce fama a caratteristiche come:

- la sua natura open source e multipiattaforma;
- l'alta disponibilità di driver per l'interazione con diversi linguaggi;
- propensione ad un'installazione scalabile su infrastrutture distribuite (auto-sharding, replicazione, implementazione dell'algoritmo MapReduce).
 
- - - - - -

## Installazione di MongoDB

Dopo questa breve introduzione al mondo del NoSQL, vediamo adesso come preparare il nostro PC all’utilizzo di MongoDB. Il punto di partenza è l'installazione del DBMS al termine della quale avremo a disposizione due strumenti: il programma **mongod** che rappresenta il server vero e proprio e l'utility **mongo** ossia il client da riga di comando.

**In Windows** è sufficiente scaricare il pacchetto della versione desiderata da [www.mongodb.org/downloads](http://www.mongodb.org/downloads) e scompattare l'archivio in una cartella di preferenza, ad esempio `<kbd>C:\mongodb</kbd>`. Essa conterrà una directory “bin” con gli eseguibili sopra citati ed altre utility. Fatto ciò bisogna creare la cartella `C:\data` ed al suo interno `C:\data\db`. Questi sono i percorsi di default utilizzati dal server per memorizzare i dati.

**In una distribuzione GNU/Linux**, sarà possibile procedere all'installazione tramite pacchetti precompilati. Ad esempio in un sistema Debian 6.0 basta lanciare il comando `# aptitude install mongo`. I comandi `mongod` e `mongo` saranno presenti nella cartella `/usr/bin` già utilizzabili da riga di comando.

A questo punto basta avviare mongod (su Windows basta eseguire **mongod.exe** da riga di comando) per avere il server disponibile in rete all'indirizzo IP locale della macchina (il classico localhost 127.0.0.1) ed alla porta TCP 27017. Altre configurazioni come file destinazione dei log, cartelle di storage dei database, autenticazione e molte altre di natura più sistemistica non vengono trattate in questo articolo ma sono ottenibili seguendo le indicazioni fornite dalla documentazione ufficiale presente su [www.mongodb.org](http://www.mongodb.org/).

![](http://www.phpnews.it/images/stories/Articoli/introduzione-mongodb/avvio-server.png)

Una volta avviato il server MongoDB, è necessario fare in modo che il linguaggio PHP abbia gli strumenti giusti per interagirvi. Stiamo parlando del “driver” ossia della libreria che traduce, nella lingua parlata dal server, i comandi che si vogliono impartire. I driver di MongoDB sono disponibili sul sito del progetto al link: [www.mongodb.org/display/DOCS/Drivers](http://www.mongodb.org/display/DOCS/Drivers).

Per l'interazione con il linguaggio PHP è necessario scaricare l'estensione adatta al nostro sistema (php\_mongo.dll per Windows), copiarla nella cartella delle estensioni di PHP e modificare il file php.ini aggiungendo la direttiva `extension=php_mongo.dll`. Anche questo aspetto è dettagliatamente riportato nella documentazione del sito di MongoDB. Coloro che utilizzano Zend Server devono invece limitarsi ad abilitare l’estensione mongo dall’interfaccia web di configurazione.

- - - - - -

Una volta che il server è installato ed avviato è possibile dialogarvi utilizzando il client *mongo* che offre una console interattiva. Ma prima dobbiamo prendere confidenza con la terminologia adottata da MongoDB.

Mentre in un sistema relazionale l'architettura dell'informazione poggia sulle tabelle che compongono il database, in un sistema orientato ai documenti come MongoDB tutta l'architettura si riduce al **documento**, che sostituisce il record di un RDBMS tradizionale.

Un documento è un insieme di **campi**, ciascuno costituito da una coppia **chiave -&gt; valore**. La chiave è una stringa (ricorda il nome della colonna di una tabella) mentre il valore può essere un tipo semplice (intero, decimale, stringa, ecc.), un array di tipi semplici o un intero documento. Per descrivere questa struttura dati, potenzialmente assai più complessa di quella del record tradizionale, MongoDB utilizza la notazione **JSON**.

**Ogni documento potrà avere un proprio insieme di campi**, una propria struttura, ed avere in comune con altri documenti solo l'appartenenza alla medesima **collection**.

La collection quindi è un insieme di documenti, così come la tabella è un insieme di record. Questa però non svolge più il forte ruolo normativo della tabella relazionale (specifica del numero di campi, tipi di dato, vincoli sui valori accettati) ma serve per lo più da accumulatore di documenti, potenzialmente molto eterogenei tra loro.

I concetti appena espressi portano, da un lato, alla definizione di una struttura dati estremamente flessibile, dall’altro, all’impossibilità di impiegare l’SQL come linguaggio per interagire col database. Al suo posto troveremo JSON e una sintassi in stile **Javascript**.

Infine in MongoDB un **database** è un insieme di collection, similmente a quanto accade in un database relazionale, costituito da un insieme di tabelle.

Lo specchietto seguente riassume la corrispondenza che si può individuare tra i concetti che regolano un tradizionale database relazionale e MongoDB:

 | RDBMS | MongoDB |
|---|---|
| database | database |
| tabella | collection |
| record (tupla o riga della tabella) | documento |
| colonna della tabella | campo del documento |
| indice | indice |
| SQL | JSON, Javascipt |

- - - - - -

## Database di esempio

Nel seguito dell'articolo si immaginerà di gestire in MongoDB un sistema di reclutamento di personale per un'azienda che richiede al candidato la conoscenza di almeno uno tra i linguaggi di programmazione Java e PHP. Queste informazioni, insieme a quelle relative ad ogni altra competenza del candidato in materia informatica o linguistica, verranno conservate nel db e classificate con una valutazione da 1 a 10.

A titolo di esempio, il sistema conserverà in documenti appartenenti alla stessa collezione, sia i dati del signor Ugo Rossi, competente in Java e PHP con le rispettive valutazioni di 10 e 7 e con un'abilità nella lingua inglese valutata anch'essa in 7/10, sia i dati del signor Silvio Verdi esperto di PHP e Python (entrambi voto 8) ma a conoscenza solo di pochi elementi della lingua inglese (voto 5).

Come anticipato, il formato usato da MongoDB per descrivere questi documenti è JSON. I dati dell'esempio corrisponderanno agli array JSON:

 ```json
{
  "nome"    : "Ugo",
  "cognome" : "rossi",
  "Java"    : "10",
  "PHP"     : "7",
  "inglese" : "7"
}
```

e

 ```json
{
  "nome"    : "Silvio",
  "cognome" : "Verdi",
  "Python"  : "8",
  "PHP"     : "8",
  "inglese" : "5"
}
```

Iniziamo a trattare questi dati in MongoDB facendo uso delle fondamentali operazioni fornite dalla console.

Per fare ciò avviamo il client mongo specificando indirizzo IP, porta TCP e nome del database (in questo caso denominato “prova”).

![](http://www.phpnews.it/images/stories/Articoli/introduzione-mongodb/connessione-server.png)

Si noti che non è richiesta l’esistenza del database a cui ci stiamo connettendo. Qualora richiedessimo la connessione ad un database non ancora esistente, Mongo lo creerà per noi, svolgendo una versione NoSQL del tradizionale CREATE DATABASE. Nel seguito vedremo come accadrà lo stesso per la creazione di una collection.

Adesso proviamo ad inserire i dati dei signori Rossi e Verdi in MongoDB e poi chiediamo al server di restituirceli con una ricerca:

 ```

> db.candidati.insert({nome: "ugo",cognome: "rossi",Java: "10",PHP: "7",inglese: "7"})
> db.candidati.insert({nome: "Silvio",cognome: "Verdi",Python: "8",PHP: "8",inglese: "5"})
> db.candidati.find()
{ "_id" : ObjectId("505335a0678b1ec66db608d3"), "nome" : "ugo", "cognome" : "rossi", "Java" : "10", "PHP" : "7", "inglese" : "7" }
{ "_id" : ObjectId("505335b9678b1ec66db608d4"), "nome" : "Silvio", "cognome" : "Verdi", "Python" : "8", "PHP" : "8", "inglese" : "5" }
```

Quello che è appena successo, in SQL, corrisponderebbe a due INSERT ed una SELECT. In termini NoSQL lo spieghiamo dicendo che sono stati inseriti nella collection “candidati” due documenti riportanti rispettivamente le competenze del sig. Ugo Rossi e del sig. Silvio Verdi, e poi ne è stato visualizzato il contenuto con il comando **find**.

Osservazioni importanti:

- i comandi iniziano sempre con “db.” che fa riferimento al database corrente;
- la collection "candidati" non esiste ancora ma al primo inserimento di un documento viene creata in automatico;
- la chiave primaria non è stata inserita direttamente ma, osservando l'output del find(), si vede che ogni documento ha un suo \_id che costituisce un identificativo univoco del documento creato automaticamente dal server.
 
- - - - - -

## Interazione PHP - MongoDB

Procediamo adesso con un esempio piuttosto basilare, finalizzato a trasformare in un programma PHP quanto precedentemente effettuato da riga di comando.

Per connetterci al database è necessario istanziare un oggetto della classe Mongo, passandogli una stringa contenente i parametri di connessione. Nel nostro caso, non avendo provvisto MongoDB di autenticazione, sarà sufficiente richiedere una connessione al servizio standard ossia localhost sulla porta 27017.

 ```php
<?php
$conn = new Mongo("mongodb://localhost:27017");
$database = $conn->prova;
$collection = $database->candidati;
```

Nell'esempio la variabile $conn contiene la connessione attiva al DBMS. Da lì si ottiene un riferimento al database “prova” ($database è un oggetto di tipo MongoDB) e poi alla collection “candidati” ($collection è un oggetto di tipo MongoCollection). Come già detto, se il database o la collection non esistono ancora, MongoDB non restituisce alcun errore ma li crea automaticamente.

Come anticipato, MongoDB non adotta un linguaggio per le query simile all’SQL. Al suo posto dobbiamo utilizzare i metodi dell’oggetto MongoCollection a cui passeremo degli array opportunamente configurati. Sarà poi il driver di Mongo a convertirli in comandi e strutture JSON, analoghi a quelli illustrati nelle operazioni da riga di comando.

Ecco un esempio di inserimento di un documento:

 ```php
$documento = array (
     'nome' => "Ugo",
     'cognome' => "Rossi",
     'PHP' => 7,
     'Java' => 10,
     'inglese' => 7
);

$collection->insert($documento);
```

L’array $documento contiene i dati del signor Ugo Rossi da inserire nel database. Nel mondo relazionale costituirebbe un record di una tabella, in MongoDB invece costituisce un documento. Il metodo insert invocato sulla collection svolge il lavoro.

E’ estremamente importante fare attenzione al set di caratteri utilizzato dalle stringhe inserite in un documento. **In tutte le funzioni di MongoDB va utilizzato l’UTF-8**. Nel caso lo script realizzato non tratti stringhe in UTF-8 è possibile convertirli usando la funzione utf8\_encode.

Per inserire un documento è possibile invocare, al posto del metodo insert, il metodo save nello stesso modo appena visto. A differenza del metodo insert, se il documento fosse stato precedentemente prelevato dal database, il metodo save avrebbe aggiornato il documento esistente. In altre parole il metodo insert corrisponde sempre ad un classico INSERT mentre il metodo save esegue un INSERT se il documento non esiste nel database, altrimenti un UPDATE.

Vediamo adesso uno degli aspetti più importanti dell'interazione con un database ossia il recupero dei dati. Iniziamo con una ricerca priva di filtri, finalizzata alla lettura di tutti i documenti presenti nella collection.

 ```php
$cursore = $collection->find();

foreach($cursore as $documento) {
     echo $documento['nome'], ' ', $documento['cognome'], "\n";
}
```

Come è possibile desumere dal precedente esempio, il risultato dell’interrogazione, ovvero dell’invocazione del metodo find, è un **cursore**. Esso rappresenta un puntatore ai dati restituiti ed il suo movimento lungo il set di risultati, determinato dalle iterazioni del ciclo foreach, permetterà di accedere ad ogni singolo documento reperito. Questo infine può essere utilizzato come un normale array PHP per poterne trattare i risultati.

Vediamo adesso come effettuare una lettura dei documenti di una collection impiegando dei filtri. Ciò che nel mondo relazionale viene specificato nel corpo di una clausola WHERE di una query ora popola un array PHP da passare come parametro al metodo find().

Se ad esempio volessimo cercare i candidati di nome ‘Ugo’:

 ```php
$query = array(
     'nome' => 'Ugo'
);

$cursore = $collection->find($query);
```

Oppure, per recuperare i documenti in cui il voto di PHP risulta minore di 8 si può impostare $query in questa maniera:

 ```php
$query = array(
     'PHP' => array('$lt' => 8)
);

$cursore = $collection->find($query);
```

Quindi, in generale, i filtri di una query saranno specificati in un array che avrà come elementi i nomi dei campi da sottoporre a valutazione. Ad essi sono associate le condizioni da verificare. Gli operatori, come $lt che ha il significato di “minore di”, dovranno essere specificati come chiavi di un array a cui sarà associato l'operando di confronto.

Ovviamente esistono operatori in grado di descrivere ogni casistica, ad esempio $gte sta per “maggiore o uguale”, $gt per “maggiore”, ecc.. Si faccia attenzione all’utilizzo dei singoli apici per definire gli operatori. Questi infatti, avendo in testa il simbolo $, possono essere confusi da PHP come variabili e sostituiti con il loro valore se inseriti in stringhe definite con i doppi apici.

Il meccanismo di definizione delle condizioni appena visto può essere usato anche per effettuare aggiornamenti e cancellazioni in una collection.

Vediamo ad esempio come cancellare tutti i documenti dei candidati che conoscono il Python

 ```php
$query = array(
     'Python' => array('$exists' => true)
);

$collection->remove($query);
```

Per cancellare i documenti abbiamo usato il metodo remove passando la condizione nello stesso modo visto per effettuare le ricerche.

Prima di concludere accenniamo alle modalità di aggiornamento dei documenti. Abbiamo già detto che se preleviamo un documento, ad esempio con il metodo find, e ne modifichiamo i dati, possiamo poi salvare le modifiche effettuate con il metodo save. Grazie al metodo update è invece possibile aggiornare, sia nei contenuti che nella struttura, tutti i documenti che rispondono ad un particolare criterio. Date le sue potenzialità, l’uso di questo metodo è piuttosto complesso e la sua descrizione esula da questo articolo introduttivo.

- - - - - -

## Conclusioni

In questo articolo abbiamo affrontato solo una piccola parte di tutto ciò che si può imparare su MongoDB e la sua interazione con un linguaggio versatile come PHP ma è quanto basta per poterne avviare lo studio.

La domanda che potrebbe venire in mente a questo punto è: “devo iniziare a rinunciare ai database relazionali e convertire le mie applicazioni al NoSQL?”

Sicuramente il nodo della questione verte sulla presenza o meno di una strutturazione dei dati. Al momento la migliore delle risposte che si può dare è quindi un'ulteriore domanda: “quanto sono intrinsecamente strutturati i dati che devo gestire?”.

Finché l'organizzazione dei dati in un modello relazionale appare naturale, i database tradizionali e le potenzialità di filtro ed aggregazione che offre SQL sono ancora imprescindibili. Viceversa la flessibilità propria di una soluzione orientata ai documenti può risultare determinante alla buona realizzazione di un progetto.