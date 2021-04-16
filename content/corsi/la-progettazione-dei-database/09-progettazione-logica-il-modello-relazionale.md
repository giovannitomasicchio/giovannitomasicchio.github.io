---
title: "Progettazione logica: il Modello Relazionale"
author: "Giovanni Tomasicchio"
type: corso
date: 2007-01-05T12:56:35+0000
description: "Il modello relazionale di una base di dati (database). La progettazione logica."
url: /corsi/la-progettazione-dei-database/progettazione-logica-il-modello-relazionale/
weight: 5
categories:
  - La progettazione dei database
  
---
Con il **Modello Relazionale** dei dati ci avviciniamo molto all'organizzazione che avrà la nostra base di dati nell'RDBMS. Il Modello Relazionale rappresenta la struttura del database attraverso un insieme di relazioni, strutture del tutto simili a comuni tabelle.

Una **relazione**, identificata da un nome, è costituita da un insieme di **tuple** (le righe della tabella) ciascuna composta da diversi **campi** (le colonne) che conservano il valore degli attributi della relazione. Come nel Modello ER, anche per i campi di una relazione va specificato un dominio di appartenenza. Inoltre uno o più campi di una relazione formano la **chiave primaria** della tabella, nello stesso modo visto per le entità del Modello ER.

E' importante notare che, anche se la relazione viene comunemente identificata con una tabella l'analogia non è totale. Infatti le tuple di una relazione non sono ordinate, non esiste la prima o l'ultima tupla, contrariamente a ciò che accade invece per le righe di una comune tabella.

In una relazione possono esserci particolari campi, che prendono il nome di **chiavi esterne** (**Foreign Key**), il cui compito è quello di realizzare legami logici tra le relazioni. Una chiave esterna è un attributo (o un insieme di attributi) di una relazione che, facendo riferimento alla chiave primaria di un'altra relazione, realizza l'associazione tra le due tabelle. Sappiamo infatti che attraverso la chiave primaria possiamo individuare una precisa tupla di una relazione. La chiave esterna di una relazione contiene dei dati che si riferiscono alla chiave primaria di un'altra relazione, creando così un legame logico tra le tuple delle due tabelle.

Come esempio ecco una rappresentazione grafica di quanto appena detto, che si riferisce al caso della rubrica personale trattato nella prima lezione. Le relazioni vengono rappresentate tramite rettangoli divisi in celle, ogni cella rappresenta un campo della relazione. Le chiavi primarie (**PK**) vengono sottolineate, mentre dalle chiavi esterne (**FK**) parte una freccia verso le chiavi primarie a cui si riferiscono.

![REL1.gif](images/stories/Corsi/progettazione_database/REL1.gif)

Per convincerci dell'efficacia della chiave esterna nella creazione di legami logici tra relazioni, basta vedere come sarebbero strutturati i dati delle due relazioni che costituiscono la nostra rubrica personale. Osserviamo in particolare come i dati della colonna "Lavoro" (FK) delle prima relazione referenziano quelli presenti nell'omonima colonna della seconda relazione.

![REL2.gif](images/stories/Corsi/progettazione_database/REL2.gif)

Il Modello Relazionale spesso va accompagnato, oltre che dalla sua rappresentazione grafica, anche da un dizionario dei dati. Tale dizionario è costituito da una descrizione testuale che ha il compito di indicare i domini di tutti gli attributi di ogni tabella con i relativi domini di appartenenza, individuare le chiavi primarie e le chiavi esterne e a volte riporta anche le descrizioni delle relazioni.

 | Persona |
|---|
| Nome | String | PK |
| Cognome | String | PK |
| Indirizzo | String |  |
| Telefono | String |  |
| Lavoro | String | FK (referenzia il campo Lavoro della relazione Lavoro) |

 | Lavoro |
|---|
| Lavoro | String | PK |
| Indirizzo | String |  |
| Telefono | String |  |