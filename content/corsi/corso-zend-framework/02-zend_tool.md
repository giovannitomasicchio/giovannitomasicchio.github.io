---
title: "Zend_Tool"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T15:57:20+0000
description: "installare ed utilizzare Zend_Tool per costruire un'applicazione MVC con Zend Framework"
url: /corsi/corso-zend-framework/zend_tool/
categories:
  - Introduzione allo Zend Framework
  
---
Zend Framework è distribuito con un nuovo strumento da usare da riga di comando. Incominceremo impostando questo.

###  Zend\_Tool per Windows

- Creiamo una nuova directory in "Programmi" chiamata ZendFrameworkCli
- Facciamo doppio click sull'archivio scaricato, ZendFramework-1.10.6-minimal.zip.
- Copiamo le cartelle bin e library presente nell'archivio nella cartella C:\\Programmi\\ZendFrameworkCli. Questa cartella ora dovrebbe avere due sottocartelle: bin e library.
- Aggiungiamo la directory bin al nostro path: 
  - Andiamo nella sezione "Sistema" del Pannello di Controllo.
  - Scegliamo "Avanzate" e poi premiamo sul bottone "Variabili di Sistema".
  - Nella lista delle "Variabili d'ambiente", cerchiamo la variabile "path" e facciamo doppio click su di essa.
  - Aggiungiamo ;C:\\Programmi\\ZendFrameworkCli\\bin in coda al valore della variabile e premiamo OK (Il punto e virgola iniziale è importante! Non cancelliamolo)
  - Riavviamo il sistema.
 
###  Zend\_Tool per OS X (per Linux è simile)

- Scompattiamo il pacchetto scaricato, ZendFramework-1.10.6-minimal.zip nella nostra directory di Download con il doppio click.
- Copiamo in /usr/local/ZendFrameworkCli aprendo un Terminale e digitando:
   sudo cp -r ~/Downloads/ZendFramework-1.10.6-minimal /usr/local/ZendFrameworkCli
   (su alcune distribuzionei Linux non si usa sudo, bisogna autenticarsi come utente root tramite il comando "su", n.d.t.)
- Modifichiamo la configurazione bash aggiungendo l'alias: 
  - Da un terminale, apriamo con un editor di testo (gli utenti Mac OS X hanno di base TextEdit):
     ~/.bash\_profile
  - Aggiungiamo
     alias zf=/usr/local/ZendFrameworkCli/bin/zf.sh 
     alla fine del file
  - Salviamo ed usciamo.
  - Usciamo dal terminale.
 
\[nota aggiunta del traduttore\] Alcune distribuzioni Linux forniscono già Zend Framework nel loro sistema di gestione software. Per Fedora si può vedere la lista dei pacchetti inerenti a Zend Framework con:

$ yum search php-ZendFramework

 

###  Proviamo Zend\_Tool

Possiamo testare la nostra installazione di Zend\_Tool aprendo un terminale e digitando il comando.

 ```

zf show version
```

Se tutto funziona dovremmo vedere una cosa simile:

 ```

Zend Framework Version: 1.10.0
```

Se non è così bisogna rivedere le impostazioni del path e se la cartella bin esiste nella directory di ZendFrameworkCli.

Quando zf tool funziona, zf --help permetterà di avere la lista dei comandi disponibili.

Se la nostra distribuzione ha una versione di Zend Framework inferiore a quella indicata in questo tutorial, la guida non funzionerà.