---
title: Cloud computing, ci interessa davvero?
author: giovannitomasicchio
type: post
date: 2017-08-30T17:51:44+00:00
url: /cloud-computing-ci-interessa-davvero/
thumbnail: /image/blog/2017/08/cloud.jpg
categories:
  - Senza categoria
tags:
  - Amazon AWS
  - cloud
  - Google Cloud
  - Microsoft Azure

---
Non sono un venditore di servizi cloud e quindi non starò qui a fare uno spot su queste tecnologie. Il web è ricco di dissertazioni sul cloud, sono certo che vi sarete già fatti una idea sui pro e contro. Il punto è un altro. Come progettisti di applicazioni, dovremmo in qualche modo farci _ispirare_ dal cloud nella realizzazione del nostro prossimo progetto?

Non mi riferisco tanto alle soluzioni di tipo &#8220;Infrastructure as a Service&#8221; (IAAS), incentrate sull&#8217;offerta di server virtuali, connettività di rete, server per lo storage e backup. Penso invece ai servizi di tipo &#8220;Platform as a Service&#8221;(PAAS) costituiti da un ecosistema di servizi dedicati alla realizzazione di applicazioni, tra cui:

  * sistemi per l&#8217;[esecuzione di applicazioni][1] scritte in diversi linguaggi (Java, PHP, Python, [linguaggi .NET][2], ecc.);
  * database relazionali e document oriented;
  * servizi di [Identity e Access Management][3];
  * repository Git e strumenti per la [CI/CD][4];
  * servizi di analisi dei dati;
  * soluzioni per la [scalabilità e l&#8217;alta affidabilità][5].

È sotto gli occhi di tutti che non c&#8217;è business che non si sia già trasformato in &#8220;as a service&#8221; o che non lo stia per fare. Banalizzando, potremmo dire che alle tradizionali vendite di beni, le imprese puntano adesso a far sottoscrivere piani &#8220;a consumo&#8221; per l&#8217;uso degli stessi, tarati sulle effettive necessità del cliente. Basti pensare, nel mondo del software, a quello che è successo all&#8217;Office 365 di Microsoft o al Photoshop CC di Adobe.

A mio avviso quindi faremmo bene a prendere subito in considerazione le soluzioni di cloud computing, ma non solo per i benefici tanto sbandierati, quanto per il fatto che in futuro il concetto stesso di server fisico su cui effettuare il deploy di una applicazione sarà solo un ricordo. Certo, ci saranno sempre delle situazioni che richiederanno l&#8217;allestimento di una infrastruttura ad-hoc, ma saranno sempre più relegate a contesti di nicchia nei quali, magari, verranno comunque adottate soluzioni di tipo private cloud (IAAS o PAAS).

Cosa accadrà quindi quando la nostra applicazione dovrà essere portata sul cloud? Funzionerà ancora? Sarà compatibile con quanto supportato dai servizi di [Amazon AWS][6], [Google Cloud][7] o [Microsoft Azure][8] in termini di linguaggio di programmazione, database, storage, strategie per la scalabilità e l&#8217;alta affidabilità e, più in generale, di architettura adottata? Certo, potremmo pensare che l&#8217;applicazione che stiamo realizzando non avrà tutta questa longevità. Oppure che, nei contesti in cui operiamo, il cloud stenterà ad arrivare. In entrambi i casi stiamo solo ignorando un processo inevitabile, come l&#8217;inverno nella favola della cicala e della formica.

## Morale della favola

Ci piaccia o no, i giganti del web stanno guidando una trasformazione verso il cloud computing,  le cloud app e le [architetture serverless][9] che è destinato a diventare, di diritto, uno standard de facto, con buona pace di chi spera in una evoluzione dell&#8217;IT più democratica. Aziende come Google, Amazon e Microsoft hanno fatto enormi investimenti per realizzare le infrastrutture informatiche, su scala planetaria, a supporto dei propri servizi. E&#8217; logico quindi aspettarsi che faranno di tutto per monetizzare tali investimenti, ad esempio rendendo economicamente sempre più vantaggioso l&#8217;utilizzo delle loro piattaforme rispetto all&#8217;adozione di soluzioni tradizionali.

Tocca quindi fare i conti con questa evoluzione ed individuare fin da subito un percorso per l&#8217;adeguamento delle nostre applicazioni al cloud. Dobbiamo identificare cosa, dei nostri sistemi software, non è immediatamente portabile sul cloud e come il cloud affronta quelle problematiche che abbiamo risolto sinora con soluzioni artigianali. Dobbiamo quindi individuare quali opportunità questa trasformazione offre al nostro business e definire una nostra strategia di adozione del cloud computing che ci porti gradualmente _tra le nuvole_.

 [1]: https://cloud.google.com/appengine/
 [2]: https://docs.microsoft.com/it-it/dotnet/azure/?view=azure-dotnet
 [3]: https://aws.amazon.com/it/iam/?hp=tile&so-exp=below
 [4]: https://aws.amazon.com/it/getting-started/projects/set-up-ci-cd-pipeline/
 [5]: https://aws.amazon.com/it/elasticloadbalancing/
 [6]: https://aws.amazon.com/it/
 [7]: https://cloud.google.com/
 [8]: https://azure.microsoft.com
 [9]: https://martinfowler.com/articles/serverless.html