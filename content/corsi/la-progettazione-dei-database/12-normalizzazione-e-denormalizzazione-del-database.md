---
title: "Normalizzazione e denormalizzazione del database"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:57:26+0000
description: "Normalizzazione e denormalizzazione di una base di dati (database)"
url: /corsi/la-progettazione-dei-database/normalizzazione-e-denormalizzazione-del-database/
categories:
  - La progettazione dei database
  
---
 Ricordate l'esempio della rubrica presentato nella prima lezione? Avevamo constatato, provando ad inserire nella tabella anche gli indirizzi dei luoghi di lavoro, che il nuovo schema presentava diverse ripetizioni. Inoltre avevamo intravisto possibili problemi nell'aggiornamento dei dati, ad esempio modificando l'indirizzo di un posto di lavoro. Avevamo concluso dicendo che solo una corretta progettazione del database avrebbe evitato simili problemi. Effettivamente, ora che conosciamo il Modello ER, possiamo dire che la rubrica andava rappresentata con 2 entità distinte, una per le persona, l'altra per i posti di lavoro. La causa dei problemi suddetti derivava quindi dall'aver unificato 2 entità distinte in un'unica struttura.

 La realizzazione del Modello ER e la sua conversione in Modello Relazionale però non garantiscono a priori che la base di dati realizzata non sia soggetta a problemi. Questi strumenti sono infatti nelle mani del progettista che inevitabilmente può sbagliare. Esistono però dei sistemi di verifica che, applicati sul Modello Relazionale, stabiliscono il grado di **normalizzazione** della stessa, ovvero il grado di immunità alle ridondanze ed alle inconsistenze dei dati. Ci sono diversi gradi di normalizzazione, si parla infatti di prima, seconda, terza, ecc. **forma normale**.

 Il processo di normalizzazione serve a verificare la struttura della base di dati ed eventualmente indica le relazioni contenenti attributi a ben vedere indipendenti. Tali relazioni andrebbero quindi suddivise in sotto-relazioni (si ricordi sempre l'esempio della rubrica della prima lezione).

 La corretta comprensione delle forme normali però richiede una conoscenza approfondita del Modello Relazionale ed il formalismo necessario alla loro descrizione non è certo banale. Per un approfondimento si rimanda quindi ai testi specializzati.

 Dal discorso sinora fatto sembra quindi che la qualità di una base di dati aumenti all'aumentare della suddivisione dei dati in relazioni. La questione in realtà è più complessa poiché più si suddividono i dati in relazioni e più è oneroso il compito di ricostruire le informazioni originarie.

 Si parla quindi di **denormalizzazione**, operazione che porta ad accorpare attributi appartenenti a relazioni diverse in un'unica relazione per garantire prestazioni migliori alle ricerche effettuate sulla base di dati. Un classico caso di denormalizzazione si ha con la creazione di attributi che contengono dati ricavati da altre relazioni.

 Come esempio consideriamo il database dei film e degli attori. Per sapere a quanti film ha partecipato un attore dovremmo ogni volta contare le tuple dalla relazione "film" associate al particolare attore. Una soluzione alternativa potrebbe essere quella di memorizzare tale numero direttamente nella relazione "attori", incrementandolo ad ogni nuovo film girato. In questo modo l'accesso a tale informazione sarebbe immediato.

 Ma si sa, tutto ha un costo! La denormalizzazione aumenta la complessità del software che utilizza il database, poiché sarà direttamente responsabile dell'aggiornamento dei dati, e di conseguenza aumentano i rischi di inconsistenza dei dati conservati. Cosa succede infatti se memorizziamo un nuovo film ma il programma, a causa di un problema, non aggiorna il contatore dei film girati nella tabella degli attori?