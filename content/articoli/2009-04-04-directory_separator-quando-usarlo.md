---
title: "DIRECTORY_SEPARATOR, quando usarlo?"
author: "Giovanni Tomasicchio"
type: article
date: 2009-04-04T07:25:29+0000
description: "Usare DIRECTORY_SEPARATOR in script PHP cross-platform"
url: /articoli/directory_separator-quando-usarlo/
categories:
  - Articoli
tags:

  
---
**DIRECTORY\_SEPARATOR** è una costante predefinita di PHP che contiene il carattere utilizzato dal sistema operativo su cui gira il server per comporre i percorsi dei file.

Windows infatti utilizza il carattere *backslash* \\ (ad esempio 'C:\\script.php') mentre gli altri sistemi operativi (Unix, Linux, Mac OS X, ecc.) utilizzano il carattere *slash* / (es.: /home/www/apache/bin/php.ini).

Con DIRECTORY\_SEPARATOR quindi possiamo scrivere del codice PHP che risulterà indipendente dal sistema operativo su cui dovrà girare.

In realtà però è inutile utilizzare DIRECTORY\_SEPARATOR per costruire i percorsi dei file, infatti **basta usare sempre il carattere slash** / poiché Windows lo riconosce comunque come un separatore di path.

La vera utilità di DIRECTORY\_SEPARATOR si ha nell'analisi dei percorsi dei file restituiti dalle funzioni PHP o recuperati dal sistema operativo. In questi casi non si conosce a priori quale carattere verrà usato come separatore e quindi DIRECTORY\_SEPARATOR scioglie ogni dubbio permettendo, ad esempio, di eseguire un explode(DIRECTORY\_SEPARATOR, $path) certi che funzionerà su qualsiasi sistema operativo.