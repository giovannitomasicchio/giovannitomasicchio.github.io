---
title: "I form e l'array $_POST"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:35:08+0000
description: "Gestire i form HTML con PHP, recuperare l'input dell'utente dall'array $_POST"
url: /corsi/corso-php-base/i-form-e-larray-$_post/
categories:
  - Corso PHP base
  
---
Già dalle prime lezioni abbiamo subito imparato a mostrare, con la funzione echo, dei messaggi all'utente ovvero a generare un **output**. Adesso vediamo invece come acquisire informazioni dagli utenti, a gestire cioè l'**input** dell'utente. Un sito web interattivo deve saper raccogliere dai suoi visitatori dati di diversa natura e per gli scopi più disparati.

Pensate ad esempio ad un forum ed alle attività ad esso collegate: registrazione, log-in, invio messaggi, ecc. Tutte queste operazioni sono caratterizzate dall'invio, da parte dell'utente, di diverse informazioni. Non solo testi (il nome, la password, i messaggi, ecc.) ma anche diverse opzioni specificate attraverso l'uso di caselle di controllo (check box), pulsanti di scelta (radio buttons), menu a discesa (drop down menu) e altro ancora.

Questi strumenti vengono messi a disposizione dall'HTML attraverso diversi tag (input, select, ecc.) contenuti all'interno di un tag **form**. I dati inseriti dall'utente vengono inviati ad una pagina specificata nell'attributo "action" dello stesso tag form. Il PHP provvederà a recuperarli ed a inserirli in un array di nome **$\_POST** come coppie chiave -&gt; valore. Questo processo avverrà in maniera trasparente ed automatica.

L'array $\_POST ha una particolarità ovvero non rispetta le normali regole di visibilità delle variabili (vedi Lezione 19) poiché è accessibile da qualsiasi parte del nostro script, anche all'interno delle funzioni. Per questo motivo lo si definisce **superglobale**.

Il primo esempio che vediamo è costituito da 2 files. Il primo è una normale pagina HTML che contiene il form in cui inserire i dati. Questi verranno inviati al secondo file, che chiameremo prova.php

Ecco l'HTML presente nel primo file. Si noti il valore dell'attributo action del tab form.

 ```php
<form name="form1" method="post" action="prova.php">
   <p>Inserisci il tuo nome: </p>
   <p>
      <input type="text" name="nome_utente">
      <input type="submit" value="Invia">
   </p>
</form>
```

Segue il codice della pagina prova.php, che riceverà i dati inviati dall'utente e che verranno inseriti dal PHP all'interno dell'array $\_POST.

 ```php
<?php
echo('Il tuo nome è ' . $_POST['nome_utente']);
?>
```

Si noti come in questo ultimo script la funzione echo riesca a mostrare il nome inviato dall'utente semplicemente utilizzando l'array $\_POST. La chiave usata per ottenere il nome dell'utente è "nome\_utente" ovvero il valore dell'attributo "name" del tag input.

In realtà non è necessario che la pagina che realizza il form e quella che raccoglie i dati inviati dall'utente siano distinte. Se l'attributo "action" viene lasciato vuoto, i dati verranno inviati alla stessa pagina che ha generato il form.

Il seguente esempio funziona proprio in questo modo: crea un form particolarmente complesso e gestisce anche i dati che verranno inviati:

 ```php
<form name="form1" method="post" action="">
  <p> campo di testo: <br>
    <input type="text" name="textfield">
  </p>
  <p>area di testo:<br>
    <textarea name="textarea"></textarea>
  </p>
  <p>
    <input type="checkbox" name="checkbox" value="checkbox">
    casella di controllo </p>
  <p>
    <label>
    <input type="radio" name="radio" value="1">
    Pulsante di scelta 1</label>
    <br>
    <label>
    <input type="radio" name="radio" value="2">
    Pulsante di scelta 2</label>
    <br>
  </p>
  <p>Menu:<br>
    <select name="select">
      <option value="1">Opzione 1</option>
      <option value="2">Opzione 2</option>
      <option value="3">Opzione 3</option>
      <option value="4">Opzione 4</option>
    </select>
  </p>
  <p>Elenco:<br>
    <select name="select2[]" size="5" multiple>
      <option value="1">Opzione 1</option>
      <option value="2">Opzione 2</option>
      <option value="3">Opzione 3</option>
      <option value="4">Opzione 4</option>
    </select>
  </p>
  <p>
    <input type="submit" name="Submit" value="Invia">
  </p>
</form>
<?php
if($_POST)
{
    // converto alcuni caratteri illeciti per l'HTML
    $testo = htmlspecialchars($_POST['textfield']);
    echo('campo di testo: ' . $testo );
    echo('<br>');

    // converto alcuni caratteri illeciti per l'HTML
    $testo = htmlspecialchars($_POST['textarea']);
    // converto gli 'a capo' con dei <br>
    $testo = nl2br($testo);
    echo('area di testo: ' .$testo);
    echo('<br>');

    // se la casella non è stata selezionata
    // allora $_POST['checkbox'] non sarà settato
    $casella = isset($_POST['checkbox']) ? 'selezionata' : 'non selezionata';
    echo('casella di controllo: ' . $casella);
    echo('<br>');

    // se neanche un bottone è stato selezionato
    // allora $_POST['radio'] non sarà settato
    if(isset($_POST['radio']))
        $pulsante = 'selezionato il numero ' . $_POST['radio'];
    else
        $pulsante = 'nessun pulsante selezionato';
    echo('Pulsante di scelta: ' . $pulsante);
    echo('<br>');

    // un menu restituisce sempre un valore
    echo('Menu: ' .$_POST['select']);
    echo('<br>');

    // un elenco a scelta multipla restituisce un array di valori
    // se nessun valore è stato selezionato però
    // $_POST['select2'] non sarà settato
    if(isset($_POST['select2']))
        $valori_selezionati = implode($_POST['select2'],', ');
    else
        $valori_selezionati = 'Nessun valore selezionato';
    echo('Elenco: ' . $valori_selezionati);
    echo('<br>');

}
else
{
    echo("Compila il modulo e premi il bottone 'Invia'");
}
?>
```

Con l'istruzione if($\_POST) verifichiamo se il form è stato inviato (vedi Lezione 12 a proposito della conversione array -&gt; booleano). In caso positivo attraverso l'array $\_POST recuperiamo le informazioni. Si noti il differente approccio usato nel trattamento dei diversi tipi di controlli.

Per mostrare il contenuto dell'area di testo, che permette all'utente di inserire un testo anche su più linee (con degli "a capo") abbiamo usato la funzione **nl2br**. Questa funzione accoda ad ogni carattere di fine riga (gli "a capo") il tag "&lt;br&gt;" in modo che nella rappresentazione del testo in una pagina HTML vengano rispettate le interruzioni di riga. Se non avessimo usato tale funzione il testo sarebbe stato visualizzato su un'unica riga.

La funzione **htmlspecialchars** invece è servita a convertire eventuali caratteri non leciti per l'HTML nella loro corretta rappresentazione. Ad esempio il simbolo di maggiore (&gt;) non può essere riportato in una pagina HTML poiché verrebbe confuso con la chiusura di un tag. Per essere mostrato quindi si fa ricorso ai caratteri "&amp;gt;".

Altre considerazioni sullo script si trovano nei commenti. Si noti in particolare che per gestire un elenco a scelta multipla è necessario che il suo nome finisca con una coppia di parentesi quadre. Nel nostro esempio infatti abbiamo usato il nome select2\[\]. Quando poi ci si riferisce ad esso attraverso l'array $\_POST non si usano più tali parentesi. Inoltre il valore di $\_POST\['select2'\] sarà un array.

Concludiamo con un accenno sulle problematiche legate alla gestione di dati provenienti dall'utente. Attraverso l'array $\_POST infatti giungono allo script dei dati che possono essere manipolati ad arte per ottenere degli effetti nocivi oppure più comunemente possono contenere dei dati errati di un utente sbadato. In tutti i casi quindi è buona norma effettuare una serie di controlli prima di utilizzare il contenuto di $\_POST in una operazione importante (ad es. un log-in).