---
title: Usare i criteri di scalabilità personalizzati con i set di scalabilità di macchine virtuali di Azure
description: Informazioni su come usare i criteri di scalabilità personalizzati con i set di scalabilità di macchine virtuali di Azure che usano la configurazione di scalabilità automatica per gestire il numero di istanze
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747795"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Usare i criteri di scalabilità personalizzati con i set di scalabilità di macchine virtuali di Azure

Una distribuzione del set di scalabilità di macchine virtuali può essere scalata orizzontalmente o ridimensionata in base a una matrice di metriche, incluse le metriche personalizzate definite dall'utente e della piattaforma. Mentre una scalabilità orizzontale crea nuove macchine virtuali in base al modello del set di scalabilità, una riduzione influiscono sulle macchine virtuali in esecuzione che possono avere configurazioni e/o funzioni diverse durante l'evoluzione del carico di lavoro del set di scalabilità. 

La funzionalità per i criteri di scalabilità consente agli utenti di configurare l'ordine in cui le macchine virtuali vengono ridimensionate in base a tre configurazioni con scalabilità orizzontale: 

1. Predefinito
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Criteri di ridimensionamento predefiniti

Per impostazione predefinita, il set di scalabilità di macchine virtuali applica questo criterio per determinare quali istanze verranno ridimensionate. Con i criteri *predefiniti* , le macchine virtuali vengono selezionate per il ridimensionamento nell'ordine seguente:

1. Bilanciare le macchine virtuali tra le zone di disponibilità (se il set di scalabilità viene distribuito nella configurazione di zona)
2. Bilanciare le macchine virtuali tra domini di errore (lavoro ottimale)
3. Elimina la macchina virtuale con l'ID istanza più elevato

Gli utenti non devono specificare un criterio di ridimensionamento se desiderano solo che venga eseguito l'ordinamento predefinito.

Si noti che il bilanciamento tra le zone di disponibilità o i domini di errore non sposta le istanze tra le zone di disponibilità o i domini di errore. Il bilanciamento viene effettuato tramite l'eliminazione di macchine virtuali dalle zone di disponibilità non bilanciate o dai domini di errore fino a quando la distribuzione delle macchine virtuali non viene bilanciata.

### <a name="newestvm-scale-in-policy"></a>Criteri di ridimensionamento NewestVM

Questo criterio eliminerà la macchina virtuale creata più recente nel set di scalabilità, dopo aver bilanciato le VM tra le zone di disponibilità (per le distribuzioni di zona). L'abilitazione di questo criterio richiede una modifica della configurazione nel modello del set di scalabilità di macchine virtuali.

### <a name="oldestvm-scale-in-policy"></a>Criteri di ridimensionamento OldestVM

Questo criterio eliminerà la macchina virtuale meno recente creata nel set di scalabilità, dopo aver bilanciato le macchine virtuali tra le zone di disponibilità (per le distribuzioni di zona). L'abilitazione di questo criterio richiede una modifica della configurazione nel modello del set di scalabilità di macchine virtuali.

## <a name="enabling-scale-in-policy"></a>Abilitazione dei criteri di scalabilità

Un criterio di ridimensionamento viene definito nel modello del set di scalabilità di macchine virtuali. Come indicato nelle sezioni precedenti, quando si usano i criteri ' NewestVM ' è OldestVM ' è necessaria una definizione dei criteri di scalabilità. Il set di scalabilità di macchine virtuali userà automaticamente i criteri di scalabilità "predefiniti" Se nel modello del set di scalabilità non è presente alcuna definizione dei criteri di scalabilità. 

Un criterio di scalabilità orizzontale può essere definito nel modello di set di scalabilità di macchine virtuali nei modi seguenti:

### <a name="azure-portal"></a>Portale di Azure
 
I passaggi seguenti definiscono i criteri di scalabilità quando si crea un nuovo set di scalabilità. 
 
1. Passare a **set di scalabilità di macchine virtuali**.
1. Selezionare **+ Aggiungi** per creare un nuovo set di scalabilità.
1. Passare alla scheda **scalabilità** . 
1. Individuare la sezione dei **criteri di ridimensionamento** .
1. Selezionare un criterio di ridimensionamento nell'elenco a discesa.
1. Al termine della creazione del nuovo set di scalabilità, selezionare il pulsante **Verifica + crea** .

### <a name="using-api"></a>Uso dell'API

Eseguire un'istruzione PUT sul set di scalabilità di macchine virtuali usando l'API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Creare un gruppo di risorse, quindi creare un nuovo set di scalabilità con i criteri di scalabilità impostati come *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Nell'esempio seguente viene aggiunto un criterio di scalabilità in durante la creazione di un nuovo set di scalabilità. Creare prima di tutto un gruppo di risorse, quindi creare un nuovo set di scalabilità con i criteri di scalabilità in *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Uso del modello

Nel modello, in "Properties", aggiungere quanto segue:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

I blocchi precedenti specificano che il set di scalabilità di macchine virtuali eliminerà la macchina virtuale meno recente in un set di scalabilità con bilanciamento della zona, quando viene attivata una riduzione delle prestazioni (tramite la scalabilità automatica o l'eliminazione manuale).

Quando un set di scalabilità di macchine virtuali non è bilanciato, il set di scalabilità eliminerà prima le macchine virtuali tra le zone sbilanciate. All'interno delle zone sbilanciate, il set di scalabilità userà i criteri di scalabilità specificati sopra per determinare la macchina virtuale di cui eseguire il ridimensionamento. In questo caso, all'interno di una zona sbilanciata, il set di scalabilità selezionerà la macchina virtuale meno recente che si trova in tale zona da eliminare.

Per il set di scalabilità di macchine virtuali non di zona, il criterio seleziona la macchina virtuale meno recente nel set di scalabilità per l'eliminazione.

Lo stesso processo si applica quando si usa ' NewestVM ' nei criteri di scalabilità precedenti.

## <a name="modifying-scale-in-policies"></a>Modifica di criteri di scalabilità

La modifica dei criteri di ridimensionamento segue lo stesso processo di applicazione dei criteri di scalabilità. Se, ad esempio, nell'esempio precedente si desidera modificare il criterio da' OldestVM ' a' NewestVM ', è possibile eseguire questa operazione in base a quanto segue:

### <a name="azure-portal"></a>Portale di Azure

È possibile modificare i criteri di scalabilità di un set di scalabilità esistente tramite il portale di Azure. 
 
1. In un set di scalabilità di macchine virtuali esistente selezionare **scalabilità** dal menu a sinistra.
1. Selezionare la scheda **criteri di ridimensionamento** .
1. Selezionare un criterio di ridimensionamento nell'elenco a discesa.
1. Al termine, selezionare **Salva**. 

### <a name="using-api"></a>Uso dell'API

Eseguire un'istruzione PUT sul set di scalabilità di macchine virtuali usando l'API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Aggiornare i criteri di scalabilità di un set di scalabilità esistente:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Di seguito è riportato un esempio per l'aggiornamento dei criteri di scalabilità di un set di scalabilità esistente: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Uso del modello

Nel modello, in "Properties", modificare il modello come riportato di seguito e ridistribuire: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Se si decide di modificare ' NewestVM ' in ' default ' o ' OldestVM ', viene applicato lo stesso processo

## <a name="instance-protection-and-scale-in-policy"></a>Criteri di protezione dell'istanza e di ridimensionamento

I set di scalabilità di macchine virtuali forniscono due tipi di [protezione dell'istanza](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Proteggi dalla riduzione
2. Proteggi da azioni di set di scalabilità

Una macchina virtuale protetta non viene eliminata tramite un'azione di riduzione delle prestazioni, a prescindere dai criteri di scalabilità applicati. Se, ad esempio, VM_0 (macchina virtuale meno recente nel set di scalabilità) è protetta dal ridimensionamento e nel set di scalabilità sono abilitati i criteri di scalabilità "OldestVM", VM_0 non verranno presi in considerazione per la scalabilità in, anche se si tratta della macchina virtuale meno recente nel set di scalabilità. 

Una macchina virtuale protetta può essere eliminata manualmente dall'utente in qualsiasi momento, indipendentemente dai criteri di ridimensionamento abilitati nel set di scalabilità. 

## <a name="usage-examples"></a>Esempi di utilizzo 

Gli esempi seguenti illustrano come un set di scalabilità di macchine virtuali selezioni le VM da eliminare quando viene attivato un evento di scalabilità. Si presuppone che le macchine virtuali con gli ID istanza più elevati siano le VM più recenti nel set di scalabilità e che le VM con gli ID di istanza più piccoli siano le macchine virtuali meno recenti del set di scalabilità. 

### <a name="oldestvm-scale-in-policy"></a>Criteri di ridimensionamento OldestVM

| Evento                 | ID istanza in zona 1  | ID istanza in zona 2  | ID istanza in zona 3  | Selezione con scalabilità                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Ridimensionamento              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Scegliere tra Zona 1 e 2, anche se Zona 3 dispone della macchina virtuale meno recente. Eliminare VM2 da Zona 2 perché si tratta della macchina virtuale meno recente in tale zona.   |
| Ridimensionamento              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Scegliere Zona 1 anche se Zona 3 dispone della macchina virtuale meno recente. Eliminare VM3 da Zona 1 perché si tratta della macchina virtuale meno recente in tale zona.                  |
| Ridimensionamento              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Le zone sono bilanciate. Eliminare VM1 in Zona 3 perché si tratta della macchina virtuale meno recente nel set di scalabilità.                                               |
| Ridimensionamento              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Scegliere tra Zona 1 e Zona 2. Eliminare VM4 in Zona 1 perché si tratta della macchina virtuale meno recente tra le due zone.                              |
| Ridimensionamento              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Scegliere Zona 2 anche se Zona 1 dispone della macchina virtuale meno recente. Eliminare la VM6 È in Zona 1 perché si tratta della macchina virtuale meno recente in tale zona.                    |
| Ridimensionamento              | ***5***, 10            | 9, 11                  | 7, 8                   | Le zone sono bilanciate. Eliminare VM5 in Zona 1 perché si tratta della macchina virtuale meno recente nel set di scalabilità.                                                |

Per i set di scalabilità di macchine virtuali non di zona, il criterio seleziona la macchina virtuale meno recente nel set di scalabilità per l'eliminazione. Eventuali macchine virtuali "protette" verranno ignorate per l'eliminazione.

### <a name="newestvm-scale-in-policy"></a>Criteri di ridimensionamento NewestVM

| Evento                 | ID istanza in zona 1  | ID istanza in zona 2  | ID istanza in zona 3  | Selezione con scalabilità                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Ridimensionamento              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Scegliere tra Zona 1 e 2. Eliminare da VM11 da Zona 2 perché è la macchina virtuale più recente tra le due zone.                                |
| Ridimensionamento              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Scegliere Zona 1 perché contiene più macchine virtuali rispetto alle altre due zone. Eliminare VM10 da Zona 1 perché è la macchina virtuale più recente in tale zona.          |
| Ridimensionamento              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Le zone sono bilanciate. Eliminare VM9 in Zona 2 perché è la macchina virtuale più recente nel set di scalabilità.                                                |
| Ridimensionamento              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Scegliere tra Zona 1 e Zona 3. Eliminare VM8 in Zona 3 perché è la macchina virtuale più recente in tale zona.                                      |
| Ridimensionamento              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Scegliere Zona 1 anche se Zona 3 dispone della VM più recente. Eliminare VM5 in Zona 1 perché è la macchina virtuale più recente in tale zona.                    |
| Ridimensionamento              | 3, 4                   | 2, 6                   | 1, ***7***             | Le zone sono bilanciate. Eliminare VM7 in Zona 3 perché è la macchina virtuale più recente nel set di scalabilità.                                                |

Per i set di scalabilità di macchine virtuali non di zona, il criterio seleziona la macchina virtuale più recente nel set di scalabilità per l'eliminazione. Eventuali macchine virtuali "protette" verranno ignorate per l'eliminazione. 

## <a name="troubleshoot"></a>Risolvere problemi

1. Errore di abilitazione di scaleInPolicy se viene visualizzato un errore ' richiesta non valida ' con un messaggio di errore che indica che "non è stato possibile trovare il membro ' scaleInPolicy ' nell'oggetto di tipo ' Properties '", quindi controllare la versione dell'API usata per il set di scalabilità di macchine virtuali. Per questa funzionalità è necessaria l'API versione 2019-03-01 o successiva.

2. Selezione errata delle VM per la scalabilità. vedere gli esempi precedenti. Se il set di scalabilità di macchine virtuali è una distribuzione di zona, i criteri di riduzione delle prestazioni vengono applicati per primi alle zone sbilanciate e quindi al set di scalabilità quando viene bilanciato con la zona. Se l'ordine di ridimensionamento non è coerente con gli esempi precedenti, generare una query con il team del set di scalabilità di macchine virtuali per la risoluzione dei problemi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire l'applicazione](virtual-machine-scale-sets-deploy-app.md) nei set di scalabilità di macchine virtuali.
