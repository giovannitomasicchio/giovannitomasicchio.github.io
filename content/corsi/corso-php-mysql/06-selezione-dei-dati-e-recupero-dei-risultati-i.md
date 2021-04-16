---
title: "Selezione dei dati e recupero dei risultati - I"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:02:12+0000
description: "Leggere i dati di una tabella MySQL con PHP. Eseguire una SELECT con PHP e MySQL."
url: /corsi/corso-php-mysql/selezione-dei-dati-e-recupero-dei-risultati-i/
weight: 5
categories:
  - Corso PHP MySQL
  
---
Adesso che sappiamo inserire dei dati in una tabella MySQL vediamo come poterli recuperare e mostrare all'utente. Si tratta ancora una volta di eseguire una query, in particolare bisogna lanciare una SELECT, ma ora dobbiamo occuparci anche di un altro compito, ovvero il recupero del **set di risultati** (**result set**) ottenuto.

Come visto in precedenza la funzione mysql\_query() ci permette di inviare una query al server MySQL. Se ad esempio inviando una query del tipo:

 ```sql
SELECT id, nome FROM utenti
```

chiediamo al server i dati presenti nei campi "id" e "nome" della tabella "utenti". Ma dove vengono inviati questi dati? Il comportamento della funzione mysql\_query() prevede che tutti i record ottenuti vengano inviati immediatamente da MySQL a PHP. Quest'ultimo li conserva nella sua memoria (in un "buffer", da cui il nome *buffered query*) ma non li rende direttamente disponibili allo script. In pratica mysql\_query() non restituisce i record estratti dal database, ma ci consegna una semplice "ricevuta" che prende il nome di "MySQL result resource". Per prelevare i dati è necessaria un'ulteriore procedura detta **fetch**. Grazie alla "ricevuta" fornitaci da mysql\_query() possiamo chiamare una delle funzioni di fetch e ottenere finalmente, un record alla volta, i dati appartenenti al set di risultati.

Ci sono diverse funzioni di fetch disponibili, che si distinguono per la modalità con cui restituiscono il record estratto:

- **mysql\_fetch\_row()** - restituisce un record del set di risultati all'interno di un array con indici numerici;
- **mysql\_fetch\_assoc()** - restituisce un record del set di risultati all'interno di un array associativo;
- **mysql\_fetch\_object()** - restituisce un record del set di risultati all'interno di un oggetto con proprietà che corrispondono ai campi del record.
 
Disponibile inoltre la funzione **mysql\_fetch\_array()** che cambia il suo comportamento in funzione del secondo parametro passatogli:

- mysql\_fetch\_array($ricevuta, **MYSQL\_ASSOC**)
   restituisce un record del set di risultati all'interno di un array associativo. Equivalente a mysql\_fetch\_assoc()
- mysql\_fetch\_array($ricevuta, **MYSQL\_NUM**)
   restituisce un record del set di risultati all'interno di un array con indici numerici - Equivalente a mysql\_fetch\_row()
- mysql\_fetch\_array($ricevuta, **MYSQL\_BOTH**)
   restituisce un record del set di risultati all'interno di un array con indici numerici e associativi - Comportamento di default in caso di assenza del secondo parametro.
 
Se si vogliono recuperare tutti i record restituiti da una query le funzioni di fetch devono essere richiamata tante volte quanti sono i record. Queste funzioni infatti restituiscono, in formati diversi, solo un record e non tutto il set di risultati. Ad ogni nuova esecuzione, queste funzioni estraggono il record successivo, fino all'ultimo. Raggiunto l'ultimo record un'ulteriore esecuzione di queste funzioni restituisce il valore FALSE, segnalando quindi che non ci sono più dati da prelevare.

Estratti tutti i risultati non bisogna dimenticare che PHP sta conservando ancora nella sua memoria tutti i record restituiti dalla query. E' quindi possibile (e consigliato) liberare tale memoria, attraverso la funzione **mysql\_free\_result()**.

Tenendo presente questo comportamento delle istruzioni di fetch è facile realizzare un ciclo WHILE che ne iteri l'esecuzione fino all'estrazione completa dei record. I seguenti esempi mostrano proprio questa tecnica, applicata alle diverse funzioni di fetch.

**- mysql\_fetch\_row**

 ```php
$ricevuta = mysql_query("SELECT id, nome FROM utenti ");

while ($row = mysql_fetch_row($ricevuta)) {
	echo 'ID: ', $row[0] , ' Nome: ', $row[1] , "\n";
}
```

**- mysql\_fetch\_assoc**

 ```php
$ricevuta = mysql_query("SELECT id, nome FROM utenti ");

while ($row = mysql_fetch_assoc($ricevuta)) {
	echo 'ID: ', $row['id'] , ' Nome: ', $row['nome'] , "\n";
}
```

**- mysql\_fetch\_object**

 ```php
$ricevuta = mysql_query("SELECT id, nome FROM utenti ");

while ($obj = mysql_fetch_object($ricevuta)) {
	echo 'ID: ', $obj->id , ' Nome: ', $obj->nome , "\n";
}
```

**- mysql\_fetch\_array**

 ```php
$ricevuta = mysql_query("SELECT id, nome FROM utenti ");

while ($row = mysql_fetch_array($ricevuta, MYSQL_BOTH)) {
	echo 'ID: ', $row[0] , ' Nome: ', $row['nome'] , "\n";
}
```

Le istruzioni di fetch mostrate estraggono un record dal set dei risultati. Non è possibile specificare quale record estrarre poiché questi sono restituiti in sequenza, dal primo all'ultimo. PHP infatti tiene traccia della posizione corrente all'interno del set di risultati attraverso un puntatore, ovvero un indice che, supponendo che la query abbia prelevato dal database N record, va da 0 (il primo) a N-1 (l'ultimo).

Effettuata una query questo puntatore è inizializzato da PHP a 0 e viene incrementato di una unità ad ogni esecuzione di una funzione di fetch. Questo spiega il funzionamento dei cicli WHILE degli esempi precedenti.

Normalmente non è necessario agire su questo puntatore, poiché quasi sempre siamo interessati ad estrarre tutti i record provenienti dalla query, nell'ordine con cui sono stati prodotti ed inviati da MySQL. Se però volessimo prelevare solo alcuni record allora possiamo utilizzare la funzione **mysql\_data\_seek()** con cui è possibile spostarsi all'interno del set di risultati. Nel seguente esempio verrà estratto solo il primo e l'ultimo record dal set dei risultati:

 ```php
$ricevuta = mysql_query("SELECT nome FROM utenti ORDER BY nome");

$row = mysql_fetch_assoc($ricevuta);
echo 'Primo nome: ', $row['nome'], "\n";

$numero_record = mysql_num_rows($ricevuta);
mysql_data_seek($ricevuta, $numero_record - 1);

$row = mysql_fetch_assoc($ricevuta);
echo 'Ultimo nome: ', $row['nome'], "\n";
```

Analizziamo nel dettaglio il funzionamento del precedente script:

- Per prima cosa viene lanciata la query
- Con la funzione mysql\_fetch\_assoc() otteniamo un record del set di risultati. Si tratta ovviamente del primo record, poiché il puntatore interno al set di risultati è inizialmente a 0
- Per sapere quanti record $numero\_record sono stati estratti impieghiamo la funzione **mysql\_num\_rows()**
- Spostiamo il puntatore del set di risultati all'ultimo record ($numero\_record - 1) con mysql\_data\_seek()
- Con la funzione mysql\_fetch\_assoc otteniamo un ulteriore record del set di risultati. Si tratta dell'ultimo record presente.
 
Prima di concludere con le istruzioni di fetch è doveroso citare **mysql\_result()**. A differenza delle altre funzioni di fetch che prelevano un intero record dal set di risultati, mysql\_result() permette di ottenere solo un campo di un particolare record (una sola cella). Ecco un esempio del suo funzionamento:

 ```php
$ricevuta = mysql_query("SELECT id, nome FROM utenti ORDER BY id");

// mostra il nome del TERZO record restituito dalla query
echo mysql_result($ricevuta, 2,'nome');
```

### Query buffered e unbuffered

Come accennato all'inizio della lezione, questo modo di effettuare una query è detto *buffered* perché è il PHP a conservare nella sua memoria tutti i record restituiti, accessibili allo script attraverso la procedura di fetch. E' possibile però lasciare a MySQL il compito di conservare i record e prelevarli, uno ad uno, direttamente con le istruzioni di fetch. Questa modalità è detta *unbuffered*. Per effettuare una query in modalità unbuffered basta usare la funzione **mysql\_unbuffered\_query()** al posto della classica mysql\_query().

A questo punto è lecito chiedersi il motivo della presenza di due approcci distinti per la gestione del set di risultati, considerato che lo scopo ultimo rimane comunque l'estrazione dei dati dal database. Vediamo i pro ed i contro delle due tecniche:

- con le query buffered i dati vengono conservati da PHP che ha quindi la possibilità di poterli manipolare più agevolmente. E' infatti possibile utilizzare mysql\_data\_seek() per spostarsi liberamente all'interno dei risultati. Inoltre è possibile richiamare mysql\_num\_rows() per sapere subito quante righe sono state restituite dall'ultima query. mysql\_data\_seek() e mysql\_num\_rows() non sono disponibili per una unbuffered query.
- Liberando immediatamente MySQL, le query buffered permettono al server di sbloccare subito i dati coinvolti dalla query in modo che altri processi (altri utenti) possano accedervi. Se ad esempio uno script legge un dato dal database, questo stesso dato non potrà essere modificato da un altro script fino a che non si chiude il set di risultati (mysql\_free\_result).
- Le query buffered costringono il PHP a conservare nella sua memoria tutto il set di risultati e questo compito può risultare oneroso se vengono gestite grosse quantità di dati.
- Le query buffered constringono lo script a sospendere la sua esecuzione fino a quando tutti i dati non hanno raggiunto il PHP, quindi il primo risultato è disponibile solo quando tutti i risultati sono estratti da MySQL, inviati e memorizzati dal PHP.
- Con una query unbuffered i risultati sono accessibili al PHP esclusivamente in maniera sequenziale ma sono disponibili non appena MySQL inizia a restituirli.
- Le query unbuffered hanno un ulteriore inconveniente. Poiché l'interazione tra PHP e MySQL non si conclude con l'invio della query ma continua per tutta la fase di fetch, per lanciare una nuova query è necessario prima prelevare tutti i risultati della precedente oppure chiudere il set di risultati (mysql\_free\_result).
 
E' chiaro quindi che non esiste una soluzione migliore rispetto all'altra. Anche se vengono privilegiate le buffered query perché permettono una migliore gestione del set di risultati, in casi di alto carico dell'applicazione e per grossi volumi di dati coinvolti, potrebbe convenire impiegare le unbuffered query.