---
title: "Progettazione concettuale: il Modello ER"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:55:20+0000
description: "Il modello ER entità relazione, la progettazione concettuale di una base di dati (database) "
url: /corsi/la-progettazione-dei-database/progettazione-concettuale-il-modello-er/
categories:
  - La progettazione dei database
  
---
 Il Modello Entità-Associazione (dall'inglese Entity-Relationship, da cui la sigla ER), permette di dare uno sguardo **visuale** al progetto di una base di dati. Spesso nella letteratura delle basi di dati in lingua italiana si parla di Modello Entità-Relazione ma tale terminologia può portare a confondere la Relazione (concetto che presenteremo nelle prossime lezioni) con l'Associazione.

 Attraverso il Modello ER è possibile vedere quali sono le entità della base di dati e quali sono le associazioni tra di esse.

 Una **entità** è una "cosa" (concreta o astratta) del mini-mondo con un'esistenza indipendente dalle altre. Gli **attributi** sono le proprietà particolari che la descrivono. Ad essere più precisi dovremmo dire che un'entità è la descrizione astratta di un insieme di oggetti che condividono gli stessi attributi.

 Leggendo la descrizione del mini-mondo della precedente lezione possiamo evidenziare le entità *film* e *persona*; una persona che ha partecipato alla realizzazione di un film può essere un regista o un attore, o anche entrambi (Carlo Verdone è regista di molti film in cui è anche attore).

 Individuiamo gli attributi: nome, data di realizzazione, genere, trama e le lingue in cui è stato tradotto il film sono sicuramente attributi dell'entità film, mentre il nome, il cognome, data di nascita e biografia caratterizzano la persona.

 Un particolare oggetto di una entità (es. "Via col Vento" dell'entità film) viene detto **istanza dell'entità**.

 Le **associazioni** tra le varie entità sono i legami logici che uniscono i dati presenti nelle entità del nostro mini-mondo. Le associazioni legano generalmente due entità e pertanto vengono dette binarie. Non considereremo invece associazioni tra più di due entità per cui nel seguito parleremo di associazioni riferendoci implicitamente a quelle binarie. Si noti che anche le associazioni possono avere attributi.

 Tra le due entità film e persona vi è un'associazione che descrive il fatto che una persona ha partecipato alla realizzazione di un film. Il ruolo avuto nella realizzazione del film, sia esso quello di regista, protagonista, attore secondario o altro, è un attributo dell'associazione.

 Nel diagramma ER le entità vengono indicate con dei rettangoli mentre le associazioni con dei rombi collegati alle entità interessate dall'associazione. Gli attributi invece vengono indicati all'interno di ellissi collegate all'entità o all'associazione cui appartengono.