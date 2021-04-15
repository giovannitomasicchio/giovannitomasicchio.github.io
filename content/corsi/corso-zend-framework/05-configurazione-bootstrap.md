---
title: "Configurazione e bootstrap"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:12:34+0000
description: "Configurare l'applicazione Zend Framework attraverso il file application.ini"
url: /corsi/corso-zend-framework/configurazione-bootstrap/
categories:
  - Introduzione allo Zend Framework
  
---
Il controller di Zend Framework usa il design pattern "Front Controller" pertanto instrada tutte le richieste attraverso un singolo file index.php. Ciò garantisce che l'ambiente sia impostato correttamente affinchè funzioni l'applicazione (fase di bootstrap). Per ottenere questo risultato usiamo un file .htaccess presente nella cartella zf-tutorial/pulic, generato precedentemente da Zend\_Tool, che redireziona tutte le richieste al file public/index.php, anch'esso creato da Zend\_Tool.

Il file index.php è il punto di inizio dell'applicazione ed è usato per creare una istanza di Zend\_Application che inizializza ed avvia l'applicazione. Il file inoltre definisce due costanti: APPLICATION\_PATH e APPLICATION\_ENV che definiscono rispettivamente il path per la directory application/ e l'ambiente, ovvero la modalità di esecuzione dell'applicazione. Quest'ultimo di default è impostato a "production" (produzione) nell'index.php ma noi dobbiamo settarlo a "development" (sviluppo) aggiungendo questa riga nel file .htaccess:

 ```

SetEnv APPLICATION_ENV development
```

Il componente Zend\_Application viene usato per avviare l'applicazione ed è configurato per usare le direttive del file di configurazione application/configs/application.ini. Anche questo file è già stato creato da Zend\_Tool. Una classe di Bootstrap che estende Zend\_Application\_Bootstrap\_Bootstrap è disponibile in application/Bootstrap.php e può essere usata per eseguire qualsiasi istruzione di avvio necessaria.

Il file application.ini, che si trova in application/configs, viene caricato attraverso il componente Zend\_Config\_Ini. Zend\_Config\_Ini carica le impostazioni di configurazione utilizzando il concetto di ereditarietà delle sezioni che viene indicato usando i due punti nel nome della sezione. Per esempio:

 ```

[staging : production]
```

Questo significa che la sezione "staging" eredita tutte le impostazioni della sezione "production". La costante APPLICATION\_ENV definisce quale sezione deve essere caricata. Ovviamente durante lo sviluppo si usa la sezione "development", mentre sul server in produzione deve essere usata la sezione "production" . Inseriremo tutte le modifiche di configurazione nella sezione "production", in questo modo anche le altre sezioni erediteranno tali cambiamenti.

La prima modifica che dobbiamo fare è aggiungere le informazioni sul fuso orario (timezone) per le funzioni di data e ora del PHP. Inseriamo nel file application/configs/application.ini:

 ```

phpSettings.date.timezone = "Europe/London"
```

dopo tutte le altre voci phpSettings nella sezione \[production\]. Ovviamente, si deve impostare il timezone di interesse (es. in Italia "Europe/Rome"). Ora possiamo procedere con la scrittura del codice dell'applicazione.