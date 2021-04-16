---
title: "Conversione del Modello ER in Modello Relazionale"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:56:55+0000
description: "Conversione del Modello ER Entità Relazione in Modello Relazionale"
url: /corsi/la-progettazione-dei-database/conversione-del-modello-er-in-modello-relazionale/
weight: 4
categories:
  - La progettazione dei database
  
---
 Il Modello Relazionale che rappresenta il mini-modo di interesse può essere ricavato direttamente dal Modello ER, attraverso una sequenza di operazioni di conversioni. Ecco i passi fondamentali da seguire.

 **1.** Creare una relazione per ciascuna entità del Modello ER, utilizzando per i campi gli stessi attributi semplici dell'entità di provenienza. La chiave primaria della relazione sarà scelta tra le chiavi candidate dell'entità.

 ![conv1.gif](/image/corsi/progettazione_database/conv1.gif)

 **2.** Inserire nelle relazioni gli attributi composti delle entità, considerando però solo gli attributi semplici componenti. Ad esempio, se nel Modello ER avevamo un attributo composto "indirizzo" dell'entità "studente" costituito a sua volta da "via", "numero", "città", ecc. allora nella relazione dovremo considerare tali sotto-attributi come tanti campi indipendenti.

 ![conv2.gif](/image/corsi/progettazione_database/conv2.gif)

 **3.** Per ogni attributo multivalore A bisogna creare una nuova relazione R che include A e, come chiave esterna, la chiave primaria della entità a cui A era inizialmente connesso. La chiave primaria della nuova relazione R sarà la combinazione della chiave esterna e di A.

 ![conv3.gif](/image/corsi/progettazione_database/conv3.gif)

 **4.** Per ogni entità debole bisogna creare una relazione R ereditandone gli attributi. Bisogna inoltre includere, come chiave esterna, la chiave primaria dell'entità "proprietaria". La chiave primaria di R sarà l'insieme della chiave esterna più la chiave parziale.

 ![conv4.gif](/image/corsi/progettazione_database/conv4.gif)

 **5.** Le associazione con cardinalità 1:1 tra due entità A e B possono essere convertite seguendo due approcci. Per prima cosa bisogna individuare il grado di partecipazione delle 2 entità all'associazione. Se tutte (o quasi) le istanze delle due entità sono coinvolte dall'associazione (partecipazione totale) allora conviene fondere A e B in un'unica relazione.

 Esempio: ogni studente ha una sola carriera e ogni carriera appartiene ad un solo studente. Vi è quindi partecipazione totale per entrambe le entità all'associazione che pertanto conviene fondere.

 ![conv5.gif](/image/corsi/progettazione_database/conv5.gif)

 Al contrario, se solo l'entità A è in partecipazione totale con B, allora devono essere create 2 relazioni A e B ed in A si inserisce la chiave primaria di B come chiave esterna ed eventuali attributi dell'associazione.

 Esempio: supponendo che solo alcuni dipendenti hanno a disposizione un'auto aziendale:

 ![conv6.gif](/image/corsi/progettazione_database/conv6.gif)

 **6.** Per ogni associazione 1:N tra le entità A e B si considera la relazione B (sul lato N) e si include in essa, come chiave esterna, la chiave primaria di A oltre ad eventuali attributi dell'associazione.

 Come esempio vediamo il Modello Relazionale della rubrica telefonica.

 ![conv7.gif](/image/corsi/progettazione_database/conv7.gif)

 **7.** Ogni associazione di tipo M:N tra le entità A e B va sostituita con una nuova relazione R. In essa andranno incluse le chiavi esterne delle entità A e B la cui combinazione formerà la chiave primaria di R. R eredita dall'associazione anche i suoi eventuali attributi.

 Come esempio applichiamo questa conversione al caso dei film e delle persone visto nelle lezioni precedenti.

 ![conv8.gif](/image/corsi/progettazione_database/conv8.gif)

 Così com'è stata definita la relazione cast non sarà possibile fare in modo che una stessa persona partecipi due volte per lo stesso film in ruoli diversi. Per far sì che questo possa avvenire la chiave primaria della relazione cast, oltre agli attributi nome film, nome persona e cognome persona, dovrà comprendere anche l'attributo ruolo.