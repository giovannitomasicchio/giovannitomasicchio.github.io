---
title: REST, applicazione al Web ed ai servizi RESTful
author: giovannitomasicchio
type: post
date: 2017-09-23T14:44:32+00:00
url: /rest-applicazione-al-web-ed-ai-servizi-restful/
thumbnail: /wp-content/uploads/2017/09/world-wide-web.jpg
categories:
  - Senza categoria
tags:
  - HTTP
  - REST
  - RESTful
  - SOAP

---
Nel [precedente post][1] vi ho raccontato delle vicende che hanno portato alla definizione del modello REST. Non è il caso di entrare nel merito della descrizione, piuttosto formale, che ne fa l&#8217;autore nella sua [tesi di dottorato][2]. Vediamo invece quali sono i concetti chiave di questa architettura e come questi siano riscontrabili nel Web e più di recente abbiano ispirato i servizi RESTful*.

## Risorse e identificativi

Nel modello REST si parla di risorse e dei relativi identificativi, che corrispondono nel Web ai documenti (html, jpg, css, ecc.) e alle funzionalità identificate dagli URL. Anche i servizi RESTful utilizzano gli **URL per identificare l&#8217;oggetto delle possibili interazioni tra client e server**. Ad esempio i servizi RESTful di una videoteca potrebbero avere i seguenti URL:

  1. /film  
    per interagire con l&#8217;intero archivio dei film
  2. /film/terminator  
    per gestire il particolare film Terminator
  3. /film/terminator/attori  
    per accedere all&#8217;elenco degli attori di Terminator
  4. /film/terminator/attori/schwarzenegger  
    per interagire con l&#8217;interpretazione di Arnold Schwarzenegger in Terminator

Gli URL degli esempi 1 e 3 si dicono di tipo _collection_ poiché fanno riferimento ad un insieme omogeneo di elementi (film e attori di un film), mentre gli URL 2 e 4 referenziano specifici elementi.

Salta subito all&#8217;occhio una differenza sostanziale con i servizi SOAP che tramite un unico URL possono esporre funzionalità relative a differenti soggetti (film, attori, ecc.). Questo avviene perché in SOAP l&#8217;oggetto dell&#8217;interazione tra client e server è codificato nel messaggio XML e non nell&#8217;URL.

## Rappresentazione delle risorse

Abbiamo detto che nel modello REST l&#8217;interazione tra client e server ha come oggetto una risorsa. Questa risorsa però si manifesta non direttamente ma tramite delle sue rappresentazioni. Una rappresentazione di una risorsa è costituita da:

  * i dati associati alla risorsa, descritti mediante una specifica codifica;
  * i metadati necessari a descrivere i suddetti dati, sotto forma di coppie nome &#8211; valore.

Per una particolare risorsa possono essere prodotte differenti rappresentazioni, ad esempio impiegando diverse sistemi di codifica della stessa.

Nel Web ad esempio la rappresentazione di una pagina HTML è costituita dai byte dell&#8217;HTML a cui si aggiungono le intestazioni HTTP che permettono al browser di interpretarli correttamente (Content-Type, charset, ecc.). Inoltre la rappresentazione di una risorsa può essere il frutto di una negoziazione tra browser e server. Questo aspetto emerge se analizziamo le intestazioni HTTP scambiate tra browser e server nel caso di accesso ad una pagina web:

  * nella richiesta del browser è presente un header HTTP con l&#8217;indicazione di quali sono le tipologie di rappresentazione della risorsa che il browser è in grado di gestire:

<pre>Accept:<strong>text/html</strong>,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8</pre>

  * nella risposta invece è presente la tipologia di rappresentazione scelta dal server per l&#8217;invio al browser della risorsa richiesta:

<pre>Content-Type:<strong>text/html</strong>; charset=UTF-8</pre>

Per i servizi RESTful questo concetto si traduce nella possibilità di realizzare una negoziazione tra client e server sul formato di codifica da utilizzare nella trasmissione dei dati, come JSON o XML, del tutto simile a quella che avviene tra browser e server web. Anche questa caratteristica differenzia i servizi RESTful da quelli SOAP che invece adottano solo messaggi XML.

## Trasporto delle rappresentazioni

Il modello REST prevede una interazione di tipo client-server tra i nodi della rete. In queste interazioni ha luogo il trasporto, ovvero la trasmissione delle rappresentazioni delle risorse tra client e server. Per descrivere queste comunicazioni, client e server utilizzano dei &#8220;control data&#8221;, con lo scopo di dichiarare 1) l&#8217;azione richiesta dal client e 2) il significato della risposta del server.

Nel Web questo si realizza mediante il protocollo di trasporto HTTP, che utilizza:

  1. dei &#8220;verbi&#8221; per specificare il tipo di richiesta che il client rivolge al server web;
  2. degli &#8220;status code&#8221; per indicare al browser l&#8217;esito della richiesta effettuata.

I verbi più utilizzati da un browser sono GET, per richiedere una &#8220;pagina&#8221; dato il suo URL, e POST per inviare al server i dati inseriti da un utente in un form HTML. Lo status code più noto del Web è certamente il 404 &#8220;pagina non trovata&#8221;.

I verbi HTTP ricoprono un ruolo del tutto simile nei servizi RESTful. Mediante questi verbi il client indica al server quale tipo di operazione intende effettuare su una particolare risorsa. Per questa ragione nel contesto dei servizi RESTful vengono impiegati, oltre a GET e POST, ulteriori verbi HTTP. Di seguito il significato di quelli maggiormente utilizzati, raggruppati in base al risultato atteso in caso di esecuzione multipla (idempotenza).

**Operazioni idempotenti** &#8211; anche se eseguite più volte su uno specifico URL, il risultato non deve cambiare rispetto a quello ottenibile con una singola esecuzione.

  * **GET**, per recuperare una rappresentazione della risorsa.  
    In caso di URL di tipo _collection_ la risposta del server dovrebbe contenere, tra l&#8217;altro, anche gli URL delle singole entità appartenenti alla _collection_.  
    Questa operazione non deve in alcun modo modificare lo stato della risorsa richiesta.
  * **PUT**, crea o aggiorna una risorsa identificata dall&#8217;URL specificato. Se la risorsa esiste già allora il server la aggiorna, altrimenti la crea, associandola all&#8217;URL specificato.  
    Generalmente l&#8217;URL specificato in una richiesta di tipo PUT non è di tipo _collection_ (vedi i precedenti esempi 1 e 3) ma identifica una specifica entità, a meno che non si voglia creare o aggiornare una intera _collection_.  
    Poiché l&#8217;identificativo della risorsa è specificato dal client, se questa non esiste il server è tenuto a crearla utilizzando proprio questo identificativo. Non è detto però che il server possa accettare che sia il client a scegliere l&#8217;identificativo delle nuove risorse, ad esempio perché questo è un numero progressivo gestito dal server. In questi casi il server, ad una richiesta PUT per una nuova risorsa, restituisce un errore.
  * **DELETE**, cancella una risorsa identificata dall&#8217;URL specificato.  
    Come per PUT, generalmente l&#8217;URL specificato in una richiesta di tipo DELETE non è di tipo _collection_.

**Operazioni non idempotenti** &#8211; il risultato può cambiare ad ogni esecuzione dell&#8217;operazione.

  * **POST**, per la creazione di una nuova risorsa affidata ad un URL che rappresenta l&#8217;entità &#8220;padre&#8221; della nuova risorsa.  
    Generalmente l&#8217;URL specificato in una richiesta di tipo POST è di tipo _collection_, poiché il client vuole aggiungere una nuova risorsa nella _collection_ indicata.

Nel caso si stia realizzando un servizio RESTful con una semantica non immediatamente riconducibile a quella di una delle suddette operazioni, bisogna comunque scegliere il verbo che si avvicina di più allo scopo del servizio tra quelli definiti dagli standard [RFC 7231][3]  (GET, HEAD, POST, PUT, DELETE, CONNECT, OPTIONS, TRACE) e [RFC 5789][4] (PATCH), facendo massima attenzione a rispettare la caratteristica di idempotenza del verbo scelto.

Come anticipato, l&#8217;esito delle richieste RESTful viene indicato dal server mediante lo [status code][5] dell&#8217;HTTP. Di seguito alcuni possibili esiti delle principali operazioni:

  * GET: 200 (Ok) se il server è in grado di restituire la risorsa associata all&#8217;URL richiesto, 404 (Not Found) se l&#8217;URL non esiste;
  * PUT: 200 (Ok) o 204 (No Content) se la risorsa viene creata o aggiornata come richiesto, 405 (Method Not Allowed) se il server non accetta di eseguire l&#8217;operazione richiesta sull&#8217;URL specificato (ad esempio in caso di richiesta di creazione di nuove risorse);
  * POST: 201 (Created) se il server è in grado di creare la nuova risorsa;
  * DELETE: 200 (Ok) o 204 (No Content) se il server è in grado di rimuovere la risorsa associata all&#8217;URL richiesto, 404 (Not Found) se l&#8217;URL non esiste;

In estrema sintesi potremmo dire che i servizi RESTful affidano tutta la gestione del trasposto delle rappresentazioni al protocollo HTTP: la semantica della richiesta del client è codificata nei verbi HTTP mentre il significato delle risposte è comunicato al client mediante gli status code. Questa costituisce una ulteriore differenza con i servizi SOAP che invece incorporano nel messaggio XML la descrizione della _request_ e della _response_.

## HATEOAS

Il modello REST è definito dall&#8217;autore come un &#8220;[architectural style for distributed hypermedia systems][6]&#8220;. Non sorprende quindi che in questo modello abbia un ruolo centrale il concetto di ipertesto: &#8220;Hypertext As The Engine Of Application State&#8221; da cui il discutibile acronimo HATEOAS. Le risorse del modello REST sono infatti collegate mediante link che permettono di navigare il &#8220;distributed hypermedia systems&#8221;.

Nel Web questi link sono presenti sia nei documenti HTML (i tag <a>), sia nei messaggi del protocollo HTTP, si pensi alla risposta [HTTP 301 &#8220;Moved Permanently&#8221;][7]. Allo stesso modo anche i servizi RESTful sono tenuti ad incorporare nei messaggi e nel protocollo HTTP l&#8217;informazione degli URL associati alla risorsa corrente. Ad esempio quando il client effettua una GET per un URL, il messaggio di risposta deve contenere gli URL delle risorse collegate all&#8217;URL richiesto. Oppure quando il client effettua una POST per creare una nuova risorsa, il server deve rispondere indicando l&#8217;URL della risorsa appena creata, nonché gli URL da utilizzare per manipolarla.

In questo modo, analizzando le risposte del server, è possibile &#8220;navigare&#8221; il servizio RESTful e scoprire tutte le funzionalità disponibili. Inoltre è possibile per il client rendere dinamico il puntamento ai diversi URL del server poiché è questo a fornirli di volta in volta.

Purtroppo non esiste una sintassi standard per incorporare questi link nei messaggi RESTful. La soluzione più adottata è quella definita nello standard [ATOM][8].

## Conclusioni

In questo post abbiamo descritto il modello REST utilizzando la sua implementazione più nota, ovvero il Web, e la più recente applicazione costituita dai servizi RESTful. Ho volutamente trascurato altri importanti aspetti del modello che ci serviranno, in un prossimo post, per rispondere alle seguenti domande:

  * i servizi RESTful sono migliori di quelli SOAP?
  * perché è stata recuperata una tecnologia non certo recente per disegnare un nuovo modello di servizi web?
  * il modello REST deve essere preso in considerazione nella progettazione di applicazioni web?
  * perché sono ossessionato da questo argomento?

\___\___

* Anche se i servizi RESTful non obbligano ad utilizzare un particolare protocollo, in questo post faccio riferimento all&#8217;implementazione più diffusa, ovvero quella che sfrutta l&#8217;HTTP.

 [1]: /architettura-rest-le-origini/
 [2]: https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm
 [3]: https://tools.ietf.org/html/rfc7231#section-4
 [4]: https://tools.ietf.org/html/rfc5789#section-2
 [5]: https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html
 [6]: https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm
 [7]: https://en.wikipedia.org/wiki/HTTP_301
 [8]: https://tools.ietf.org/html/rfc4287