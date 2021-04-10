---
title: Informazioni sui servizi cloud di Azure (versione classica) | Microsoft Docs
description: Informazioni sui servizi cloud di Azure, in particolare per supportare applicazioni scalabili, affidabili ed economiche da usare.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cbb9aae57b952c05aa722f81309158a11aef826b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742812"
---
# <a name="overview-of-azure-cloud-services-classic"></a>Panoramica dei servizi cloud di Azure (versione classica)

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

Servizi cloud di Azure è un esempio di [piattaforma distribuita come servizio](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Analogamente a [Servizio app di Azure](../app-service/overview.md), questa tecnologia è stata progettata per supportare applicazioni scalabili, attendibili ed economicamente efficienti. Proprio come Servizio app, anche Servizi cloud di Azure è ospitato in macchine virtuali (VM). Tuttavia, il controllo sulle macchine virtuali è maggiore. È possibile installare software personalizzato nelle macchine virtuali che usano Servizi cloud di Azure e accedervi in remoto.

![Schema di Servizi cloud di Azure](./media/cloud-services-choose-me/diagram.png)

Maggiore controllo significa anche minore semplicità d'uso. Pertanto, se non sono necessarie opzioni di controllo aggiuntive, in genere è più semplice e rapido creare ed eseguire un'applicazione Web nella funzionalità App Web di Servizio app che non in Servizi cloud di Azure.

Sono disponibili due tipi di ruoli di Servizi cloud di Azure. L'unica differenza tra i due è il modo in cui il ruolo è ospitato nella macchina virtuale:

* **Ruolo Web**: distribuisce e ospita automaticamente l'app tramite IIS.

* **Ruolo di lavoro**: non usa IIS ed esegue l'app autonomamente.

Ad esempio, un'applicazione semplice può usare un solo ruolo Web che serve un sito Web. Un'applicazione più complessa può usare un ruolo Web per la gestione delle richieste in arrivo dagli utenti e quindi passare tali richieste a un ruolo di lavoro per l'elaborazione. Per questa comunicazione potrebbe essere usato il [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md) o l'[archiviazione code di Azure](../storage/common/storage-introduction.md).

Come illustrato nella figura precedente, tutte le VM in una singola applicazione vengono eseguite nello stesso servizio cloud. Gli utenti accedono all'applicazione attraverso un indirizzo IP pubblico che esegue automaticamente il bilanciamento del carico delle richieste nelle VM dell'applicazione. La piattaforma [ridimensiona e distribuisce](cloud-services-how-to-scale-portal.md) le macchine virtuali in un'applicazione di Servizi cloud di Azure, in modo da evitare un singolo punto di errore hardware.

Anche se le applicazioni sono eseguite nelle macchine virtuali, è importante capire che Servizi cloud di Azure fornisce una piattaforma distribuita come servizio, non un'infrastruttura distribuita come servizio (IaaS). La spiegazione seguente aiuta a comprendere meglio il concetto. Un'infrastruttura IaaS, quale Macchine virtuali di Azure, consente di creare e configurare prima di tutto l'ambiente in cui l'applicazione viene eseguita, quindi di distribuire l'applicazione in tale ambiente. L'utente ha la responsabilità di gestire questo ambiente, ad esempio distribuendo nuove versioni con patch del sistema operativo in ogni macchina virtuale. La tecnologia PaaS, invece, consente di procedere come se l'ambiente esistesse già. È sufficiente è distribuire l'applicazione. La gestione della piattaforma in cui viene eseguita l'applicazione, inclusa la distribuzione di nuove versioni del sistema operativo, non è a carico dell'utente.

## <a name="scaling-and-management"></a>Scalabilità e gestione
Se si usa Servizi cloud di Azure, non è necessario creare macchine virtuali. Si fornisce invece un file di configurazione che specifica ad Azure quante macchine virtuali di ogni tipo sono richieste, ad esempio "tre istanze del ruolo Web" e "due istanze del ruolo di lavoro". Le macchine virtuali vengono poi create automaticamente dalla piattaforma. L'utente decide le [dimensioni](cloud-services-sizes-specs.md) delle macchine virtuali di supporto, ma non le crea esplicitamente. Se l'applicazione deve gestire un carico maggiore, è possibile richiedere VM aggiuntive e tali istanze verranno create automaticamente da Azure. Se il carico diminuisce, è possibile chiudere tali istanze e interromperne il pagamento.

Un'applicazione di Servizi cloud di Azure viene in genere resa disponibile agli utenti mediante un processo in due fasi. Prima di tutto lo sviluppatore [carica l'applicazione](cloud-services-how-to-create-deploy-portal.md) nell'area di gestione temporanea della piattaforma. Quando lo sviluppatore è pronto per l'attivazione dell'applicazione, usa il portale per il passaggio in produzione. Il [passaggio dalla gestione temporanea alla produzione](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) può essere eseguito senza tempi di inattività, consentendo l'aggiornamento di un'applicazione in esecuzione a una nuova versione senza interferire con l'uso da parte degli utenti.

## <a name="monitoring"></a>Monitoraggio
Servizi cloud di Azure offre inoltre funzionalità di monitoraggio. Analogamente a Macchine virtuali, rileva un server fisico in errore e riavvia le VM in esecuzione su tale server in una nuova macchina. Servizi cloud di Azure consente inoltre di rilevare errori di macchine virtuali e applicazioni, non solo errori hardware. A differenza di Macchine virtuali, dispone di un agente in ogni ruolo Web e di lavoro ed è quindi in grado di avviare nuove macchine virtuali e istanze di applicazione in caso di errori.

L'uso della tecnologia PaaS da parte di Servizi cloud di Azure comporta anche altre implicazioni, A tale scopo, è necessario che lo stato di un'applicazione di Servizi cloud non venga salvato nel file system delle rispettive macchine virtuali. A tale scopo, è necessario che lo stato di un'applicazione di Servizi cloud di Azure non venga salvato nel file system delle rispettive macchine virtuali. A differenza delle VM create tramite Macchine virtuali, le operazioni di scrittura effettuate nelle VM di Servizi cloud di Azure non sono persistenti. La persistenza è offerta solo dai dischi dati di Macchine virtuali. È pertanto necessario che tutti gli stati di un'applicazione di Servizi cloud di Azure vengano scritti esplicitamente nel database SQL di Azure, in BLOB, tabelle o altre risorse di archiviazione esterne. Le applicazioni create in questo modo risulteranno più scalabili e più resistenti agli errori, due obiettivi essenziali di Servizi cloud di Azure.

## <a name="next-steps"></a>Passaggi successivi
* [Creare un'app di servizio cloud in .NET](cloud-services-dotnet-get-started.md) 
* [Creare un'app di servizio cloud in Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Creare un'app di servizio cloud in PHP](../cloud-services-php-create-web-role.md) 
* [Creare un'app del servizio cloud in Python](cloud-services-python-ptvs.md)






