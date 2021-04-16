---
title: "Autenticare gli utenti con Zend_Auth - 2"
author: "Giovanni Tomasicchio"
type: article
date: 2007-09-15T09:29:38+0000
description: "Creare un sistema di login in PHP con Zend_Auth utilizzando Zend_Auth_Adapter_DbTable per l'accesso ala database"
url: /articoli/autenticare-gli-utenti-con-zend-auth-2/
categories:
  - Articoli
tags:
  - "database"
  - "Zend Framework"
  - "MySQL"
  - "crittografia"
  - "CSS"
  - "exception"
  - "XML"

  
---
###  Zend\_Auth\_Adapter\_DbTable

 Nel precedente articolo abbiamo analizzato i diversi componenti che concorrono alla realizzazione del sistema di autenticazione utente presente nello Zend Framework. In particolare abbiamo visto come il controllo delle credenziali dell'utente avviene grazie ad una classe detta Adapter. Tra gli Adapter già presenti nello Zend Framework vi è **Zend\_Auth\_Adapter\_DbTable** che può essere usato per confrontare lo username e la password forniti dall'utente con quelli memorizzati in una tabella di un database.

 Per accedere al database lo Zend\_Auth\_Adapter\_DbTable necessita di una connessione al database, ovvero di uno Zend\_Db\_Adapter, oggetto che deve essere fornito direttamente al costruttore della classe. Gli ulteriori parametri per configurare Zend\_Auth\_Adapter\_DbTable sono:

- **tableName**: il nome della tabella del database che contiene username e password di tutti gli utenti, utilizzate per la verifica delle credenziali durante il login.
- **identityColumn**: il nome della colonna della tabella del database che contiene l'username degli utenti registrati.
- **credentialColumn**: il nome della colonna che contiene la password degli utenti.
- **credentialTreatment**: indica un eventuale sistema utilizzato per oscurare le password all'interno del database (crittografica, hash, ecc.). Ad esempio, se in un database MySQL vengono conservati gli hash MD5 delle password bisogna specificare 'MD5(?)'. Si noti che credentialTreatment non fa riferimento ad una funzione PHP bensì ad una funzione del database impiegato.
 
 Lo username e la password da verificare vengono fornite all'Adapter attraverso i metodi **setIdentity($username)** e **setCredential($password)**. Come per qualsiasi Adapter, il metodo authenticate() effettua il controllo di questi dati e restituisce un oggetto di tipo Zend\_Auth\_Result che, oltre a contenere l'esito della procedura, contiene anche lo username dell'utente. Se si procede con una autenticazione indiretta (si ricordi quanto detto nel precedente articolo) questa sarà l'unica informazione che Zend\_Auth conserverà nello Storage.

 Spesso però è conveniente rendere persistenti ulteriori informazioni relative all'utente loggato (ad esempio user\_id, data ultimo accesso, nome da mostrare, ecc.) e alcune di queste informazioni risiedono proprio sullo stesso record della tabella utenti utilizzata per l'autenticazione appena effettuata. Per questa ragione Zend\_Auth\_Adapter\_DbTable fornisce il metodo **getResultRowObject()** per recuperare anche le altre informazioni presenti nel record della tabella utenti. Effettuando una autenticazione indiretta potremo memorizzare tali informazioni nello Storage di Zend\_Auth.

 getResultRowObject() permette anche di recuperare solo alcune informazioni dal record della tabella. Se ad esempio vogliamo recuperare solo i campi "campo1" e "campo2" bisogna procedere in questo modo:

 ```php
$my_auth_adapter->getResultRowObject(array('campo1', 'campo2'));
```

 Se invece vogliamo recuperare tutti i campi tranne "campo3" e "campo4":

 ```php
$my_auth_adapter->getResultRowObject(null, array('campo3', 'campo4'));
```

- - - - - -

###  Applicazione di esempio

 Nelle pagine che seguono realizzeremo una piccola applicazione per vedere all'opera il componente Zend\_Auth ed applicare in un caso concreto alcune delle tecniche illustrate in questo e nel precedente articolo. L'applicazione è costituita da un unico Controller, IndexController, e da un unico template index.phtml che realizza un semplice form di login. Ad autenticazione avvenuta, con lo stesso template mostreremo un messaggio di benvenuto ed un link per effettuare il logout. L'ErrorController ed il relativo template non saranno realizzati.

 L'Adapter impiegato è lo Zend\_Auth\_Adapter\_DbTable che utilizza una connessione a MySQL, creata nel file di bootstrap, ed una tabella di nome "users" che è possibile creare con le seguenti query:

 ```sql
CREATE TABLE `users` (
   `user_id` int(11) NOT NULL auto_increment,
   `user_name` varchar(32) default NULL,
   `password` varchar(32) default NULL,
   `nome` varchar(32) default NULL,
   PRIMARY KEY  (`user_id`)
);


insert into `users` (`user_name`, `password`, `nome`) values('mario76','525d5d4f0cfb94d045c48971aa1aa974','mario rossi');
```

 La query di inserimento è utile per popolare la tabella degli utenti in modo da poter subito provare l'applicazione effettuando il login conusername "mario76" e password "segreto", memorizzata con il suo hash MD 5. Non verranno realizzate pagine per la creazione degli utenti, operazione che viene lasciata al lettore che potrà prendere spunto daltutorial sullo Zend Framework.

 Potete scaricare tutti gli script realizzati da [questo link](/download/ZF-auth.zip).

- - - - - -

###  Il file di bootstrap

 Il file di bootstrap utilizzato per questa semplice applicazione non contiene importanti variazioni rispetto a quello analizzato in dettaglio nel [tutorial sullo Zend Framework](/articoli/zend-framework/zend-framework-tutorial-1/) al quale rimando per eventuali approfondimenti.

 ```php
<?php
set_include_path(get_include_path().PATH_SEPARATOR.'C:\Programmi\ZendFramework\library');

require_once('Zend/Loader.php');

Zend_Loader::registerAutoload();

date_default_timezone_set('Europe/Rome');

try {
    $db = Zend_Db::factory('Pdo_Mysql', array(
    'host'     => 'localhost',
    'username' => 'root',
    'password' => 'secret',
    'dbname'   => 'ZF-auth'
    ));

    $db->getConnection();
} catch (Zend_Db_Adapter_Exception $e) {
    die("Zend_Db_Adapter_Exception: ".$e->getMessage());
} catch (Zend_Exception $e) {
    die("Zend_Exception".$e->getMessage());
}

Zend_Registry::set('db', $db);

define('BASE_URL', str_replace('index.php','',$_SERVER['PHP_SELF']));
Zend_Controller_Front::run('controllers');
?>
```

- - - - - -

###  IndexController

 Considerato lo scopo didattico di questa applicazione e senza perdere di generalità, si è scelto di implementare tutto il sistema di login e logout direttamente nell'IndexController. Analizziamo il funzionamento delle diverse Action:

 **indexAction**: quando viene mostrata la pagina principale di login (nel nostro caso si tratta dell'unica pagina realizzata) dobbiamo semplicemente passare al template i dati relativi ad un eventuale utente loggato. In questo modo il template potrà mostrare un messaggio di benvenuto personalizzato.

 **loginAction**: questa action viene invocata quando l'utente effettua il submit del form di login. Per verificare le credenziali ricevute bisogna prima effettuare il setup di Zend\_Auth\_Adapter\_DbTable. Dopo aver verificato che username e password non sono vuote, passiamo questi dati all'Adapter e li verifichiamo chiamando authenticate(). Attraverso $result controlliamo l'esito della procedura. In caso di errore mostreremo un messaggio. Se invece l'utente viene riconosciuto memorizziamo nello Storage lo user\_id ed il nome dell'utente. La procedura si conclude con il redirect alla stessa pagina di login.

 **logoutAction**: viene invocata dall'utente cliccando su un link "logout". L'operazione richiede semplicemente l'esecuzione del metodo clearIdentity() di Zend\_Auth.

 ```php
<?php
class IndexController extends Zend_Controller_Action
{
    protected $_flashMessenger = null;

    public function init()
    {
        $this->_flashMessenger = $this->_helper->getHelper('FlashMessenger');
        $this->view->messaggi = $this->_flashMessenger->getMessages();
    }

    public function indexAction()
    {
        $this->view->identity = Zend_Auth::getInstance()->getIdentity();
    }

    public function loginAction()
    {
        $db = Zend_Registry::get('db');

        $my_auth_adapter = new Zend_Auth_Adapter_DbTable($db, 'users', 'user_name', 'password', 'MD5(?)');

        $username = trim($this->_getParam('username'));
        $password = trim($this->_getParam('password'));

        // non possiamo invocare authenticate() se username o password sono vuoti
        // altrimenti Zend_Auth_Adapter_DbTable solleva una eccezione
        if(!$username || ! $password) {
            $this->_flashMessenger->addMessage('Username o password non inserita');
            $this->_redirect('/');
        }

        $my_auth_adapter->setIdentity($username)->setCredential($password);

        $result = $my_auth_adapter->authenticate();

        // dal codice contenuto nel risultato dell'autenticazione recuperiamo l'esito
        // potevamo usare semplicemente $result->isValid()
        switch ($result->getCode()){
            //Login riuscito.
            case Zend_Auth_Result::SUCCESS :
                // la verifica è andata a buon fine
                // dobbiamo quindi memorizzare i dati dell'utente nello Storage di Zend_Auth (Zend_Auth_Storage_Session)
                // non memorizziamo tutti i dati del record della tabella relativa all'utente ma solo quelli di nostro interesse
                 Zend_Auth::getInstance()->getStorage()->write($my_auth_adapter->getResultRowObject(array('user_id', 'nome')));

                 // per memorizzare tutti i dati del record, tranne la password
                 //$this->_auth->getStorage()->write($adapter->getResultRowObject(null, 'password'));
                break;
                //Username non trovato
            case Zend_Auth_Result::FAILURE_IDENTITY_NOT_FOUND :
                $this->_flashMessenger->addMessage('Username non trovato');
                break;
                //Password non corretta
            case Zend_Auth_Result::FAILURE_CREDENTIAL_INVALID :
                $this->_flashMessenger->addMessage('Password errata');
                break;
                //Errore sconosciuto
            default:
                $this->_flashMessenger->addMessage('Errore Sconosciuto');
                break;
        }
        $this->_redirect('/');
    }

    public function logoutAction()
    {
        // per effettuare il logout basta cancellare i dati dell'utente corrente
        // memorizzati in Zend_Auth_Storage_Session
        // con il seguente metodo sarà lo Zend_Auth ad occuparsene
        Zend_Auth::getInstance()->clearIdentity();
        $this->_flashMessenger->addMessage('Log-out effettuato con successo.');
        $this->_redirect('/');
    }
}
?>
```

- - - - - -

###  Il template

 Di seguito viene riportato l'unico template presente nella nostra piccola applicazione web. Per gli utenti non ancora identificati è necessario mostrare il form di login mentre per gli utenti loggati bisogna mostrare un messaggio di benvenuto personalizzato ed un link per il logout. Questa distinzione viene realizzata con un semplice IF che interroga il metodo hasIdentity() di Zend\_Auth. Si noti infine l'utilizzo di $this-&gt;identity-&gt;nome per recuperare il nome dell'utente corrente da mostrare nel messaggio di benvenuto.

 Anche in questo caso si rimanda al tutorial sullo Zend Framework per i dettagli sulle altre funzioni presenti.

 ```php

<html xmlns="http://www.w3.org/1999/xhtml" lang="it" xml:lang="it">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
    <title>Zend_Auth Tutorial</title>
    <link rel="stylesheet" type="text/css" media="screen, handheld, print" href="<?php echo BASE_URL?>main.css" />
</head>
<body>
    <div id="page">
        <h1>Home page</h1>
<p class="errore"><?php echo $this->escape($this->messaggi[0])?></p>
<?php if (!Zend_Auth::getInstance()->hasIdentity()): ?>
    <form action="<?php echo BASE_URL?>index/login" method="post">
    <fieldset>
    <legend>Login</legend>
    <p><label>Username:<br />
    <?php echo $this->formText('username')?></label></p>
    <p><label>Password:<br />
    <?php echo $this->formPassword('password')?></label></p>
    <p><?php echo $this->formSubmit('submit', 'Login')?></p>
    </fieldset>
</form>
<?php else: ?>
    <p>Benvenuto <?php echo $this->escape($this->identity->nome)?></p>
    <p><a href="<?php echo BASE_URL?>index/logout">log-out</a>
<?php endif; ?>
    </div>
    </body>
</html>
```