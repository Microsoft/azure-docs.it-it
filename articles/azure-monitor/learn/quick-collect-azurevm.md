---
title: Raccogliere dati da una macchina virtuale di Azure con Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come abilitare l'estensione VM dell'agente di Log Analytics e la raccolta di dati dalle VM di Azure con Log Analytics.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2e63b2ca0ee7246009068e6a2875795a14aeddb3
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84695369"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Raccogliere dati da una macchina virtuale di Azure con Monitoraggio di Azure

[Monitoraggio di Azure](../overview.md) può raccogliere dati direttamente dalle macchine virtuali di Azure in un'area di lavoro di Log Analytics per l'analisi dettagliata e la correlazione. L'installazione dell'estensione Log Analytics VM per [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) consente a Monitoraggio di Azure di raccogliere i dati dalle VM di Azure. Questa guida di avvio rapido illustra come configurare e raccogliere dati dalle VM Linux o Windows di Azure in pochi semplici passaggi tramite l'estensione VM.  
 
Questa guida introduttiva presuppone che esista già una macchina virtuale di Azure. In caso contrario, è possibile [creare una VM Windows](../../virtual-machines/windows/quick-create-portal.md) o [creare una VM Linux](../../virtual-machines/linux/quick-create-cli.md) seguendo le guide introduttive sulle VM.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Creare un'area di lavoro

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.

    ![Portale di Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selezionare **Crea** e quindi scegliere le opzioni per gli elementi seguenti:

   * Specificare un nome per la nuova **area di lavoro Log Analytics**, ad esempio *DefaultLAWorkspace*.  
   * Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   * Per **Gruppo di risorse**, selezionare un gruppo di risorse esistente contenente una o più macchine virtuali di Azure.  
   * Selezionare la **località** in cui sono distribuite le VM.  Per altre informazioni, vedere le [are in cui è disponibile Log Analytics](https://azure.microsoft.com/regions/services/).
   * Se si sta creando un'area di lavoro in una nuova sottoscrizione creata dopo il 2 aprile 2018, verrà automaticamente usato il piano di determinazione dei prezzi *Per GB* e non sarà disponibile l'opzione che consente di selezionare un piano tariffario.  Se si sta creando un'area di lavoro per una sottoscrizione esistente creata prima del 2 aprile o per una sottoscrizione collegata a un Contratto Enterprise esistente, selezionare il piano tariffario preferito.  Per altre informazioni sui piani specifici, vedere [Dettagli prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Creare il pannello della risorsa Log Analytics](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Dopo aver specificato le informazioni necessarie nel riquadro **Area di lavoro di Log Analytics**, selezionare **OK**.  

Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu. 

## <a name="enable-the-log-analytics-vm-extension"></a>Abilitare l'estensione macchina virtuale di Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Per le macchine virtuali Windows e Linux già distribuite in Azure, si installa l'agente di Log Analytics con l'estensione VM di Log Analytics. L'uso dell'estensione macchina virtuale consente di semplificare il processo di installazione e di configurare automaticamente l'agente per l'invio di dati all'area di lavoro Log Analytics specificata. L'agente viene anche aggiornato automaticamente quando viene rilasciata una nuova versione, assicurando la presenza delle funzionalità e delle correzioni più recenti. Prima di procedere, verificare che la macchina virtuale sia in esecuzione, in caso contrario il processo non verrà completato correttamente.  

>[!NOTE]
>L'agente di Log Analytics per Linux non può essere configurato per inviare report a più di un'area di lavoro Log Analytics. 

1. Nel portale di Azure selezionare **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.

2. Nell'elenco di aree di lavoro di Log Analytics selezionare *DefaultLAWorkspace* creata prima.

3. Nel menu a sinistra selezionare **Macchine virtuali** in Origini dati dell'area di lavoro.  

4. Nell'elenco **Macchine virtuali** selezionare una macchina virtuale in cui si vuole installare l'agente. Si noti che lo **stato della connessione di Log Analytics** per la macchina virtuale sarà **Non connesso**.

5. Nei dettagli relativi alla macchina virtuale selezionare **Connetti**. L'agente viene automaticamente installato e configurato per l'area di lavoro Log Analytics. Il processo richiede alcuni minuti, durante i quali lo **stato** visualizzato è **Connessione in corso**.

6. Al termine dell'installazione e della connessione dell'agente, lo **stato della connessione di Log Analytics** verrà aggiornato in **Questa area di lavoro**.

## <a name="collect-event-and-performance-data"></a>Raccogliere dati su eventi e prestazioni

Monitoraggio di Azure può raccogliere gli eventi dai registri eventi di Windows o da Syslog Linux e i contatori delle prestazioni specificati per l'analisi più a lungo termine e la creazione di report, nonché intervenire quando viene rilevata una particolare condizione. Seguire questi passaggi per configurare la raccolta di eventi dal registro di sistema di Windows e da Syslog Linux e di diversi comuni contatori delle prestazioni con cui iniziare.  

### <a name="data-collection-from-windows-vm"></a>Raccolta di dati da una VM Windows

1. Selezionare **Impostazioni avanzate**.

    ![Impostazioni avanzate di Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Selezionare **Dati** e quindi selezionare **Log eventi Windows**.

3. Si aggiunge un registro eventi digitandone il nome.  Digitare **Sistema** e quindi selezionare il segno più **+** .

4. Nella tabella selezionare i livelli di gravità **Errore** e **Avviso**.

5. Selezionare **Salva** nella parte superiore della pagina per salvare la configurazione.

6. Selezionare **Windows Performance Data** (Dati di prestazione di Windows) per abilitare la raccolta di contatori delle prestazioni in un computer Windows.

7. Quando si configurano i contatori delle prestazioni di Windows per la prima volta per una nuova area di lavoro Log Analytics, è possibile creare rapidamente numerosi contatori comuni. Viene visualizzato l'elenco dei contatori con le caselle di controllo corrispondenti.

    ![Contatori delle prestazioni di Windows predefiniti selezionati](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Fare clic su **Aggiungi i contatori delle prestazioni selezionati**.  Vengono aggiunti e preimpostati con un intervallo di esempio tra le raccolte di dieci secondi.
  
8. Selezionare **Salva** nella parte superiore della pagina per salvare la configurazione.

### <a name="data-collection-from-linux-vm"></a>Raccolta di dati da una VM Linux

1. Selezionare **Syslog**.  

2. Si aggiunge un registro eventi digitandone il nome.  Digitare **Syslog** e quindi selezionare il segno più **+** .  

3. Nella tabella deselezionare i livelli di gravità **Informativo**, **Avviso** e **Debug**. 

4. Selezionare **Salva** nella parte superiore della pagina per salvare la configurazione.

5. Selezionare **Linux Performance Data** (Dati di prestazione di Linux) per abilitare la raccolta di contatori delle prestazioni in un computer Linux. 

6. Quando si configurano i contatori delle prestazioni di Linux per la prima volta per una nuova area di lavoro Log Analytics, è possibile creare rapidamente numerosi contatori comuni. Viene visualizzato l'elenco dei contatori con le caselle di controllo corrispondenti.

    ![Contatori delle prestazioni di Windows predefiniti selezionati](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selezionare **Applica la configurazione seguente alle macchine virtuali** e quindi **Aggiungi i contatori delle prestazioni selezionati**.  Vengono aggiunti e preimpostati con un intervallo di esempio tra le raccolte di dieci secondi.  

7. Selezionare **Salva** nella parte superiore della pagina per salvare la configurazione.

## <a name="view-data-collected"></a>Visualizzare i dati raccolti

Ora che la raccolta di dati è stata abilitata, verrà eseguito un semplice esempio di ricerca log per visualizzare alcuni dati dalle VM di destinazione.  

1. Nel riquadro sinistro dell'area di lavoro selezionata scegliere **Log**.

2. Nella pagina di query di Log digitare `Perf` nell'editor di query e selezionare **Esegui**.

    ![Esempio di query di ricerca log di Log Analytics](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    La query nella figura seguente, ad esempio, ha restituito 10.000 record relativi alle prestazioni. I risultati effettivi saranno molti di meno.

    ![Risultato della ricerca log di Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessaria, eliminare l'area di lavoro Log Analytics. A questo scopo, selezionare l'area di lavoro Log Analytics creata prima e quindi **Elimina** nella pagina delle risorse.


![Eliminare la risorsa Log Analytics](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è in corso la raccolta di dati operativi e sulle prestazioni dalle macchine virtuali Windows o Linux, è possibile iniziare facilmente a esplorare, analizzare e modificare i dati raccolti *gratuitamente*.  

Per informazioni su come visualizzare e analizzare i dati, passare all'esercitazione.

> [!div class="nextstepaction"]
> [View or analyze data in Log Analytics (Visualizzare o analizzare i dati in Log Analytics)](../../azure-monitor/learn/tutorial-viewdata.md)
