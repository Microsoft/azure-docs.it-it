---
title: Spostare le risorse associate a una configurazione di manutenzione in un'altra area
description: Informazioni su come spostare le risorse associate a una configurazione di manutenzione della macchina virtuale in un'altra area di Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 88082c441dafdc7571f2b9775bfc07ebe3ca5aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730507"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Spostare le risorse in una configurazione di controllo di manutenzione in un'altra area

Seguire questo articolo per spostare le risorse associate a una configurazione del controllo di manutenzione in un'area di Azure diversa. Potrebbe essere necessario spostare una configurazione per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area, per distribuire le funzionalità o i servizi disponibili in un'area specifica, per soddisfare i requisiti di governance e criteri interni oppure in risposta alla pianificazione della capacità.

Il [controllo della manutenzione](maintenance-control.md), con configurazioni di manutenzione personalizzate, consente di controllare la modalità di applicazione degli aggiornamenti della piattaforma alle macchine virtuali e agli host dedicati di Azure. Esistono un paio di scenari per lo trasferimento del controllo di manutenzione tra le aree:

- Per spostare le risorse associate a una configurazione di manutenzione, ma non la configurazione stessa, seguire questo articolo.
- Per spostare la configurazione del controllo di manutenzione, ma non le risorse associate alla configurazione, seguire [queste istruzioni](move-region-maintenance-configuration.md).
- Per spostare sia la configurazione di manutenzione che le risorse associate, seguire prima [queste istruzioni](move-region-maintenance-configuration.md). Quindi, seguire le istruzioni riportate in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a trasferire le risorse associate a una configurazione del controllo di manutenzione:

- Prima di iniziare, assicurarsi che le risorse che si stanno muovendo siano presenti nella nuova area.
- Verificare le configurazioni del controllo di manutenzione associate alle macchine virtuali di Azure e agli host dedicati di Azure che si desidera spostare. Controllare ogni risorsa singolarmente. Attualmente non è possibile recuperare le configurazioni per più risorse.
- Quando si recuperano le configurazioni per una risorsa:
    - Assicurarsi di usare l'ID sottoscrizione per l'account, non un ID host dedicato di Azure.
    - INTERFACCIA della riga di comando: il parametro--output Table viene usato solo per la leggibilità e può essere eliminato o modificato.
    - PowerShell: il parametro del nome del Format-Table viene usato solo per la leggibilità e può essere eliminato o modificato.
    - Se si usa PowerShell, viene ricevuto un errore se si tenta di elencare le configurazioni per una risorsa che non dispone di configurazioni associate. L'errore sarà simile al seguente: "operazione non riuscita con stato:' non trovato '. Dettagli: 404 errore del client: Impossibile trovare l'URL ".

    
## <a name="prepare-to-move"></a>Preparare lo spostamento

1. Prima di iniziare, definire queste variabili. È stato fornito un esempio per ognuno di essi.

    **Variabile** | **Dettagli** | **Esempio**
    --- | ---
    $subId | ID per la sottoscrizione contenente le configurazioni di manutenzione | "The-Subscription-ID"
    $rsrcGroupName | Nome del gruppo di risorse (VM di Azure) | "VMResourceGroup"
    $vmName | Nome della risorsa VM |  MyVM
    $adhRsrcGroupName |  Gruppo di risorse (host dedicati) | "HostResourceGroup"
    $adh | Nome host dedicato | MyHost
    $adhParentName | Nome risorsa padre | HostGroup
    
2. Per recuperare le configurazioni di manutenzione usando il comando PowerShell [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) :

    - Per gli host dedicati di Azure, eseguire:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Per le macchine virtuali di Azure, eseguire:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Per recuperare le configurazioni di manutenzione usando l'interfaccia della riga di comando [AZ Maintenance Assignment](/cli/azure/ext/maintenance/maintenance/assignment) Command:

    - Per gli host dedicati di Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Per le macchine virtuali di Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Sposta 

1. [Seguire queste istruzioni](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) per spostare le macchine virtuali di Azure nella nuova area.
2. Dopo lo spostamento delle risorse, riapplicare le configurazioni di manutenzione alle risorse nella nuova area in base alle esigenze, a seconda che siano state spostate le configurazioni di manutenzione. È possibile applicare una configurazione di manutenzione a una risorsa usando [PowerShell](../virtual-machines/maintenance-control-powershell.md) o l' [interfaccia](../virtual-machines/maintenance-control-cli.md)della riga di comando.


## <a name="verify-the-move"></a>Verificare lo spostamento

Verificare le risorse nella nuova area e verificare le configurazioni associate per le risorse nella nuova area. 

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Dopo lo spostamento, provare a eliminare le risorse spostate nell'area di origine.


## <a name="next-steps"></a>Passaggi successivi

Seguire [queste istruzioni](move-region-maintenance-configuration.md) se è necessario spostare le configurazioni di manutenzione. 
