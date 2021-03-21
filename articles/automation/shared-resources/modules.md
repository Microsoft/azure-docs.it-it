---
title: Gestire i moduli in Automazione di Azure
description: Questo articolo descrive come usare i moduli di PowerShell per abilitare i cmdlet nei runbook e le risorse DSC nelle configurazioni DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: ae3329401a138bc0566ea93a8fbf2071fd44f02c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503420"
---
# <a name="manage-modules-in-azure-automation"></a>Gestire i moduli in Automazione di Azure

Automazione di Azure usa alcuni moduli di PowerShell per abilitare i cmdlet nei runbook e nelle risorse DSC nelle configurazioni DSC. I moduli supportati includono:

* [Az.Automation di Azure PowerShell](/powershell/azure/new-azureps-module-az).
* [AzureRM.Automation di Azure PowerShell](/powershell/module/azurerm.automation/).
* Altri moduli di PowerShell.
* Modulo `Orchestrator.AssetManagement.Cmdlets` interno.
* Moduli Python 2.
* Moduli personalizzati creati dall'utente.

Quando si crea un account di Automazione, Automazione di Azure importa alcuni moduli per impostazione predefinita. Vedere [Moduli predefiniti](#default-modules).

## <a name="sandboxes"></a>Sandbox

Quando Automazione esegue runbook e processi di compilazione DSC, carica i moduli in sandbox in cui è possibile eseguire i runbook e la compilazione di configurazioni DSC. Automazione inserisce automaticamente anche eventuali risorse DSC nei moduli nel server di pull DSC. I computer possono effettuare il pull delle risorse quando applicano le configurazioni DSC.

>[!NOTE]
>Assicurarsi di importare solo i moduli necessari per i runbook e le configurazioni DSC. Si sconsiglia di importare il modulo Az radice perché include molti altri moduli che potrebbero non essere necessari e che possono causare problemi di prestazioni. Importare invece singoli moduli, ad esempio Az.Compute.

Cloud sandbox supporta un massimo di 48 chiamate di sistema e limita tutte le altre chiamate per motivi di sicurezza. Altre funzionalità, ad esempio la gestione delle credenziali e alcune reti, non sono supportate in sandbox cloud.

A causa del numero di moduli e cmdlet inclusi, è difficile stabilire in anticipo quale dei cmdlet effettuerà chiamate non supportate. In genere, sono stati riscontrati problemi con i cmdlet che richiedono l'accesso con privilegi elevati, richiedono una credenziale come parametro o i cmdlet correlati alla rete. Tutti i cmdlet che eseguono operazioni di rete di stack completi non sono supportati in sandbox, incluso [Connect-AipService](/powershell/module/aipservice/connect-aipservice) dal modulo AipService di PowerShell e [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) dal modulo DNSClient.

Si tratta di limitazioni note con la sandbox. La soluzione alternativa consigliata consiste nel distribuire un ruolo di [lavoro ibrido per Runbook](../automation-hybrid-runbook-worker.md) o usare [funzioni di Azure](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Moduli predefiniti

La tabella seguente elenca i moduli importati da Automazione di Azure per impostazione predefinita quando si crea l'account di Automazione. Automazione può importare versioni più recenti di questi moduli. Non è tuttavia possibile rimuovere la versione originale dall'account di Automazione, anche se si elimina la versione più recente. Si noti che questi moduli predefiniti includono diversi moduli AzureRM.

I moduli predefiniti sono noti anche come moduli globali. Nel portale di Azure la proprietà del **modulo globale** sarà **true** quando si visualizza un modulo importato al momento della creazione dell'account.

![Screenshot della proprietà del modulo globale nel portale di Azure](../media/modules/automation-global-modules.png)

Automazione non importa automaticamente il modulo Az radice negli account di Automazione nuovi o esistenti. Per altre informazioni sull'uso di questi moduli, vedere [Migrazione a moduli Az](#migrate-to-az-modules).

> [!NOTE]
> Si sconsiglia di modificare moduli e runbook negli account di Automazione usati per la distribuzione della funzionalità [Avvio/Arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md).

|Nome del modulo|Versione|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Moduli Az

Per Az.Automation, la maggior parte dei cmdlet hanno gli stessi nomi di quelli usati per i moduli AzureRM, ad eccezione del fatto che il prefisso `AzureRM` è stato modificato in `Az`. Per un elenco dei moduli Az che non seguono questa convenzione di denominazione, vedere l'[elenco di eccezioni](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Cmdlet interni

Automazione di Azure supporta il modulo `Orchestrator.AssetManagement.Cmdlets` interno per l'agente Log Analytics per Windows, installato per impostazione predefinita. La tabella seguente definisce i cmdlet interni. Questi cmdlet sono progettati per essere usati al posto dei cmdlet di Azure PowerShell per interagire con le risorse condivise. Possono recuperare i segreti da variabili crittografate, credenziali e connessioni crittografate.

>[!NOTE]
>I cmdlet interni sono disponibili solo quando si eseguono runbook nell'ambiente sandbox di Azure o in un ruolo di lavoro ibrido per runbook Windows. 

|Nome|Descrizione|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Si noti che i cmdlet interni differiscono per denominazione dai cmdlet Az e AzureRM. I nomi dei cmdlet interni non contengono parole come `Azure` o `Az`, ma usano la parola `Automation`. È consigliabile usare i cmdlet interni rispetto ai cmdlet Az o AzureRM durante l'esecuzione di runbook in una sandbox di Azure o in un ruolo di lavoro ibrido per runbook Windows. Richiedono un minor numero di parametri e vengono eseguiti nel contesto del processo già in esecuzione.

Usare i cmdlet Az o AzureRM per modificare le risorse di Automazione al di fuori del contesto di un runbook. 

## <a name="python-modules"></a>Moduli Python

È possibile creare runbook Python 2 in Automazione di Azure. Per informazioni sui moduli Python, vedere [Gestire pacchetti Python 2 in Automazione di Azure](../python-packages.md).

## <a name="custom-modules"></a>Moduli personalizzati

Automazione di Azure supporta moduli di PowerShell personalizzati creati per l'uso con i runbook e le configurazioni DSC. Un tipo di modulo personalizzato è un modulo di integrazione che contiene facoltativamente un file di metadati per definire la funzionalità personalizzata per i cmdlet del modulo. Un esempio di uso di un modulo di integrazione è disponibile in [Aggiungere un tipo di connessione](../automation-connections.md#add-a-connection-type).

Automazione di Azure può importare un modulo personalizzato per rendere disponibili i relativi cmdlet. Dietro le quinte, archivia il modulo e lo usa nelle sandbox di Azure, proprio come gli altri moduli.

## <a name="migrate-to-az-modules"></a>Eseguire la migrazione a moduli Az

Questa sezione illustra come eseguire la migrazione ai moduli Az in Automazione. Per altre informazioni, vedere [Eseguire la migrazione di Azure PowerShell da AzureRM ad Az](/powershell/azure/migrate-from-azurerm-to-az).

Si sconsiglia di eseguire i moduli AzureRM e i moduli Az nello stesso account di Automazione. Quando si è sicuri di voler eseguire la migrazione da AzureRM ad Az, è consigliabile effettuare una migrazione completa. Automazione riutilizza spesso sandbox all'interno dell'account di Automazione per ridurre i tempi di avvio. Se non si esegue la migrazione completa dei moduli, è possibile che si avvii un processo che usa solo moduli AzureRM e quindi un altro che usa solo moduli Az. La sandbox si arresta presto in modo anomalo e viene visualizzato un errore che informa che i moduli non sono compatibili. Questa situazione comporta arresti anomali casuali per un runbook o una configurazione particolare.

>[!NOTE]
>Anche dopo la migrazione ai moduli Az, quando si crea un nuovo account di Automazione vengono installati i moduli AzureRM per impostazione predefinita. È comunque possibile aggiornare i runbook dell'esercitazione con i cmdlet AzureRM. Non è tuttavia consigliabile eseguire questi runbook.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testare i runbook e le configurazioni DSC prima della migrazione dei moduli

Assicurarsi di testare attentamente tutti i runbook e le configurazioni DSC in un account di Automazione separato prima di eseguire la migrazione ai moduli Az. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Arrestare e annullare la pianificazione di tutti i runbook che usano moduli AzureRM

Per assicurarsi di non eseguire runbook o configurazioni DSC esistenti che usano moduli AzureRM, è necessario arrestare e annullare la pianificazione di ogni runbook e configurazione interessati. Assicurarsi prima di tutto di esaminare ogni runbook o configurazione DSC e le relative pianificazioni separatamente, per verificare che sia possibile ripianificare l'elemento in futuro, se necessario.

Quando si è pronti per rimuovere le pianificazioni, è possibile usare il portale di Azure o il cmdlet [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule). Vedere [Rimuovere una pianificazione](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Rimuovere i moduli AzureRM

È possibile rimuovere i moduli AzureRM prima di importare i moduli Az. Se tuttavia si esegue questa operazione, è possibile interrompere la sincronizzazione del controllo del codice sorgente e causare la mancata esecuzione di script ancora pianificati. Se si decide di rimuovere i moduli, vedere [Disinstallare AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Importare moduli Az

L'importazione di un modulo Az nell'account di Automazione non comporta automaticamente l'importazione del modulo nella sessione di PowerShell usata dai runbook. I moduli vengono importati nella sessione di PowerShell nelle situazioni seguenti:

* Quando un runbook richiama un cmdlet da un modulo.
* Quando un runbook importa il modulo in modo esplicito con il cmdlet [Import-Module](/powershell/module/microsoft.powershell.core/import-module).
* Quando un Runbook importa il modulo in modo esplicito con l'istruzione [using Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) . L'istruzione using è supportata a partire da Windows PowerShell 5,0 e supporta le classi e l'importazione di tipi enum.
* Quando un runbook importa un altro modulo dipendente.

È possibile importare i moduli Az nel portale di Azure. Ricordarsi di importare solo i moduli Az necessari, non l'intero modulo Az.Automation. [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) è una dipendenza per gli altri moduli Az, quindi assicurarsi di importare questo modulo prima di qualsiasi altro.

1. Dall'account di Automazione selezionare **Moduli** in **Risorse condivise**.
2. Selezionare **Esplora raccolta**.  
3. Nella barra di ricerca immettere il nome del modulo (ad esempio `Az.Accounts`).
4. Nella pagina Modulo PowerShell selezionare **Importa** per importare il modulo nell'account di Automazione.

    ![Screenshot dell'importazione di moduli nell'account di Automazione](../media/modules/import-module.png)

Questa operazione può anche essere eseguita tramite [PowerShell Gallery](https://www.powershellgallery.com), cercando il modulo da importare. Quando si trova il modulo, selezionarlo e scegliere la scheda **Automazione di Azure**. Selezionare **Distribuire in Automazione di Azure**.

![Screenshot dell'importazione di moduli direttamente da PowerShell Gallery](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testare i runbook

Dopo aver importato i moduli Az nell'account di Automazione, è possibile iniziare a modificare i runbook e le configurazioni DSC per l'uso dei nuovi moduli. Un modo per testare la modifica di un runbook per l'uso dei nuovi cmdlet consiste nell'usare il comando `Enable-AzureRmAlias -Scope Process` all'inizio del runbook. Aggiungendo questo comando al runbook, lo script può essere eseguito senza modifiche.

## <a name="author-modules"></a>Creare moduli

Si consiglia di seguire le considerazioni in questa sezione quando si crea un modulo PowerShell personalizzato da usare in Automazione di Azure. Per preparare il modulo per l'importazione, è necessario creare almeno un file con estensione **dll** del modulo. psd1,. Psm1 o PowerShell con lo stesso nome della cartella del modulo. Comprimere quindi la cartella del modulo in modo che Automazione di Azure possa importarlo come singolo file. Il pacchetto **ZIP** deve avere lo stesso nome della cartella del modulo contenuta.

Per altre informazioni sulla creazione di un modulo di PowerShell, vedere [Come scrivere un modulo di script di PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Cartella delle versioni

Il controllo delle versioni dei moduli affiancati di PowerShell consente di usare più di una versione di un modulo in PowerShell. Questa operazione può essere utile se sono presenti script meno recenti che sono stati testati e funzionano solo con una determinata versione di un modulo di PowerShell, ma per altri script è necessaria una versione più recente dello stesso modulo di PowerShell.

Per costruire moduli di PowerShell in modo che contengano più versioni, creare la cartella del modulo e quindi creare una cartella all'interno di questa cartella del modulo per ogni versione del modulo che si vuole usare. Nell'esempio seguente, un modulo denominato *TestModule* offre due versioni, 1.0.0 e 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

All'interno di ciascuna cartella della versione, copiare i file con estensione **dll** di PowerShell. psm1,. Psd1 o PowerShell module che costituiscono un modulo nella rispettiva cartella della versione. Comprimere la cartella del modulo in modo che automazione di Azure possa importarlo come singolo file con estensione zip. Mentre l'automazione Mostra solo la versione più recente del modulo importato, se il pacchetto del modulo contiene versioni side-by-side del modulo, sono tutti disponibili per l'uso nelle configurazioni manuali operativi o DSC.  

Sebbene l'automazione supporti i moduli contenenti versioni affiancate all'interno dello stesso pacchetto, non supporta l'uso di più versioni di un modulo tra le importazioni dei pacchetti di moduli. Ad esempio, si importa il **modulo A**, che contiene le versioni 1 e 2 nell'account di automazione. Successivamente si aggiornerà il **modulo** a in modo da includere le versioni 3 e 4, quando si importano nell'account di automazione, solo le versioni 3 e 4 sono utilizzabili all'interno di qualsiasi configurazione manuali operativi o DSC. Se è necessario che siano disponibili tutte le versioni-1, 2, 3 e 4, il file zip deve contenere le versioni 1, 2, 3 e 4.

Se si usano versioni diverse dello stesso modulo tra manuali operativi, è sempre necessario dichiarare la versione che si vuole usare nel runbook usando il `Import-Module` cmdlet e includere il parametro `-RequiredVersion <version>` . Anche se la versione che si vuole usare è la versione più recente. Ciò è dovuto al fatto che i processi Runbook possono essere eseguiti nella stessa sandbox. Se il sandbox ha già caricato in modo esplicito un modulo di un determinato numero di versione, perché un processo precedente in tale sandbox ha detto di eseguire questa operazione, i processi futuri in tale sandbox non caricherà automaticamente la versione più recente del modulo. Questo è dovuto al fatto che una versione di questa è già caricata in sandbox.

Per una risorsa DSC, usare il comando seguente per specificare una determinata versione:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Informazioni della Guida

Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire le informazioni della Guida per i cmdlet usando il cmdlet `Get-Help`. L'esempio seguente illustra come definire un riepilogo e un URI della Guida in un file di modulo con estensione **psm1**.

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Fornendo queste informazioni viene visualizzato il testo della Guida tramite il cmdlet `Get-Help` nella console di PowerShell. Questo testo viene visualizzato anche nel portale di Azure.

  ![Screenshot della Guida del modulo di integrazione](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo di connessione

Se il modulo si connette a un servizio esterno, definire un tipo di connessione usando un [modulo di integrazione personalizzato](#custom-modules). Ogni cmdlet del modulo deve accettare un'istanza del tipo di connessione (oggetto di connessione) come parametro. Gli utenti eseguono il mapping dei parametri dell'asset della connessione ai parametri del cmdlet corrispondenti ogni volta che chiamano un cmdlet.

![Usare una connessione personalizzata nel portale di Azure](../media/modules/connection-create-new.png)

Nell'esempio di runbook seguente si usa un asset di connessione Contoso denominato `ContosoConnection` per accedere alle risorse di Contoso e restituire i dati dal servizio esterno. In questo esempio, i campi vengono mappati alle proprietà `UserName` e `Password` di un oggetto `PSCredential` e quindi passati al cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Un approccio più semplice ed efficiente a questo comportamento consiste nel passare direttamente l'oggetto connessione al cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

È possibile abilitare un comportamento simile per i cmdlet consentendo loro di accettare direttamente un oggetto di connessione come parametro, anziché soltanto i campi di connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto di connessione. Il set di parametri `UserAccount` viene usato per passare le proprietà dei campi di connessione. `ConnectionObject` consente di passare direttamente la connessione.

### <a name="output-type"></a>Tipo di output

Definire il tipo di output per tutti i cmdlet nel modulo. Definendo un tipo di output per un cmdlet, IntelliSense in fase di progettazione consente di determinare le proprietà di output del cmdlet durante la creazione. Questa prassi è particolarmente utile durante la creazione di runbook grafici, in cui la conoscenza in fase di progettazione è essenziale per semplificare l'esperienza dell'utente con il modulo.

Aggiungere `[OutputType([<MyOutputType>])]`, dove `MyOutputType` è un tipo valido. Per altre informazioni su `OutputType`, vedere [Informazioni sulle funzioni OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Il codice seguente è un esempio di aggiunta di `OutputType` a un cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Screenshot del tipo di output del runbook grafico](../media/modules/runbook-graphical-module-output-type.png)

  Questo comportamento è simile alla funzionalità di "completamento automatico" dell'output di un cmdlet nell'ambiente del servizio di integrazione PowerShell, senza che sia necessario eseguirlo.

  ![Screenshot di POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stato del cmdlet

Assicurarsi che tutti i cmdlet del modulo siano senza stato. Più processi Runbook possono essere eseguiti contemporaneamente nello stesso `AppDomain`, nello stesso processo e nella stessa sandbox. Se è presente uno stato condiviso in questi livelli, i processi possono interferire tra loro. Questo comportamento può causare problemi intermittenti e difficili da diagnosticare. Di seguito è riportato un esempio della soluzione da non adottare:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Dipendenza del modulo

Verificare che il modulo sia completamente contenuto in un pacchetto che può essere copiato tramite xcopy. I moduli di Automazione vengono distribuiti nelle sandbox di Automazione quando vengono eseguiti i runbook. I moduli devono funzionare indipendentemente dall'host che li esegue.

Dovrebbe essere possibile comprimere un pacchetto del modulo e spostarlo, facendo in modo che funzioni normalmente dopo l'importazione nell'ambiente PowerShell di un altro host. A questo scopo, verificare che il modulo non dipenda da file esterni alla cartella del modulo compresso quando il modulo viene importato in Automazione.

Il modulo non deve dipendere da impostazioni univoche del Registro di sistema in un host. Esempi sono le impostazioni che vengono eseguite quando si installa un prodotto.

### <a name="module-file-paths"></a>Percorsi dei file del modulo

Verificare che tutti i file del modulo abbiano percorsi con meno di 140 caratteri. Qualsiasi percorso con lunghezza superiore a 140 caratteri provoca problemi di importazione dei runbook. Automazione non può importare nella sessione di PowerShell un file con una dimensione del percorso superiore a 140 caratteri con `Import-Module`.

## <a name="import-modules"></a>Importare i moduli

Questa sezione definisce diversi modi in cui è possibile importare un modulo nell'account di Automazione.

### <a name="import-modules-in-the-azure-portal"></a>Importare i moduli nel portale di Azure

Per importare un modulo nel portale di Azure:

1. Passare all'account di Automazione.
2. In **Risorse condivise** selezionare **Moduli**.
3. Selezionare **Aggiungere un modulo**.
4. Selezionare il file **ZIP** che contiene il modulo.
5. Selezionare **OK** per iniziare a importare il processo.

### <a name="import-modules-by-using-powershell"></a>Importare i moduli usando PowerShell

È possibile usare il cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) per importare un modulo nell'account di Automazione. Il cmdlet accetta un URL per un pacchetto ZIP del modulo.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

È anche possibile usare lo stesso cmdlet per importare un modulo direttamente da PowerShell Gallery. Assicurarsi di selezionare `ModuleName` e `ModuleVersion` da [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importare moduli da PowerShell Gallery

È possibile importare moduli di [PowerShell Gallery](https://www.powershellgallery.com) direttamente dalla Gallery o dal proprio account di Automazione.

Per importare un modulo direttamente da PowerShell Gallery:

1. Passare a https://www.powershellgallery.com e cercare il modulo da importare.
2. Selezionare **Distribuisci in Automazione di Azure** nella scheda **Automazione di Azure** in **Opzioni di installazione**. Verrà aperto il portale di Azure. 
3. Nella pagina Importa selezionare l'account di Automazione e selezionare **OK**.

![Screenshot di importazione del modulo di PowerShell Gallery](../media/modules/powershell-gallery.png)

Per importare un modulo di PowerShell Gallery direttamente dall'account di Automazione:

1. In **Risorse condivise** selezionare **Moduli**. 
2. Selezionare **Esplora raccolta**, quindi cercare un modulo nella raccolta. 
3. Selezionare il modulo da importare e selezionare **Importa**. 
4. Selezionare **OK** per avviare il processo di importazione.

![Screenshot dell'importazione di un modulo di PowerShell Gallery dal portale di Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Eliminare moduli

Se si verificano problemi con un modulo oppure è necessario eseguire il rollback a una versione precedente di un modulo, è possibile eliminare il modulo dall'account di Automazione. Non è possibile eliminare le versioni originali dei [moduli predefiniti](#default-modules) importati quando si crea un account di Automazione. Se il modulo da eliminare è una versione più recente di uno dei [moduli predefiniti](#default-modules), viene eseguito il rollback alla versione installata con l'account di Automazione. In caso contrario viene rimosso qualsiasi modulo eliminato dall'account di Automazione.

### <a name="delete-modules-in-the-azure-portal"></a>Eliminare i moduli nel portale di Azure

Per rimuovere un modulo nel portale di Azure:

1. Passare all'account di Automazione. In **Risorse condivise** selezionare **Moduli**.
2. Selezionare il modulo che si vuole rimuovere.
3. Nella pagina modulo selezionare **Elimina**. Se questo modulo è uno dei [moduli predefiniti](#default-modules), viene eseguito il rollback alla versione esistente al momento della creazione dell'account di Automazione.

### <a name="delete-modules-by-using-powershell"></a>Eliminare i moduli usando PowerShell

Per rimuovere un modulo tramite PowerShell, eseguire questo comando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sull'utilizzo dei moduli di Azure PowerShell, vedere [Introduzione a Azure PowerShell](/powershell/azure/get-started-azureps).

* Per altre informazioni sulla creazione di moduli di PowerShell, vedere [scrittura di un modulo di Windows PowerShell](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
