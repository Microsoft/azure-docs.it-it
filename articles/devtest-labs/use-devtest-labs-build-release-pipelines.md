---
title: Usare DevTest Labs nelle pipeline di compilazione e versione di Azure Pipelines
description: Informazioni su come usare Azure DevTest Labs in Azure Pipelines le pipeline di compilazione e rilascio.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 71af1e0dfe205fe1028f7b82b41f3ed38ebefd3c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483075"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Usare DevTest Labs nelle pipeline di compilazione e versione di Azure Pipelines
Questo articolo fornisce informazioni sul modo in cui è possibile usare DevTest Labs in Azure Pipelines pipeline di compilazione e rilascio. 

## <a name="overall-flow"></a>Flusso generale
Il flusso di base consiste nel disporre di una **pipeline di compilazione** che esegue le attività seguenti:

1. Compilare il codice dell'applicazione.
1. Creare l'ambiente di base in DevTest Labs.
1. Aggiornare l'ambiente con informazioni personalizzate.
1. Distribuire l'applicazione nell'ambiente DevTest Labs
1. Testare il codice. 

Una volta completata la compilazione, la pipeline di **rilascio** utilizzerà gli elementi di compilazione per distribuire la gestione temporanea o la produzione. 

Una delle sedi necessarie è che tutte le informazioni necessarie per ricreare l'ecosistema testato sono disponibili negli elementi di compilazione, inclusa la configurazione delle risorse di Azure. Poiché le risorse di Azure comportano un costo quando vengono usate, le aziende vogliono controllare o tenere traccia dell'uso di queste risorse. In alcune situazioni, i modelli di Azure Resource Manager usati per creare e configurare le risorse possono essere gestiti da un altro reparto, ad esempio. Questi modelli possono essere archiviati in un repository diverso. Ciò comporta una situazione interessante in cui verrà creata e testata una compilazione e il codice e la configurazione devono essere archiviati all'interno degli elementi di compilazione per ricreare correttamente il sistema in produzione. 

Utilizzando DevTest Labs durante la fase di compilazione/test, è possibile aggiungere Azure Resource Manager modelli e i file di supporto alle origini di compilazione in modo che durante la fase di rilascio la configurazione esatta utilizzata per il test di venga distribuita nell'ambiente di produzione. L'attività **crea Azure DevTest Labs ambiente** con la configurazione corretta salverà i modelli di gestione risorse all'interno degli elementi di compilazione. Per questo esempio verrà usato il codice dell' [esercitazione: compilare un'app Web .NET Core e database SQL nel servizio app Azure](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)per distribuire e testare l'app Web in Azure.

![Flusso generale](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure
È necessario creare prima di tutto un paio di elementi:

- Due repository. Il primo con il codice dell'esercitazione e un modello di Gestione risorse con due macchine virtuali aggiuntive. Il secondo conterrà il modello di Azure Resource Manager di base (configurazione esistente).
- Un gruppo di risorse per la distribuzione del codice di produzione e della configurazione.
- Un lab deve essere configurato con una [connessione al repository di configurazione](devtest-lab-create-environment-from-arm.md) per la pipeline di compilazione. Il modello di Gestione risorse deve essere archiviato nel repository di configurazione come azuredeploy.jscon il metadata.json per consentire a DevTest Labs di riconoscere e distribuire il modello.

La pipeline di compilazione creerà un ambiente DevTest Labs e distribuirà il codice per il test.

## <a name="set-up-a-build-pipeline"></a>Configurare una pipeline di compilazione
In Azure Pipelines creare una pipeline di compilazione usando il codice dell' [esercitazione: compilare un'app Web .NET Core e database SQL nel servizio app Azure](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md). Usare il modello di **ASP.NET Core** , che consente di popolare l'attività necessaria per compilare, testare e pubblicare il codice.

![Selezionare il modello ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

È necessario aggiungere altre tre attività per creare l'ambiente in DevTest Labs e distribuirlo nell'ambiente.

![Pipeline con tre attività](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Crea attività ambiente
Nell'attività crea ambiente (**Azure DevTest Labs attività crea ambiente** ) utilizzare gli elenchi a discesa per selezionare i valori seguenti:

- Sottoscrizione di Azure
- nome del Lab
- nome del repository
- nome del modello, che mostra la cartella in cui è archiviato l'ambiente. 

Si consiglia di utilizzare elenchi a discesa nella pagina anziché immettere manualmente le informazioni. Se si immettono manualmente le informazioni, immettere ID di risorsa di Azure completi. L'attività Visualizza i nomi descrittivi anziché gli ID risorsa. 

Il nome dell'ambiente è il nome visualizzato in DevTest Labs. Deve essere un nome univoco per ogni compilazione. Ad esempio: **TestEnv $ (Build. ID compilazione)**. 

È possibile specificare il file dei parametri o i parametri per passare le informazioni nel modello di Gestione risorse. 

Selezionare l'opzione **crea variabili di output in base all'output del modello di ambiente** e immettere un nome di riferimento. Per questo esempio, immettere **BaseEnv** per il nome di riferimento. Questo BaseEnv verrà usato durante la configurazione dell'attività successiva. 

![Crea attività Azure DevTest Labs ambiente](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Popola attività ambiente
La seconda attività (**Azure DevTest Labs popolare l'attività dell'ambiente** ) consiste nell'aggiornare l'ambiente DevTest Labs esistente. L'attività crea ambiente restituisce **BaseEnv. environmentResourceId** usato per configurare il nome dell'ambiente per questa attività. Il modello di Gestione risorse per questo esempio ha due parametri: **con valori AdminUsername** e **adminPassword**. 

![Popola Azure DevTest Labs attività ambiente](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Attività di distribuzione del servizio app
La terza attività è l'attività di **distribuzione del servizio app Azure** . Il tipo di app è impostato su **app Web** e il nome del servizio app è impostato su **$ (sito Web)**.

![Attività di distribuzione del servizio app](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Configurare la pipeline di rilascio
Si crea una pipeline di rilascio con due attività: **distribuzione di Azure: creare o aggiornare il gruppo di risorse** e **distribuire app Azure servizio**. 

Per la prima attività, specificare il nome e il percorso del gruppo di risorse. Il percorso del modello è un artefatto collegato. Se il modello di Gestione risorse include modelli collegati, è necessario implementare una distribuzione del gruppo di risorse personalizzata. Il modello si trova nell'artefatto di rilascio pubblicato. Eseguire l'override dei parametri di modello per il modello di Gestione risorse. È possibile lasciare le impostazioni rimanenti con i valori predefiniti. 

Per la seconda attività **distribuire il servizio app Azure**, specificare la sottoscrizione di Azure, selezionare **app Web** per il **tipo di app**e **$ (sito Web)** per il **nome del servizio app**. È possibile lasciare le impostazioni rimanenti con i valori predefiniti. 

## <a name="test-run"></a>Esecuzione dei test
Ora che entrambe le pipeline sono configurate, accodare manualmente una compilazione e visualizzarne il funzionamento. Il passaggio successivo consiste nell'impostare il trigger appropriato per la compilazione e connettere la compilazione alla pipeline di rilascio.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Integrare Azure DevTest Labs nella pipeline di integrazione e recapito Azure Pipelines continua](devtest-lab-integrate-ci-cd.md)
- [Integra gli ambienti nelle pipeline di integrazione continua/recapito continuo Azure Pipelines](integrate-environments-devops-pipeline.md)
