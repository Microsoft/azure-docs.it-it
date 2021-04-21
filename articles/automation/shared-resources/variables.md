---
title: Gestire variabili in Automazione di Azure
description: Questo articolo descrive come usare le variabili nei runbook e nelle configurazioni DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f1ace00356583dbb6102317e3d157fb58682710
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832580"
---
# <a name="manage-variables-in-azure-automation"></a>Gestire variabili in Automazione di Azure

Gli asset di tipo variabile sono valori disponibili per tutti i runbook e le configurazioni DSC nell'account di Automazione. È possibile gestirli dal portale di Azure, da PowerShell, all'interno di un runbook o in una configurazione DSC.

Le variabili di automazione sono utili per gli scenari seguenti:

- Condivisione di un valore tra più runbook o configurazioni DSC.

- Condivisione di un valore tra più processi dello stesso runbook o configurazione DSC.

- Gestione di un valore usato dai runbook o dalle configurazioni DSC dal portale o dalla riga di comando di PowerShell. Un esempio è un set di elementi di configurazione comuni, ad esempio un elenco specifico di nomi di macchine virtuali, un gruppo di risorse specifico, un nome di dominio AD e altro ancora.  

Automazione di Azure salva in modo permanente le variabili e le rende disponibili anche in caso di errore per un runbook o una configurazione DSC. Questo comportamento consente a un runbook o a una configurazione DSC di impostare un valore che viene quindi usato da un altro runbook o dallo stesso runbook o dalla stessa configurazione DSC alla successiva esecuzione.

Automazione di Azure archivia in modo sicuro ogni variabile crittografata. Quando si crea una variabile, è possibile specificarne la crittografia e l'archiviazione Automazione di Azure come asset sicuro. Dopo aver creato la variabile, non è possibile cambiarne lo stato di crittografia senza crearla nuovamente. Se sono disponibili variabili dell'account di Automazione usate per archiviare dati sensibili che non sono già crittografati, è necessario eliminarle e ricrearle come variabili crittografate. Una raccomandazione del Centro sicurezza di Azure suggerisce di crittografare tutte le variabili di Automazione di Azure, come descritto in [Le variabili dell'account di Automazione devono essere crittografate](../../security-center/recommendations-reference.md#recs-compute). Se si vogliono escludere alcune variabili non crittografate da questa raccomandazione di sicurezza, vedere [Esentare una risorsa dalle raccomandazioni e dal punteggio di sicurezza](../../security-center/exempt-resource.md) per creare una regola di esenzione.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Tali asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di Automazione. Automazione di Azure memorizza la chiave nel Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, Automazione carica la chiave da Key Vault e quindi la usa per crittografare l'asset.

## <a name="variable-types"></a>Tipi di variabile

Quando si crea una variabile con il portale di Azure, è necessario selezionare un tipo di dati nell'elenco a discesa, in modo che nel portale sia visualizzato il controllo appropriato per immettere il valore della variabile. Di seguito sono riportati i tipi di variabile disponibili in Automazione di Azure:

* string
* Integer
* Datetime
* Boolean
* Null

La variabile non è limitata al tipo di dati specificato. È necessario impostarla usando Windows PowerShell se si vuole specificare un valore di tipo diverso. Se si indica `Not defined`, il valore della variabile viene impostato su Null. È necessario impostare il valore con il cmdlet [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) o il cmdlet interno `Set-AutomationVariable`. Usare nei runbook che devono essere eseguiti nell'ambiente sandbox di Azure o in un ruolo di lavoro ibrido `Set-AutomationVariable` per runbook di Windows.

Non è possibile usare il portale di Azure per creare o modificare il valore per un tipo di variabile complesso. Tuttavia, è possibile fornire un valore di qualsiasi tipo usando Windows PowerShell. I tipi complessi vengono recuperati come [Newtonsoft.Jssu . Linq.JProperty per](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) un tipo di oggetto Complex anziché psObject [di tipo PSCustomObject.](/dotnet/api/system.management.automation.pscustomobject)

È possibile archiviare più valori in una singola variabile creando una matrice o una tabella hash e salvandola nella variabile.

>[!NOTE]
>Le variabili del nome della macchina virtuale possono avere una lunghezza massima di 80 caratteri. Le variabili dei gruppi di risorse possono contenere un massimo di 90 caratteri. Vedere [Regole di denominazione e restrizioni per le risorse di Azure](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>Cmdlet di PowerShell per accedere alle variabili

I cmdlet nella tabella seguente vengono usati per creare e gestire le variabili di Automazione con PowerShell. Sono inclusi nei [moduli Az](modules.md#az-modules).

| Cmdlet | Descrizione |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Recupera il valore di una variabile esistente. Se il valore è di tipo semplice, verrà recuperato lo stesso tipo. Se è un tipo complesso, viene recuperato un tipo `PSCustomObject`. <sup>1</sup>|
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Crea una nuova variabile e ne imposta il valore.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Rimuove una variabile esistente.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Imposta il valore di una variabile esistente. |

<sup>1</sup> Non è possibile usare questo cmdlet per recuperare il valore di una variabile crittografata. L'unico modo per eseguire questa operazione consiste nell'usare il cmdlet `Get-AutomationVariable` interno in un runbook o una configurazione DSC. Ad esempio, per visualizzare il valore di una variabile crittografata, è possibile creare un runbook per ottenere la variabile e quindi scriverla nel flusso di output:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>Cmdlet interni per accedere alle variabili

I cmdlet interni nella tabella seguente vengono usati per accedere alle variabili nei runbook e nelle configurazioni DSC. Questi cmdlet sono inclusi nel modulo globale `Orchestrator.AssetManagement.Cmdlets`. Per altre informazioni, vedere [Cmdlet interni](modules.md#internal-cmdlets).

| Cmdlet interni | Descrizione |
|:---|:---|
|`Get-AutomationVariable`|Recupera il valore di una variabile esistente.|
|`Set-AutomationVariable`|Imposta il valore di una variabile esistente.|

> [!NOTE]
> Evitare di usare variabili nel `Name` parametro del cmdlet in un `Get-AutomationVariable` runbook o in una configurazione DSC. L'uso di una variabile può complicare l'individuazione delle dipendenze tra runbook e variabili di automazione in fase di progettazione.

## <a name="python-functions-to-access-variables"></a>Funzioni Python per accedere alle variabili

Le funzioni nella tabella seguente vengono usate per accedere alle variabili in un runbook Python 2 e 3. I runbook Python 3 sono attualmente in anteprima.

|Funzioni Python|Descrizione|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera il valore di una variabile esistente. |
|`automationassets.set_automation_variable`|Imposta il valore di una variabile esistente. |

> [!NOTE]
> È necessario importare il modulo `automationassets` nella parte superiore del runbook Python per accedere alle funzioni dell'asset.

## <a name="create-and-get-a-variable"></a>Creare e ottenere una variabile

>[!NOTE]
>Se si vuole rimuovere la crittografia per una variabile, è necessario eliminare la variabile e ricrearla come non crittografata.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Creare e ottenere una variabile usando il portale di Azure

1. Nel riquadro sinistro dell'account di Automazione selezionare **Variabili** in **Risorse condivise.**
2. Nella pagina **Variabili** selezionare **Aggiungi una variabile.**
3. Completare le opzioni nella **pagina Nuova** variabile e quindi selezionare **Crea per** salvare la nuova variabile.

> [!NOTE]
> Dopo averla salvata, una variabile crittografata non può essere visualizzata nel portale. Può essere solo aggiornata.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Creare e ottenere una variabile in Windows PowerShell

Il runbook o la configurazione DSC usa il cmdlet `New-AzAutomationVariable` per creare una nuova variabile e impostarne il valore iniziale. Se la variabile è crittografata, la chiamata deve usare il parametro `Encrypted`. Lo script può recuperare il valore della variabile usando `Get-AzAutomationVariable`.

>[!NOTE]
>Uno script di PowerShell non può recuperare un valore crittografato. L'unico modo per eseguire questa operazione consiste nell'usare il cmdlet `Get-AutomationVariable` interno.

L'esempio seguente illustra come creare una variabile stringa e quindi restituirne il valore.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"
$variableValue = "My String"

New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable' `
-Encrypted $false -Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable').Value
```

L'esempio seguente illustra come creare una variabile di tipo complesso e quindi recuperarne le proprietà. In questo caso, viene usato un oggetto macchina virtuale da [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) specificando un subset delle relative proprietà.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" -Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" -AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable" -Encrypted $false -Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable"

$vmName = $vmValue.Value.Name
$vmTags = $vmValue.Value.Tags
```

## <a name="textual-runbook-examples"></a>Esempi di runbook testuali

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

L'esempio seguente illustra come impostare e recuperare una variabile in un runbook testuale. Questo esempio presuppone la creazione di variabili integer denominate **numberOfIterations** e **numberOfRunnings** e di una variabile stringa denominata **sampleMessage**.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$numberOfIterations = Get-AutomationVariable -Name "numberOfIterations"
$numberOfRunnings = Get-AutomationVariable -Name "numberOfRunnings"
$sampleMessage = Get-AutomationVariable -Name "sampleMessage"

Write-Output "Runbook has been run $numberOfRunnings times."

for ($i = 1; $i -le $numberOfIterations; $i++) {
    Write-Output "$i`: $sampleMessage"
}
Set-AutomationVariable -Name numberOfRunnings -Value ($numberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

L'esempio seguente illustra come recuperare una variabile, impostare una variabile e gestire un'eccezione per una variabile inesistente in un runbook di Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

# <a name="python-3"></a>[Python 3](#tab/python3)

L'esempio seguente illustra come ottenere una variabile, impostarne una e gestire un'eccezione per una variabile inesistente in un runbook Python 3 (anteprima).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Esempi di runbook grafici

In un runbook grafico è possibile aggiungere attività per i cmdlet interni **Get-AutomationVariable** o **Set-AutomationVariable.** È sufficiente fare clic con il pulsante destro del mouse su ogni variabile nel riquadro della raccolta dell'editor grafico e selezionare l'attività desiderata.

![Aggiungere una variabile al canvas](../media/variables/runbook-variable-add-canvas.png)

La figura seguente illustra attività di esempio per aggiornare una variabile con un valore semplice in un runbook grafico. In questo esempio l'attività per recupera una singola macchina virtuale di Azure e salva il nome `Get-AzVM` del computer in una variabile stringa di Automazione esistente. Non è importante se il [collegamento è una pipeline o una sequenza](../automation-graphical-authoring-intro.md#use-links-for-workflow) poiché il codice si aspetta un singolo oggetto nell'output.

![Impostare una variabile semplice](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui cmdlet usati per accedere alle variabili, vedere [Gestire i moduli in Automazione di Azure](modules.md).

- Per informazioni generali sui runbook, vedere [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md).

- Per informazioni dettagliate sulle configurazioni DSC, vedere [Panoramica di State Configuration di Automazione di Azure](../automation-dsc-overview.md).
