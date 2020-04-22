---
title: Guida alla procedure per l'ottimizzazione automatica delle notifiche e-mail
description: Abilitare le notifiche tramite posta elettronica per l'ottimizzazione automatica delle query del database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b3b235833e794e48ae655d184bf938effc0d7ac0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768387"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notifiche tramite posta elettronica per l'ottimizzazione automatica

I suggerimenti di ottimizzazione per i database SQL vengono generati dall'opzione [Ottimizzazione automatica](sql-database-automatic-tuning.md) per database SQL di Azure. Questa soluzione monitorizza e analizza costantemente i carichi di lavoro di database SQL e offre suggerimenti di ottimizzazione personalizzati per ogni database inerenti, ad esempio, alla creazione dell'indice, all'eliminazione dell'indice e all'ottimizzazione dei piani di esecuzione delle query.

I suggerimenti di ottimizzazione automatica per database SQL possono essere visualizzati nel [portale di Azure](sql-database-advisor-portal.md), recuperati con chiamate all'[API REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) oppure usando comandi di [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [ PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction). Questo articolo si basa sull'utilizzo di uno script di PowerShell per recuperare suggerimenti di ottimizzazione automatica.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizzare le notifiche tramite posta elettronica per i suggerimenti di ottimizzazione automatica

La soluzione seguente consente di automatizzare l'invio di notifiche tramite posta elettronica contenenti suggerimenti di ottimizzazione automatica. La soluzione descritta consiste nell'esecuzione automatica di uno script di PowerShell per recuperare i suggerimenti di ottimizzazione usando [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) e automatizzare la pianificazione di un processo di recapito di posta elettronica usando [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Creare un account di Automazione di Azure

Per usare Automazione di Azure, il primo passaggio consiste nel creare un account di automazione e configurarlo con le risorse di Azure da usare per l'esecuzione dello script di PowerShell. Per altre informazioni su Automazione di Azure e le relative funzionalità, vedere [Introduzione ad Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Seguire questa procedura per creare un account di Automazione di Azure tramite il metodo di selezione e configurazione dell'app Automazione dal Marketplace:

1. Accedere al portale di Azure.
1. Fare clic su "**- Crea una risorsa**" nell'angolo in alto a sinistra.
1. Cercare "**Automazione**" (premere INVIO).
1. Fai clic sull'app Automazione nei risultati della ricerca.

    ![Aggiunta di Automazione di Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

1. Una volta all'interno del riquadro "Crea un account di automazione", fare clic su **"Crea".**
1. Popolare le informazioni necessarie: immettere un nome per questo account di automazione, selezionare l'ID sottoscrizione di Azure e le risorse di Azure da usare per l'esecuzione dello script di PowerShell.Populate the required information: enter a name for this automation account, select your Azure subscription ID and Azure resources to be used for the PowerShell script execution.
1. Per l'opzione " Crea account RunAs di Azure ", selezionare Sì per configurare il tipo di account con cui viene eseguito lo script di PowerShell con l'aiuto di Automazione di Azure.For the "**Create Azure RunAs account**", select **Yes** to configure the type of account with which PowerShell script runs with the help of Azure Automation. Per ulteriori informazioni sui tipi di account, vedere [Account RunAs](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
1. Concludere la creazione dell'account di automazione facendo clic su **Crea**.

> [!TIP]
> Annotare il nome dell'account di Automazione di Azure, l'ID della sottoscrizione e le risorse (ad esempio, copiandoli e incollandoli in un blocco note), esattamente come sono stati immessi durante la creazione dell'app Automazione. Queste informazioni saranno necessarie più avanti.
>

Se si hanno più sottoscrizioni di Azure per le quali si vuole creare la stessa automazione, è necessario ripetere questo processo anche per le altre sottoscrizioni.

## <a name="update-azure-automation-modules"></a>Aggiornare i moduli di Automazione di Azure

Lo script di PowerShell per recuperare la raccomandazione di ottimizzazione automatica usa i comandi [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) per i quali è necessario Azure Module versione 4 e successive.

- Nel caso in cui sia necessario aggiornare i moduli di Azure, vedere Supporto dei [moduli Az in Automazione](../automation/shared-resources/modules.md)di Azure.In case your Azure Modules need updating, see Az module support in Azure Automation .

## <a name="create-azure-automation-runbook"></a>Creare un runbook di Automazione di Azure

Il passaggio successivo consiste nella creazione di un runbook in Automazione di Azure all'interno del quale si trova lo script di PowerShell per il recupero dei suggerimenti di ottimizzazione.

Per creare un nuovo runbook di Automazione di Azure, seguire questa procedura:

1. Accedere all'account di Automazione di Azure creato nel passaggio precedente.
1. Una volta nel riquadro dell'account di automazione, fare clic sulla voce di menu "**Runbooks**" sul lato sinistro per creare un nuovo runbook di Automazione di Azure con lo script di PowerShell. Per altre informazioni sulla creazione di runbook di automazione, vedere [Creazione di un nuovo runbook](../automation/manage-runbooks.md#creating-a-runbook).
1. Per aggiungere un nuovo runbook, fare clic sull'opzione di menu "**-Aggiungi un runbook**" e quindi fare clic su "**Creazione rapida – Crea un nuovo runbook**"..
1. Nel riquadro Runbook digitare il nome del runbook (ai fini di questo esempio, viene utilizzato "**AutomaticTuningEmailAutomation**"), selezionare il tipo di runbook come **PowerShell** e scrivere una descrizione di questo runbook per descriverne lo scopo.
1. Fare clic sul pulsante **Crea** per completare la creazione di un nuovo runbook.

    ![Aggiungere un runbook di Automazione di Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Seguire questa procedura per caricare uno script di PowerShell nel runbook creato:

1. All'interno del riquadro "**Edit PowerShell Runbook**", selezionare "**RUNBOOKS**" nella struttura dei menu ed espandere la visualizzazione fino a visualizzare il nome del runbook (in questo esempio "**AutomaticTuningEmailAutomation**"). Selezionare questo runbook.
1. Nella prima riga del Runbook di PowerShell di modifica (a partire dal numero 1), copiare e incollare il codice di script di PowerShell seguente. Questo script di PowerShell viene fornito così com'è per iniziare. Modificare lo script in base alle esigenze.

Nell'intestazione dello script di PowerShell fornito è necessario sostituire `<SUBSCRIPTION_ID_WITH_DATABASES>` con l'ID della sottoscrizione di Azure. Per informazioni su come recuperare l'ID della sottoscrizione di Azure, vedere [Getting your Azure Subscription GUID](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/) (Recupero del codice GUID della sottoscrizione).

Nel caso di più sottoscrizioni, è possibile aggiungerle come delimitate da virgole alla proprietà "$subscriptions" nell'intestazione dello script.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Fare clic sul pulsante "**Salva**" nell'angolo in alto a destra per salvare lo script. Quando si è soddisfatti dello script, fare clic sul pulsante "**Pubblica**" per pubblicare questo runbook.

Nel riquadro principale del runbook, è possibile scegliere di fare clic sul pulsante "**Start**" per **testare** lo script. Fare clic su "**Output**" per visualizzare i risultati dello script eseguito. Questo output diventerà il contenuto del messaggio di posta elettronica. L'output dello script di esempio può essere visualizzato nella schermata seguente.

![Eseguire i suggerimenti di ottimizzazione automatica con Automazione di Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Modificare il contenuto personalizzando lo script di PowerShell in base alle esigenze.

Nel corso della procedura precedente è stato caricato in Automazione di Azure lo script di PowerShell che consente di recuperare i suggerimenti di ottimizzazione automatica. Il passaggio successivo consiste nell'automatizzazione e nella pianificazione del processo di recapito della posta elettronica.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizzare i processi di posta elettronica con Microsoft Flow

Per completare la soluzione, come ultimo passaggio, creare un flusso di automazione in Microsoft Flow composto da tre azioni (processi):

 - "**Automazione di Azure - Crea processo**" – usato per eseguire lo script di PowerShell per recuperare i consigli di ottimizzazione automatica all'interno del runbook di Automazione di Azure.
 - "**Automazione di Azure - Ottenere l'output del processo**" – utilizzato per recuperare l'output dallo script di PowerShell eseguito.
 - "**Office 365 Outlook – Invia un messaggio di posta elettronica**" – utilizzato per inviare posta elettronica. I messaggi di posta elettronica vengono inviati usando l'account Office 365 dell'utente che ha creato il flusso.

Per altre informazioni sulle funzionalità di Microsoft Flow, vedere [Attività iniziali con Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

I prerequisiti per questo passaggio comprendono la sottoscrizione di un account di [Microsoft Flow](https://flow.microsoft.com) e l'accesso. Dopo aver eseguito l'accesso alla soluzione, seguire questa procedura per configurare un **nuovo flusso**:

1. Accedere alla voce di menu "**Flussi**personali ".
1. All'interno di Flussi personali, selezionare il collegamento "**- Crea da vuoto**" nella parte superiore della pagina.
1. Fare clic sul collegamento "**Cerca centinaia di connettori e trigger**" nella parte inferiore della pagina.
1. Nel campo di ricerca digitare "**recurrence**" e selezionare "**Pianificazione - Ricorrenza**" dai risultati della ricerca per pianificare l'esecuzione del processo di recapito della posta elettronica.
1. Nel campo Frequenza del riquadro Ricorrenza selezionare la frequenza di pianificazione per l'esecuzione del flusso, ad esempio per inviare messaggi di posta elettronica automatici ogni minuto, ora, giorno, settimana e così via.

Il passaggio successivo consiste nell'aggiunta dei tre processi (creazione, acquisizione dell'output e invio del messaggio di posta elettronica) al flusso ricorrente appena creato. Per aggiungere al flusso i tre processi richiesti, seguire questa procedura:

1. Creare l'azione per eseguire lo script di PowerShell per recuperare i suggerimenti di ottimizzazione

   - Selezionare "**" - Nuovo passaggio**", seguito da "**Aggiungi un'azione**" all'interno del riquadro del flusso Ricorrenza.
   - Nel campo di ricerca digitare "**automazione**" e selezionare "**Automazione di Azure – Crea processo**" dai risultati della ricerca.
   - Nel riquadro di creazione del processo configurare le proprietà del processo. Per questa configurazione saranno necessari l'ID della sottoscrizione di Azure, il gruppo di risorse e l'account di Automazione **precedentemente annotati** dal **riquadro dell'account di Automazione**. Per altre informazioni sulle opzioni disponibili in questa sezione, vedere [Azure Automation - Create Job](https://docs.microsoft.com/connectors/azureautomation/#create-job) (Automazione di Azure - Creare il processo).
   - Completare la creazione di questa azione facendo clic su "**Salva flusso**".

2. Creare un'azione per recuperare l'output dello script di PowerShell eseguito

   - Selezionare "**"Nuovo passaggio**", seguito da "**Aggiungi un'azione**" all'interno del riquadro del flusso Ricorrenza
   - Nella ricerca file di tipo "**automazione**" e selezionare "**Automazione di Azure – Ottenere l'output**del processo " dai risultati della ricerca. Per altre informazioni sulle opzioni disponibili in questa sezione, vedere [Azure Automation – Get job output](https://docs.microsoft.com/connectors/azureautomation/#get-job-output) (Automazione di Azure - Ottenere l'output del processo).
   - Compilare i campi obbligatori (simile alla creazione del processo precedente): popolare l'ID sottoscrizione di Azure, il gruppo di risorse e l'account di automazione (come immesso nel riquadro Account di automazione).
   - Fare clic all'interno del campo "**JOB ID**" per visualizzare il menu "**Contenuto dinamico**". All'interno di questo menu, selezionare l'opzione "**JOB ID**".
   - Completare la creazione di questa azione facendo clic su "**Salva flusso**".

3. Creare l'azione per inviare messaggi di posta elettronica mediante l'integrazione di Office 365

   - Selezionare "**" - Nuovo passaggio**", seguito da "**Aggiungi un'azione**" all'interno del riquadro del flusso Ricorrenza.
   - Nel file di ricerca digitare "**invia un'e-mail**" e selezionare "**Office 365 Outlook – Invia un messaggio di posta elettronica**" dai risultati della ricerca.
   - Nel campo "**A**" digitare l'indirizzo e-mail a cui è necessario inviare l'e-mail di notifica.
   - Nel campo "**Oggetto**" digitare l'oggetto del messaggio di posta elettronica, ad esempio "Notifica e-mail di raccomandazioni di ottimizzazione automatica".
   - Fare clic all'interno del campo "**Body**" per visualizzare il menu "**Contenuto dinamico**". All'interno di questo menu, in "**Ottieni output di lavoro**", selezionare "**Contenuto**".
   - Completare la creazione di questa azione facendo clic su "**Salva flusso**".

> [!TIP]
> Per inviare messaggi di posta elettronica automatizzati a destinatari diversi, creare flussi separati. In questi flussi aggiuntivi, modificare l'indirizzo di posta elettronica del destinatario nel campo "A" e la riga dell'oggetto del messaggio di posta elettronica nel campo "Oggetto". La creazione di nuovi runbook in Automazione di Azure con script di PowerShell personalizzati (ad esempio, modificando l'ID della sottoscrizione di Azure) consente un'ulteriore personalizzazione degli scenari automatizzati, ad esempio l'invio di messaggi di posta elettronica a destinatari separati in merito ai suggerimenti di ottimizzazione automatica per sottoscrizioni separate.
>

Si è così conclusa la procedura per configurare il flusso di lavoro del processo di recapito di posta elettronica. Nell'immagine seguente è illustrato il flusso intero costituito dalle tre azioni create.

![Visualizzare il flusso delle notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Per testare il flusso, fare clic su "**Esegui ora**" nell'angolo superiore destro all'interno del riquadro del flusso.

Le statistiche di esecuzione dei processi automatizzati, con esito positivo delle notifiche di posta elettronica inviate, possono essere visualizzate nel riquadro Analisi di flusso.

![Esecuzione del flusso delle notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Il riquadro Analisi di flusso è utile per monitorare l'esito delle esecuzioni dei processi e, se necessario, per risolvere i problemi.  Nel caso in cui sia necessario risolvere un problema, è anche possibile esaminare il log di esecuzione dello script di PowerShell accessibile tramite l'app Automazione di Azure.

L'output finale del messaggio di posta elettronica automatizzato è simile al seguente messaggio di posta elettronica ricevuto dopo aver compilato ed eseguito questa soluzione:

![Esempio di output di notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Modificando lo script di PowerShell è possibile modificare l'output e la formattazione del messaggio di posta elettronica automatizzato in base alle proprie esigenze.

In base agli scenari personalizzati, è possibile anche personalizzare ulteriormente la soluzione in modo da creare notifiche tramite posta elettronica basate su uno specifico evento di ottimizzazione.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come l'ottimizzazione automatica contribuisca a migliorare le prestazioni del database, vedere [Ottimizzazione automatica nel database SQL di Azure](sql-database-automatic-tuning.md).
- Per consentire all'ottimizzazione automatica nel database SQL di Azure di gestire il carico di lavoro, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per esaminare e applicare manualmente le raccomandazioni di ottimizzazione automatica, vedere [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).
