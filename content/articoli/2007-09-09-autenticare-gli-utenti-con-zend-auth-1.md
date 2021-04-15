---
title: "Autenticare gli utenti con Zend_Auth - 1"
author: "Giovanni Tomasicchio"
type: article
date: 2007-09-09T10:50:02+0000
description: "Introduzione al componente Zend_Auth dello Zend Framework per l'autenticazione degli utenti"
url: /articoli/autenticare-gli-utenti-con-zend-auth-1/
categories:
  - Articoli
tags:
  - "database"
  - "Zend Framework"
  - "singleton"

  
---
###  Introduzione

 L'autenticazione degli utenti è una delle funzionalità più diffuse nelle applicazioni web. Questa operazione, comunemente chiamata login, si basa sul controllo delle credenziali fornite e non deve essere confusa con la gestione delle autorizzazioni, processo con cui si stabilisce se un utente (già identificato) possiede i diritti per accedere ad una particolare risorsa.

 Lo Zend Framework offre due componenti per la gestione dell'autenticazione e delle autorizzazioni, rispettivamente lo Zend\_Auth e lo Zend\_Acl. In questo articolo analizzeremo i principi di funzionamento di Zend\_Auth e nel successivo vedremo come realizzare un sistema di login basato su database.

 E' importante tener presente da subito che l'architettura di Zend\_Auth è fortemente modulare, come del resto lo è tutto il framework. Questo perché le modalità di autenticazione dell'utente possono essere molteplici, difficilmente esauribili con una rigida implementazione del componente.

- - - - - -

###  Architettura di Zend\_Auth

 **Zend\_Auth** è la classe principale del sistema di gestione dell'autenticazione utente dello Zend Framework. In una applicazione può esistere un solo oggetto Zend\_Auth, infatti la classe implementa il Singleton pattern. Non è necessario quindi istanziare direttamente questa classe ma, quando se ne ha bisogno, basta usare Zend\_Auth::getInstance() per accedere all'unica istanza, anche se nulla vieta di assegnare tale istanza ad una variabile.

 Come anticipato lo Zend\_Auth non è un componente monolitico ma si affida a due ulteriori classi per realizzare due importanti compiti: la verifica delle credenziali e la persistenza dell'identità dell'utente riconosciuto. La verifica delle credenziali viene realizzata da una classe detta **Zend\_Auth Adapter**. Ciascun Adapter è dedicato ad un particolare meccanismo di autenticazione. Quelli già presenti nello Zend Framework sono:

- **Zend\_Auth\_Adapter\_DbTable**, in grado di autenticare un utente verificando le sue credenziali (username e password) all'interno di una tabella di un database.
- **Zend\_Auth\_Adapter\_Digest**, che utilizza il sistema di autenticazione nativo dell'HTTP basato sulla richiesta di nome e password (crittografata con un sistema basato su MD5)
- **Zend\_Auth\_Adapter\_Http**, un sistema che implementa quasi completamente l'autenticazione HTTP, con invio di password sia in chiaro che attraverso digest.
 
 Se nessuno di questi Adapter fa al caso nostro, possiamo scriverne uno personalizzato, creando una classe che implementi l'interfaccia Zend\_Auth\_Adapter\_Interface.

 In generale un Adapter riceve in input le credenziali dell'utente, alcune informazioni per il setup del servizio di autenticazione (ad esempio nome della tabella del DB), e fornisce un metodo **authenticate()** per verificare l'identità dell'utente.

 Indipendentemente dall'Adapter utilizzato, il suo metodo authenticate() restituisce un oggetto di tipo **Zend\_Auth\_Result** attraverso il quale è possibile conoscere l'esito dell'operazione, richiamando uno dei seguenti metodi:

 **isValid()** - restituisce true se le credenziali dell'utente sono corrette

 **getCode()** - restituisce un codice (una costante della classe Zend\_Auth\_Result) che permette di determinare non solo l'esito della validazione ma anche le cause di un eventuale insuccesso. Di seguito i valori possibili:

- Zend\_Auth\_Result::SUCCESS: autenticazione effettuata con successo
- Zend\_Auth\_Result::FAILURE: autenticazione fallita
- Zend\_Auth\_Result::FAILURE\_IDENTITY\_NOT\_FOUND: username non trovato
- Zend\_Auth\_Result::FAILURE\_IDENTITY\_AMBIGUOUS: trovati più risultati per l'username fornito
- Zend\_Auth\_Result::FAILURE\_CREDENTIAL\_INVALID: password errata
- Zend\_Auth\_Result::FAILURE\_UNCATEGORIZED: altro tipo di errore
 
 **getIdentity()** - restituisce l'identità dell'utente utilizzata dura l'autenticazione. Generalmente si tratta dello username.

 **getMessages()** - restituisce un array di messaggi di errore generati durante il processo di autenticazione.

 Lo Zend\_Auth Adapter si limita a verificare le credenziali ma non conserva il risultato dell'operazione. La persistenza dell'identità dell'utente riconosciuto viene affidata ad un ulteriore componente, lo **Storage di Zend\_Auth**. Se non specificato diversamente si tratta dello **Zend\_Auth\_Storage\_Session**, il quale utilizza Zend\_Session (che a sua volta di default usa le sessioni di PHP). Anche in questo caso, come per l'Adapter, è possibile realizzare uno Storage personalizzato creando una classe che implementa l'interfaccia Zend\_Auth\_Storage\_Interface. A differenza dello Zend\_Auth Adapter che è necessario istanziare esplicitamente nel codice, lo Storage di Zend\_Auth viene creato e gestito in maniera trasparente da Zend\_Auth per cui nella maggior parte delle situazioni non dovremo preoccuparcene.

- - - - - -

###  Utilizzo di Zend\_Auth

 Vediamo ora quali sono i passi principali per effettuare l'autenticazione dell'utente, verificare successivamente la sua identità ed infine effettuare il logout.

 L'autenticazione dell'utente parte con la creazione ed il setup dell'Adapter adeguato. Ad esempio nel caso dello Zend\_Auth\_Adapter\_DbTable passiamo all'Adapter alcune informazioni sulla tabella del DB contenete le credenziali degli utenti e successivamente passiamo username e password dell'utente corrente (maggiori dettagli nel prossimo articolo).

 A questo punto si può procedere seguendo 2 strade:

 **1) Autenticazione diretta**: richiamiamo il metodo authenticate() dell'Adapter e recuperiamo l'oggetto Zend\_Auth\_Result per verificarne l'esito. Se l'autenticazione ha successo procediamo alla memorizzazione dell'identità dell'utente nello Storage di Zend\_Auth. Per farlo recuperiamo l'oggetto Storage col metodo getStorage() di Zend\_Auth e poi utilizziamo il metodo write().

 ```
<pre class="brush: php">
// Creo il mio Adapter (viene considerato un generico Adapter personalizzato)
$authAdapter = new MyAuthAdapter($username, $password);

// Effettuo l'autenticazione direttamente
$result = $authAdapter->authenticate();

if ($result->isValid()) {
    // Autenticazione avvenuta con successo, devo memorizzare l'esito
    Zend_Auth::getInstance()->getStorage()->write($username);
} else {
    // Autenticazione fallita
}
```

 **2) Autenticazione indiretta**: richiamiamo Zend\_Auth::authenticate() a cui passiamo come parametro proprio l'Adapter precedentemente creato. Zend\_Auth::authenticate() chiamerà a sua volta il metodo authenticate() dell'Adapter e restituirà un oggetto Zend\_Auth\_Result (analogamente a quanto avviene al punto 1). Inoltre, se l'autenticazione ha esito positivo, memorizzerà l'identità dell'utente nello Storage in modo da rendere permanente il risultato dell'autenticazione.

 ```
<pre class="brush: php">
$auth = Zend_Auth::getInstance();

// Creo il mio Adapter (viene considerato un generico Adapter personalizzato)
$authAdapter = new MyAuthAdapter($username, $password);

// Effettuo l'autenticazione indirettamente, attraverso Zend_Auth
$result = $auth->authenticate($authAdapter);

if ($result->isValid()) {
    // Autenticazione avvenuta con successo
} else {
    // Autenticazione fallita
}
```

 Anche se a prima vista l'autenticazione indiretta sembra più conveniente, per via della memorizzazione automatica dell'esito (l'identità dell'utente loggato), il primo metodo è da preferire quando vogliamo procedere personalmente alla memorizzazione di alcuni dati dell'utente nello Storage di Zend\_Auth, come vedremo nell'esempio che svilupperemo nel prossimo articolo.

 Effettuata l'autenticazione e memorizzato l'esito, per verificare se l'utente corrente è loggato basta controllare se la sua identità sia presente nello Storage di Zend\_Auth ed eventualmente recuperarla:

 ```
<pre class="brush: php">
$auth = Zend_Auth::getInstance();
if ($auth->hasIdentity()) {
    // recupero l'identità dell'utente
    $identity = $auth->getIdentity();
}
```

 Anche se queste operazioni vengono effettuate interrogando Zend\_Auth, in realtà questo componente interroga lo Storage per ottenere le informazioni necessarie.

 L'operazione di logout avviene invece cancellando dallo Storage l'identità precedentemente memorizzata, ed anche in questo caso possiamo demandare l'operazione a Zend\_Auth:

 ```
<pre class="brush: php">
Zend_Auth::getInstance()->clearIdentity();
```