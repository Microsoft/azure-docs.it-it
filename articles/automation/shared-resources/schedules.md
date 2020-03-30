---
title: Pianificazioni in Automazione di Azure
description: Le pianificazioni di Automazione di Azure vengono usate per pianificare l'esecuzione automatica dei runbook in Automazione di Azure. Illustra come creare e gestire una pianificazione in modo da poter avviare automaticamente un runbook a una determinata ora o in base a una pianificazione ricorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252662"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Pianificazione di un runbook in Automazione di Azure

Per pianificare un runbook in Automazione di Azure per l'avvio a un'ora specifica, è necessario collegarlo a una o più pianificazioni. È possibile configurare una pianificazione in modo che i runbook vengano eseguiti una sola volta oppure con cadenza oraria o giornaliera nel portale di Azure. È inoltre possibile pianificare un'esecuzione settimanale, mensile o in giorni specifici della settimana o del mese oppure in un determinato giorno del mese. Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook.

> [!NOTE]
> Le pianificazioni attualmente non supportano le configurazioni DSC di automazione di Azure.

## <a name="powershell-cmdlets"></a>Cmdlet di PowerShell

The cmdlets in the following table are used to create and manage schedules with PowerShell in Azure Automation. Vengono forniti nel [modulo Azure PowerShell](/powershell/azure/overview).

| Cmdlet | Descrizione |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Recupera una pianificazione. |
| [Pianificazione dell'automazione di New AzureRmAutomation](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Crea una nuova pianificazione. |
| [Rimozione-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Rimuove una pianificazione. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Imposta le proprietà di una pianificazione esistente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Recupera i runbook pianificati. |
| [Registrare AzureRmAutomationScheduledRunbookRegisterRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa un runbook a una pianificazione. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Annulla l'associazione di un runbook a una pianificazione. |

## <a name="creating-a-schedule"></a>Creazione di una pianificazione

È possibile creare una nuova pianificazione per i runbook nel portale di Azure o con PowerShell.You can create a new schedule for runbooks in the Azure portal or with PowerShell.

> [!NOTE]
> Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione.  Per evitare conseguenze per i runbook e i processi che vengono automatizzati, è necessario innanzitutto testare tutti i runbook che dispongono di pianificazioni collegate con un account di automazione dedicato ai test.  In questo modo vengono convalidati i runbook pianificati, che continuano a funzionare correttamente; in caso contrario è possibile risolvere i problemi e applicare eventuali modifiche necessarie prima della migrazione della versione aggiornata del runbook all'ambiente di produzione.
> L'account di automazione non riceve automaticamente nuove versioni dei moduli a meno che tali versioni non siano state aggiornate manualmente selezionando l'opzione [Aggiorna moduli di Azure](../automation-update-azure-modules.md) nel riquadro **Moduli**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Per creare una nuova pianificazione nel portale di Azure

1. Nel portale di Azure selezionare **Pianificazioni** nella sezione **Risorse condivise** a sinistra.
2. Fare clic su **Aggiungi pianificazione** nella parte superiore della pagina.
3. Nel riquadro **Nuova pianificazione** digitare un **Nome** e facoltativamente una **Descrizione** per la nuova pianificazione.
4. Specificare se la pianificazione verrà eseguita una volta o in modo ricorrente, selezionando **Una sola volta** o **Ricorrente**. Se si seleziona **Una sola volta** specificare un'**Ora di inizio** e quindi fare clic su **Crea**. Se si seleziona **Ricorrente**, specificare un valore per **Ora di inizio** ed **Esegui ogni**, selezionare la frequenza di ripetizione del runbook, ad esempio **Ora**, **Giorno**, **Settimana** o **Mese**.
    1. Se si seleziona **settimana,** viene fornito un elenco dei giorni della settimana tra cui scegliere. Selezionare tutti i giorni necessari. La prima esecuzione della pianificazione avverrà il primo giorno selezionato all'ora di inizio. Ad esempio, per scegliere una pianificazione del fine settimana, scegliere **Sabato** e **Domenica**.

       ![Impostazione della pianificazione ricorrente del fine settimana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se si seleziona **mese**, vengono fornite diverse opzioni. Per l'opzione **Occorrenze mensili,** selezionare **Giorni mese** o **Giorni settimana**. Se si sceglie **Mese giorni**, viene visualizzato un calendario che consente di scegliere tutti i giorni desiderati. Se si sceglie una data, ad esempio la 31a che non si verifica nel mese corrente, la pianificazione non verrà eseguita. Se si vuole che la pianificazione venga eseguita l'ultimo giorno, scegliere **Sì** sotto **Esegui nell'ultimo giorno del mese**. Se si sceglie **Giorni della settimana**, viene visualizzata l'opzione **Esegui ogni**. Selezionare **Primo**, **Secondo**, **Terzo**, **Quarto** o **Ultimo**. Scegliere il giorno della ripetizione.

       ![Programma mensile il primo, il quindicesimo e l'ultimo giorno del mese](../media/schedules/monthly-first-fifteenth-last.png)

5. Al termine, fare clic su **Crea**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Per creare una nuova pianificazione con PowerShellTo create a new schedule with PowerShell

Usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) per creare le pianificazioni. Specificare l'ora di inizio per la pianificazione e la frequenza di esecuzione. Negli esempi seguenti viene illustrato come creare molti scenari di pianificazione diversi.

#### <a name="create-a-one-time-schedule"></a>Creare una pianificazione una tantera

I comandi di esempio seguenti creano una pianificazione una tantera.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Creare una pianificazione ricorrente

I comandi di esempio seguenti illustrano come creare una pianificazione ricorrente che viene eseguita ogni giorno alle 13:00 per un anno.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Creare una pianificazione ricorrente settimanale

I comandi di esempio seguenti illustrano come creare una pianificazione settimanale che viene eseguita solo nei giorni feriali.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Creare una pianificazione ricorrente settimanale per i fine settimana

I comandi di esempio seguenti illustrano come creare una pianificazione settimanale che viene eseguita solo nei fine settimana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Creare una pianificazione ricorrente per il primo, il quindicesimo e l'ultimo giorno del mese

I comandi di esempio seguenti illustrano come creare una pianificazione ricorrente che viene eseguita il primo, il quindicesimo e l'ultimo giorno del mese.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Collegamento di una pianificazione a un runbook

Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook. Se un runbook dispone di parametri, è possibile fornire valori per tali parametri. È necessario specificare i valori per tutti i parametri obbligatori, mentre è possibile scegliere se specificare o meno i valori per i parametri facoltativi. Questi valori vengono usati ogni volta che il runbook viene avviato dalla pianificazione. È possibile collegare lo stesso runbook a un'altra pianificazione e specificare valori diversi per i parametri.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Per collegare una pianificazione a un runbook con il portale di Azure

1. Dall'account di automazione nel portale di Azure, selezionare **Runbook** nella sezione **Automazione processi** a sinistra.
2. Fare clic sul nome del runbook da pianificare.
3. Se il runbook non è attualmente collegato a una pianificazione, ti viene offerta la possibilità di creare una nuova pianificazione o un nuovo collegamento a una pianificazione esistente.
4. Se il runbook include parametri, è possibile selezionare l'opzione **Modifica impostazioni esecuzione (Default:Azure)** e viene visualizzato il riquadro **Parametri** in cui è possibile immettere le informazioni.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>To link a schedule to a runbook with PowerShell

È possibile usare il cmdlet [Register AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) per collegare una pianificazione. È possibile specificare i valori per i parametri del runbook con il parametro Parameters. Per altre informazioni su come specificare i valori dei parametri, vedere [Avvio di un runbook in Automazione di Azure](../automation-starting-a-runbook.md).
I comandi di esempio seguenti mostrano come collegare una pianificazione a un runbook usando un cmdlet di Azure Resource Manager con parametri.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Pianificazione più frequente dei runbook

L'intervallo più frequente per il quale è possibile configurare una pianificazione in Automazione di Azure è un'ora. Se sono necessarie pianificazioni da eseguire più frequentemente rispetto a questa, sono disponibili due opzioni:

* Creare un [webhook](../automation-webhooks.md) per il runbook e usare [app per](../../logic-apps/logic-apps-overview.md) la logica di Azure per chiamare il webhook. App per la logica di Azure offre una granularità più dettagliata quando si definisce una pianificazione.

* Creare quattro pianificazioni, tutte in avvio entro 15 minuti una dall'altra, in esecuzione una volta ogni ora. Questo scenario consente l'esecuzione del runbook ogni 15 minuti con pianificazioni diverse.

## <a name="disabling-a-schedule"></a>Disabilitazione di una pianificazione

Quando si disabilita una pianificazione, i runbook a essa collegati non vengono più eseguiti in base a tale pianificazione. È possibile disabilitare manualmente una pianificazione o impostare un'ora di scadenza per le pianificazioni con una frequenza durante la fase di creazione. Una volta raggiunta l'ora di scadenza, la pianificazione viene disabilitata.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Per disabilitare una pianificazione dal portale di Azure

1. Dall'account di automazione nel portale di Azure, selezionare **Pianificazioni** nella sezione **Risorse condivise** a sinistra.
2. Fare clic sul nome di una pianificazione per aprire il rispettivo riquadro dei dettagli.
3. Impostare **Abilitata** su **No**.

> [!NOTE]
> Se si desidera disabilitare una pianificazione con un'ora di inizio in passato, è necessario modificare la data di inizio in un'ora futura prima di salvarla.

### <a name="to-disable-a-schedule-with-powershell"></a>Per disabilitare una pianificazione con PowerShellTo disable a schedule with PowerShell

È possibile usare il cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) per modificare le proprietà di una pianificazione esistente. Per disabilitare la pianificazione, specificare **false** per il parametro **IsEnabled**.

I comandi di esempio seguenti mostrano come disabilitare una pianificazione per un runbook usando un cmdlet di Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure](../automation-starting-a-runbook.md)

