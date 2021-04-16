---
title: "Creare la struttura del progetto"
author: "Mario Santagiuliana"
type: corso
date: 2010-12-28T16:10:20+0000
description: "Creazione della struttura dell'applicazione Zend Framework con Zend_Tool"
url: /corsi/corso-zend-framework/creare-la-struttura-del-progetto/
weight: 13
categories:
  - Introduzione allo Zend Framework
  
---
Partiamo con la costruzione della nostra applicazione. Dove possibile, cercheremo di utilizzare lo strumento zf (Zend\_Tool n.d.t.) da riga di comando, che permetterà di risparimare tempo e fatica. La prima cosa da fare è creare lo scheletro dei file e delle cartelle del progetto.

Apriamo un terminale o il promt dei comandi e spostiamoci nella directory di root del nostro server web usando il comando cd. Assicuriamoci di avere i permessi per creare i file in questa directory e che il server web abbia i permessi di lettura. Digitiamo:

 ```

zf create project zf-tutorial
```

Lo strumento ZF creerà una directory chiamata zf-tutorial e la popolerà con la struttura di directory necessaria. Questa struttura presuppone che si abbia il controllo completo della configurazione di Apache, così si potrà mantenere la maggior parte dei file al di fuori della directory root web. Si dovrebbero vedere i seguenti file e cartelle:

![albero delle cartelle di progetto](/images/stories/Corsi/introduzione_zend_framework/albero_progetto.gif)

(In public/ c'è anche un file .htaccess nascosto).

La directory application/ è quella che contiene il codice sorgente del sito. Come si può vedere, abbiamo una directory separata per i file model, view e controller dell'applicazione. La directory public/ è la root pubblica del sito web, questo significa che l'url http://localhost/zf-tutorial/public punta qui. In questo modo la maggior parte dei file dell'applicazione non sono direttamente accessibili tramite Apache e così sono più al sicuro.

Per un vero sito web, si deve creare un virtual host per il sito ed impostare la document root direttamente sulla directory public. Per esempio si può creare un virtual host chiamato zf-tutorial.localhost che assomiglia a qualcosa del genere:

 ```

<VirtualHost *:80>
    ServerName zf-tutorial.localhost
    DocumentRoot /var/www/html/zf-tutorial/public
    <Directory "/var/www/html/zf-tutorial/public">
       AllowOverride All
    </Directory>
</VirtualHost>
```

Il sito web sarà accessibile da http://zf-tutorial.localhost/ (assicuriamoci di aver aggiornato /etc/hosts o C:\\WINDOWS\\system32\\drivers\\etc\\hosts in modo che zf-tutorial.localhost sia mappato sull'indirizzo 127.0.0.1). Non lo faremo in questo tutorial perché è più facile usare una sottodirectory per le prove.

 

Le immagini, i JavaScript e i file Css di supporto devono essere salvati in una directory separata sotto la directory public/. I file di Zend Framework che abbiamo scaricato verranno messi nella directory library/. Se avremo la necessità di usare una qualsiasi altra libreria, potremo metterla qui.

Copiamo la directory library/Zend/ dall'archivio scaricato (ZendFramework-1.10.6- minimal.zip) in zf-tutorial/library/, in questo modo zf-tutorial/library/ conterrà una sotto-directory chiamata Zend/.

Possiamo adesso fare un primo test andando con il browser su http://localhost/zf-tutorial/public. Dovremmo vedere qualcosa di simile:

![pagina di benvenuto Zend Framework](/images/stories/Corsi/introduzione_zend_framework/welcome.png)