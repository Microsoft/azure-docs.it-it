---
title: 'Avvio rapido: Creare una pipeline CI/CD per il linguaggio di programmazione Go usando Azure DevOps Starter'
description: DevOps Starter consente di iniziare a usare Azure senza difficoltà. Con pochi rapidi passaggi, è possibile avviare un'app Web nel linguaggio di programmazione Go in un servizio di Azure.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 6d6181686eaeb90d4fcdae0231430623b84e2c1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548513"
---
# <a name="create-a-cicd-pipeline-for-go-using-azure-devops-starter"></a>Creare una pipeline CI/CD per Go con Azure DevOps Starter

Configurare l'integrazione continua (CI, Continuous Integration) e il recapito continuo (CD, Continuous Delivery) per l'app Go con Azure DevOps Starter. DevOps Starter semplifica la configurazione iniziale di una pipeline di compilazione e di versione di Azure DevOps.

Se non si ha ancora una sottoscrizione di Azure, è possibile ottenerne una gratuita tramite [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

DevOps Starter crea una pipeline CI/CD in Azure Pipelines. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Starter crea anche risorse di Azure nella sottoscrizione di Azure selezionata.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca digitare **DevOps Starter** e quindi selezionare. Fare clic su **Aggiungi** per crearne una nuova.

    ![Dashboard di DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-app-and-azure-service"></a>Selezionare un'app di esempio e un servizio di Azure

1. Selezionare l'app di esempio **Go** e quindi selezionare **Avanti**.  
    
1. Il framework predefinito è **Simple Go app**. Selezionare **Avanti**.  Il framework app, scelto in precedenza, determina il tipo di destinazioni della distribuzione del servizio di Azure disponibili. 
    
1. Lasciare il servizio di Azure predefinito e quindi selezionare **Avanti**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurare Azure DevOps e una sottoscrizione di Azure 

1. Creare una nuova organizzazione gratuita di Azure DevOps Services o scegliere un'organizzazione esistente. 

1. Immettere un nome per il progetto Azure DevOps. 

1. Selezionare la sottoscrizione di Azure e la posizione, immettere un nome per l'app, quindi selezionare **Fine**. Dopo alcuni minuti, il dashboard di DevOps Starter viene visualizzato nel portale di Azure. Viene configurata un'app di esempio in un repository nell'organizzazione Azure DevOps, viene eseguita una compilazione e l'app viene distribuita in Azure. 

    Il dashboard fornisce visibilità su repository di codice, pipeline CI/CD e l'app in Azure. A destra, selezionare **Sfoglia** per visualizzare l'app in esecuzione.

    ![Visualizzazione dashboard](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline CI/CD

DevOps Starter crea un repository Git in Azure Repos o in GitHub. Per visualizzare il repository e apportare modifiche al codice nell'app, seguire questa procedura:

1. Sul lato sinistro della finestra di DevOps Starter selezionare il collegamento per il ramo principale. Il collegamento apre una visualizzazione del repository Git appena creato.

1. Per visualizzare l'URL clone del repository, selezionare **Clona** in alto a destra. È possibile clonare il repository Git nell'IDE preferito. Nei passaggi successivi, è possibile usare il Web browser per apportare modifiche al codice ed eseguirne il commit direttamente nel ramo principale.

1. A sinistra, passare al file *views/index.html*, quindi selezionare **Modifica**.

1. Apportare una modifica al file. Modificare, ad esempio, parte del testo all'interno di uno dei tag div.

1. Selezionare **Esegui commit** e quindi salvare le modifiche.

1. Nel browser passare al dashboard di DevOps Projects. Dovrebbe essere in corso una compilazione. Le modifiche apportate vengono compilate e distribuite automaticamente tramite una pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Esaminare la pipeline CI/CD

DevOps Starter configura automaticamente una pipeline CI/CD completa in Azure Repos. Esplorare e personalizzare la pipeline in base alle esigenze. Per acquisire familiarità con le pipeline di compilazione e di versione di Azure DevOps, eseguire questa procedura:

1. Passare al dashboard di DevOps Starter.

1. In alto, selezionare **Pipeline di compilazione**. In una scheda del browser viene visualizzata la pipeline di compilazione per il nuovo progetto.

1. Puntare al campo **Stato** e quindi selezionare i puntini di sospensione (...). Un menu mostra diverse opzioni, ad esempio accodare una nuova compilazione, sospendere una compilazione e modificare la pipeline di compilazione.

1. Selezionare **Modifica**.

1. In questo riquadro è possibile esaminare le diverse attività per la pipeline di compilazione. La compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, il ripristino delle dipendenze e la pubblicazione degli output usati per le distribuzioni.

1. Nella parte superiore della pipeline di compilazione selezionare il nome della pipeline di compilazione.

1. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo, selezionare **Salva e accoda**, quindi selezionare **Salva**.

1. Sotto il nome della pipeline di compilazione selezionare **Cronologia**. Questo riquadro mostra un log di controllo delle modifiche recenti per la compilazione. Azure DevOps tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

1. Selezionare **Trigger**. DevOps Starter crea automaticamente un trigger di integrazione continua e a ogni commit nel repository viene avviata una nuova compilazione. Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

1. Selezionare **Conservazione**. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

1. Selezionare **Compilazione e versione** e quindi **Versioni**.  DevOps Starter crea una pipeline di versione per gestire le distribuzioni per Azure.

1. Selezionare i puntini di sospensione (...) accanto alla pipeline di versione e quindi scegliere **Modifica**. La pipeline di versione contiene una *pipeline* che definisce il processo per la versione.

1. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata in precedenza produce l'output usato per l'artefatto. 

1. A destra dell'icona **Elimina** selezionare **Trigger di distribuzione continua**. Questa pipeline di versione ha un trigger di distribuzione continua abilitato, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale. 

1. A sinistra selezionare **Attività**. Le attività sono le operazioni eseguite dal processo di distribuzione. In questo esempio è stata creata un'attività per la distribuzione in Servizio app di Azure.

1. A destra, selezionare **Visualizza versioni** per visualizzare una cronologia delle versioni.

1. Selezionare i puntini di sospensione (...) accanto a una versione e quindi selezionare **Apri**. È possibile esplorare diversi menu, ad esempio un riepilogo delle versioni, gli elementi di lavoro associati e i test.

1. Selezionare **Commit**. Questa visualizzazione mostra i commit di codice associati a questa distribuzione. 

1. Selezionare **Log**. I log contengono informazioni utili sul processo di distribuzione. È possibile visualizzarli durante e dopo le distribuzioni.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, è possibile eliminare l'istanza del Servizio app di Azure e le risorse correlate create in questa guida introduttiva. A tale scopo, usare la funzionalità **Elimina** del dashboard di DevOps Starter.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modifica delle pipeline di compilazione e di versione in base alle esigenze del team, vedere:

> [!div class="nextstepaction"]
> [Definire la pipeline di distribuzione continua (CD) in più fasi](/azure/devops/pipelines/release/define-multistage-release-process)