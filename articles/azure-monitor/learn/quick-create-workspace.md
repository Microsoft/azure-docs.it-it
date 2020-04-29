---
title: Creare un'area di lavoro Log Analytics nel portale di Azure | Microsoft Docs
description: Informazioni su come creare un'area di lavoro Log Analytics per abilitare soluzioni di gestione e la raccolta dei dati dagli ambienti cloud e locali nel portale di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77656280"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Log Analytics nel portale di Azure
Usare il menu **Aree di lavoro Log Analytics** per creare un'area di lavoro Log Analytics tramite il portale di Azure. Un'area di lavoro Log Analytics è un ambiente univoco per i dati di log di Monitoraggio di Azure. Ogni area di lavoro ha un proprio repository di dati e una propria configurazione, mentre le origini dati e le soluzioni sono configurate per l'archiviazione dei dati in un'area di lavoro specifica. È necessaria un'area di lavoro Log Analytics se si intende raccogliere dati dalle origini seguenti:

* Risorse di Azure nella sottoscrizione
* Computer locali monitorati tramite System Center Operations Manager
* Raccolte di dispositivi da Configuration Manager 
* Dati di diagnostica o di log dall'archiviazione di Azure

Per altre origini, ad esempio macchine virtuali di Azure e macchine virtuali di Windows o Linux presenti nell'ambiente, vedere gli argomenti seguenti:

*  [Raccogliere dati dalle macchine virtuali di Azure](../learn/quick-collect-azurevm.md) 
*  [Raccogliere dati dal computer Linux ibrido](../learn/quick-collect-linux-computer.md)
*  [Raccogliere dati dal computer Windows ibrido](quick-collect-windows-computer.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro
1. Nella portale di Azure fare clic su **tutti i servizi**. Nell'elenco di risorse digitare **log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **log Analytics aree di lavoro**.

    ![Portale di Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Fare clic su **Aggiungi** e quindi selezionare le opzioni per gli elementi seguenti:

   * Specificare un nome per la nuova **area di lavoro Log Analytics**, ad esempio *DefaultLAWorkspace*. Questo nome deve essere univoco a livello globale in tutte le sottoscrizioni di Monitoraggio di Azure.
   * Selezionare una **sottoscrizione** a cui collegarsi selezionando nell'elenco a discesa se l'impostazione predefinita selezionata non è appropriata.
   * Per **Gruppo di risorse** scegliere di usare un gruppo di risorse esistente già configurato oppure crearne uno nuovo.  
   * Selezionare una **località** disponibile.  Per ulteriori informazioni, vedere quali [aree log Analytics è disponibile in](https://azure.microsoft.com/regions/services/) e cercare monitoraggio di Azure dal campo **ricerca di un prodotto** .  
   * Se si sta creando un'area di lavoro in una nuova sottoscrizione creata dopo il 2 aprile 2018, verrà automaticamente usato il piano di determinazione dei prezzi *Per GB* e non sarà disponibile l'opzione che consente di selezionare un piano tariffario.  Se si sta creando un'area di lavoro per una sottoscrizione esistente creata prima del 2 aprile o per una sottoscrizione collegata a un Contratto Enterprise (EA) esistente, selezionare il piano tariffario preferito.  Per altre informazioni sui piani specifici, vedere [Dettagli prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Creare il pannello della risorsa Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Dopo aver specificato le informazioni necessarie nel riquadro **area di lavoro Log Analytics**, fare clic su **OK**.  

Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu. 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un'area di lavoro, è possibile configurare la raccolta di dati di telemetria di monitoraggio, eseguire ricerche nei log per analizzare i dati e aggiungere una soluzione di gestione per fornire informazioni analitiche dettagliate e dati aggiuntivi. 

* Per abilitare la raccolta di dati dalle risorse di Azure con Diagnostica di Azure o l'archiviazione di Azure, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Aggiungere System Center Operations Manager come origine dati](../platform/om-agents.md) per raccogliere i dati dagli agenti che segnalano il gruppo di gestione Operations Manager e archiviarli nell'area di lavoro log Analytics. 
* Connettere [Configuration Manager](../platform/collect-sccm.md) per importare computer che sono membri di raccolte nella gerarchia.  
* Esaminare le [soluzioni di monitoraggio](../insights/solutions.md) disponibili e verificare come aggiungere o rimuovere una soluzione dall'area di lavoro.
