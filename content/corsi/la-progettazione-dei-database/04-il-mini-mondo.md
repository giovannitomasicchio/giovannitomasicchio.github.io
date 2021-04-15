---
title: "Il mini-mondo"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:55:12+0000
description: "Analisi del mini-mondo della base di dati (database)"
url: /corsi/la-progettazione-dei-database/il-mini-mondo/
categories:
  - La progettazione dei database
  
---
 Il primo passo nella progettazione di una base di dati viene effettuato studiando approfonditamente il problema, tramite interviste a chi ha commissionato la base di dati ed agli operatori chi ne faranno utilizzo. Quando sono chiari i requisiti dell'applicazione e tutti i vincoli del problema, si scrive una breve ma essenziale descrizione del mini-mondo da rappresentare.

 Un esempio dovrebbe chiarire un po' le idee. Si immagini di voler realizzare una semplice base di dati per un sito web che tratta di film. Una (breve) descrizione del mini-mondo potrebbe essere:

 *Il sito web mostra ai visitatori informazioni sui film: data di realizzazione, genere, trama e le lingue in cui è stato doppiato. Saranno presenti anche informazioni sulle persone (regista e attori) che lo hanno realizzato: data di nascita, biografia e ruolo dell'attore (protagonista, antagonista, comparsa, ...).*

 E' importante individuare correttamente i dati da includere nel nostro mini-modo anche in relazione alle funzionalità che dovrà fornire l'applicazione che stiamo realizzando. Ad esempio se viene richiesto che il nostro sito sia in grado di mostrare la locandina dei film allora sarà necessario conservare nel nostro mini-mondo anche il nome dell'immagine associata.

 Inoltre bisogna stare attenti ai dati derivati, come ad esempio l'età degli attori, che in realtà deriva dalla data di nascita. Oppure il numero dei film girati da un determinato regista, informazione che (in generale) non va contemplata nel mini-mondo poiché è recuperabile attraverso un semplice conteggio.

 Potremo considerare conclusa l'analisi del nostro mini-mondo quando, dai dati in esso contemplati, saremo in grado di espletare tutte le funzionalità previste dal progetto.