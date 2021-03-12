---
title: Gestire le pianificazioni in Automazione di Azure
description: Questo articolo descrive come creare e usare una pianificazione in Automazione di Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: f40e3d555d6e1472b9d2368a114ee27d588f6383
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149478"
---
# <a name="manage-schedules-in-azure-automation"></a>Gestire le pianificazioni in Automazione di Azure

Per pianificare un runbook in Automazione di Azure per l'avvio a un'ora specifica, è necessario collegarlo a una o più pianificazioni. È possibile configurare una pianificazione in modo che i runbook vengano eseguiti una sola volta oppure con cadenza oraria o giornaliera nel portale di Azure. È inoltre possibile pianificare un'esecuzione settimanale, mensile o in giorni specifici della settimana o del mese oppure in un determinato giorno del mese. Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook.

> [!NOTE]
> Automazione di Azure supporta l'ora legale e ne tiene conto per pianificare in modo appropriato le operazioni di automazione.

> [!NOTE]
> Le pianificazioni non sono attualmente abilitate per le configurazioni di Azure Automation DSC.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Cmdlet di PowerShell usati per accedere alle pianificazioni

I cmdlet nella tabella seguente consentono di creare e gestire le pianificazioni di Automazione con PowerShell. Sono inclusi nei [moduli Az](modules.md#az-modules).

| Cmdlet | Descrizione |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Recupera una pianificazione. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Recupera i runbook pianificati. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Crea una nuova pianificazione. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Associa un runbook a una pianificazione. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Rimuove una pianificazione. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Imposta le proprietà di una pianificazione esistente. |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Annulla l'associazione di un runbook a una pianificazione. |

## <a name="create-a-schedule"></a>Creare una pianificazione

È possibile creare una nuova pianificazione per i runbook nel portale di Azure o con PowerShell. Per evitare conseguenze per i runbook e i processi che vengono automatizzati, è consigliabile prima di tutto testare tutti i runbook che includono pianificazioni collegate con un account di Automazione dedicato ai test. Un test convalida che i runbook pianificati continuino a funzionare correttamente. Se si verifica un problema, è possibile risolverlo e applicare eventuali modifiche necessarie prima di eseguire la migrazione della versione aggiornata del runbook in produzione.

> [!NOTE]
> L'account di Automazione non riceve automaticamente nuove versioni dei moduli a meno che tali versioni non siano state aggiornate manualmente selezionando l'opzione [Aggiorna moduli di Azure](../automation-update-azure-modules.md) in **Moduli**. Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Creare una nuova pianificazione nel portale di Azure

1. Nell'account di automazione, nel riquadro a sinistra, selezionare **pianificazioni** in **risorse condivise**.
2. Nella pagina **pianificazioni** selezionare **Aggiungi pianificazione**.
3. Nella pagina **nuova pianificazione** immettere un nome e, facoltativamente, immettere una descrizione per la nuova pianificazione.

    >[!NOTE]
    >Le pianificazioni di automazione attualmente non supportano l'uso di caratteri speciali nel nome della pianificazione.
    >

4. Consente di indicare se la pianificazione viene eseguita una volta o in base a una pianificazione ricorrente selezionando **una sola volta** o **ricorrendo**. Se si seleziona **Una sola volta** specificare un'ora di inizio e quindi selezionare **Crea**. Se si seleziona **Ricorrente** specificare un'ora di inizio. Per **Ricorre ogni** selezionare la frequenza con cui si vuole ripetere l'esecuzione del runbook. Selezionare ora, giorno, settimana o mese.

    * Se si seleziona **Settimana** verranno visualizzati i giorni della settimana tra cui scegliere. Selezionare tutti i giorni necessari. La prima esecuzione della pianificazione avverrà il primo giorno selezionato all'ora di inizio. Ad esempio, per scegliere una pianificazione nel weekend, selezionare sabato e domenica.

    ![Impostazione di una pianificazione ricorrente nel weekend](../media/schedules/week-end-weekly-recurrence.png)

    * Se si seleziona **Mese** sono disponibili opzioni diverse. In **Occorrenze mensili** selezionare **Giorni del mese** oppure **Giorni della settimana**. Se si seleziona **Giorni del mese** viene visualizzato un calendario in cui è possibile scegliere il numero di giorni desiderato. Se si sceglie una data, ad esempio il 31, che non è contemplata nel mese corrente, la pianificazione non verrà eseguita. Se si vuole che la pianificazione venga eseguita l'ultimo giorno, selezionare **Sì** in **Esegui nell'ultimo giorno del mese**. Se si seleziona **Giorni della settimana** viene visualizzata l'opzione **Ricorre ogni**. Selezionare **Primo**, **Secondo**, **Terzo**, **Quarto** o **Ultimo**. Scegliere infine il giorno della ripetizione.

    ![Pianificazione mensile per il primo, il quindicesimo e l'ultimo giorno del mese](../media/schedules/monthly-first-fifteenth-last.png)

5. Al termine, selezionare **Crea**.

### <a name="create-a-new-schedule-with-powershell"></a>Creare una nuova pianificazione con PowerShell

Usare il cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) per creare le pianificazioni. Specificare l'ora di inizio per la pianificazione e la frequenza di esecuzione. Negli esempi seguenti viene illustrato come creare diversi scenari di pianificazione.

>[!NOTE]
>Le pianificazioni di automazione attualmente non supportano l'uso di caratteri speciali nel nome della pianificazione.
>

#### <a name="create-a-one-time-schedule"></a>Creare una pianificazione occasionale

Nell'esempio seguente viene creata una pianificazione occasionale.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Creare una pianificazione ricorrente

Nell'esempio seguente viene illustrato come creare una pianificazione ricorrente che viene eseguita ogni giorno alle 13:00 per un anno.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Creare una pianificazione ricorrente settimanale

Nell'esempio seguente viene illustrato come creare una pianificazione settimanale eseguita solo nei giorni feriali.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Crea una pianificazione ricorrente settimanale per i fine settimana

Nell'esempio seguente viene illustrato come creare una pianificazione settimanale eseguita solo nei fine settimana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Creare una pianificazione ricorrente per il primo, il quindicesimo e l'ultimo giorno del mese

Nell'esempio seguente viene illustrato come creare una pianificazione ricorrente eseguita il primo, il quindicesimo e l'ultimo giorno del mese.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Creare una pianificazione con un modello di Gestione risorse

In questo esempio viene usato un modello di Gestione risorse di automazione (ARM) che crea una nuova pianificazione del processo. Per informazioni generali su questo modello per gestire le pianificazioni dei processi di automazione, vedere [Microsoft. Automation automationAccounts/jobSchedules template Reference](/templates/microsoft.automation/automationaccounts/jobschedules#quickstart-templates).

Copiare il file modello in un editor di testo:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Modificare i valori dei parametri seguenti e salvare il modello come file JSON:

* Nome oggetto pianificazione processo: viene usato un GUID (identificatore univoco globale) come nome dell'oggetto pianificazione processo.

   >[!IMPORTANT]
   > Per ogni pianificazione del processo distribuita con un modello ARM, il GUID deve essere univoco. Anche se si sta ripianificando una pianificazione esistente, sarà necessario modificare il GUID. Questo vale anche se in precedenza è stata eliminata una pianificazione di processo esistente creata con lo stesso modello. Il riutilizzo dello stesso GUID comporta una distribuzione non riuscita.</br></br>
   > Sono disponibili servizi online che possono generare un nuovo GUID, ad esempio il generatore di [GUID online gratuito](https://guidgenerator.com/).

* Nome pianificazione: rappresenta il nome della pianificazione del processo di automazione che verrà collegata al Runbook specificato.
* Runbook Name: rappresenta il nome del Runbook di automazione a cui deve essere associata la pianificazione del processo.

Una volta salvato il file, è possibile creare la pianificazione del processo runbook con il comando di PowerShell seguente. Il comando usa il `TemplateFile` parametro per specificare il percorso e il nome file del modello.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Collegare una pianificazione a un runbook

Un runbook può essere collegato a più pianificazioni e a una pianificazione possono essere collegati più runbook. Se un runbook prevede parametri è possibile fornire valori per tali parametri. È necessario specificare i valori per tutti i parametri obbligatori, mentre è possibile scegliere se specificare o meno i valori per i parametri facoltativi. Questi valori vengono usati ogni volta che il runbook viene avviato dalla pianificazione. È possibile collegare lo stesso runbook a un'altra pianificazione e specificare valori diversi per i parametri.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Collegare una pianificazione a un runbook con il portale di Azure

1. Dall'account di Automazione nel portale di Azure selezionare **Runbook** in **Automazione processi**.
1. Selezionare il nome del runbook da pianificare.
1. Se il runbook non è attualmente collegato a una pianificazione, è possibile creare una nuova pianificazione o collegarsi a una pianificazione esistente.
1. Se il runbook include parametri, è possibile selezionare l'opzione **Modifica le impostazioni di esecuzione (impostazione predefinita: Azure)** . Viene visualizzato il riquadro **Parametri** in cui è possibile immettere le informazioni sui parametri.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Collegare una pianificazione a un runbook con PowerShell

È possibile usare il cmdlet [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) per collegare una pianificazione. È possibile specificare i valori per i parametri del runbook con il parametro Parameters. Per altre informazioni su come specificare i valori dei parametri, vedere [Avvio di un runbook in Automazione di Azure](../start-runbooks.md).
L'esempio seguente illustra come collegare una pianificazione a un runbook usando un cmdlet di Azure Resource Manager con parametri.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Pianificare l'esecuzione più frequente dei runbook

L'intervallo più frequente per il quale è possibile configurare una pianificazione in Automazione di Azure è un'ora. Se sono necessarie pianificazioni da eseguire più frequentemente rispetto a questa, sono disponibili due opzioni:

* Creare un [webhook](../automation-webhooks.md) per il runbook e usare [App per la logica di Azure](../../logic-apps/logic-apps-overview.md) per chiamare il webhook. App per la logica di Azure offre una granularità più dettagliata per definire una pianificazione.

* Creare quattro pianificazioni che iniziano tutte entro 15 minuti l'una dall'altra e vengono eseguite una volta ogni ora. Questo scenario consente l'esecuzione del runbook ogni 15 minuti con pianificazioni diverse.

## <a name="disable-a-schedule"></a>Disabilitare una pianificazione

Quando si disabilita una pianificazione, i runbook a essa collegati non vengono più eseguiti in base a tale pianificazione. È possibile disabilitare manualmente una pianificazione o impostare un'ora di scadenza per le pianificazioni con una frequenza durante la fase di creazione. Quando viene raggiunta l'ora di scadenza, la pianificazione viene disabilitata.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Disabilitare una pianificazione dal portale di Azure

1. Nel riquadro sinistro dell'account di automazione selezionare **pianificazioni** in **risorse condivise**.
1. Selezionare il nome di una pianificazione per aprire il rispettivo riquadro dei dettagli.
1. Impostare **Abilitata** su **No**.

> [!NOTE]
> Se si vuole disabilitare una pianificazione con un'ora di inizio nel passato, è necessario modificare la data di inizio impostando un'ora nel futuro prima di salvarla.

### <a name="disable-a-schedule-with-powershell"></a>Disabilitare una pianificazione con PowerShell

È possibile usare il cmdlet [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) per modificare le proprietà di una pianificazione esistente. Per disabilitare la pianificazione, specificare false per il parametro `IsEnabled`.

L'esempio seguente illustra come disabilitare una pianificazione per un runbook usando un cmdlet di Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Rimuovere una pianificazione

Quando si è pronti per rimuovere le pianificazioni, è possibile usare il portale di Azure o PowerShell. Tenere presente che è possibile rimuovere solo una pianificazione che è stata disabilitata, come descritto nella sezione precedente.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Rimuovere una pianificazione usando il portale di Azure

1. Nel riquadro sinistro dell'account di automazione selezionare **pianificazioni** in **risorse condivise**.
2. Selezionare il nome di una pianificazione per aprire il rispettivo riquadro dei dettagli.
3. Fare clic su **Elimina**.

### <a name="remove-a-schedule-with-powershell"></a>Rimuovere una pianificazione con PowerShell

È possibile usare il cmdlet `Remove-AzAutomationSchedule` come illustrato di seguito per eliminare una pianificazione esistente.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui cmdlet usati per accedere alle pianificazioni, vedere [Gestire i moduli in Automazione di Azure](modules.md).
* Per informazioni generali sui runbook, vedere [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md).