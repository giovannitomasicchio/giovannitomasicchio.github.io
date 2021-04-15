---
title: "Query con filtri dinamici"
author: "Giovanni Tomasicchio"
type: article
date: 2008-11-29T14:25:25+0000
description: "Tecniche per realizzare con MySQL una query con filtri dinamici."
url: /articoli/query-con-filtri-dinamici/
categories:
  - Articoli
tags:
  - "database"
  - "MySQL"
  - "SQL"

  
---
Capita spesso di dover realizzare una ricerca all'interno del database secondo dei criteri che variano in base alle condizioni impostate dall'utente. Supponiamo ad esempio di dover realizzare un **motore di ricerca** dei comuni italiani. L'utente potra' specificare il nome del comune o parte di esso, inoltre potra' selezionare la regione o la provincia di appartenenza per restringere il campo di ricerca.

Poiché non vi è alcun obbligo di specificare tutti i criteri di ricerca, è necessario trovare un sistema per poter realizzare una query SQL che consideri solo le condizioni specificate dall'utente ed ignori gli altri criteri. Una soluzione comunemente adottata consiste nel **comporre dinamicamente lo statement SQL** attraverso degli "spezzoni" di SQL da accodare l'uno all'altro in base all'input dell'utente. In questo modo potremo inserire nella clausola WHERE solo le condizioni realmente indicate dall'utente.

Questa tecnica, sicuramente flessibile, ha però uno svantaggio, ovvero **frammenta lo statement SQL** in tante stringhe, rendendolo di difficile comprensione. In alternativa possiamo lasciare a MySQL il compito di **ignorare le condizioni non specificate** e considerare solo quelle realmente impostate dall'utente. Vediamo uno statement SQL in grado di fare quanto appena detto.

 ```php
<?php
// tutti i comuni
$nome_comune = '';
$cod_provincia = 0;
$cod_regione = 0;

// i comuni che contengono il testo 'to'
$nome_comune = 'to';
$cod_provincia = 0;
$cod_regione = 0;

// tutti i comuni del Piemonte
$nome_comune = '';
$cod_provincia = 0;
$cod_regione = 1; // 1 = Piemonte

// tutti i comuni della provincia di torino che contengono il testo 'to'
$nome_comune = 'to';
$cod_provincia = 1; // 1 = Torino
$cod_regione = 0;

$sql = "SELECT * 
		FROM comune c, provincia p, regione r
		WHERE
		   c.COD_PROVINCIA = p.COD_PROVINCIA AND
		   p.COD_REGIONE = r.COD_REGIONE AND
		   ('$nome_comune' = '' OR c.DENOMINAZIONE LIKE '%$nome_comune%') AND
		   ($cod_provincia = 0 OR p.COD_PROVINCIA = $cod_provincia) AND
		   ($cod_regione = 0 OR r.COD_REGIONE = $cod_regione)
		ORDER BY c.DENOMINAZIONE";
?>
```

La query dell'esempio parte dal presupposto che l'utente può specificare il nome del comune (oppure lasciarlo "in bianco"), può selezionare con una casella di selezione &lt;select&gt; il codice della regione di appartenenza (in caso contrario il codice sarà 0), ed infine sempre con una casella di selezione può scegliere la provincia di appartenenza (in caso contrario il codice sarà 0). Osservando le tre condizioni poste nella clausola WHERE noteremo che sono proprio la stringa vuota ed il codice 0 ad inibire il filtro associato.

Questo succede perché MySQL quando valuta una espressione del tipo *condizione\_1 OR condizione\_2*, se trova che la *condizione\_1* è verificata evita di controllare anche la *condizione\_2* poiché il risultato dell'espressione sarà comunque *true*.

Se volete provare la query dell'esempio potete staricare il database dei comuni italiani da [questo link](http://www.phpnews.it/download/italia.zip).