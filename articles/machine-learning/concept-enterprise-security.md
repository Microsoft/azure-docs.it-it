---
title: Sicurezza aziendale
titleSuffix: Azure Machine Learning
description: 'Usare Azure Machine Learning in modo sicuro: autenticazione, autorizzazione, sicurezza di rete, crittografia dei dati e monitoraggio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: d5edfab0963ec3fca24969d7a54038066ba08765
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188396"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Sicurezza aziendale per Azure Machine Learning

In questo articolo vengono fornite informazioni sulle funzionalità di sicurezza disponibili per Azure Machine Learning.

Quando si usa un servizio cloud, una procedura consigliata consiste nel limitare l'accesso solo agli utenti che lo richiedono. Per iniziare, è necessario comprendere il modello di autenticazione e autorizzazione utilizzato dal servizio. Potrebbe anche essere opportuno limitare l'accesso alla rete o unire in modo sicuro le risorse nella rete locale con il cloud. La crittografia dei dati è anche fondamentale, sia inattivi che durante lo spostamento dei dati tra i servizi. Infine, è necessario essere in grado di monitorare il servizio e produrre un log di controllo di tutte le attività.

> [!NOTE]
> Le informazioni contenute in questo articolo funzionano con Azure Machine Learning Python SDK versione 1.0.83.1 o successiva.

## <a name="authentication"></a>Authentication

La funzionalità autenticazione a più fattori è supportata se Azure Active Directory (Azure AD) è configurata per l'utilizzo. Ecco il processo di autenticazione:

1. Il client accede per Azure AD e ottiene un token di Azure Resource Manager.  Gli utenti e le entità servizio sono completamente supportati.
1. Il client presenta il token per Azure Resource Manager e per tutti i Azure Machine Learning.
1. Il servizio Machine Learning fornisce un token di servizio Machine Learning alla destinazione di calcolo dell'utente, ad esempio ambiente di calcolo di Machine Learning. Questo token viene usato dalla destinazione di calcolo utente per richiamare il servizio Machine Learning al termine dell'esecuzione. L'ambito è limitato all'area di lavoro.

[![Autenticazione in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Per altre informazioni, vedere [configurare l'autenticazione per Azure Machine Learning risorse e flussi di lavoro](how-to-setup-authentication.md). Questo articolo fornisce informazioni ed esempi sull'autenticazione, tra cui l'uso di entità servizio e flussi di lavoro automatizzati.

### <a name="authentication-for-web-service-deployment"></a>Autenticazione per la distribuzione del servizio Web

Azure Machine Learning supporta due forme di autenticazione per i servizi Web: chiave e token. Ogni servizio Web può abilitare solo una forma di autenticazione alla volta.

|Metodo di autenticazione|Descrizione|Istanze di Azure Container|Servizio Azure Kubernetes|
|---|---|---|---|
|Chiave|Le chiavi sono statiche e non è necessario aggiornarle. Le chiavi possono essere rigenerate manualmente.|Disattivata per impostazione predefinita| Abilitato per impostazione predefinita|
|token|I token scadono dopo un periodo di tempo specificato ed è necessario aggiornarli.| Non disponibile| Disattivata per impostazione predefinita |

Per esempi di codice, vedere la [sezione autenticazione del servizio Web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorizzazione

È possibile creare più aree di lavoro, ciascuna delle quali può essere condivisa da più utenti. Quando si condivide un'area di lavoro, è possibile controllarne l'accesso assegnando questi ruoli agli utenti:

* Proprietario
* Collaboratore
* Reader

Nella tabella seguente sono elencate alcune delle principali operazioni di Azure Machine Learning e i ruoli che possono eseguirli:

| Operazione Azure Machine Learning | Proprietario | Collaboratore | Reader |
| ---- |:----:|:----:|:----:|
| Creare un'area di lavoro | ✓ | ✓ | |
| Condividere l'area di lavoro | ✓ | |  |
| Aggiornare l'area di lavoro a Enterprise Edition | ✓ | |
| Creare una destinazione di calcolo | ✓ | ✓ | |
| Connetti destinazione di calcolo | ✓ | ✓ | |
| Connetti archivi dati | ✓ | ✓ | |
| Eseguire esperimento | ✓ | ✓ | |
| Visualizza esecuzioni/metriche | ✓ | ✓ | ✓ |
| Registrare il modello | ✓ | ✓ | |
| Creare un'immagine | ✓ | ✓ | |
| Distribuire il servizio Web | ✓ | ✓ | |
| Visualizza modelli/immagini | ✓ | ✓ | ✓ |
| Chiama servizio Web | ✓ | ✓ | ✓ |

Se i ruoli predefiniti non soddisfano le proprie esigenze, è possibile creare ruoli personalizzati. I ruoli personalizzati sono supportati solo per le operazioni nell'area di lavoro e ambiente di calcolo di Machine Learning. I ruoli personalizzati possono disporre di autorizzazioni di lettura, scrittura o eliminazione per l'area di lavoro e per la risorsa di calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile a livello di area di lavoro specifico, a un livello di gruppo di risorse specifico o a un livello di sottoscrizione specifico. Per altre informazioni, vedere [gestire utenti e ruoli in un'area di lavoro Azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> Azure Machine Learning è supportata con Azure Active Directory collaborazione business-to-business, ma attualmente non è supportata con Azure Active Directory collaborazione business-to-consumer.

### <a name="securing-compute-targets-and-data"></a>Protezione dei dati e delle destinazioni di calcolo

I proprietari e i collaboratori possono usare tutte le destinazioni di calcolo e gli archivi dati collegati all'area di lavoro.  

A ogni area di lavoro è associata anche un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. L'identità gestita ha le autorizzazioni seguenti per le risorse associate utilizzate nell'area di lavoro.

Per altre informazioni sulle identità gestite, vedere [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Risorsa | Autorizzazioni |
| ----- | ----- |
| Area di lavoro | Collaboratore |
| Account di archiviazione | Collaboratore ai dati del BLOB di archiviazione |
| Insieme di credenziali delle chiavi | Accesso a tutte le chiavi, segreti, certificati |
| Registro Azure Container | Collaboratore |
| Gruppo di risorse che contiene l'area di lavoro | Collaboratore |
| Gruppo di risorse che contiene l'insieme di credenziali delle chiavi (se diverso da quello che contiene l'area di lavoro) | Collaboratore |

Non è consigliabile che gli amministratori revocano l'accesso dell'identità gestita alle risorse indicate nella tabella precedente. È possibile ripristinare l'accesso tramite l'operazione di risincronizzazione delle chiavi.

Azure Machine Learning crea un'applicazione aggiuntiva (il nome inizia `aml-` con `Microsoft-AzureML-Support-App-`o) con accesso a livello di collaboratore nella sottoscrizione per ogni area dell'area di lavoro. Se, ad esempio, si dispone di un'area di lavoro negli Stati Uniti orientali e una in Europa settentrionale nella stessa sottoscrizione, verranno visualizzate due di queste applicazioni. Queste applicazioni consentono Azure Machine Learning di gestire le risorse di calcolo.

## <a name="network-security"></a>Sicurezza di rete

Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. È possibile creare queste destinazioni di calcolo in una rete virtuale. Ad esempio, è possibile usare Data Science Virtual Machine di Azure per eseguire il training di un modello e quindi distribuire il modello in AKS.  

Per ulteriori informazioni, vedere [come eseguire esperimenti e inferenza in una rete virtuale](how-to-enable-virtual-network.md).

È anche possibile abilitare il collegamento privato di Azure per l'area di lavoro. Collegamento privato consente di limitare le comunicazioni all'area di lavoro da una rete virtuale di Azure. Per ulteriori informazioni, vedere [la pagina relativa alla modalità di configurazione del collegamento privato](how-to-configure-private-link.md).

> [!TIP]
> È possibile combinare congiuntamente rete virtuale e collegamento privato per proteggere le comunicazioni tra l'area di lavoro e altre risorse di Azure. Tuttavia, alcune combinazioni richiedono un'area di lavoro Enterprise Edition. Usare la tabella seguente per comprendere gli scenari che richiedono Enterprise Edition:
>
> | Scenario | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | Nessuna rete virtuale o collegamento privato | ✔ | ✔ |
> | Area di lavoro senza collegamento privato. Altre risorse (ad eccezione di Azure Container Registry) in una rete virtuale | ✔ | ✔ |
> | Area di lavoro senza collegamento privato. Altre risorse con collegamento privato | ✔ | |
> | Area di lavoro con collegamento privato. Altre risorse (ad eccezione di Azure Container Registry) in una rete virtuale | ✔ | ✔ |
> | Area di lavoro e qualsiasi altra risorsa con collegamento privato | ✔ | |
> | Area di lavoro con collegamento privato. Altre risorse senza collegamento privato o rete virtuale | ✔ | ✔ |
> | Container Registry di Azure in una rete virtuale | ✔ | |
> | Chiavi gestite dal cliente per l'area di lavoro | ✔ | |
> 

> [!WARNING]
> Azure Machine Learning l'anteprima delle istanze di calcolo non è supportata in un'area di lavoro in cui è abilitato il collegamento privato.
> 
> Azure Machine Learning non supporta l'uso di un servizio Azure Kubernetes con collegamento privato abilitato. In alternativa, è possibile usare il servizio Kubernetes di Azure in una rete virtuale. Per altre informazioni, vedere [proteggere i processi di sperimentazione e inferenza di Azure ml in una rete virtuale di Azure](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Crittografia dei dati

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

> [!IMPORTANT]
> Se l'area di lavoro contiene dati sensibili, è consigliabile impostare il [flag di hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) durante la creazione dell'area di lavoro. 

Il `hbi_workspace` flag controlla la quantità di dati raccolti da Microsoft per scopi diagnostici e Abilita la crittografia aggiuntiva negli ambienti gestiti da Microsoft. Consente inoltre quanto segue:

* Avvia la crittografia del disco scratch locale nel cluster Amlcompute a condizione che non siano stati creati cluster precedenti nella sottoscrizione. In caso contrario, è necessario generare un ticket di supporto per abilitare la crittografia del disco scratch dei cluster di elaborazione 
* Pulisce il disco scratch locale tra le esecuzioni
* Passa in modo sicuro le credenziali per l'account di archiviazione, il registro contenitori e l'account SSH dal livello di esecuzione ai cluster di calcolo usando l'insieme di credenziali delle chiavi
* Abilita il filtro IP per assicurarsi che i pool di batch sottostanti non possano essere chiamati da servizi esterni diversi da AzureMachineLearningService


Per altre informazioni sul funzionamento della crittografia dei dati inattivi in Azure, vedere la pagina relativa [alla crittografia dei dati](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)inattivi di Azure.

#### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

Azure Machine Learning archivia gli snapshot, l'output e i log nell'account di archiviazione BLOB di Azure associato all'area di lavoro Azure Machine Learning e alla sottoscrizione. Tutti i dati archiviati nell'archivio BLOB di Azure vengono crittografati a riposo con le chiavi gestite da Microsoft.

Per informazioni su come usare le proprie chiavi per i dati archiviati nell'archivio BLOB di Azure, vedere [crittografia di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

I dati di training vengono in genere archiviati anche nell'archivio BLOB di Azure in modo che sia accessibile alle destinazioni di calcolo di training. Questo spazio di archiviazione non è gestito da Azure Machine Learning ma montato per le destinazioni di calcolo come file system remoto.

Se è necessario __ruotare o revocare__ la chiave, è possibile farlo in qualsiasi momento. Quando si ruota una chiave, l'account di archiviazione inizierà a usare la nuova chiave (versione più recente) per crittografare i dati inattivi. Quando si revoca una chiave (disabilitazione), l'account di archiviazione si occupa delle richieste non riuscite. Per la rotazione o la revoca è in genere necessaria un'ora.

Per informazioni sulla rigenerazione delle chiavi di accesso, vedere [rigenerazione delle chiavi di accesso alle archiviazione](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning archivia metriche e metadati in un'istanza di Azure Cosmos DB. Questa istanza è associata a una sottoscrizione Microsoft gestita da Azure Machine Learning. Tutti i dati archiviati in Azure Cosmos DB vengono crittografati a riposo con chiavi gestite da Microsoft.

Per usare chiavi personalizzate (gestite dal cliente) per crittografare l'istanza di Azure Cosmos DB, è possibile creare un'istanza di Cosmos DB dedicata da usare con l'area di lavoro. Questo approccio è consigliato se si desidera archiviare i dati, ad esempio le informazioni sulla cronologia di esecuzione, al di fuori dell'istanza di Cosmos DB multi-tenant ospitata nella sottoscrizione Microsoft. 

Per abilitare il provisioning di un'istanza di Cosmos DB nella sottoscrizione con chiavi gestite dal cliente, eseguire le azioni seguenti:

* Abilitare le funzionalità chiave gestite dal cliente per Cosmos DB. A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

* Registrare i provider di risorse Azure Machine Learning e Azure Cosmos DB nella sottoscrizione, se non è già stato fatto.

* Autorizzare l'app Machine Learning (in gestione delle identità e degli accessi) con le autorizzazioni di collaboratore per la sottoscrizione.

    ![Autorizzare l'app ' Azure Machine Learning ' nella gestione delle identità e degli accessi nel portale](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Usare i parametri seguenti quando si crea l'area di lavoro Azure Machine Learning. Entrambi i parametri sono obbligatori e supportati in SDK, CLI, API REST e modelli di Gestione risorse.

    * `resource_cmk_uri`: Questo parametro è l'URI di risorsa completo della chiave gestita dal cliente nell'insieme di credenziali delle chiavi, incluse le [informazioni sulla versione della chiave](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Questo parametro è l'ID risorsa dell'insieme di credenziali delle chiavi nella sottoscrizione. Questo insieme di credenziali delle chiavi deve trovarsi nella stessa area e nella stessa sottoscrizione che verrà usata per l'area di lavoro Azure Machine Learning. 
    
        > [!NOTE]
        > Questa istanza dell'insieme di credenziali delle chiavi può essere diversa dall'insieme di credenziali delle chiavi creato da Azure Machine Learning quando si effettua il provisioning dell'area di lavoro. Se si vuole usare la stessa istanza dell'insieme di credenziali delle chiavi per l'area di lavoro, passare lo stesso insieme di credenziali delle chiavi durante il provisioning dell'area di lavoro usando il [parametro key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Questa istanza di Cosmos DB viene creata in un gruppo di risorse gestito da Microsoft nella sottoscrizione. Il nome del gruppo di risorse gestite è nel `<AML Workspace Resource Group Name><GUID>`formato.

> [!IMPORTANT]
> * Se è necessario eliminare questa istanza di Cosmos DB, è necessario eliminare l'area di lavoro Azure Machine Learning che la utilizza. 
> * Le [__unità richiesta__](../cosmos-db/request-units.md) predefinite per l'account Cosmos DB sono impostate su __8000__. La modifica di questo valore non è supportata. 

Se è necessario __ruotare o revocare__ la chiave, è possibile farlo in qualsiasi momento. Quando si ruota una chiave, Cosmos DB inizierà a usare la nuova chiave (versione più recente) per crittografare i dati inattivi. Quando si revoca una chiave, Cosmos DB si occupa delle richieste non riuscite. Per la rotazione o la revoca è in genere necessaria un'ora.

Per ulteriori informazioni sulle chiavi gestite dal cliente con Cosmos DB, vedere [configurare chiavi gestite dal cliente per l'account Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Registro Azure Container

Tutte le immagini del contenitore nel registro di sistema (Container Registry di Azure) vengono crittografate a riposo. Azure crittografa automaticamente un'immagine prima di archiviarla e la decrittografa quando Azure Machine Learning estrae l'immagine.

Per usare chiavi personalizzate (gestite dal cliente) per crittografare la Container Registry di Azure, è necessario creare un record di registro di sistema personalizzato e collegarlo durante il provisioning dell'area di lavoro o crittografare l'istanza predefinita che viene creata al momento del provisioning dell'area di lavoro.

Per un esempio di creazione di un'area di lavoro con un Container Registry di Azure esistente, vedere gli articoli seguenti:

* [Creare un'area di lavoro per Azure Machine Learning con l'interfaccia della](how-to-manage-workspace-cli.md)riga di comando di Azure
* [Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Istanza di contenitore di Azure

È possibile crittografare una risorsa dell'istanza di contenitore di Azure (ACI) distribuita usando chiavi gestite dal cliente. La chiave gestita dal cliente usata per ACI può essere archiviata nella Azure Key Vault per l'area di lavoro. Per informazioni sulla generazione di una chiave, vedere [crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Per usare la chiave quando si distribuisce un modello in un'istanza di contenitore di Azure, creare `AciWebservice.deploy_configuration()`una nuova configurazione di distribuzione usando. Fornire le informazioni chiave utilizzando i parametri seguenti:

* `cmk_vault_base_url`: URL dell'insieme di credenziali delle chiavi contenente la chiave.
* `cmk_key_name`: Nome della chiave.
* `cmk_key_version`: Versione della chiave.

Per altre informazioni sulla creazione e sull'uso di una configurazione di distribuzione, vedere gli articoli seguenti:

* Guida di riferimento a [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Dove e come eseguire la distribuzione](how-to-deploy-and-where.md)
* [Distribuire un modello a Istanze di Azure Container](how-to-deploy-azure-container-instance.md)

Per altre informazioni sull'uso di una chiave gestita dal cliente con ACI, vedere [crittografare i dati con una chiave gestita dal cliente](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

È possibile crittografare una risorsa del servizio Azure Kubernetes distribuita usando chiavi gestite dal cliente in qualsiasi momento. Per altre informazioni, vedere [Bring your own key with Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Questo processo consente di crittografare sia i dati sia il disco del sistema operativo delle macchine virtuali distribuite nel cluster Kubernetes.

> [!IMPORTANT]
> Questo processo funziona solo con AKS K8s versione 1,17 o successiva. Azure Machine Learning aggiunto il supporto per AKS 1,17 il 13 gennaio 2020.

#### <a name="machine-learning-compute"></a>ambiente di calcolo di Machine Learning

Il disco del sistema operativo per ogni nodo di calcolo archiviato in archiviazione di Azure viene crittografato con le chiavi gestite da Microsoft in Azure Machine Learning account di archiviazione. Questa destinazione di calcolo è effimera e i cluster vengono in genere ridimensionati quando nessuna esecuzione viene accodata. Viene effettuato il deprovisioning della macchina virtuale sottostante e viene eliminato il disco del sistema operativo. Crittografia dischi di Azure non è supportata per il disco del sistema operativo.

Ogni macchina virtuale dispone anche di un disco temporaneo locale per le operazioni del sistema operativo. Se lo si desidera, è possibile utilizzare il disco per organizzare i dati di training. Il disco è crittografato per impostazione predefinita per le aree `hbi_workspace` di lavoro con `TRUE`il parametro impostato su. Questo ambiente è di breve durata solo per la durata dell'esecuzione e il supporto della crittografia è limitato solo alle chiavi gestite dal sistema.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks possono essere utilizzate nelle pipeline Azure Machine Learning. Per impostazione predefinita, il file System databricks (DBFS) usato da Azure Databricks viene crittografato con una chiave gestita da Microsoft. Per configurare Azure Databricks per l'uso delle chiavi gestite dal cliente, vedere [configurare chiavi gestite dal cliente in base all'impostazione predefinita (radice) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Crittografia in transito

Azure Machine Learning usa TLS per proteggere le comunicazioni interne tra diversi microservizi Azure Machine Learning. Tutti gli accessi di archiviazione di Azure si verificano anche su un canale sicuro.

Per proteggere le chiamate esterne all'endpoint di assegnazione dei punteggi Azure Machine Learning utilizza TLS. Per altre informazioni, vedere [usare TLS per proteggere un servizio Web tramite Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Uso di Azure Key Vault

Azure Machine Learning utilizza l'istanza Azure Key Vault associata all'area di lavoro per archiviare le credenziali di diversi tipi:

* Stringa di connessione dell'account di archiviazione associato
* Password per le istanze del repository contenitore di Azure
* Stringhe di connessione agli archivi dati

Le password e le chiavi SSH per le destinazioni di calcolo come Azure HDInsight e le macchine virtuali vengono archiviate in un insieme di credenziali delle chiavi separato associato alla sottoscrizione Microsoft. Azure Machine Learning non archivia le password o le chiavi fornite dagli utenti. Al contrario, genera, autorizza e archivia le proprie chiavi SSH per connettersi alle macchine virtuali e HDInsight per eseguire gli esperimenti.

A ogni area di lavoro è associata un'identità gestita assegnata dal sistema con lo stesso nome dell'area di lavoro. Questa identità gestita può accedere a tutte le chiavi, i segreti e i certificati nell'insieme di credenziali delle chiavi.

## <a name="data-collection-and-handling"></a>Raccolta e gestione dei dati

### <a name="microsoft-collected-data"></a>Dati raccolti da Microsoft

Microsoft può raccogliere informazioni di identificazione non utente, come i nomi delle risorse, ad esempio il nome del set di dati o il nome dell'esperimento di Machine Learning, o le variabili di ambiente del processo per scopi diagnostici. Tutti questi dati vengono archiviati usando chiavi gestite da Microsoft nell'archiviazione ospitata in sottoscrizioni di proprietà di Microsoft e seguono [gli standard di gestione dei dati e l'informativa sulla privacy standard di Microsoft](https://privacy.microsoft.com/privacystatement).

Microsoft consiglia inoltre di non archiviare informazioni riservate, ad esempio i segreti chiave dell'account, nelle variabili di ambiente. Le variabili di ambiente vengono registrate, crittografate e archiviate da Microsoft. Analogamente, quando si denomina [RunId](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py), evitare di includere informazioni riservate, ad esempio nomi utente o nomi di progetto segreti. Queste informazioni possono essere visualizzate nei log di telemetria accessibili ai tecnici supporto tecnico Microsoft.

È possibile rifiutare esplicitamente la raccolta dei dati di diagnostica impostando `hbi_workspace` il `TRUE` parametro su durante il provisioning dell'area di lavoro. Questa funzionalità è supportata quando si usa AzureML Python SDK, l'interfaccia della riga di comando, le API REST o i modelli Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Dati generati da Microsoft

Quando si usano servizi come Machine Learning automatizzati, Microsoft può generare dati temporanei pre-elaborati per il training di più modelli. Questi dati vengono archiviati in un archivio dati nell'area di lavoro, che consente di applicare i controlli di accesso e la crittografia in modo appropriato.

Potrebbe anche essere necessario crittografare le [informazioni di diagnostica registrate dall'endpoint distribuito](how-to-enable-app-insights.md) nell'istanza di applicazione Azure Insights.

## <a name="monitoring"></a>Monitoraggio

### <a name="metrics"></a>Metriche

È possibile usare le metriche di monitoraggio di Azure per visualizzare e monitorare le metriche per l'area di lavoro Azure Machine Learning. Nella [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro e quindi selezionare **metrica**:

[![Screenshot che mostra le metriche di esempio per un'area di lavoro](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Le metriche includono informazioni sulle esecuzioni, le distribuzioni e le registrazioni.

Per altre informazioni, vedere [metriche in monitoraggio di Azure](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Log attività

È possibile visualizzare il log attività di un'area di lavoro per visualizzare varie operazioni eseguite sull'area di lavoro. Il log include informazioni di base, ad esempio il nome dell'operazione, l'iniziatore di eventi e il timestamp.

Questa schermata mostra il log attività di un'area di lavoro:

[![Screenshot che mostra il log attività di un'area di lavoro](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

I dettagli della richiesta di assegnazione dei punteggi vengono archiviati in Application Insights. Quando si crea un'area di lavoro, Application Insights viene creato nella sottoscrizione. Le informazioni registrate includono campi come:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Alcune azioni nell'area di lavoro Azure Machine Learning non registrano le informazioni nel log attività. Ad esempio, l'inizio di un'esecuzione del training e la registrazione di un modello non vengono registrate.
>
> Alcune di queste azioni vengono visualizzate nell'area **attività** dell'area di lavoro, ma queste notifiche non indicano chi ha avviato l'attività.

## <a name="data-flow-diagrams"></a>Diagrammi di flusso dei dati

### <a name="create-workspace"></a>Creare un'area di lavoro

Il diagramma seguente illustra il flusso di lavoro Crea area di lavoro.

* Si accede a Azure AD da uno dei client Azure Machine Learning supportati (interfaccia della riga di comando di Azure, Python SDK, portale di Azure) e si richiede il token di Azure Resource Manager appropriato.
* Chiamare Azure Resource Manager per creare l'area di lavoro. 
* Azure Resource Manager Contatta il provider di risorse Azure Machine Learning per eseguire il provisioning dell'area di lavoro.

Le risorse aggiuntive vengono create nella sottoscrizione dell'utente durante la creazione dell'area di lavoro:

* Key Vault (per archiviare i segreti)
* Un account di archiviazione di Azure (incluse la condivisione file e BLOB)
* Azure Container Registry (per archiviare le immagini Docker per l'inferenza, il punteggio e la sperimentazione)
* Application Insights (per archiviare i dati di telemetria)

L'utente può anche effettuare il provisioning di altre destinazioni di calcolo collegate a un'area di lavoro, ad esempio il servizio o le VM Kubernetes di Azure, in base alle esigenze.

[![Crea flusso di lavoro area di lavoro](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Salva codice sorgente (script di training)

Il diagramma seguente illustra il flusso di lavoro snapshot del codice.

Associate a un'area di lavoro Azure Machine Learning sono directory (esperimenti) che contengono il codice sorgente (script di training). Questi script vengono archiviati nel computer locale e nel cloud (nell'archivio BLOB di Azure per la sottoscrizione). Gli snapshot del codice vengono usati per l'esecuzione o l'ispezione per il controllo cronologico.

[![Flusso di lavoro snapshot del codice](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Formazione

Il diagramma seguente illustra il flusso di lavoro di training.

* Azure Machine Learning viene chiamato con l'ID dello snapshot del codice salvato nella sezione precedente.
* Azure Machine Learning crea un ID esecuzione (facoltativo) e un token del servizio Machine Learning, che verrà usato in seguito da destinazioni di calcolo come ambiente di calcolo di Machine Learning/VM per comunicare con il servizio Machine Learning.
* È possibile scegliere una destinazione di calcolo gestita, ad esempio ambiente di calcolo di Machine Learning, o una destinazione di calcolo non gestita, ad esempio macchine virtuali, per eseguire i processi di training. Ecco i flussi di dati per entrambi gli scenari:
   * VM/HDInsight, a cui si accede tramite credenziali SSH in un insieme di credenziali delle chiavi nella sottoscrizione Microsoft. Azure Machine Learning esegue il codice di gestione nella destinazione di calcolo che:

   1. Prepara l'ambiente. (Docker è un'opzione per le macchine virtuali e i computer locali. Ambiente di calcolo di Machine Learning per informazioni sull'esecuzione di esperimenti sui contenitori Docker, vedere i passaggi seguenti.
   1. Scarica il codice.
   1. Imposta le configurazioni e le variabili di ambiente.
   1. Esegue gli script utente (lo snapshot del codice indicato nella sezione precedente).

   * Ambiente di calcolo di Machine Learning, a cui si accede tramite un'identità gestita dall'area di lavoro.
Poiché ambiente di calcolo di Machine Learning è una destinazione di calcolo gestita (ovvero, è gestita da Microsoft) viene eseguita con la sottoscrizione Microsoft.

   1. Se necessario, la costruzione remota di Docker viene avviata.
   1. Il codice di gestione viene scritto nella condivisione File di Azure dell'utente.
   1. Il contenitore viene avviato con un comando iniziale. Ovvero il codice di gestione come descritto nel passaggio precedente.

#### <a name="querying-runs-and-metrics"></a>Esecuzione di query su esecuzioni e metriche

Nel diagramma di flusso riportato di seguito questo passaggio si verifica quando la destinazione di calcolo di training scrive le metriche di esecuzione in Azure Machine Learning dalla risorsa di archiviazione nel database di Cosmos DB. I client possono chiamare Azure Machine Learning. Machine Learning effettuerà a sua volta il pull delle metriche dal database Cosmos DB e le restituirà al client.

[![Flusso di lavoro di training](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Creazione di servizi Web

Il diagramma seguente illustra il flusso di lavoro di inferenza. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione.

Di seguito sono riportati i dettagli:

* L'utente registra un modello usando un client come Azure Machine Learning SDK.
* L'utente crea un'immagine usando un modello, un file di punteggio e altre dipendenze del modello.
* L'immagine Docker viene creata e archiviata in Container Registry di Azure.
* Il servizio Web viene distribuito nella destinazione di calcolo (istanze di contenitore/AKS) usando l'immagine creata nel passaggio precedente.
* I dettagli della richiesta di assegnazione dei punteggi vengono archiviati in Application Insights, che si trova nella sottoscrizione dell'utente.
* Viene anche effettuato il push della telemetria alla sottoscrizione Microsoft/Azure.

[![Flusso di lavoro inferenza](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere Azure Machine Learning i servizi Web con TLS](how-to-secure-web-service.md)
* [Utilizzare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Usare Azure Machine Learning con il firewall di Azure](how-to-access-azureml-behind-firewall.md)
* [Usare Azure Machine Learning con rete virtuale di Azure](how-to-enable-virtual-network.md)
* [Best practices for building recommendation systems](https://github.com/Microsoft/Recommenders) (Procedure consigliate per creare sistemi di raccomandazione)
* [Creare un'API per raccomandazioni in tempo reale in Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
