---
title: "Installare PHP"
author: "Giovanni Tomasicchio"
type: article
date: 2010-10-10T14:31:35+0000
description: "Installare PHP con Zend Server, XAMPP e EasyPHP. Guide per l'installazione manuale di PHP su Windows, Linux e Mac OS X."
url: /articoli/installare-php/
categories:
  - Articoli
tags:
  - "Joomla!"
  - "Zend Framework"
  - "MySQL"
  - "WordPress"
  - "Drupal"
  - "Java"
  - "Zend Server"
  - "Oracle"
  - "email"
  - "FTP"

  
---
In questo articolo vedremo quali sono i metodi e gli strumenti che abbiamo a disposizione per **installare PHP** sul nostro computer. Prenderemo in considerazione sia i principali pacchetti **software autoinstallanti**, sia le **procedure manuali** adatte ai diversi sistemi operativi.

##  Zend Server

Come suggerisce il nome, [Zend Server](http://www.zend.com/en/products/server/) nasce in casa Zend, azienda leader nel panorama PHP e principale sostenitore dello sviluppo del linguaggio. Disponibile per Windows, Linux e Mac OS X, Zend Server è adatto a preparare sia un PC di sviluppo sia un server di produzione.

Viene distribuito in due versioni, la [community edition](http://www.zend.com/en/products/server-ce/index) è gratuita e permette l’installazione di PHP (5.2 e 5.3) su Apache o IIS, MySQL (solo su Windows e Mac OS X), Zend Java bridge, driver per Oracle, Zend Framework, Zend Optimizer+ e Zend Debugger.

La versione commerciale dello Zend Server integra ulteriori funzionalità, pensate principalmente per server di produzione: sistema di caching delle pagine, sistema di gestione di una coda di job, monitor degli script PHP e tracing del codice, supporto allo Zend Server Cluster Manager.

La gestione dello Zend Server e la configurazione di tutte le sue componenti avviene attraverso una comoda interfaccia web.

 | [![](/image/articoli//installare_php/zend_server/T01.jpg)](</image/articoli//installare_php/zend_server/01 setup di zend server.jpg>) | [![](/image/articoli//installare_php/zend_server/T02.jpg)](</image/articoli//installare_php/zend_server/02 interfaccia web di zend server.jpg>) | [![](/image/articoli//installare_php/zend_server/T03.jpg)](</image/articoli//installare_php/zend_server/03 estensioni di php.jpg>) |
|---|---|---|

##  XAMPP

[XAMPP](http://www.apachefriends.org) è una distribuzione gratuita di Apache 2.2, MySQL 5., PHP 5.3 e Perl 5.10 disponibile per Windows, Linux, Mac OS X e Solaris. Il pacchetto è ricco di ulteriori programmi utili allo sviluppo: OpenSSL, phpMyAdmin, Webalizer, server e client FTP, server Mail, ecc..

La versione Windows di XAMPP installa anche un pannello di controllo per l’avvio e l’arresto dei server Apache, MySQL, FileZilla (FTP) e Mercury (posta).

 | [![](/image/articoli//installare_php/xampp/T01.jpg)](</image/articoli//installare_php/xampp/01 setup di XAMPP.jpg>) | [![](/image/articoli//installare_php/xampp/T02.jpg)](</image/articoli//installare_php/xampp/02 pannello di controllo di XAMPP.jpg>) | [![](/image/articoli//installare_php/xampp/T03.jpg)](</image/articoli//installare_php/xampp/03 interfaccia web.jpg>) |
|---|---|---|

##  EasyPHP

[EasyPHP](http://www.easyphp.org/) è una distribuzione di PHP 5.3, Apache 2.2, MySQL 5.1 e PhpMyAdmin 3.3 per Windows. Attraverso un sistema di moduli è possibile installare facilmente anche le più diffuse applicazioni web, tra cui Joomla, Wordpress, Drupal, phpBB, ecc..

EasyPHP aggiunge una icona nella system tray di windows da cui è possibile avviare ed arrestare Apache e MySQL, accedere ai log di Apache e MySQL, aprire l’interfaccia web di configurazione, ecc.

 | [![](/image/articoli//installare_php/easyphp/T1.jpg)](/image/articoli//installare_php/easyphp/easyphp_1.jpg) | [![](/image/articoli//installare_php/easyphp/T2.jpg)](/image/articoli//installare_php/easyphp/easyphp_2.jpg) | [![](/image/articoli//installare_php/easyphp/T3.jpg)](/image/articoli//installare_php/easyphp/easyphp_3.jpg) |
|---|---|---|

- - - - - -

##  Installazione di PHP su Windows

Coloro che preferiscono effettuare manualmente il setup del proprio PC di sviluppo o del server di produzione possono consultare, oltre alla documentazione ufficiale dei diversi pacchetti, anche diverse guide disponibili sul web. Segnaliamo quelle pubblicate su PHPnews.it:

- [Installare Apache 2.2, PHP5.2 e MySQL 5 su Windows](/articoli/installare-apache-22-php-52-e-mysql-5-su-windows/)
- [Installazione di PHP su Internet Information Services](/articoli/installazione-di-php-su-internet-information-services/) (per Windows XP)
- [IIS7 e PHP in Windows Vista con FastCGI](/articoli/iis-7-e-php-in-windows-vista-con-fastcgi/)
 
##  Installazione di PHP su Mac OS X

Per gli utenti di OS X che vogliono configurare personalmente Apache, PHP e MySQL sul proprio Mac segnaliamo la seguente guida, scritta in inglese, di Rob Allen, guru del mondo PHP:

[Setting up PHP &amp; MySQL on OS X 10.6 Snow Leopard](http://akrabat.com/php/setting-up-php-mysql-on-os-x-10-6-snow-leopard/)

##  Installazione di PHP su Linux

Tutte le distribuzioni Linux dispongono di pacchetti (RPM, deb, ecc.) per installare Apache, PHP e MySQL. La procedura è diferente per ciascuna distribuzione pertanto come punto di partenza si rimanda alla documentazione ufficiale della propria distribuzione.