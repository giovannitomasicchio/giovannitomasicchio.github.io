---
title: "Cicli e iterazioni: for e foreach"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:34:09+0000
description: "Cicli e iterazioni negli script PHP, i costrutti FOR e FOREACH"
url: /corsi/corso-php-base/cicli-e-iterazioni-for-e-foreach/
categories:
  - Corso PHP base
  
---
Avete presente la pagina principale di PHPnews.it? Nella colonna centrale sono presenti le ultime 10 news. Ciascuna è composta da un titolo, il testo della news e la data di pubblicazione. Sicuramente lo script PHP che si occupa di costruire questa pagina avrà delle istruzioni dedicate alla visualizzazione di una singola news. Fortunatamente per visualizzare 10 news non è necessario ricopiare tali righe per 10 volte nello script, è sufficiente invece istruire il PHP affinché la loro esecuzione venga ripetuta 10 volte.

Quello citato è solo un esempio di situazione nella quale ci si trova a ripetere un determinato compito per più volte (iterazioni). Ad ogni ciclo si eseguono le stesse istruzioni, quello che cambia sono i dati su cui agiscono tali istruzioni. Nel caso precedente ogni ciclo mostra un titolo, un testo ed una data differenti ma il codice che li visualizza è sempre lo stesso.

Diversi sono i costrutti che permettono di iterare più volte un gruppo di istruzioni. In questa lezione ci occuperemo dei cicli **for** e **foreach**.

### Ciclo for

Il ciclo for viene comunemente usato per realizzare delle iterazioni basate su un indice numerico. Questo indice viene prima impostato ad un valore iniziale, di solito 0 o 1. Ad ogni iterazione viene poi incrementato fino al raggiungimento di un valore finale, evento che determina l'interruzione del ciclo.

In generale il ciclo for è costituito da un costrutto del tipo

for (espressione1 ; espressione2 ; espressione3)

seguito dal gruppo di istruzioni che andranno ripetute, raggruppate da una coppia di parentesi graffe.

L'espressione1 viene eseguita una sola volta prima della prima iterazione. Viene utilizzata comunemente per inizializzare l'indice di cui parlavamo. L'espressione2 viene invece valutata all'inizio di ogni ciclo. Se assume il valore true il ciclo viene effettuato. E' questa l'espressione che verifica l'eventuale raggiungimento del valore finale dell'indice. L'espressione3 viene invece eseguita al termine di ogni iterazione. Viene usata comunemente per incrementare il valore dell'indice.

Adesso vediamo un esempio concreto, utilizzato per la visualizzazione del quadrato dei numeri da 1 a 10.

 ```php
<?php
for ($i=1 ; $i <= 10 ; $i++)
{
    $quadrato = $i*$i;
    echo "Il quadrato di $i è $quadrato <br>\n";
}
?>
```

La variabile $i viene utilizzata come indice per questo ciclo. Il suo valore è impostato inizialmente ad 1 (espressione1: $i=1) e viene incrementato ad ogni iterazione (espressione3: $i++). Prima di ciascuna iterazione viene valutata l'espressione2 $i &lt;= 10 così che quando $i supera il 10 il ciclo viene interrotto. Le istruzioni ad essere ripetute sono quelle racchiuse dalle parentesi graffe. Sono quelle che si occupano di calcolare e mostrare il quadrato dei numeri. Notiamo come queste istruzioni utilizzano direttamente l'indice $i per eseguire tale compito. L'utilizzo del ciclo for è infatti consigliato proprio quando le istruzioni da iterare fanno uso di un indice numerico.

Vediamo ora come scorrere gli elementi di un array e mostrarli all'utente.

 ```php
<?php
// array da scorrere
$animali = array('cane','gatto','topo','elefante');

// notare la prima espressione che inizializza sia $i che $n
for ($i = 0, $n = count($animali) ; $i < $n ; $i++)
{
    echo $animali[$i] , '<br>';
}
?>
```

la particolarità di questo esempio sta sopratutto nella prima espressione. Tale espressione infatti inizializza non solo l'indice $i a 0 ma assegna ad $n il numero degli elementi dell'array da scorrere ($n = count($animali)). La seconda espressione usa proprio questo valore come limite per l'indice $i. Notiamo come anche in questo esempio $i venga direttamente impiegato nell'istruzione da iterare.

### Ciclo foreach

Il ciclo for, anche se molto potente e versatile, può risultare piuttosto macchinoso nell'impiego di semplici compiti come l'attraversamento di un array. In questi casi risulta più comodo l'uso del ciclo foreach. Tale ciclo ha una sintassi del tipo:

foreach ($array\_da\_attraversare as $valore\_elemento)

{

// istruzioni da iterare

}

Le istruzioni raggruppate nelle parentesi graffe verranno ripetute tante volte quanti sono gli elementi presenti nell'array $array\_da\_attraversare. Ad ogni iterazione verrà letto un valore presente in tale array e copiato nella variabile $valore\_elemento. Vediamo un esempio:

 ```php
<?php
$colori = array('bianco','rosso','verde','blu','giallo','nero');

foreach ($colori as $colore)
{
    echo $colore , '<br>';
}
?>
```

Ad ogni iterazione la variabile $colore assumerà il valore di un elemento presente nell'array $colori. E' chiaro quindi che un ciclo foreach serve a iterare delle operazioni che vanno eseguite sugli elementi di un array. Vediamo invece che succede se nell'esempio precedente usiamo il ciclo foreach per effettuare una modifica ai valori contenuti nell'array.

 ```php
<?php
$colori = array('bianco','rosso','verde','blu','giallo','nero');

foreach ($colori as $colore)
{
    $colore = strtoupper($colore);
}

foreach ($colori as $colore)
{
    echo $colore , '<br>';
}
?>
```

Abbiamo attraversato 2 volte l'array $colori. Una volta per modificare ogni suo elemento in modo che tutte le lettere fossero maiuscole (strtoupper). La seconda volta invece è servita a mostrarne il contenuto che però risulta inalterato! Perché? La causa di tale comportamento è riconducibile al fatto che la variabile $colore contiene solo la copia di un valore dell'array $colori e pertanto non è direttamente collegato ad esso. La modifica alla variabile $colore quindi non influisce sul relativo valore dell'array $colori. Per questo motivo i programmatori PHP fanno spesso ricorso al ciclo for per effettuare delle modifiche agli elementi di un array.

Con PHP 5 però le cose sono cambiate. Infatti è sufficiente porre il carattere &amp; prima della variabile $colore nell'espressione del ciclo foreach per far si che ogni modifica effettuata su di essa venga in realtà effettuata sul relativo valore dell'array. L'esempio seguente è identico al precedente, tranne che per il carattere &amp; posto a sinistra della variabile $colore. Questa volta le modifiche ai valori dell'array avranno effetto.

 ```php
<?php
$colori = array('bianco','rosso','verde','blu','giallo','nero');

foreach ($colori as &$colore)
{
    $colore = strtoupper($colore);
}

unset($colore);

foreach ($colori as $colore)
{
    echo $colore , '<br>';
}
?>
```

**Attenzione**: l'utilizzo del carattere &amp; fa si che alla fine del primo ciclo foreach la variabile $colore non è semplicemente una copia dell'ultimo elemento dell'array bensì è l'utlimo elemento dell'array. Per questa ragione dobbiamo "distruggere" $colore con l'istruzione unset() prima di riutilizzare questa variabile nel secondo ciclo. In caso contrario il secondo foreach andrebbe a sovrascrivere ad ogni iterazione l'utlimo elemento di $colori con ciascuno degli altri elementi dello stesso array.

 

Mostriamo adesso un ultima variante, non meno importante, della sintassi del ciclo foreach che permette di scorrere un array e recuperarne gli elementi sotto forma di coppie chiave =&gt; valore. Ecco la struttura tipica:

 ```php
foreach ($array_da_attraversare as $chiave_elemento => $valore_elemento)
{
   // istruzioni da iterare
}
```

Il funzionamento è lo stesso della struttura vista in precedenza. L'unica differenza è che adesso anche le chiavi di ciascun elemento vengono lette e copiate nella variabile $chiave\_elemento. Vediamo un semplice esempio:

 ```php
<?php
$film = array('titolo' => 'Via col Vento', 'anno' => 1939,'regista' => 'Victor Fleming');

foreach ($film as $info => $valore)
{
    echo "$info: $valore <br>\n";
}
?>
```

Anche con questa sintassi è possibile usare il carattere &amp; per far si che la variabile $valore sia legata direttamente al corrispondente valore dell'array da attraversare in modo che questo possa essere modificato.

Concludiamo risolvendo il problema posto all'inizio della Lezione 8: mostrare un elenco di recensioni di films. In quella lezione avevamo detto che la struttura migliore per contenere questi dati sarebbe stata un array multilivello, ovvero un array i cui elementi fossero a loro volta degli array contenenti i dati di ciascun film. Vediamo come usare un ciclo foreach per questo tipo di struttura.

 ```php
<?php
$films = array(array('titolo' => 'Via col Vento',
                     'anno' => 1939,
                     'regista' => 'Victor Fleming'),
               array('titolo' => 'Star Wars',
                     'anno' => 1977,
                     'regista' => 'George Lucas'));

foreach ($films as $film)
{
    echo 'Titolo film: ' , $film['titolo'];
    echo '<br>';
    echo 'Anno: ' , $film['anno'];
    echo '<br>';
    echo 'Regista: ' , $film['regista'];
    echo '<br><br>';
}
?>
```

Notiamo come la variabile $film sia ancora un array e pertanto i dati in esso contenuti sono accessibili attraverso l'uso delle diverse chiavi (titolo, anno, regista).

Tale approccio è comunemente usato anche nell'estrazione di dati da un database. Questi infatti spesso sono disponibili ad uno script PHP proprio sotto forma di array multilivello.