---
title: Eventi di dominio in Spring framework
author: giovannitomasicchio
type: post
date: 2017-12-16T16:22:54+00:00
url: /eventi-dominio-spring-framework/
thumbnail: /image/blog/2017/12/eventi.jpg
categories:
  - Senza categoria
tags:
  - domain event
  - Domain-driven design
  - Spring

---
Qualche giorno fa un collega mi ha chiesto consigli su come disaccoppiare il codice che gestisce la logica di business dal codice che produce eventuali &#8220;reazioni&#8221; del sistema all&#8217;operazione eseguita. La sua necessità sorgeva dal fatto che l&#8217;applicazione su cui sta lavorando, realizzata con Spring framework, sarebbe stata utilizzata da diversi clienti, ciascuno dei quali avrebbe potenzialmente richiesto, per la medesima operazione principale, l&#8217;esecuzione di differenti operazioni secondarie. Una soluzione a questo problema è l&#8217;introduzione nel sistema degli eventi di dominio.

## Eventi di dominio

Il concetto di evento di dominio appartiene al [Domain-driven design][1] (DDD), ma a ben vedere non viene esplicitamente citato nel [testo][2] di Eric Evans del 2003. L&#8217;autore del DDD infatti introdusse gli eventi di dominio solo in lavori successivi e forse è per questo che la loro adozione è meno diffusa rispetto ai concetti di [Entity e Value Object][3].

In estrema sintesi, un evento di dominio rappresenta un accadimento che ha modificato lo stato del sistema, del modello, avvenuto in un preciso istante di tempo e scatenato da una precisa causa, ad esempio da una richiesta dell&#8217;utente. Ad un evento sono associate anche altre caratteristiche, come la tipologia dell&#8217;evento (&#8220;prodotto acquistato&#8221;, &#8220;utente iscritto&#8221;, ecc.) ed ulteriori informazioni specifiche per ciascuna tipologia (identificativo del prodotto acquistato, identificativo dell&#8217;utente registrato, ecc.).

Gli eventi di dominio possono essere utilizzati per molteplici scopi. Ad esempio, memorizzandoli su file o database si realizza un log dettagliato delle operazioni effettuate sul sistema. Oppure possono essere impiegati per disaccoppiare cause ed effetti all&#8217;interno di uno stesso dominio, o ancora per notificare le modifiche subite da un dominio a domini esterni.

## Eventi di Spring framework

Spring framework, fin dalle sue prime versioni, possiede un sistema di gestione degli eventi. Inizialmente era destinato principalmente alla gestione degli eventi specifici del framework, come il &#8220;refresh del context&#8221;, ma di recente (versioni 4.2+) tale sistema è stato reso più flessibile per semplificare la gestione di eventi definiti dallo sviluppatore, come ad esempio gli eventi di dominio.

Per modellare un evento, come ad esempio la creazione di un nuovo post di un blog, basta creare una generica classe, con gli attributi che riteniamo sufficienti a descrivere i dettagli dell&#8217;evento. E&#8217; consigliabile assegnare alla classe un nome che sia identificativo della tipologia di evento. Inoltre è opportuno renderla immutabile: una volta istanziato un evento non deve poter essere modificato, non c&#8217;è motivo per farlo.

```java
    public final class PostCreatedEvent {

	private final int postId;
	
	private final int userId;
	
	private final boolean isPublished;
	
	private final LocalDateTime creationDateTime;
	
	public PostCreatedEvent(int postId, int userId, boolean isPublished) {
		this.postId = postId;
		this.userId = userId;
		this.isPublished = isPublished;
		this.creationDateTime = LocalDateTime.now();
		
	}

	public int getPostId() {
		return postId;
	}

	public int getUserId() {
		return userId;
	}

	public boolean isPublished() {
		return isPublished;
	}

	public LocalDateTime getCreationDateTime() {
		return creationDateTime;
	}	
}
```

### Pubblicare un evento

Per trasmettere un evento ai potenziali fruitori è sufficiente utilizzare il metodo publishEvent della classe [ApplicationEventPublisher][4] di Spring.

```java
public class PostService {

	@Autowired
	ApplicationEventPublisher publisher;

	public void create() {
		...
		publisher.publishEvent(new PostCreatedEvent(123, 456, true));
	}
}
```

### Ricevere un evento

Per processare un evento basta sviluppare un metodo che lo preveda come parametro di input, in una qualunque classe, e annotare il metodo con [@EventListener][5]:

```java
@Component
public class PostEventsListener {

	private static final Logger logger = LoggerFactory.getLogger(PostEventsListener.class);

	@EventListener
	public void onCreate(PostCreatedEvent event) {
		logger.info("PostEventsListener.onCreate for post " + event.getPostId());
	}
}
```

In caso di presenza di più listener per lo stesso tipo di evento, per specificare un ordine è possibile utilizzare l&#8217;annotazione [@Order][6].

Nel precedente esempio, abbiamo definito il metodo onCreate come void. Interessante notare che se invece avessimo restituito un oggetto, questo sarebbe stato interpretato da Spring come un nuovo evento. In questo modo è possibile, ad esempio, realizzare un **layer di traduzione degli eventi** di un dominio in eventi di un dominio esterno.

### Ricezione condizionata di un evento

E&#8217; anche possibile ricevere l&#8217;evento in maniera condizionata, ovvero soltanto se uno specifico attributo dell&#8217;evento vale _true_, utilizzando una [SpEl][7]:

```java
@EventListener(condition = "#event.isPublished")
```

E&#8217; inoltre possibile condizionare la ricezione di un evento legandolo ad una **fase della transazione** corrente. Ad esempio, se vogliamo ricevere un evento solo se la transazione si è conclusa correttamente, al posto dell&#8217;annotazione @EventListener dovremo usare l&#8217;annotazione [@TransactionalEventListener][8], che di default attiva il metodo listener solo in caso di commit. Potremo configurare questa annotazione per ricevere l&#8217;evento anche in altre fasi della transazione, ad esempio BEFORE\_COMMIT,  AFTER\_ROLLBACK e AFTER_COMPLETION.

### Ricezione sincrona e asincrona

I metodi annotati con @EventListener ricevono l&#8217;evento in maniera sincrona con il metodo che ha generato l&#8217;evento. Anche i metodi annotati con @TransactionalEventListener vengono eseguiti in maniera sincrona, con l&#8217;unica differenza che non vengono invocati nell&#8217;esatto momento in cui l&#8217;evento viene generato ma la loro esecuzione è posticipata all&#8217;esecuzione della fase della transazione a cui sono associati, ad esempio al commit della transazione. Sono quindi eseguiti dallo stesso thread che ha generato l&#8217;evento e pertanto, in una applicazione web, la _response_ non viene restituita al chiamante fintanto che sono in esecuzione i metodi listener.

E&#8217; possibile però utilizzare un altra caratteristica dello Spring framework per rendere asincrona l&#8217;esecuzione dei listener, quindi affidare la loro esecuzione ad un thread differente da quello nel quale è stato generato l&#8217;evento. Per farlo è sufficiente configurare l&#8217;applicazione con l&#8217;annotazione [@EnableAsync][9] e applicare sui metodi listener l&#8217;annotazione [@Async][10].

```java
@Component
public class PostEventsListener {

	private static final Logger logger = LoggerFactory.getLogger(PostEventsListener.class);
	
	@EventListener
	public void onCreate(PostCreatedEvent event) {
		logger.info("PostEventsListener.onCreate for post " + event.getPostId()); 
	}
	
	@TransactionalEventListener
	public void onCreateTrans(PostCreatedEvent event) {
		logger.info("PostEventsListener.onCreateTrans for post " + event.getPostId()); 
	}
	
	@Async
	@TransactionalEventListener
	public void onCreateAsyncTrans(PostCreatedEvent event) {
		logger.info("PostEventsListener.onCreateAsyncTrans for post " + event.getPostId());
	}
}
```

Spring in questo caso, nel momento in cui viene generato un evento, avvia un nuovo thread in cui eseguire il metodo del listener.

Se questa modalità di esecuzione è molto utilizzata nella nostra applicazione allora, per evitare l&#8217;overhead dovuto alla creazione di sempre nuovi thread, è possibile indicare a Spring di adottare un gestore dei thread asincroni che supporti meccanismi di cache (pool) dei thread, come il [ThreadPoolTaskExecutor][11], istanziabile nel seguente modo:

```java
@Bean
public ThreadPoolTaskExecutor threadPoolTaskExecutor() {
	ThreadPoolTaskExecutor pool = new ThreadPoolTaskExecutor();
	pool.setCorePoolSize(10);
	return pool;
}
```

## Eventi di dominio in un sistema distribuito

L&#8217;approccio descritto è utile a disaccoppiare il codice che genera un evento di dominio da quello che lo gestisce ma richiede che produttore e consumatore di eventi siano sviluppati all&#8217;interno della stessa applicazione Spring. Spesso però è necessario notificare un evento di dominio ad un altro applicativo, ma purtroppo le cose si complicano poiché significa che ci troviamo di fronte ad un sistema distribuito. In tal caso è opportuno utilizzare strumenti di integrazione, come [Apache Kafka][12] o [RabbitMQ][13].

In questo caso l&#8217;approccio descritto tornerebbe comunque utile, ad esempio, per disaccoppiare il codice che genera l&#8217;evento dal codice che inoltra l&#8217;evento ad uno di questi sistemi di integrazione.

 [1]: https://it.wikipedia.org/wiki/Domain-driven_design
 [2]: https://www.amazon.it/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215/
 [3]: https://martinfowler.com/bliki/EvansClassification.html
 [4]: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisher.html
 [5]: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/event/EventListener.html
 [6]: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/annotation/Order.html
 [7]: https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#expressions
 [8]: https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/event/TransactionalEventListener.html
 [9]: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/annotation/EnableAsync.html
 [10]: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/annotation/Async.html
 [11]: https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html
 [12]: https://kafka.apache.org/
 [13]: https://www.rabbitmq.com/