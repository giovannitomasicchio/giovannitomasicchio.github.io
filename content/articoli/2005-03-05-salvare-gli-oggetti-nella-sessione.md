---
title: "Salvare gli oggetti nella sessione"
author: "Giovanni Tomasicchio"
type: article
date: 2005-03-05T16:24:42+0000
description: "Salvare gli oggetti PHP nella sessione"
url: /articoli/salvare-gli-oggetti-nella-sessione/
categories:
  - Articoli
tags:
  - "database"

  
---
 Il supporto per la programmazione ad oggetti in PHP migliora di versione in versione e con PHP 5 ha raggiunta una certa maturità. Si realizzano script che maneggiano un numero sempre crescente di oggetti e per alcuni di questi si pone il problema della persistenza, ovvero della necessità di conservarne lo "stato" tra una pagina e l'altra del sito.

 Anche in questo caso ci vengono in aiuto le sessioni. E' infatti sufficiente effettuare una semplice assegnazione per memorizzare un oggetto in una sessione. Stesso discorso per recuperarne il contenuto.

 Quindi per creare (o salvare) un oggetto in sessione:

 ```php
$_SESSION['oggetto'] = new oggetto();
```

 Per recuperare invece il contenuto:

 ```php
$oggetto = $_SESSION['oggetto'];
```

 La precedente assegnazione ha un risultato diverso a seconda che stiamo usando PHP 4 o 5. In **PHP 4** $oggetto diventa una copia dell'oggetto memorizzato in sessione, tutte le modifiche che subirà nello script non verranno quindi salvate in sessione. In **PHP 5** invece $oggetto diventa un modo diverso ma equivalente di riferirsi all'oggetto salvato in sessione. Tutte le modifiche fatte su $oggetto verranno salvate in sessione. In PHP 4 si può risolvere la precedente questione facendo una assegnazione per indirizzo usando il carattere "&amp;":

 ```php
$oggetto = &$_SESSION['oggetto'];
```

 Nel seguente esempio viene mostrato un modo veloce per inizializzare o recuperare un oggetto dalla sessione. Nel caso l'oggetto non sia in sessione verrà creato e memorizzato, se invece l'oggetto è già in sessione allora verrà recuperato. In entrambi i casi l'oggetto sarà disponibile nella variabile $oggetto e tutte le modifiche effettuate su di esso verranno memorizzate automaticamente a fine script.

 ```php
// se non c'è l'oggetto in sessione allora lo creo e lo inserisco
if (!isset($_SESSION['oggetto'])) $_SESSION['oggetto'] = new oggetto();

// lego la variabile $oggetto all'oggetto in sessione
$oggetto = &$_SESSION['oggetto'];
```

 Come già detto l'uso del carattere "&amp;" non è necessario in PHP 5. Alcune precisazioni:

- E' necessario che la definizione della classe a cui appartiene l'oggetto sia posta prima delle precedenti istruzioni in modo che PHP sappia come "sbrogliare" il contenuto della sessione in un oggetto. In caso contrario si riceverà un messaggio di errore.
- Gli oggetti memorizzati in sessione non possono contenere variabili di tipo risorsa, tipo quelle restituite dalle istruzioni di apertura file, connessione al database, ecc.
- In generale è consigliabile non memorizzare nella sessione oggetti di grandi dimensioni.
 
 Un'ultima osservazione: la memorizzazione ed il recupero di oggetti nella sessione avviene attraverso la trasformazione di questi in stringhe e viceversa. Questa procedura viene detta **serializzazione** ed è lo stesso PHP ad occuparsene in maniera trasparente.