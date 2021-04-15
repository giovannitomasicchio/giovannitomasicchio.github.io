---
title: "L'applicazione di esempio"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:08:18+0000
description: "Presentazione dell'applicazione MVC che si sta per costruire con lo Zend Framework"
url: /corsi/corso-zend-framework/applicazione-di-esempio/
categories:
  - Introduzione allo Zend Framework
  
---
Ora che tutti i pezzi sono a posto, possiamo procedere con la costruzione dell'applicativo con Zend Framework. Prima, però, diamo uno sguardo all'applicazione che stiamo per realizzare.

Stiamo per costruire un sistema molto semplice che permetta di catalogare una collezione di CD. La pagina principale presenterà la nostra collezione e permetterà di aggiungere, modificare e cancellare CD.

Così come avviene in tutti i progetti software, è utile elaborare un piano di lavoro. Avremo bisogno di quattro pagine sul nostro sito web:

 | Home page | Questa mostrerà la lista dei nostri album e fornirà i link per modificarli e cancellarli. Inoltre fornirà un link per aggiungere nuovi album. |
|---|---|
| Add New Album | Questa pagina darà la possibilità di aggiungere un nuovo album. |
| Edit Album | Questa pagina permetterà di modificare un album. |
| Delete Album | Questa pagina chiederà confermerà della cancellazione di un album per poi procedere con la cancellazione. |

Avremo bisogno inoltre di salvare i dati in un database. Ci servirà una sola tabella con questi campi:

 | Nome campo | Tipo | Nullo? | Note |
|---|---|---|---|
| id | integer | No | Primary Key, auto increment |
| artist | varchar(100) | No |  |
| title | varchar(100) | No |  |