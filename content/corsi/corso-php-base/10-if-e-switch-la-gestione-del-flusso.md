---
title: "if e switch: la gestione del flusso"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:33:06+0000
description: "Le strutture per gestire il flusso di uno script PHP, IF e SWITCH"
url: /corsi/corso-php-base/if-e-switch-la-gestione-del-flusso/
categories:
  - Corso PHP base
  
---
 Gli esempi visti finora erano composti da una sequenza di istruzioni da eseguire una dopo l'altra, dalla prima all'ultima. Raggiunta la fine dello script il PHP ci ha mostrato i risultati di tale esecuzione. Il **flusso del programma**, ovvero l'ordine di esecuzione delle istruzioni, procedeva quindi in maniera lineare.

 Ci sono però diverse situazioni in cui questo modo di procedere non va bene. Immaginiamo ad esempio di dover realizzare una pagina web in cui gli utenti registrati possono scaricare un certo file mentre gli utenti non registrati devono ricevere un avviso. E' evidente che il nostro script si troverà ad un bivio: se l'utente è registrato deve mostrare un collegamento al file da scaricare, altrimenti deve visualizzare un messaggio. Alcune istruzioni dovranno essere eseguite in un caso ma non nell'altro e viceversa. Come facciamo a gestire questa situazione? Quello che segue è lo schema generale di una struttura messa a disposizione dal PHP per risolvere il problema:

 ```
<pre class="brush: php">
if(condizione da verificare)
{
    // istruzioni da eseguire in caso positivo
}
else
{
    // istruzioni da eseguire in caso contrario
}
```

 Potremmo leggerla in questo modo: se (if) la condizione tra parentesi tonde è vera allora esegui il primo gruppo di istruzioni, delimitate dalle parentesi graffe. Altrimenti (else) esegui il secondo gruppo di istruzioni, sempre delimitate dalle parentesi graffe.

 Il PHP quindi valuterà se ciò che abbiamo messo tra parentesi tonde, accanto alla parola if, è vero (true) o falso (false). Ricordate le variabili booleane? Nel primo caso verrà eseguito solo il primo gruppo di istruzioni, in caso contrario solo il secondo gruppo.

 Le parentesi graffe hanno il compito di raggruppare le istruzioni. Le incontreremo anche in altri contesti nelle prossime lezioni ma la loro funzionalità sarà la stessa. Se il gruppo di istruzioni è in realtà composto solo da un unico comando allora queste parentesi sono facoltative.

 Spesso questa struttura si usa anche senza il ramo else:

 ```
<pre class="brush: php">
if(condizione da verificare)
{
    // istruzioni da eseguire in caso positivo
}
```

 In questi casi il gruppo di istruzioni che segue verrà eseguito solo nel caso in cui la condizione valga true. In caso contrario tali istruzioni verranno ignorate e il PHP continuerà ad eseguire le rimanenti righe.

 Adesso vediamo un esempio più concreto, nel quale viene risolto il problema dell'accesso al file per l'utente registrato posto all'inizio della lezione:

 ```
<pre class="brush: php">
<?php
/* ATTENZIONE !!! In un vero programma questa istruzione
non esisterebbe ma il valore di $utente_registrato verrebbe ricavato
da determinati controlli sull'identità dell'utente */

$utente_registrato = true;

if($utente_registrato)
{
    // metto qui le istruzioni da eseguire per un utente registrato
    echo 'Clicca <a href="file.zip">qui</a> per scaricare il file';
}
else
{
    // metto qui le istruzioni da eseguire per un utente ospite
    echo 'Non puoi scaricare il file, devi prima registrarti!';
}
?>
```

 Il commento iniziale è abbastanza chiaro. In un vero script per verificare se un utente è registrato o meno dovremo fare una serie di controlli. La riga $utente\_registrato = true serve proprio a simulare l'esito di questi controlli memorizzato in una variabile booleana.

 La struttura if controlla il valore di $utente\_registrato. Se vale true verrà mostrato il link da cui scaricare il file. Se vale false l'utente riceverà un messaggio esplicativo.

 La condizione controllata dall'if non deve essere necessariamente una variabile, ma può essere una qualunque espressione il cui risultato sia però true o false. Il seguente esempio chiarirà il concetto.

 ```
<pre class="brush: php">
<?php
$eta_utente = 21;

if($eta_utente > 17)
{
    echo 'Clicca <a href="file.zip">qui</a> per scaricare il file';
}
else
{
    echo 'Non puoi scaricare il file, non sei maggiorenne!';
}
?>
```

 Il PHP per prima cosa valuterà l'espressione $eta\_utente &gt; 17. Se il valore di $eta\_utente è maggiore di 17 allora questa espressione varrà true, altrimenti varrà false. Anche in questo caso quindi il costrutto if decide quale istruzioni eseguire a seconda del valore (vero o falso) messo nelle parentesi tonde. Stavolta però questo valore è ottenuto dalla valutazione di una espressione. Il simbolo &gt; indica il maggiore ed è uno degli operatori che possiamo utilizzare in una espressione. Nella prossima lezione verranno mostrati gli altri operatori più comunemente usati in PHP.

 Analizziamo ora il seguente esempio:

 ```
<pre class="brush: php">
<?php
$giorno_della_settimana = 2;

if($giorno_della_settimana == 1)
{
    echo 'Oggi è lunedì';
}
if($giorno_della_settimana == 2)
{
    echo 'Oggi è martedì';
}
if($giorno_della_settimana == 3)
{
    echo 'Oggi è mercoledì';
}
?>
```

 Notiamo l'uso del doppio simbolo di uguale "==" che serve ad effettuare un confronto tra il valore contenuto nella variabile $giorno\_della\_settimana ed i valori 1, 2 e 3. Ricordiamo che il semplice "=" invece serve ad assegnare un valore ad una variabile.

 E' chiaro che delle 3 strutture if precedenti solo una potrà andare a buon fine. Inoltre se la prima è verificata le altre sicuramente non lo saranno! Nonostante ciò tutti e tre gli if vengono eseguiti. Per ovviare si può usare la seguente variante:

 ```
<pre class="brush: php">
<?php
$giorno_della_settimana = 2;

if($giorno_della_settimana == 1)
{
    echo 'Oggi è lunedì';
}
elseif($giorno_della_settimana == 2)
{
    echo 'Oggi è martedì';
}
elseif($giorno_della_settimana == 3)
{
    echo 'Oggi è mercoledì';
}
?>
```

 Notiamo che i successivi if sono stati sostituiti da degli elseif che potremmo leggere "altrimenti se". Questa volta, se una delle 3 condizioni viene verificata, le successive non verranno valutate.

 Un simile risultato potrebbe essere raggiunto usando una struttura switch. Vediamo come:

 ```
<pre class="brush: php">
<?php
$giorno_della_settimana = 2;

switch ($giorno_della_settimana) {
    case 1:
    echo 'Oggi è lunedì';
    break;

    case 2:
    echo 'Oggi è martedì';
    break;

    case 3:
    echo 'Oggi è mercoledì';
    break;

    default:
    echo 'giovedì, venerdì sabato o domenica?';
    break;
}
?>
```

 La variabile passata a switch viene confrontata con diversi valori posti accanto ad ogni clausola case. Se viene riscontrata una corrispondenza allora vengono eseguite tutte le istruzioni comprese tra il case corrente ed il successivo break.

 La struttura switch si differenzia dall'if principalmente per 3 motivi:

- le condizioni che determinano l'esecuzione o meno di un blocco di istruzioni possono essere solo delle uguaglianze;
- il PHP valuta l'espressione da confrontare con le diverse clausole *case* una sola volta;
- è previsto un blocco opzionale di istruzioni, comprese tra il comando default ed il successivo break, che verrà eseguito nel caso in cui nessun'altra condizione è stata verificata.
 
 Con un if è comunque possibile gestire tutte le evenienze gestite da uno switch, anche il blocco di default. Lo script che segue adotta infatti un ulteriore else posto alla fine proprio per contemplare l'evenienza che nessuna delle precedenti condizioni sia soddisfatta.

 ```
<pre class="brush: php">
<?php
$giorno_della_settimana = 2;

if($giorno_della_settimana == 1)
{
    echo 'Oggi è lunedì';
}
elseif($giorno_della_settimana == 2)
{
    echo 'Oggi è martedì';
}
elseif($giorno_della_settimana == 3)
{
    echo 'Oggi è mercoledì';
}
else
{
    // equivalente al blocco "default" dello switch
    echo 'giovedì, venerdì sabato o domenica?';
}
?>
```