---
title: "Cosa serve per programmare in PHP?"
author: "Giovanni Tomasicchio"
type: corso
date: 2005-07-13T22:27:03+0000
description: "Cosa serve per programmare in PHP"
url: /corsi/corso-php-base/cosa-serve-per-programmare-in-php/
weight: 19
categories:
  - Corso PHP base
  
---
Prima di procedere con lo studio del linguaggio PHP è necessario predisporre il nostro computer all'esecuzione di programmi PHP, chiamati anche **script PHP**. Abbiamo visto infatti che tali programmi vengono eseguiti durante l'interazione tra il nostro browser ed un server web. Anche se avessimo a disposizione tale server (quelli offerti dai diversi servizi di hosting) sarebbe abbastanza scomodo dover inviare i nostri files di volta in volta, magari via FTP, per vederne i risultati.

E' più conveniente invece installare sul nostro stesso computer il server web e configurarlo per l'esecuzione di pagine PHP così che tutti i nostri esperimenti potranno essere svolti anche senza un accesso ad internet. Le 6 fasi dell'interazione tra browser e server che abbiamo visto nella lezione precedente avverranno tutte all'interno del nostro PC.

Per installare PHP sul nostro PC possiamo seguire [questo articolo](articoli/installare-php/) che presenta le diverse procedure alternative a nostra disposizione.

Fatto? Bene, adesso potremo visualizzare le pagine PHP che andremo a realizzare. Se ad esempio volessimo testare un nostro script dal nome "prova.php" basterà salvarlo nella cartella C:\\Programmi\\Apache Group\\Apache2\\htdocs\\ per gli utenti di Apache oppure nella cartella C:\\Inetpub\\wwwroot\\ per chi ha installato IIS, e far puntare il nostro browser in entrambi i casi alla pagina http:\\\\localhost\\prova.php

Per realizzare uno script PHP serve un comunissimo editor di testi, ad esempio il Blocco Note di Windows. Bisogna stare però attenti all'estensione da assegnare a questi script che deve essere ".php". Il Blocco Note infatti mette automaticamente l'estensione ".txt" a tutti i file salvati e per evitare ciò conviene racchiudere tra doppi apici il nome degli script per esteso nel pannello "Salva con nome".

Ok, tutto è pronto. E' il momento di sporcarsi le mani...