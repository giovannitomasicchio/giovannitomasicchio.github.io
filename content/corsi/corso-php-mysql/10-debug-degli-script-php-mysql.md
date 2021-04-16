---
title: "Debug degli script PHP/MySQL"
author: "Giovanni Tomasicchio"
type: corso
date: 2006-08-26T18:03:45+0000
description: "Risolvere gli errori di uno script PHP MySQL. Debug degli script PHP/MySQL"
url: /corsi/corso-php-mysql/debug-degli-script-php-mysql/
weight: 1
categories:
  - Corso PHP MySQL
  
---
 Individuare i bachi di uno script PHP/MySQL ed interpretare correttamente le segnalazioni di errore può essere un compito non banale, sopratutto se si è alle prime armi. Inutile sottolineare che una corretta impostazione degli script ed una adeguata gestione degli errori facilitano le operazioni di debug e sopratutto riducono la probabilità di malfunzionamenti del codice. Senza alcuna pretesa di completezza, di seguito vengono proposte alcune linee guida da seguire nella realizzazione di script PHP/MySQL:

- Durante la fase di sviluppo, impostare il livello di segnalazione degli errori al massimo; attraverso il php.ini, impostando la direttiva "error\_reporting" a "E\_ALL", oppure direttamente da script, con l'istruzione error\_reporting(E\_ALL) da inserire all'inizio del codice. E' più semplice risolvere un baco quando si hanno informazioni anche sulle anomalie considerate (forse a torto) meno importanti.
- Controllare l'esito delle funzioni mysql\_connect, mysql\_select\_db, mysql\_query ecc. verificando il valore che restituiscono e confrontandolo con quello atteso. Ad esempio se il valore restituito da mysql\_query è false allora la query non è corretta.
- Usare il carattere @ anteposto alle funzioni mysql come "silenziatore" dei messaggi di errore solo se l'esito della funzione stessa viene verificato in altro modo. In caso contrario un errore passerebbe inosservato.
- Interrompere l'esecuzione dello script al primo errore segnalato. Si eviterà così la generazione di errori a catena che possono confondere e complicare la ricerca della causa primaria del malfunzionamento. Ad esempio, è inutile cercare di eseguire una query quando la connessione al server non ha avuto successo. Si otterrebbe un errore anche per la query ma il realtà il problema è a monte.
- Memorizzare tutte le query create dinamicamente in una variabile prima di lanciarle con la funzione mysql\_query. In caso di errore mostrare il contenuto di questa variabile, ad esempio con la funzione die(), per verificare se la costruzione della query è corretta.
- Prima di realizzare lo script provare ad eseguire le query direttamente con un client MySQL in modo da essere sicuri della loro correttezza. Come approfondiremo più avanti, è importante saper distinguere tra gli errori dell'SQL e quelli dello script PHP.
 
 Nonostante tutte le precauzioni che è possibile (e doveroso) prendere, prima o poi qualcosa nei vostri script andrà storto e vi ritroverete ad interpretare le classiche segnalazioni di errore di PHP, alcune delle quali possono risultare davvero sibilline. Di seguito vengono riportate quelle più comuni insieme alla spiegazione delle possibili cause e soluzioni.

 **Fatal error: Call to undefined function mysql\_connect()**

 PHP non riconosce la funzione mysql\_connect() a causa del mancato caricamento del modulo mysql. Solo la versione Windows di PHP 4 ha il modulo mysql già presente all'interno di PHP. Trovate le istruzioni per caricare il modulo mysql con PHP 5 su Windows XP in questo articolo. Con Linux è invece necessario ricompilare PHP con l'opzione --with-mysql oppure installare i pacchetti (RPM, deb, ecc.) opportuni offerti dalla distribuzione in uso.

 **Warning: mysql\_connect() \[function.mysql-connect\]: Unknown MySQL server host 'nome\_server' (11001)**

 PHP non riesce ad individuare l'host (la macchina) su cui dovrebbe girare il server MySQL. Controllare quindi la sintassi del nome\_server.

 **Warning: mysql\_connect() \[function.mysql-connect\]: Can't connect to MySQL server on 'localhost' (10061)**

 Nella maggior parte dei casi questo errore si verifica se il server MySQL non è attivo. In Windows è facile verificarlo, accedendo al Task Manager (Ctrl+Alt+Canc) e verificando la presenza di un processo dal nome mysqld-nt.exe. Con linux si può invece cercare il demone mysqld dalla shell con il comando "ps aux". Se non è attivo le cause possono essere diverse: MySQL non è stato installato, l'installazione non è stata completata correttamente, MySQL semplicemente non è stato avviato. Se state cercando di collegarvi ad un server remoto (non in localhost), la connessione potrebbe essere impedita da un firewall, oppure MySQL potrebbe essere stato configurato per non accettare connessioni dall'esterno.

 **Warning: mysql\_connect() \[function.mysql-connect\]: Access denied for user 'nome'@'localhost' (using password: YES/NO)**

 Il nome e/o la password forniti alla funzione mysql\_connect() non sono corretti. L'errore mostra il nome utente e l'host al quale ci si sta connettendo e pertanto potrebbe costituire un problema di sicurezza. In questo caso è consigliato usare il silenziatore degli errori @ e verificare l'esito di mysql\_connect controllandone il risultato restituito.

 **Client does not support authentication protocol requested by server; consider upgrading MySQL client**

 Tipico errore che si presenta la prima volta che si esegue uno script di connessione con PHP 4 a MySQL 4.1 o successivi. PHP 4 infatti viene distribuito con una libreria per l'accesso a MySQL che non supporta la nuova modalità di connessione introdotta con MySQL 4.1 e pertanto il tentativo fallisce. Una possibile soluzione risiede nell'indicare a MySQL di usare il vecchio metodo di autenticazione. Per farlo basta lanciare questa query impiegando un client MySQL:

 SET PASSWORD FOR 'nome\_uteste'@'nome\_host' = OLD\_PASSWORD('nuova\_password');

 **Unknown database 'db\_inesistente'**

 E' stata eseguita l'istruzione mysql\_select\_db() specificando il nome di un database che però non esiste. Controllare quindi il nome del database indicato.

 **Warning: mysql\_select\_db(): supplied argument is not a valid MySQL-Link resource**

 Il messaggio avverte che il secondo parametro passato alla funzione mysql\_select\_db() non è un valido identificativo di connessione MySQL, ovvero il valore restituito da una mysql\_connect() eseguita con successo. Una delle cause più frequenti è un fallito tentativo di connessione al server MySQL ed il successivo tentativo di selezione del database. In tal caso quindi bisogna verificare la procedura di connessione. Oppure, più banalmente, il secondo parametro passato a mysql\_select\_db() non è il risultato restituito dalla mysql\_connect().

 **Warning: mysql\_fetch\_array(): supplied argument is not a valid MySQL result resource**

 **Warning: mysql\_free\_result(): supplied argument is not a valid MySQL result resource**

 Il messaggio avverte che il parametro passato all'istruzione di fetch o alla mysql\_free\_result() non è un valido "MySQL result resource", ovvero quella particolare "ricevuta" restituita da mysql\_query() in caso di una SELECT eseguita con successo (se n'è discusso nella [Lezione 6](/corsi/corso-php-mysql/selezione-dati-recupero-risultati-i/)). Probabilmente quindi stiamo cercando di effettuare un operazione sul set di risultati ma in realtà tali dati non esistono perché la query non ha avuto successo. Oppure, più banalmente, il parametro passato all'istruzione di fetch o alla mysql\_free\_result() non è il risultato restituito da mysql\_query().

 Questi errori si verificano quando è stata effettuata una query contenente degli errori. L'istruzione mysql\_query ha segnalato il problema restituendo il valore FALSE ma nello script non c'è stato un controllo su tale valore. Lo script pertanto prosegue fino ad arrivare alle istruzioni di fetch che però non possono agire poiché non esiste un set di risultati.

###  Errori nella sintassi delle query

 Le segnalazioni di errore fin qui mostrate vengono generate da PHP, ad esempio perché non è in grado di connettersi al server MySQL, oppure perché le funzioni non vengono adoperate correttamente. Sono quindi errori che coinvolgono PHP direttamente che pertanto provvede subito a segnalarli con dei Warning. Ci sono però errori che non riguardano PHP ma si verificano nel server MySQL, generati ad esempio da una query con sintassi errata. In questi casi è MySQL ad accorgersi del problema ma non potendo comunicare direttamente con l'utente, può solo segnalare l'accaduto a PHP che a sua volta si limita a restituire FALSE come risultato della mysql\_query(). Tali errori quindi possono passare del tutto inosservati se nello script PHP non vengono implementati gli opportuni controlli. Per simili errori la funzione mysql\_error() è l'unica a poterci dare informazioni precise sul problema verificatosi.