---
title: Architettura REST, le origini
author: giovannitomasicchio
type: post
date: 2017-09-18T06:45:49+00:00
url: /architettura-rest-le-origini/
thumbnail: /wp-content/uploads/2017/09/www.jpg
categories:
  - Senza categoria
tags:
  - HTML
  - HTTP
  - REST
  - RESTful

---
Chiacchierando tra colleghi mi sono accorto che chi viene dal mondo SOAP e si avvicina ai servizi RESTful commette spesso l&#8217;errore di pensare che la principale differenza tra i due approcci sia solo la notazione utilizzata per lo scambio dei dati, XML per i servizi SOAP e JSON per quelli RESTful. Per avere un&#8217;idea più corretta sui concetti alla base di questi servizi è necessario fare un salto nel passato e seguire la nascita del Web per come lo conosciamo adesso.

{{< figure src="/wp-content/uploads/2017/09/Tim-Berners-Lee.jpg" title="Tim Berners-Lee" >}}

Tutto è iniziato quanto un fisico inglese, [Tim Berners-Lee][1], durante la sua attività al CERN di Ginevra, realizzò un sistema per la condivisione delle informazioni con le comunità scientifiche di tutto il mondo. Tim si occupò di definire i protocolli HTTP e HTML, sviluppo inoltre il primo server e client Web. Era il [20 dicembre 1990][2] quando fu messa online la [sua prima pagina Web][3], la prima nel mondo.

Superato l&#8217;iniziale scetticismo, l&#8217;interesse suscitato da questa iniziativa portò negli anni successivi al proliferare dei siti Web, soprattutto negli ambienti accademici e scientifici. All&#8217;inizio l&#8217;evoluzione del Web avveniva mediante un **processo informale**: una [mailing list][4] utilizzata da tutti gli sviluppatori Web del mondo per discutere e concordate le migliorie da applicare ai protocolli.

Ma il business non tardò a fiutare la valenza del Web e nel 1994 lo stesso Tim Berners-Lee capì che era necessario fare un salto di qualità. Egli fondò quindi il [W3C][5] , una organizzazione non governativa con lo scopo di dare più consistenza al processo di evoluzione del Web e renderlo più appetibile agli investimenti privati. Non più una serie di protocolli in continua evoluzione, da inseguire su una mailing list, ma una rete di precisi **standard** da adottare.

{{< figure src="/wp-content/uploads/2017/09/appies2000-roy.jpg" title="Roy Thomas Fielding" >}}

A far parte di questo processo di standardizzazione venne invitato un informatico californiano, [Roy Thomas Fielding][6], che si occupò di URL, HTML e successivamente divenne il revisore capo dell&#8217;HTTP. In quegli anni Roy si divideva tra le attività di standardizzazione del Web ed il mondo accademico, per via dei suoi studi come dottorando.

Gli anni passavano e Roy, come racconta lui stesso in [un&#8217;intervista][7], non riusciva ad individuare quale potesse essere l&#8217;argomento da utilizzare per la sua tesi di dottorato. Incoraggiato da diverse parti, Roy decise di descrivere, in termini formali, il modello di architettura complessiva del Web che aveva elaborato negli anni. Roy infatti aveva formulato un proprio modello di funzionamento del Web che utilizzava nelle sue attività di definizione degli standard per valutare gli impatti che una particolare modifica ad uno standard avrebbe potuto avere sull&#8217;intero ecosistema del Web.

Ne venne fuori nel 2000 la [definizione di un modello architetturale][8], definito REST, ovvero Representational State Transfer, con l&#8217;auspicio dell&#8217;autore che potesse guidare le future evoluzioni del Web. In estrema sintesi questo modello descrive principi e vincoli di una architettura nella quale le applicazioni Web posseggono un proprio **stato** e lo **trasferiscono**, sotto forma di una sua **rappresentazione**, attraverso l&#8217;utilizzo di **protocolli standard**.

Il modello REST, grazie alla sua maturità nonché alla sua formulazione astratta, ovvero non legata a specifiche implementazioni, guida tuttora l&#8217;evoluzione del Web e ha ispirato, più di recente, la nascita di un nuovo approccio alla realizzazione dei servizi Web, i cosiddetti servizi RESTful, di cui parlerò nel prossimo post.

 [1]: https://www.w3.org/People/Berners-Lee/
 [2]: http://timeline.web.cern.ch/timelines/The-birth-of-the-World-Wide-Web/overlay#1990-12-20 00:00:00
 [3]: http://info.cern.ch/hypertext/WWW/TheProject.html
 [4]: https://lists.w3.org/Archives/Public/www-talk/
 [5]: https://it.wikipedia.org/wiki/World_Wide_Web_Consortium
 [6]: https://it.wikipedia.org/wiki/Roy_Fielding
 [7]: https://www.youtube.com/watch?v=w5j2KwzzB-0
 [8]: https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm