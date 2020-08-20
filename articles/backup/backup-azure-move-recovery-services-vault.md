---
title: Come spostare gli insiemi di credenziali dei servizi di ripristino di backup di Azure
description: Istruzioni su come spostare l'insieme di credenziali di servizi di ripristino tra sottoscrizioni e gruppi di risorse di Azure.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: fbbe914b3e567a1a136d735fc52965524bc17b67
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612572"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Spostare un insieme di credenziali di servizi di ripristino tra sottoscrizioni e gruppi di risorse di Azure

Questo articolo illustra come spostare un insieme di credenziali di Servizi di ripristino configurato per Backup di Azure tra sottoscrizioni di Azure o in un altro gruppo di risorse nella stessa sottoscrizione. È possibile usare il portale di Azure o PowerShell per spostare un insieme di credenziali di Servizi di ripristino.

## <a name="supported-regions"></a>Aree supportate

Lo spostamento di risorse per l'insieme di credenziali di servizi di ripristino è supportato in Australia orientale, Australia sud-orientale, Canada centrale, Canada orientale, Sud Asia orientale, Asia orientale, Stati Uniti centrali, Stati Uniti centro-settentrionali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-meridionali, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali 2, Stati Uniti occidentali, Stati Uniti occidentali 2, India centrale, India meridionale, Giappone orientale, Giappone occidentale , Europa occidentale, Sudafrica settentrionale, Sudafrica occidentale, Regno Unito meridionale e Regno Unito occidentale.

## <a name="unsupported-regions"></a>Aree non supportate

Francia centrale, Francia meridionale, Germania nord-orientale, Germania centrale, US Gov Iowa, Cina settentrionale, Cina North2, Cina orientale, Cina orientali 2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Prerequisiti per lo stato di trasferimento dell'insieme di credenziali di servizi di ripristino

- Durante lo spostamento dell'insieme di credenziali tra gruppi di risorse, i gruppi di risorse di origine e di destinazione sono bloccati impedendo le operazioni di scrittura ed eliminazione. Per altre informazioni, vedere questo [articolo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Solo la sottoscrizione amministrativa dispone delle autorizzazioni per spostare un insieme di credenziali.
- Per lo trasferimento di insiemi di credenziali tra le sottoscrizioni, la sottoscrizione di destinazione deve trovarsi nello stesso tenant della sottoscrizione di origine e il relativo stato deve essere abilitato.
- È necessario avere l'autorizzazione per eseguire operazioni di scrittura sul gruppo di risorse di destinazione.
- Lo spostamento dell'insieme di credenziali modifica solo il gruppo di risorse. L'insieme di credenziali di servizi di ripristino si trova nello stesso percorso e non può essere modificato.
- È possibile spostare solo un insieme di credenziali di servizi di ripristino, per ogni area, alla volta.
- Se una macchina virtuale non viene spostata con l'insieme di credenziali di servizi di ripristino tra sottoscrizioni o in un nuovo gruppo di risorse, i punti di ripristino della macchina virtuale correnti rimarranno intatti nell'insieme di credenziali fino a quando non scadono.
- Sia che macchina virtuale venga spostata nell'insieme di credenziali o meno, è sempre possibile ripristinare la macchina virtuale dalla cronologia di backup conservata nell'insieme di credenziali.
- Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area e nella stessa sottoscrizione di Azure.
- Per spostare una macchina virtuale con dischi gestiti, vedere questo [articolo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- Le opzioni per lo scorrimento delle risorse distribuite con il modello classico variano a seconda che si stiano migrando le risorse all'interno di una sottoscrizione o a una nuova sottoscrizione. Per altre informazioni, vedere questo [articolo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- I criteri di backup definiti per l'insieme di credenziali vengono conservati dopo che l'insieme di credenziali viene spostato tra le sottoscrizioni o in un nuovo gruppo di risorse.
- È possibile spostare solo un insieme di credenziali contenente uno dei seguenti tipi di elementi di backup. Tutti gli elementi di backup dei tipi non elencati di seguito dovranno essere arrestati e i dati verranno eliminati definitivamente prima di trasferire l'insieme di credenziali.
  - Macchine virtuali di Azure
  - Agente di Servizi di ripristino di Microsoft Azure (MARS)
  - Server di Backup di Microsoft Azure (MAB)
  - Data Protection Manager (DPM)
- Se si sposta un insieme di credenziali contenente i dati di backup delle VM, tra le sottoscrizioni, è necessario spostare le macchine virtuali nella stessa sottoscrizione e usare lo stesso nome del gruppo di risorse della VM di destinazione (come nella sottoscrizione precedente) per continuare i backup.

> [!NOTE]
> Lo stato di un insieme di credenziali di servizi di ripristino per backup di Azure in aree di Azure non è supportato<br><br>
> Se sono state configurate VM (Azure IaaS, Hyper-V, VMware) o computer fisici per il ripristino di emergenza con **Azure Site Recovery**, l'operazione di spostamento verrà bloccata. Per spostare gli insiemi di credenziali per Azure Site Recovery, vedere [questo articolo](../site-recovery/move-vaults-across-regions.md) per informazioni sullo spostamento manuale degli insiemi di credenziali.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Usare portale di Azure per spostare l'insieme di credenziali di servizi di ripristino in un gruppo di risorse diverso

Per spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in un gruppo di risorse diverso

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire l'elenco **Insiemi di credenziali dei servizi di ripristino** e selezionare l'insieme di credenziali che si vuole spostare. Quando il dashboard dell'insieme di credenziali viene aperto, viene visualizzato come illustrato nell'immagine seguente.

   ![Insieme di credenziali di Servizi di ripristino aperto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se non vengono visualizzate le **informazioni di base per l'insieme** di credenziali, selezionare l'icona a discesa. Le informazioni di base dovrebbero ora essere visualizzate.

   ![Scheda delle informazioni di base](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Nel menu Panoramica dell'insieme di credenziali selezionare **Cambia** accanto al **gruppo di risorse**per aprire il riquadro **Sposta risorse** .

   ![Cambiare il gruppo di risorse](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Nel riquadro **Sposta risorse** , per l'insieme di credenziali selezionato è consigliabile spostare le risorse correlate facoltative selezionando la casella di controllo come illustrato nella figura seguente.

   ![Spostare la sottoscrizione](./media/backup-azure-move-recovery-services/move-resource.png)

5. Per aggiungere il gruppo di risorse di destinazione, nell'elenco a discesa **gruppo di risorse** selezionare un gruppo di risorse esistente o selezionare l'opzione **Crea un nuovo gruppo** .

   ![Creare la risorsa](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Dopo aver aggiunto il gruppo di risorse, confermare di aver **compreso che gli strumenti e gli script associati alle risorse spostate non funzioneranno fino a quando non li aggiorno per l'uso della nuova opzione ID risorsa** , quindi selezionare **OK** per completare lo spostamento dell'insieme di credenziali.

   ![Messaggio di conferma](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Usare portale di Azure per spostare l'insieme di credenziali di servizi di ripristino in una sottoscrizione diversa

È possibile spostare un insieme di credenziali di Servizi di ripristino e le risorse associate in una sottoscrizione diversa

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire l'elenco Insiemi di credenziali dei servizi di ripristino e selezionare l'insieme di credenziali che si vuole spostare. Quando il dashboard dell'insieme di credenziali viene aperto, viene visualizzato come illustrato nell'immagine seguente.

    ![Insieme di credenziali di Servizi di ripristino aperto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se non vengono visualizzate le **informazioni di base per l'insieme** di credenziali, selezionare l'icona a discesa. Le informazioni di base dovrebbero ora essere visualizzate.

    ![Scheda delle informazioni di base](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Nel menu Panoramica dell'insieme di credenziali selezionare **Cambia** accanto a **sottoscrizione**per aprire il riquadro **Sposta risorse** .

   ![Cambiare la sottoscrizione](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selezionare le risorse da spostare. In questo caso, è consigliabile usare l'opzione **Seleziona tutto** per selezionare tutte le risorse facoltative elencate.

   ![Spostare la risorsa](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Dall'elenco a discesa **Sottoscrizione ** selezionare la sottoscrizione di destinazione in cui si vuole spostare l'insieme di credenziali.
6. Per aggiungere il gruppo di risorse di destinazione, nell'elenco a discesa **gruppo di risorse** selezionare un gruppo di risorse esistente o selezionare l'opzione **Crea un nuovo gruppo** .

   ![Aggiungere la sottoscrizione](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Selezionare **ho compreso che gli strumenti e gli script associati alle risorse spostate non funzioneranno fino a quando non li aggiorno per usare la nuova opzione ID risorsa** per confermare, quindi selezionare **OK**.

> [!NOTE]
> Il backup tra sottoscrizioni (l'insieme di credenziali di Servizi di ripristino e le macchine virtuali protette si trovano in sottoscrizioni diverse) non è uno scenario supportato. Neppure l'opzione di ridondanza di archiviazione può essere modificata da archiviazione con ridondanza locale ad archiviazione con ridondanza globale e viceversa durante l'operazione di spostamento dell'insieme di credenziali.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Usare PowerShell per spostare l'insieme di credenziali di servizi di ripristino

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet `Move-AzureRMResource`. `Move-AzureRMResource` richiede il nome e il tipo della risorsa. È possibile ottenerli entrambi dal cmdlet `Get-AzureRmRecoveryServicesVault`.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Per spostare le risorse in una diversa sottoscrizione, includere il parametro `-DestinationSubscriptionId`.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Dopo aver eseguito i cmdlet precedenti, verrà chiesto di confermare che si desidera spostare le risorse specificate. Digitare **Y** per confermare. Dopo la convalida, la risorsa viene spostata.

## <a name="use-cli-to-move-recovery-services-vault"></a>Usare CLI per spostare l'insieme di credenziali di servizi di ripristino

Per spostare un insieme di credenziali di Servizi di ripristino in un altro gruppo di risorse, usare il cmdlet seguente:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Per spostare in una nuova sottoscrizione, inserire il parametro `--destination-subscription-id`.

## <a name="post-migration"></a>Dopo la migrazione

1. Impostare/verificare i controlli di accesso per i gruppi di risorse.  
2. La funzionalità di creazione di report e monitoraggio di Backup deve essere configurata nuovamente per l'insieme di credenziali dopo il completamento dello spostamento. La configurazione precedente andrà persa durante l'operazione di spostamento.

## <a name="next-steps"></a>Passaggi successivi

È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni.

Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/management/move-resource-group-and-subscription.md).
