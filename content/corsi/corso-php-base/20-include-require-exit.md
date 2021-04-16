---
title: "include - require - exit"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:36:20+0000
description: "Inclusione di script PHP esterni con le funzioni include e require. Terminare uno script PHP con la funzione exit e die."
url: /corsi/corso-php-base/include-require-exit/
weight: 1
categories:
  - Corso PHP base
  
---
### include - require

Quando si realizzano applicazioni PHP di una certa complessità è naturale ritrovarsi con degli script composto da centinaia o addirittura migliaia di righe di codice, enormi files nei quali è facile perdersi. Le operazioni di modifica e manutenzione del codice diventano così sempre più onerose, si perde tempo anche a cercare quale sia la corretta riga su cui intervenire.

Un'altra inefficienza insita nella realizzazione di script monolitici, composti cioè da un unico file, sta nel fatto che spesso ci si ritrova a dover scrivere sempre le stesse sequenze di istruzioni. Immaginate ad esempio di dover realizzare una sezione privata del vostro sito. Per ogni pagina appartenente a questa sezione dovrete ripetere le istruzioni per il controllo sui diritti di accesso dell'utente, praticamente ricopiandole pagina per pagina.

La soluzione ad entrambi i problemi appena esposti risiede nella divisione del codice PHP in più file da richiamare attraverso dei comandi di inclusione.

Supponiamo di aver memorizzato le impostazioni generali del nostro sito in alcune variabili. Invece di ricopiare tali impostazioni in ciascuna pagina, le inseriamo in un unico file "impostazioni.php" che includeremo poi nelle singole pagine web. Vediamo come.

Ecco il file impostazioni.php:

 ```php
<?php
// file contenente le impostazioni generali del sito
$colore_sfondo = 'blu';
$colore_testo = 'bianco';
?>
```

Questa sarà la generica pagina del sito:

 ```php
<?php
// carico le impostazioni generali del sito
include 'impostazioni.php';

// il resto del file PHP ...

?>
```

Passando quindi il nome del file da caricare al comando include facciamo si che il PHP, prima di proseguire con l'esecuzione dello script corrente, vada ad eseguire tale file. E' come se l'include fosse sostituito dal codice dello script specificato.

L'utilità di tale approccio è evidente. Ogni pagina del sito conterrà meno righe, inoltre si potranno effettuare delle modifiche sul codice da includere e tali cambiamenti avranno immediato effetto su tutti i files. In questo modo sarà possibile anche riciclare codice da altri progetti PHP.

Nell'esempio precedente abbiamo specificato il file da caricare passando semplicemente il suo nome al comando include. Se tale file non è presente nella stessa cartella dello script che l'ha richiesto allora andrà specificato il percorso corretto, relativo o assoluto.

E' interessante notare come un file incluso possa a sua volta caricare un ulteriore file: il file primo.php include il file secondo.php che a sua volta include il file terzo.php. In questi casi è molto importante ricordarsi che se si usano percorsi relativi questi dovranno essere calcolati sempre relativamente al primo file e non relativamente al chiamante. Facciamo un esempio: il file primo.php include il file secondo.php presente in una sottocartella "librerie". A sua volta il file secondo.php include il file terzo.php anch'esso presente nella cartella "librerie". I percorsi relativi da utilizzare saranno i seguenti:

primo.php

 ```php
<?php
include ('librerie/secondo.php');
// il resto del file
?>
```

secondo.php

 ```php
<?php
include 'librerie/terzo.php';
// il resto del file
?>
```

Questo avviene perché tutto quanto il codice incluso, anche quello presente nel file terzo.php, va ad inserirsi nel file primo.php ed è quindi relativamente a questo che vanno calcolati tutti i percorsi.

Notiamo inoltre che include non è una vera funzione ma un costrutto PHP. Ecco perché, proprio come accade per il comando echo, l'uso delle parentesi tonde è facoltativo.

Esistono altri tre costrutti PHP che permettono l'inclusione di codice presente in altri files. Sono caratterizzati da un diverso comportamento nel caso in cui il file richiesto non venga trovato e in caso di ripetute inclusioni dello stesso file. La seguente tabella schematizza le loro caratteristiche.

 | Nome costrutto | se il file richiesto non viene trovato | permette di caricare  più volte un file |
|---|---|---|
| include | viene segnalato l'errore | si |
| include\_once | viene segnalato l'errore | no |
| require | viene segnalato l'errore e termina l'esecuzione dello script | si |
| require\_once | viene segnalato l'errore e termina l'esecuzione dello script | no |

### exit

Abbiamo visto quindi come i costrutti include e require, con le loro varianti, permettano di "allungare" il codice di uno script attraverso l'inclusione di codice proveniente da altri files. Il PHP mette a disposizione il comando exit per ottenere l'effetto opposto, ovvero terminare l'esecuzione di uno script. Le successive istruzioni verranno ignorate e lo script risulterà virtualmente tagliato. Vediamo un semplice esempio:

 ```php
<?php
echo 'unico testo che leggerai <br>';

exit ('un eventuale messaggio di chiusura');

echo 'questo testo non verrà mai mostrato';
?>
```

La funzione exit accetta anche un eventuale testo da mostrare prima che l'esecuzione dello script venga interrotta. Se questo testo non viene fornito allora l'uso delle parentesi è facoltativo. Questo perché anche exit non è una funzione ma un costrutto.

Infine citiamo l'esistenza di die, un altro comando che permette la terminazione di uno script equivalente ad exit.