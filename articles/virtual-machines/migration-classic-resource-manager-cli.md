---
title: Eseguire la migrazione di VM a Gestione risorse usando l'interfaccia della riga
description: Questo articolo illustra la migrazione di risorse supportata dalla piattaforma dal modello di distribuzione classica a Azure Resource Manager usando l'interfaccia della riga di comando di Azure.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669329"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Attualmente, circa il 90% delle VM IaaS USA [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partire dal 28 febbraio 2020, le macchine virtuali classiche sono state deprecate e saranno completamente ritirate il 1 ° marzo 2023. [Scopri di più]( https://aka.ms/classicvmretirement) su questa deprecazione e sul [modo in cui influiscono sull'utente](classic-vm-deprecation.md#how-does-this-affect-me).

Questi passaggi mostrano come usare i comandi dell'interfaccia della riga di comando di Azure per eseguire la migrazione dalle risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager. Ai fini dell'articolo, è necessario usare l'[interfaccia della riga di comando classica di Azure](/cli/azure/install-classic-cli). Poiché l'interfaccia della riga di comando di Azure è disponibile solo per le risorse di Azure Resource Manager, non può essere usata per la migrazione.

> [!NOTE]
> Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma proverà di nuovo a eseguire l'azione.
> 
> 

<br>
Ecco un diagramma di flusso per identificare l'ordine di esecuzione dei passaggi durante un processo di migrazione

![Screenshot that shows the migration steps](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Passaggio 1: Preparare la migrazione
Ecco alcune procedure consigliate per valutare la migrazione delle risorse IaaS dal modello classico al modello di Resource Manager:

* Leggere [l'elenco di configurazioni e funzionalità non supportate](migration-classic-resource-manager-overview.md). Se sono disponibili macchine virtuali che usano configurazioni o funzionalità non supportate, è consigliabile attendere l'annuncio del supporto di tali configurazioni o funzionalità. In alternativa, è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione se ciò soddisfa le esigenze.
* Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.

> [!IMPORTANT]
> I gateway applicazione non sono attualmente supportati per la migrazione dal modello di distribuzione classica a Resource Manager. Per eseguire la migrazione di una rete virtuale classica con un gateway applicazione, rimuovere il gateway prima di eseguire un'operazione di preparazione dello spostamento della rete. Dopo aver completato la migrazione, riconnettere il gateway in Azure Resource Manager. 
>
>Non è possibile eseguire la migrazione automatica di gateway ExpressRoute che si connettono a circuiti ExpressRoute in un'altra sottoscrizione. In tal caso, rimuovere il gateway ExpressRoute, eseguire la migrazione della rete virtuale e ricreare il gateway. Per altre informazioni, vedere [Eseguire la migrazione di circuiti ExpressRoute e delle reti virtuali associate dalla distribuzione classica al modello di distribuzione Resource Manager](../expressroute/expressroute-migration-classic-resource-manager.md).
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Passaggio 2: Impostare la sottoscrizione e registrare il provider
Per gli scenari di migrazione è necessario configurare l'ambiente per il modello classico e di Resource Manager. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-classic-cli) e [selezionare la sottoscrizione](/cli/azure/authenticate-azure-cli).

Accedere al proprio account.

```azurecli
azure login
```

Selezionare la sottoscrizione di Azure usando il comando seguente.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> La registrazione è un passaggio da eseguire un'unica volta, tuttavia deve essere eseguita prima di tentare la migrazione. Senza la registrazione verrà visualizzato il seguente messaggio di errore 
> 
> *BadRequest: Subscription is not registered for migration* (Richiesta non valida: la sottoscrizione non è registrata per la migrazione) 
> 
> 

Registrarsi con il provider di risorse di migrazione utilizzando il comando seguente. Si noti che in alcuni casi si verifica il timeout di questo comando. Tuttavia, la registrazione avrà esito positivo.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Attendere cinque minuti che la registrazione venga completata. È possibile controllare lo stato dell'approvazione con il comando seguente. Assicurarsi che RegistrationState sia `Registered` prima di procedere.

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Passare ora dall'interfaccia della riga di comando alla modalità `asm` .

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passaggio 3: verificare che siano disponibili sufficienti CPU virtuali delle macchine virtuali di Azure Resource Manager nell'area di Azure di cui fa parte la distribuzione corrente o la rete virtuale
Per questo passaggio è necessario passare alla modalità `arm` . A tale scopo, eseguire il comando seguente.

```azurecli
azure config mode arm
```

È possibile usare il comando seguente dell'interfaccia della riga di comando per controllare il numero corrente di CPU virtuali in Azure Resource Manager. Per altre informazioni sulle quote di CPU virtuali, vedere [Limiti e Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Dopo aver verificato questo passaggio, è possibile passare nuovamente alla modalità `asm` .

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Passaggio 4: Opzione 1 - Eseguire la migrazione delle macchine virtuali a un servizio cloud
Ottenere l'elenco dei servizi cloud con il comando seguente e selezionare il servizio cloud di cui si vuole eseguire la migrazione. Si noti che se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web/di lavoro, verrà visualizzato un messaggio di errore.

```azurecli
azure service list
```

Eseguire il comando seguente per ottenere il nome della distribuzione per il servizio cloud dall'output dettagliato. Nella maggior parte dei casi, il nome della distribuzione corrisponde al nome del servizio cloud.

```azurecli
azure service show <serviceName> -vv
```

Per prima cosa, verificare se è possibile eseguire la migrazione del servizio cloud usando i comandi seguenti:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

Se si vuole eseguire la migrazione delle VM a una rete virtuale creata dalla piattaforma, usare il comando seguente.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Se si vuole eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Resource Manager, usare il comando seguente.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Dopo aver completato l'operazione di preparazione, è possibile osservare l'output dettagliato per ottenere lo stato di migrazione delle VM, verificando che sia `Prepared` .

```azurecli
azure vm show <vmName> -vv
```

Controllare la configurazione per le risorse preparate tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Passaggio 4: Opzione 2 - Eseguire la migrazione delle macchine virtuali a una rete virtuale
Selezionare la rete virtuale per cui si vuole eseguire la migrazione. Si noti che se la rete virtuale contiene ruoli Web/di lavoro o VM con configurazioni non supportate, verrà visualizzato un messaggio di errore di convalida.

Ottenere tutte le reti virtuali nella sottoscrizione con il comando seguente.

```azurecli
azure network vnet list
```

Verrà visualizzato un risultato simile al seguente:

![Schermata della riga di comando con evidenziato l'intero nome della rete virtuale.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Nell'esempio precedente il valore **virtualNetworkName** è l'intero nome **"Gruppo classicubuntu16 classicubuntu16"**.

Per prima cosa, verificare se sia possibile eseguire la migrazione della rete virtuale usando il comando seguente:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Preparare la rete virtuale scelta per la migrazione con il comando seguente.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

Controllare la configurazione per le macchine virtuali preparate tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>Passaggio 5: Eseguire la migrazione di un account di archiviazione
Dopo aver completato la migrazione delle macchine virtuali, si consiglia di migrare l'account di archiviazione.

Preparare l'account di archiviazione per la migrazione con il comando seguente.

```azurecli
azure storage account prepare-migration <storageAccountName>
```

Controllare la configurazione per l'account di archiviazione preparato tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sulla migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica al modello Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Community tools for assisting with migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md) (Strumenti della community per assistenza alla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md)
