---
title: "Introduzione al concetto di database Relazionale"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:54:01+0000
description: "Introduzione ai database relazionali"
url: /corsi/la-progettazione-dei-database/introduzione-al-concetto-di-database-relazionale/
categories:
  - La progettazione dei database
  
---
Al giorno d'oggi le basi di dati sono talmente diffuse che è difficile trascorrere una giornata senza aver, anche inavvertitamente, interagito con una di esse. Prelevare contanti da un bancomat, acquistare un libro, prenotare un volo, fare shopping on-line e persino fare la spesa, sono tutte operazioni che probabilmente coinvolgono un operatore o un programma informatico che interagiscono con una base di dati.

Proviamo a dare una definizione di **base di dati**:

*Una base di dati è un insieme di dati correlati, organizzati in modo che possano essere manipolati e che possano evolvere nel tempo.*

Un semplice esempio di base di dati è l'elenco telefonico. Una pagina di un elenco telefonico cittadino può presentare più o meno i seguenti dati:

![rubrica1.gif](images/stories/Corsi/progettazione_database/rubrica1.gif)

Si noti che i dati dell'esempio sono tutti correlati tra di loro e sono organizzati in ordine alfabetico per facilitarne la ricerca. I titoli delle colonne (Cognome, Nome, Indirizzo e Numero di Telefono) vengono chiamati **metadati**, mentre tutti gli altri sono i dati veri e propri. Ogni "riga di dati" prende il nome di tupla o record o riga, mentre tutto l'insieme forma una **tabella**.

Ora, proviamo ad ampliare l'esempio supponendo di voler costruire una base di dati per la gestione di una rubrica personale. Oltre al nome, al cognome e all'indirizzo di ogni persona vogliamo sapere il posto in cui lavora con relativo indirizzo e recapito telefonico. La tabella diventerebbe:

![rubrica2.gif](images/stories/Corsi/progettazione_database/rubrica2.gif)

Paperon De Paperoni vive di rendita e non ha bisogno di lavorare.

Anche se a prima vista corretta, una memorizzazione del genere non è conveniente: ogni posto di lavoro ha sempre gli stessi recapiti e Brambilla Michele e Rossi Luca lavorano presso la stessa banca. Tale soluzione spreca più spazio di quanto sia davvero necessario e in più ci complica la vita nel caso in cui vogliamo modificare i dati. Infatti se un'azienda cambia numero di telefono dovremo cambiare il numero di telefono in tutte le righe in cui è presente.

Un modo più intelligente di memorizzare tale base di dati è il seguente:

![rubrica3.gif](images/stories/Corsi/progettazione_database/rubrica3.gif)

Abbiamo creato due tabelle **associate** tra di loro. Brambilla Michele e Rossi Luca saranno associati entrambi alla riga relativa a Banca Europe, De Magistris Alessandro sarà associato alla riga della KL Elettronix, Paperon. De Paperoni invece non sarà associato a nessun posto di lavoro. Si noti che adesso un'eventuale modifica ai dati di un posto di lavoro si riflettere in modo automatico e trasparente su tutte le persone che vi lavorano.

Da questo semplice esempio è evidente come sia importante progettare nel modo corretto una base di dati. L'organizzazione dei dati ha un'importanza cruciale che si ripercuote sulla semplicità di gestione e sulle prestazioni del sistema. Se la progettazione viene fatta in modo sbagliato o, peggio ancora, non viene per nulla effettuata si rischia di ottenere un database di difficile utilizzo e che farà sicuramente perdere molto tempo nella stesura delle funzioni per la manipolazione dei dati.