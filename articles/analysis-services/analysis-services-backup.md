---
title: Backup e ripristino del database di Azure Analysis Services | Microsoft Docs
description: Questo articolo descrive come eseguire il backup e il ripristino dei dati e dei metadati del modello da un database di Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 66d09c2faa52cee3e94402be708d654b548c0de1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506996"
---
# <a name="analysis-services-database-backup-and-restore"></a>Backup e ripristino di Analysis Services database

Il backup dei database modello tabulare in Azure Analysis Services è molto simile a quello di Analysis Services in locale. La differenza principale è dove vengono archiviati i file di backup. I file di backup devono essere salvati in un contenitore in un [account di archiviazione di Azure](../storage/common/storage-create-storage-account.md). È possibile usare un account di archiviazione e un contenitore già esistenti, o è possibile crearne di nuovi durante la configurazione delle impostazioni di archiviazione per il server.

> [!NOTE]
> La creazione di un account di archiviazione può dare luogo a un nuovo servizio fatturabile. Per altre informazioni, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

> [!NOTE]
> Se l'account di archiviazione si trova in un'area diversa, configurare le impostazioni del firewall dell'account di archiviazione per consentire l'accesso dalle **reti selezionate**. In **intervallo di indirizzi**del firewall specificare l'intervallo di indirizzi IP per l'area in cui si trova il server Analysis Services. La configurazione delle impostazioni del firewall dell'account di archiviazione per consentire l'accesso da tutte le reti è supportata, tuttavia è preferibile scegliere le reti selezionate e specificare un intervallo di indirizzi IP. Per altre informazioni, vedere [domande frequenti sulla connettività di rete](analysis-services-network-faq.md#backup-and-restore).

I backup vengono salvati con estensione abf. Per i modelli tabulari in memoria, vengono archiviati sia i dati del modello che i metadati. Per i modelli tabulari DirectQuery, vengono archiviati solo i metadati del modello. I backup possono essere compressi e crittografati, a seconda delle opzioni scelte.


## <a name="configure-storage-settings"></a>Configurare le impostazioni di archiviazione
Prima di eseguire il backup, è necessario configurare le impostazioni di archiviazione per il server.


### <a name="to-configure-storage-settings"></a>Per configurare le impostazioni di archiviazione
1.  Nel portale di Azure > **Impostazioni**, fare clic su **Backup**.

    ![Backup in Impostazioni](./media/analysis-services-backup/aas-backup-backups.png)

2.  Fare clic su **Abilitata** e quindi su **Impostazioni di archiviazione**.

    ![Abilita](./media/analysis-services-backup/aas-backup-enable.png)

3. Selezionare l'account di archiviazione o crearne uno nuovo.

4. Selezionare un contenitore o crearne uno nuovo.

    ![Selezionare un contenitore](./media/analysis-services-backup/aas-backup-container.png)

5. Salvare le impostazioni di backup.

    ![Salvare le impostazioni di backup](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Per eseguire il backup tramite SSMS

1. In SSMS fare clic con il pulsante destro del mouse su un database > **Backup**.

2. In **backup database**  >  **backup file**fare clic su **Sfoglia**.

3. Nella finestra di dialogo **Salva file con nome** verificare il percorso della cartella e quindi digitare un nome per il file di backup. 

4. Nella finestra di dialogo **Backup database** selezionare le opzioni.

    **Consenti sostituzione file**: selezionare questa opzione per sovrascrivere i file di backup con lo stesso nome. Se questa opzione non è selezionata, il file da salvare non può avere lo stesso nome di un file già esistente nello stesso percorso.

    **Applica compressione**: selezionare questa opzione per comprimere il file di backup. I file di backup compressi consentono di risparmiare spazio su disco, ma richiedono un utilizzo della CPU leggermente più elevato. 

    **Crittografa file di backup**: selezionare questa opzione per crittografare il file di backup. Questa opzione richiede una password specificata dall'utente per proteggere il file di backup. La password impedisce la lettura dei dati di backup con qualsiasi mezzo che non sia un'operazione di ripristino. Se si sceglie di crittografare i backup, archiviare la password in un luogo sicuro.

5. Fare clic su **OK** per creare e salvare il file di backup.


### <a name="powershell"></a>PowerShell
Usare il cmdlet [Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase).

## <a name="restore"></a>Restore
Durante il ripristino, il file di backup deve essere nell'account di archiviazione configurato per il server. Se è necessario spostare un file di backup da un percorso locale all'account di archiviazione, usare [Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) o l'utilità della riga di comando [AzCopy](../storage/common/storage-use-azcopy.md). 



> [!NOTE]
> Se si sta eseguendo il ripristino da un server locale, è necessario rimuovere tutti gli utenti di dominio dai ruoli del modello e aggiungerli nuovamente ai ruoli come utenti di Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Per eseguire il ripristino usando SSMS

1. In SSMS fare clic con il pulsante destro del mouse su un database > **Ripristina**.

2. Nella finestra di dialogo **Backup database** in **File di backup** fare clic su **Sfoglia**.

3. Nella finestra di dialogo **Trova file di database** selezionare il file da ripristinare.

4. In **Ripristina database** selezionare il database.

5. Specificare le opzioni. Le opzioni di sicurezza devono corrispondere alle opzioni di backup usate per eseguire il backup.


### <a name="powershell"></a>PowerShell

Usare il cmdlet [Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase).


## <a name="related-information"></a>Informazioni correlate

[Account di archiviazione di Azure](../storage/common/storage-create-storage-account.md)  
[Disponibilità elevata](analysis-services-bcdr.md)      
[Domande frequenti sulla connettività di rete Analysis Services](analysis-services-network-faq.md)