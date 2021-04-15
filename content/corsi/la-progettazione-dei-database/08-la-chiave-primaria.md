---
title: "La chiave primaria"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:56:05+0000
description: "La chiave primaria"
url: /corsi/la-progettazione-dei-database/la-chiave-primaria/
categories:
  - La progettazione dei database
  
---
 Nel primo esempio della lezione 6 si può notare la presenza di un altro tipo di attributo che ha un significato speciale all'interno delle entità: l'attributo **chiave primaria**. Nel diagramma ER questi attributi vengono indicati con una sottolineatura (nome per l'entità film e la coppia nome cognome per l'entità persona).

 Gli attributi chiave primaria permettono di individuare univocamente ogni istanza dell'entità cui appartengono. Nel nostro caso ad esempio il nome "Via col vento" individua univocamente una istanza (un preciso film) dell'entità film. Non abbiamo bisogno di ulteriori indicazioni sui restati attributi di tale entità per sapere a quale film stiamo facendo riferimento. Per questo motivo in una entità non possono esisterci doppioni, ovvero due o più istanze uguali, altrimenti la chiave primaria non permetterebbe di distinguerle. Quindi non saranno ammessi film con lo stesso nome oppure, se pensiamo all'entità persona, non saranno ammesse persone che hanno contemporaneamente lo stesso nome e cognome.

 Se dovessimo realizzare la base di dati dell'anagrafe italiana non potremmo mai scegliere la coppia nome e cognome come chiave primaria ma dovremmo ricorrere al codice fiscale che garantisce l'univocità per ogni persona.

 Può capitare che in una entità ci siano diversi gruppi di attributi **candidati** a diventare chiave primaria. Nel diagramma ER la scelta ricade sulla chiave che ha una semantica (un significato) maggiormente legato con l'entità. Ad esempio nell'entità studenti di una università gli attributi matricola e codice fiscale potrebbero essere entrambi usati come chiave primaria. Tra i due si potrebbe scegliere il numero di matricola perché più legato all'entità studente considerato il contesto universitario che stiamo modellando.

 Ritorneremo sul concetto di chiave primaria nella lezione 11, quando passeremo alla traduzione dei diagrammi ER in modelli relazionali, e vedremo che la scelta della chiave primaria spesso sarà dettata da considerazioni più contingenti e tecniche. Per ora basta sapere che la chiave primaria è l'insieme minimo di attributi che permettono di individuare univocamente ogni istanza dell'entità cui appartengono. Proprio per l'individuazione univoca non sono ammesse istanze di una stessa entità che abbiano i valori della chiave primaria duplicati ed inoltre nel dominio di valori della chiave non può esserci il NULL. Nel caso peggiore una chiave primaria può essere costituita anche da tutti gli attributi dell'entità.

 Ci sono entità per cui non si riesce a trovare un sottoinsieme di attributi in grado di costituire una chiave primaria. Si tratta di entità fortemente connesse attraverso una relazione ad un'ulteriore entità, detta **proprietaria**, dalla quale dipende l'esistenza di tutte le sue istanze. Per chiarire il concetto vediamo il seguente esempio.

 ![debole.gif](images/stories/Corsi/progettazione_database/debole.gif)

 L'entità capitolo deve la sua esistenza all'entità proprietaria Libro, infatti i suoi attributi non sono sufficienti a definire una chiave primaria. Questa **entità debole** userà come chiave primaria l'insieme dell'attributo "numero capitolo" (detta **chiave parziale**) e della stessa relazione "contiene". Infatti un capitolo è univocamente individuabile conoscendo il libro da cui è tratto ed il numero di capitolo.

 Prima di concludere è bene precisare che i diagrammi ER contemplano anche altri accorgimenti e costrutti che però non verranno trattati perché esulano dagli obiettivi di questo corso.