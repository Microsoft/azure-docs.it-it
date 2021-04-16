---
title: Eseguire il backup di una farm di SharePoint in Azure con DPM
description: Questo articolo offre una panoramica sulla protezione del server DPM/Backup di Azure di una farm di SharePoint in Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 3524107b545c151fcf931b89c629a61d83f47ace
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515159"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Eseguire il backup di una farm di SharePoint in Azure con DPM

Il backup di una farm di SharePoint in Azure si esegue tramite System Center Data Protection Manager (DPM) in modo analogo al backup delle altre origini dati. Backup di Azure offre flessibilità nella pianificazione di backup per creare punti di backup quotidiani, settimanali, mensili o annuali e offre diverse opzioni in termini di criteri di conservazione per i vari intervalli di backup. DPM offre la possibilità di archiviare copie dei dischi locali per obiettivi di tempi di ripristino (RTO) rapidi e di archiviare copie in Azure per una conservazione economicamente conveniente e a lungo termine.

Il backup di SharePoint in Azure con DPM è un processo molto simile al backup di SharePoint in DPM in locale. In questo articolo vengono indicate alcune considerazioni specifiche per Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versioni supportate di SharePoint e relativi scenari di protezione

Per un elenco delle versioni di SharePoint supportate e delle versioni di DPM necessarie per eseguirne il backup, vedere [What can DPM back up?](/system-center/dpm/dpm-protection-matrix#applications-backup) (Elementi dei quali DPM può eseguire il backup)

## <a name="before-you-start"></a>Prima di iniziare

È necessario verificare alcuni aspetti prima di eseguire il backup di una farm di SharePoint in Azure.

### <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi che tutti i [prerequisiti per l'uso di Backup di Microsoft Azure](backup-azure-dpm-introduction.md#prerequisites-and-limitations) per la protezione dei carichi di lavoro siano stati soddisfatti. Alcune attività per i prerequisiti includono: creare insiemi di credenziali di backup, scaricare credenziali di insiemi di credenziali, installare l'agente di Backup di Azure e registrare il server di Backup di Azure o DPM con l'insieme di credenziali.

Altri prerequisiti e limitazioni sono disponibili nell'articolo [Eseguire il backup di SharePoint con DPM.](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations)

## <a name="configure-backup"></a>Configurare il backup

Per eseguire il backup della farm di SharePoint configurare la protezione per SharePoint tramite ConfigureSharePoint.exe e creare un gruppo protezione dati in DPM. Per istruzioni, vedere [Configurare il backup](/system-center/dpm/back-up-sharepoint#configure-backup) nella documentazione di DPM.

## <a name="monitoring"></a>Monitoraggio

Per monitorare il processo di backup, seguire le istruzioni in [Monitoraggio del backup di DPM](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>Ripristinare i dati di SharePoint

Per informazioni su come ripristinare un elemento di SharePoint da un disco con DPM, vedere [Ripristinare i dati di SharePoint.](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Ripristinare un database di SharePoint da Azure tramite DPM

1. Per ripristinare un database del contenuto di SharePoint, scorrere i vari punti di ripristino (come illustrato in precedenza) e selezionare il punto di ripristino che si vuole ripristinare.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Fare doppio clic sul punto di ripristino di SharePoint per visualizzare le informazioni del catalogo di SharePoint disponibili.

   > [!NOTE]
   > Poiché la farm di SharePoint è protetta per la conservazione a lungo termine in Azure, non è disponibile nessuna informazione sul catalogo (metadati) nel server DPM. Di conseguenza, ogni volta che si vuole ripristinare un database del contenuto di SharePoint temporizzato, si deve ricatalogare la farm di SharePoint.
   >
   >
3. Selezionare **Ri-cataloga**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Viene visualizzata la finestra di stato di **Ricatalogazione cloud** .

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Dopo aver completato la catalogazione, viene visualizzato lo stato *Operazione completata*. Selezionare **Chiudi**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selezionare l'oggetto SharePoint visualizzato nella scheda **Ripristino** DPM per ottenere la struttura del database del contenuto. Fare clic con il pulsante destro del mouse sull'elemento e quindi **scegliere Ripristina**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. A questo punto seguire i passaggi di ripristino illustrati precedentemente in questo articolo per ripristinare il database del contenuto di SharePoint dal disco.

## <a name="switching-the-front-end-web-server"></a>Cambio del server Web front-end

Se si dispone di più server Web front-end e si vuole cambiare il server utilizzato da DPM per proteggere la farm, seguire le istruzioni in [Switching the Front-End Web Server](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server).

## <a name="next-steps"></a>Passaggi successivi

* [server di Backup di Azure e DPM - Domande frequenti](backup-azure-dpm-azure-server-faq.yml)
* [Risolvere i problemi relativi a System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
