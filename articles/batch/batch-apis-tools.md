---
title: API e strumenti per sviluppatori
description: Informazioni sulle API e gli strumenti disponibili per lo sviluppo di soluzioni con il servizio Azure Batch.
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 56c268675a39dfd3c70b3d8d8e457a2a200d7db6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114922"
---
# <a name="overview-of-batch-apis-and-tools"></a>Panoramica delle API e degli strumenti di Batch

L'elaborazione dei carichi di lavoro paralleli con Azure Batch viene in genere eseguita a livello di codice usando una delle API Batch. L'applicazione o il servizio client può usare le API Batch per comunicare con il servizio Batch. Con le API Batch, è possibile creare e gestire pool di nodi di calcolo, che siano macchine virtuali o servizi cloud. È quindi possibile programmare processi e attività da eseguire in tali nodi. 

È possibile elaborare in modo efficiente carichi di lavoro su larga scala per l'organizzazione oppure offrire ai clienti un front-end di servizio perché possano eseguire processi e attività, su richiesta o in base a una pianificazione, in uno, centinaia o anche migliaia di nodi. È anche possibile usare il servizio Azure Batch nell'ambito di un flusso di lavoro più ampio, gestito da strumenti come [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Per approfondire l'API Batch e le relative funzionalità, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Account Azure per lo sviluppo Batch
Quando si sviluppano soluzioni Batch, è necessario usare gli account seguenti nella sottoscrizione di Azure:

* **Account batch** : le risorse di Azure batch, tra cui pool, nodi di calcolo, processi e attività, sono associate a un [account Azure batch](batch-api-basics.md#account). Quando l'applicazione effettua una richiesta sul servizio Batch, autentica la richiesta usando il nome dell'account Azure Batch, l'URL dell'account e una chiave di accesso o un token di Azure Active Directory. È possibile [creare un account Batch](batch-account-create-portal.md) nel portale di Azure oppure a livello di codice.
* **Account di archiviazione**: Batch include il supporto predefinito per l'uso di file in [Archiviazione di Azure][azure_storage]. Quasi tutti gli scenari di Batch usano l'archivio BLOB di Azure per lo staging dei programmi eseguiti e dei dati elaborati dalle attività e per l'archiviazione dei dati di output generati. Per le opzioni dell'account di archiviazione in Batch, vedere [Panoramica delle funzionalità di Batch](batch-api-basics.md#azure-storage-account).

## <a name="batch-service-apis"></a>API di servizio Batch

Le applicazioni e i servizi possono rilasciare chiamate API REST dirette o usare una o più delle librerie client seguenti per eseguire e gestire i carichi di lavoro di Azure Batch.

| API | Informazioni di riferimento sulle API | Download | Esercitazione | Esempi di codice | Altre informazioni |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |N/D |- |- | [Versioni supportate](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[Esercitazione](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Note sulla versione](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Esercitazione](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [File Leggimi](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[NPM][api_nodejs_npm] |[Esercitazione](batch-nodejs-get-started.md) |- | [File Leggimi](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[File Leggimi][api_sample_java] | [File Leggimi](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API per la gestione di Batch

Le API di Azure Resource Manager per Batch forniscono l'accesso a livello di codice agli account Batch. Usando queste API, è possibile gestire a livello di codice gli account Batch, le quote, i pacchetti dell'applicazione e altre risorse tramite il provider Microsoft.Batch.  

| API | Informazioni di riferimento sulle API | Download | Esercitazione | Esempi di codice |
| --- | --- | --- | --- | --- |
| **REST per la gestione di Batch** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET per la gestione di Batch** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [Esercitazione](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Python per la gestione di Batch** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Node.js per la gestione di Batch** |[docs.microsoft.com][api_nodejs_mgmt] |[NPM][api_nodejs_mgmt_npm] |- |- | 
| **Java per la gestione di Batch** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Strumenti da riga di comando di Batch

Questi strumenti da riga di comando forniscono le stesse funzionalità delle API del servizio Batch e di gestione di Batch: 

* [Cmdlet di PowerShell per Batch][batch_ps]: i cmdlet di Azure Batch nel modulo di [Azure PowerShell](/powershell/azure/overview) consentono di gestire le risorse Batch con PowerShell.
* [Interfaccia della riga di comando di Azure](/cli/azure): l'interfaccia della riga di comando di Azure è un set di strumenti multipiattaforma che offre comandi della shell per l'interazione con molti servizi di Azure, inclusi il servizio Batch e il servizio di gestione di Batch. Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure con Batch, vedere [Gestire le risorse di Batch con l'interfaccia della riga di comando di Azure](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Altri strumenti per lo sviluppo di applicazioni

Di seguito sono riportati alcuni strumenti aggiuntivi che possono risultare utili per la compilazione e il debug di applicazioni e servizi Batch:

* [Portale di Azure][portal]: nel portale di Azure è possibile creare, monitorare ed eliminare pool, processi e attività di Batch. È possibile visualizzare informazioni sullo stato di queste e altre risorse mentre si eseguono i processi, nonché scaricare file dai nodi di calcolo dei pool. È ad esempio possibile scaricare il file `stderr.txt` di un'attività non riuscita durante la risoluzione dei problemi. Si possono anche scaricare i file desktop remoto (RDP) che possono essere usati per accedere ai nodi di calcolo.
* [Azure Batch Explorer][batch_labs]: Batch Explorer (in precedenza denominato Batch Labs) è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare, eseguire il debug e monitorare le applicazioni di Azure Batch. Scaricare un [pacchetto di installazione](https://azure.github.io/BatchExplorer/) per Mac, Linux o Windows.
* [Azure batch cantiere](https://github.com/Azure/batch-shipyard): batch Shipyard è uno strumento che consente di effettuare il provisioning, l'esecuzione e il monitoraggio di carichi di lavoro HPC e di elaborazione batch basati su contenitori su Azure batch.
* [Azure Storage Explorer][storage_explorer]: Sebbene non sia esclusivamente uno strumento Azure batch, il Storage Explorer è un altro strumento utile per lo sviluppo e il debug delle soluzioni batch.

## <a name="additional-resources"></a>Risorse aggiuntive

- Per informazioni sulla registrazione di eventi dall'applicazione Batch, vedere [Registrare gli eventi per la diagnostica e il monitoraggio delle soluzioni Batch](batch-diagnostics.md). Per informazioni dettagliate sugli eventi generati dal servizio Batch, vedere [Analisi Batch](batch-analytics.md).
- Per informazioni sulle variabili di ambiente per i nodi di calcolo, vedere [Variabili di ambiente per i nodi di calcolo di Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Panoramica sulle funzionalità di Batch per sviluppatori](batch-api-basics.md)per informazioni essenziali per chiunque si prepari all'uso di Batch. L'articolo contiene informazioni più dettagliate sulle risorse del servizio Batch, ad esempio pool, nodi, processi e attività, e sulle numerose funzionalità delle API che è possibile usare durante la compilazione dell'applicazione Batch.
* [Introduzione alla libreria di Azure Batch per .NET](tutorial-parallel-dotnet.md) . Sono disponibili anche una [versione Python](tutorial-parallel-python.md) e un'[esercitazione su Node.js](batch-nodejs-get-started.md).
* Scaricare gli [esempi di codice in GitHub][github_samples] per vedere come C# e Python possono interfacciarsi con Batch per pianificare ed elaborare carichi di lavoro di esempio.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
