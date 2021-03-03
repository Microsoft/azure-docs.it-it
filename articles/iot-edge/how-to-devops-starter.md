---
title: Pipeline CI/CD con Azure DevOps Starter-Azure IoT Edge | Microsoft Docs
description: Azure DevOps Starter consente di iniziare a usare Azure senza difficoltà. Con pochi rapidi passaggi, è possibile avviare un'app in un servizio di Azure IoT Edge a scelta.
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 933ddfb5fa5d58231e954dfd54bcc069b53dfe56
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721515"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Creare una pipeline di integrazione continua/distribuzione continua per IoT Edge con Azure DevOps Starter

Configurare integrazione continua (CI, Continuous Integration) e recapito continuo (CD, Continuous Delivery) per un'applicazione IoT Edge con DevOps Projects. DevOps Starter semplifica la configurazione iniziale di una pipeline di compilazione e di versione in Azure Pipelines.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

DevOps Starter crea una pipeline CI/CD in Azure DevOps. È possibile creare una nuova organizzazione di Azure DevOps o usare un'organizzazione esistente. DevOps Starter crea anche risorse di Azure nella sottoscrizione di Azure selezionata.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).

1. Nel riquadro sinistro selezionare **Crea una risorsa** e quindi cercare **DevOps Starter**.  

1. Selezionare **Crea**.

1. Per impostazione predefinita, lo starter DevOps è configurato con GitHub. Per usare le funzionalità di questa procedura, cambiare lo starter DevOps per configurare usando Azure DevOps. Seguire il collegamento **modificare le impostazioni qui** .

   ![Selezionare Modifica impostazioni qui per passare da GitHub ad Azure DevOps](./media/how-to-devops-starter/create-with-github-change-settings.png)

1. Nel riquadro di destra, scegliere il riquadro **Azure DevOps** , quindi fare clic su **fine**.

   ![Selezionare Azure DevOps per configurare il dispositivo di avvio DevOps](./media/how-to-devops-starter/select-azure-devops.png)

   Si noterà ora che l'avvio di DevOps è configurato con Azure DevOps.

## <a name="create-a-new-application-pipeline"></a>Creare una nuova pipeline applicazione

1. I moduli Azure IoT Edge possono essere scritti in [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selezionare il linguaggio di programmazione preferito per avviare una nuova applicazione: **.NET**, **Node.js**, **Python**, **C** o **Java**. Selezionare **Avanti** per continuare.

   ![Selezionare un linguaggio di programmazione per creare una nuova applicazione](./media/how-to-devops-starter/select-language.png)

2. Selezionare **IoT semplice** come framework applicazione e quindi selezionare **Avanti**.

   ![Selezionare il framework IoT semplice](media/how-to-devops-starter/select-iot.png)

3. Selezionare **IoT Edge** come servizio di Azure che distribuisce l'applicazione e quindi selezionare **Avanti**.

   ![Selezionare servizio IoT Edge](media/how-to-devops-starter/select-iot-edge.png)

4. Creare una nuova organizzazione gratuita di Azure DevOps Services o scegliere un'organizzazione esistente.

   1. Specificare un nome per il progetto.

   2. Selezionare l'organizzazione di Azure DevOps. Se non si ha un'organizzazione esistente, selezionare **Impostazioni aggiuntive** per crearne una.

   3. Selezionare la sottoscrizione di Azure.

   4. Usare il nome dell'hub IoT generato dal nome del progetto o specificare un proprio nome.

   5. Accettare il percorso predefinito oppure sceglierne uno vicino.

   6. Selezionare **Impostazioni aggiuntive** per configurare le risorse di Azure create da DevOps Starter per conto dell'utente.

   7. Selezionare **Fine** per completare la creazione del progetto.

   ![Nome e creazione progetto](media/how-to-devops-starter/create-project.png)

Dopo alcuni minuti, il dashboard di DevOps Starter viene visualizzato nel portale di Azure. Selezionare il nome del progetto per visualizzare lo stato di avanzamento. Potrebbe essere necessario aggiornare la pagina. Viene configurata un'applicazione di esempio in un repository nell'organizzazione Azure DevOps, viene eseguita una compilazione e l'applicazione viene distribuita in Azure. Questo dashboard fornisce visibilità su repository di codice, pipeline CI/CD e applicazione in Azure.

   ![Visualizza progetto in portale di Azure](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Eseguire il commit delle modifiche al codice e la pipeline di CI/CD

DevOps Starter ha creato un repository Git per il progetto in Azure Repos. In questa sezione si visualizza il repository e si apportano modifiche al codice dell'applicazione.

1. Per passare al repository creato per il progetto, selezionare **Repository** nel menu del dashboard del progetto. Questo collegamento consente di aprire una scheda del browser e il repository Azure DevOps per il nuovo progetto.

   ![Visualizzare il repository generato in Azure Repos](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > I passaggi seguenti descrivono come usare il Web browser per apportare modifiche al codice. Se si preferisce clonare il repository in locale, selezionare **Clona** nella parte superiore destra della finestra. Usare l'URL fornito per clonare il repository Git in Visual Studio Code o nel proprio strumento di sviluppo preferito.

2. Il repository contiene già il codice per un modulo denominato **FilterModule** basato sulla lingua dell'applicazione che si è scelta nel processo di creazione. Aprire il file **modules/FilterModule/module.json**.

   ![Aprire il file module.json in Azure Repos](./media/how-to-devops-starter/open-module-json.png)

3. Tenere presente che questo file usa le [variabili di compilazione di Azure DevOps](/azure/devops/pipelines/build/variables#build-variables) nel parametro **Versione**. Questa configurazione garantisce che venga creata una nuova versione del modulo ogni volta che viene eseguita una nuova compilazione.

## <a name="examine-the-cicd-pipeline"></a>Esaminare la pipeline CI/CD

Nelle sezioni precedenti Azure DevOps Starter configurava automaticamente una pipeline di integrazione continua/recapito continuo completa per l'applicazione IoT Edge. Ora è possibile esplorare e personalizzare la pipeline in base alle esigenze. Seguire questa procedura per acquisire familiarità con le pipeline di compilazione e di versione di Azure DevOps.

1. Per visualizzare le pipeline di compilazione nel progetto DevOps, selezionare **Pipeline di compilazione** nel menu del dashboard del progetto. Questo collegamento apre una scheda del browser e la pipeline di compilazione di Azure DevOps per il nuovo progetto.

   ![Visualizzare le pipeline di compilazione in Azure Pipelines](./media/how-to-devops-starter/view-build-pipelines.png)

2. Aprire la pipeline di compilazione generata automaticamente e selezionare **modifica** in alto a destra.

    ![Modificare la pipeline di compilazione](media/how-to-devops-starter/click-edit-button.png)

3. Nel pannello che viene aperto è possibile esaminare le attività che si verificano durante l'esecuzione della pipeline di compilazione. La pipeline di compilazione esegue diverse attività, ad esempio il recupero delle origini dal repository Git, la compilazione delle immagini di moduli IoT Edge, l'esecuzione del push dei moduli IoT Edge in un registro contenitori e la pubblicazione degli output usati per le distribuzioni. Per altre informazioni sulle attività Azure IoT Edge in Azure DevOps, vedere [Configurare Azure Pipelines per l'integrazione continua](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration).

4. Selezionare l'intestazione **Pipeline** nella parte superiore della pipeline di compilazione per aprire i dettagli della pipeline. Modificare il nome della pipeline di compilazione scegliendo un testo più descrittivo.

   ![Modificare i dettagli della pipeline](./media/how-to-devops-starter/edit-build-pipeline.png)

5. Selezionare **Salva e accoda** e quindi **Salva**. Si tratta di un commento facoltativo.

6. Selezionare **Trigger** nel menu della pipeline di compilazione. DevOps Starter crea automaticamente un trigger di integrazione continua e ogni commit nel repository avvia una nuova compilazione.  Facoltativamente, è possibile scegliere di includere o escludere rami dal processo di integrazione continua.

7. Selezionare **Conservazione**. Seguire il collegamento per reindirizzare le impostazioni del progetto, in cui si trovano i criteri di conservazione. A seconda dello scenario specifico, è possibile indicare i criteri per conservare o rimuovere un determinato numero di compilazioni.

8. Selezionare **Cronologia**. Il riquadro della cronologia contiene un audit trail delle modifiche recenti alla compilazione. Azure Pipelines tiene traccia di tutte le modifiche apportate alla pipeline di compilazione e consente di confrontare le versioni.

9. Una volta completata l'esplorazione della pipeline di compilazione, passare alla pipeline di versione corrispondente. Selezionare **Versioni** sotto **Pipeline**, quindi selezionare **Modifica** per visualizzare i dettagli della pipeline.

    ![Visualizza la pipeline di versione](media/how-to-devops-starter/release-pipeline.png)

10. In **Artefatti** selezionare **Elimina**. La pipeline di compilazione esaminata nei passaggi precedenti produce l'output usato per l'artefatto.

11. Accanto all'icona **Elimina** selezionare l'icona a forma di fulmine del **Trigger di distribuzione continua**. Questa pipeline di versione ha abilitato il trigger, che esegue una distribuzione ogni volta che è disponibile un nuovo artefatto di compilazione. Facoltativamente, è possibile disabilitare il trigger, in modo che le distribuzioni richiedano l'esecuzione manuale.  

12. Nel menu della pipeline di versione selezionare **Attività**, quindi scegliere la fase **dev** dall'elenco a discesa. DevOps Projects ha creato una fase di versione che crea un hub IoT, crea un dispositivo IoT Edge in tale hub, distribuisce il modulo di esempio dalla pipeline di compilazione ed effettua il provisioning di una macchina virtuale da eseguire come dispositivo IoT Edge. Per altre informazioni sulle attività Azure IoT Edge per la distribuzione continua, vedere [Configurare la distribuzione continua](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment).

    ![Visualizza attività di distribuzione continua](media/how-to-devops-starter/choose-release.png)

13. A destra, selezionare **Visualizza versioni**. Questa visualizzazione mostra una cronologia delle versioni.

14. Selezionare il nome di una versione per visualizzare altre informazioni su di essa.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile eliminare il Servizio app di Azure e altre risorse correlate creati in precedenza. Usare la funzionalità **Elimina** del dashboard di DevOps Starter.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle attività per Azure IoT Edge su Azure DevOps in [Integrazione continua e distribuzione continua in Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-at-scale.md).
