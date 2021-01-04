---
title: Panoramica di AMQP 1,0 nel bus di servizio di Azure
description: Informazioni su come il bus di servizio di Azure supporta Advance Message Queueing Protocol (AMQP), un protocollo standard aperto.
ms.topic: article
ms.date: 11/20/2020
ms.openlocfilehash: 241a6b7fb9f9055ce610e44e5cd37dba87c77554
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2020
ms.locfileid: "97709283"
---
# <a name="amqp-10-support-in-service-bus"></a>Supporto per il protocollo AMQP 1.0 nel bus di servizio
Il servizio cloud del bus di servizio di Azure usa il [protocollo AMQP (Advanced Message Queueing Protocol) 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) come mezzo di comunicazione principale. Microsoft è stata impegnata con i partner di tutto il settore, sia per i clienti che per i fornitori di broker di messaggistica in competizione, per sviluppare e sviluppare AMQP negli ultimi decenni, con nuove estensioni sviluppate nel [Comitato tecnico AMQP di Oasis](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp). AMQP 1,0 è uno standard ISO e IEC ([iso 19464:20149](https://www.iso.org/standard/64955.html)). 

AMQP consente di creare applicazioni ibride multipiattaforma usando un protocollo standard aperto indipendente dal fornitore e dall'implementazione. È possibile creare applicazioni usando componenti creati in linguaggi e framework diversi e in esecuzione su sistemi operativi diversi. Tutti questi componenti possono connettersi al bus di servizio e scambiare in modo lineare ed efficiente messaggi di business strutturati con assoluta fedeltà.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduzione: informazioni sul protocollo AMQP 1.0 e sulla sua rilevanza
I prodotti middleware orientati ai messaggi hanno tradizionalmente usato protocolli proprietari per le comunicazioni tra applicazioni client e broker. Dopo la selezione di un broker di messaggistica di un fornitore specifico, è pertanto necessario utilizzare le librerie di tale fornitore per connettere le applicazioni client a tale broker. Ne risulta un livello di dipendenza rispetto a tale fornitore, poiché il trasferimento di un'applicazione a un prodotto diverso richiede la modifica del codice per tutte le applicazioni connesse. Nella community Java, gli standard API specifici del linguaggio come Java Message Service (JMS) e le astrazioni di Spring Framework hanno attenuato il dolore, ma hanno un ambito di funzionalità molto ridotto ed escludono gli sviluppatori che usano altri linguaggi.

La connessione di broker di messaggistica da fornitori diversi è inoltre complicata e richiede in genere bridging a livello di applicazione per lo spostamento dei messaggi da un sistema all'altro e per la conversione nei diversi formati di messaggio proprietari. Questa esigenza è molto comune, ad esempio, quando è necessario fornire una nuova interfaccia unificata per sistemi diversi precedenti oppure integrare sistemi IT diversi dopo una fusione tra società. AMQP consente di collegare direttamente i broker di connessione, ad esempio usando router come [Apache Qpid Dispatch router](https://qpid.apache.org/components/dispatch-router/index.html) o "pale" native di broker come quello di [RabbitMQ](service-bus-integrate-with-rabbitmq.md).

Il settore software è caratterizzato da rapido sviluppo. Nuovi linguaggi di programmazione e framework applicazione vengono introdotti a ritmo sorprendente. Analogamente, i requisiti dei sistemi IT si evolvono nel tempo e gli sviluppatori desiderano avvalersi delle possibilità offerte dai linguaggi e dalle funzionalità più recenti della piattaforma. A volte, tuttavia, il fornitore di messaggistica selezionato non supporta tali piattaforme. Se i protocolli di messaggistica sono proprietari, non è possibile che altri forniscano librerie per queste nuove piattaforme. Pertanto, è necessario usare approcci come la creazione di gateway o bridge che consentano di continuare a usare il prodotto di messaggistica.

Il protocollo AMQP (Advanced Message Queuing Protocol) 1.0 è stato sviluppato per risolvere questi problemi presso JP Morgan Chase, che, come la maggior parte delle aziende del settore dei servizi finanziari, fa ampio uso di prodotti middleware orientati alla messaggistica. L'obiettivo da raggiungere era semplice: creare un protocollo di messaggistica a standard aperto che consentisse di creare applicazioni basate sui messaggi usando componenti creati in linguaggi, framework e sistemi operativi diversi, avvalendosi dei migliori prodotti offerti da diversi fornitori.

## <a name="amqp-10-technical-features"></a>Caratteristiche tecniche del protocollo AMQP 1.0
AMQP 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere utilizzato per creare applicazioni di messaggistica multipiattaforma e affidabili. Questo protocollo assolve a uno scopo semplice, ovvero definire i meccanismi per la trasmissione sicura, affidabile ed efficiente dei messaggi tra due parti. I messaggi stessi vengono codificati usando una rappresentazione dati portatile che consente a mittenti e destinatari eterogenei di scambiarsi messaggi di business strutturati con assoluta fedeltà. Di seguito sono riepilogate le caratteristiche più importanti:

* **Efficienza**: AMQP 1.0 è un protocollo orientato alla connessione che usa una codifica binaria per le istruzioni del protocollo e per i messaggi aziendali trasmessi. Integra sofisticati schemi di controllo di flusso per massimizzare l'uso della rete e dei componenti connessi. In ogni caso, il protocollo è stato progettato per assicurare un equilibrio tra efficienza, flessibilità e interoperabilità.
* **Affidabilità**: il protocollo AMQP 1.0 consente lo scambio di messaggi con una serie di garanzie di affidabilità, dal "fire-and-forget" al recapito confermato affidabile di tipo "exactly-once".
* **Flessibilità**: AMQP 1.0 è un protocollo flessibile che consente di supportare diverse topologie. È possibile utilizzare lo stesso protocollo per le comunicazioni client-client, client-broker e broker-broker.
* **Indipendenza dal modello di broker**: il protocollo AMQP 1.0 non prevede requisiti relativi al modello di messaggistica usato da un broker. È pertanto possibile aggiungere con facilità il supporto per il protocollo AMQP 1.0 ai broker di messaggistica esistenti.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 è uno standard affermato
AMQP 1.0 è uno standard internazionale approvato da ISO e IEC come ISO/IEC 19464:2014.

Lo sviluppo di AMQP 1.0 procede dal 2008 a opera di un gruppo fondamentale di oltre 20 società, di cui fanno parte fornitori di tecnologie e aziende utenti finali. In questo lasso di tempo, le aziende utenti hanno comunicato i propri requisiti aziendali reali e i fornitori di tecnologie hanno proseguito lo sviluppo del protocollo con l'intento di soddisfarli. Nel corso del processo i fornitori hanno partecipato a workshop, che hanno permesso loro di collaborare per verificare l'interoperabilità tra le diverse implementazioni.

A ottobre 2011 il lavoro di sviluppo è passato a un comitato tecnico all'interno della Organization for the Advancement of Structured Information Standards (OASIS) e a ottobre 2012 è stato rilasciato lo standard OASIS AMQP 1.0. Del comitato tecnico hanno fatto parte le seguenti aziende durante lo sviluppo dello standard:

* **Fornitori di tecnologie**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Aziende utenti**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Le attuali cattedre del [Comitato tecnico Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) rappresentano Red Hat e Microsoft.

Di seguito sono indicati alcuni dei vantaggi più apprezzati degli standard aperti:

* Minore possibilità di blocco da parte del fornitore
* Interoperabilità
* Ampia disponibilità di librerie e strumenti
* Protezione dall'obsolescenza
* Disponibilità di personale competente
* Rischio ridotto e gestibile

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e bus di servizio
Il supporto di AMQP 1,0 nel bus di servizio di Azure significa che è possibile sfruttare le funzionalità di Accodamento del bus di servizio e di messaggistica negoziata di pubblicazione/sottoscrizione da una vasta gamma di piattaforme usando un protocollo binario efficiente. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

La figura riportata di seguito illustra una distribuzione di esempio, in cui i client Java in esecuzione su Linux, scritti usando l'API JMS (Java Message Service) standard, e i client .NET in esecuzione su Windows si scambiano messaggi tramite il bus di servizio usando il protocollo AMQP 1.0.

![Diagramma che mostra un bus di servizio che scambia messaggi con due ambienti Linux e due ambienti Windows.][0]

**Figura 1: Scenario di distribuzione di esempio, in cui per la messaggistica tra diverse piattaforme usa il bus di servizio e il protocollo AMQP 1.0**

Tutte le librerie client del bus di servizio supportate disponibili tramite Azure SDK usano AMQP 1,0.

- [Bus di servizio di Azure per .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true&view=azure-dotnet)
- [Librerie del bus di servizio di Azure per Java](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-stable)
- [Provider del bus di servizio di Azure per Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Moduli del bus di servizio di Azure per JavaScript e TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true&view=azure-node-latest)
- [Librerie del bus di servizio di Azure per Python](/python/api/overview/azure/servicebus?preserve-view=true&view=azure-python)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Inoltre, è possibile usare il bus di servizio da qualsiasi stack di protocolli conforme a AMQP 1,0:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Riepilogo
* AMQP 1.0 è un protocollo di messaggistica aperto e affidabile, che può essere utilizzato per creare applicazioni ibride multipiattaforma. AMQP 1.0 è uno standard OASIS.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere i collegamenti seguenti:

* [Uso del bus di servizio da .NET con AMQP]
* [Uso del bus di servizio da Java con AMQP]
* [Installazione di Apache Qpid Proton-C in una macchina virtuale Linux di Azure]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Uso del bus di servizio da .NET con AMQP]: service-bus-amqp-dotnet.md
[Uso del bus di servizio da Java con AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md
[Installazione di Apache Qpid Proton-C in una VM Linux di Azure]::
