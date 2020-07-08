---
title: Gestire le autorizzazioni dei ruoli e la sicurezza in Automazione di Azure
description: Questo articolo descrive come usare il controllo degli accessi in base al ruolo, che consente di gestire gli accessi per le risorse di Azure.
keywords: controllo degli accessi in base al ruolo di Automazione, controllo degli accessi in base al ruolo, controllo degli accessi in base al ruolo di Azure
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: ac05d5b4eb8dd9d7a39f56ec6efae4831f00c623
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85100009"
---
# <a name="manage-role-permissions-and-security"></a>Gestire le autorizzazioni e la sicurezza dei ruoli

Il controllo degli accessi in base al ruolo consente di gestire gli accessi per le risorse di Azure. Con il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) è possibile separare i compiti all'interno del team e concedere a utenti, gruppi e applicazioni solo il livello di accesso necessario per svolgere le proprie attività. È possibile concedere l'accesso in base al ruolo agli utenti tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

## <a name="roles-in-automation-accounts"></a>Ruoli negli account di Automazione

In Automazione di Azure l'accesso viene concesso assegnando il ruolo Controllo degli accessi in base al ruolo appropriato a utenti, gruppi e applicazioni nell'ambito dell'account di automazione. Di seguito sono elencati i ruoli predefiniti supportati da un account di automazione:

| **Ruolo** | **Descrizione** |
|:--- |:--- |
| Proprietario |Il ruolo Proprietario consente l'accesso a tutte le risorse e le azioni in un account di Automazione, inclusa la possibilità di concedere l'accesso ad altri utenti e gruppi e ad altre applicazioni per gestire l'account di Automazione. |
| Collaboratore |Il ruolo Collaboratore consente di gestire tutto, tranne la modifica delle autorizzazioni di accesso di altri utenti a un account di automazione. |
| Reader |Il ruolo Lettore consente di visualizzare tutte le risorse in un account di automazione, ma non di apportare modifiche. |
| Operatore di automazione |Il ruolo Operatore di automazione consente di visualizzare le proprietà e il nome del runbook e di creare e gestire i processi per tutti i runbook in un account di Automazione. Questo ruolo è utile per proteggere dalla visualizzazione o dalla modifica le risorse dell'account di Automazione come gli asset delle credenziali e i runbook, consentendo però ai membri dell'organizzazione di eseguire i runbook. |
|Operatore processo di automazione|Il ruolo Operatore processo di automazione consente di creare e gestire i processi per tutti i runbook in un account di Automazione.|
|Operatore runbook di automazione|Il ruolo Operatore runbook di automazione consente di visualizzare il nome e le proprietà di un runbook.|
| Collaboratore di Log Analytics | Il ruolo Collaboratore di Log Analytics consente di leggere tutti i dati di monitoraggio e di modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log da Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di funzionalità di Automazione di Azure e la configurazione di Diagnostica di Azure in tutte le risorse di Azure.|
| Lettore di Log Analytics | Il ruolo Lettore di Log Analytics consente di visualizzare e cercare tutti i dati di monitoraggio, oltre alle impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure. |
| Collaboratore al monitoraggio | Il ruolo Monitoraggio del ruolo Collaboratore consente di leggere tutti i dati di monitoraggio e di aggiornare le impostazioni di monitoraggio.|
| Lettore di monitoraggio | Il ruolo Lettore di monitoraggio consente di leggere tutti i dati di monitoraggio. |
| Amministratore accessi utente |Il ruolo Amministratore Accesso utenti consente di gestire l'accesso utente agli account di automazione di Azure. |

## <a name="role-permissions"></a>Autorizzazioni ruoli

Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo. Può trattarsi di proprietà Actions, che concedono le autorizzazioni, e NotActions, che le limitano.

### <a name="owner"></a>Proprietario

Un proprietario può gestire tutto, compresi gli accessi. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|Azioni|Descrizione|
|---|---|
|Microsoft.Automation/automationAccounts/|Può creare e gestire ogni tipo di risorsa.|

### <a name="contributor"></a>Collaboratore

Un collaboratore può gestire tutto a eccezione degli accessi. La tabella seguente illustra le autorizzazioni concesse e negate per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|È in grado di creare e gestire ogni tipo di risorsa|
|**Non azioni**||
|Microsoft.Authorization/*/Delete| Elimina ruoli e assegnazioni di ruoli.       |
|Microsoft.Authorization/*/Write     |  Crea ruoli e assegnazioni di ruoli.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega la possibilità di creare un amministratore Accesso utenti.       |

### <a name="reader"></a>Reader

Un lettore può visualizzare tutte le risorse in un account di Automazione, ma non apportare modifiche.

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Visualizza tutte le risorse in un account di Automazione. |

### <a name="automation-operator"></a>Operatore di automazione

Il ruolo Operatore di automazione può creare e gestire i processi, oltre che leggere le proprietà e i nomi, per tutti i runbook in un account di Automazione.  Nota: se si vuole controllare l'accesso dell'operatore ai singoli runbook, non impostare questo ruolo e usare invece i ruoli "Operatore processo di automazione" e "Operatore runbook di automazione" in combinazione. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Authorization/*/read|Legge l'autorizzazione.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Legge le risorse del ruolo di lavoro ibrido per runbook.|
|Microsoft.Automation/automationAccounts/jobs/read|Elenca i processi del runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Riprende un processo in pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annulla un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Legge i flussi e l'output di processo.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Ottiene l'output di un processo.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Sospende un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crea processi.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Ottiene una pianificazione del processo di Automazione di Azure.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Crea una pianificazione del processo di Automazione di Azure.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Ottiene l'area di lavoro collegata all'account di Automazione.|
|Microsoft.Automation/automationAccounts/read|Ottiene un account di Automazione di Azure.|
|Microsoft.Automation/automationAccounts/runbooks/read|Ottiene un runbook di Automazione di Azure.|
|Microsoft.Automation/automationAccounts/schedules/read|Ottiene un asset della pianificazione di Automazione di Azure.|
|Microsoft.Automation/automationAccounts/schedules/write|Crea o aggiorna un asset della pianificazione di Automazione di Azure.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Legge i ruoli e le assegnazioni di ruoli.         |
|Microsoft.Resources/deployments/*      |Crea e gestisce distribuzioni di gruppi di risorse.         |
|Microsoft.Insights/alertRules/*      | Crea e gestisce regole di avviso.        |
|Microsoft.Support/* |Crea e gestisce ticket di supporto.|

### <a name="automation-job-operator"></a>Operatore processo di automazione

Viene concesso un ruolo Operatore processo di automazione nell'ambito dell'account di Automazione. In questo modo vengono concesse all'operatore le autorizzazioni per creare e gestire i processi per tutti i runbook nell'account. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Authorization/*/read|Legge l'autorizzazione.|
|Microsoft.Automation/automationAccounts/jobs/read|Elenca i processi del runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Riprende un processo in pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Annulla un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Legge i flussi e l'output di processo.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Sospende un processo in corso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crea processi.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Legge i ruoli e le assegnazioni di ruoli.       |
|Microsoft.Resources/deployments/*      |Crea e gestisce distribuzioni di gruppi di risorse.         |
|Microsoft.Insights/alertRules/*      | Crea e gestisce regole di avviso.        |
|Microsoft.Support/* |Crea e gestisce ticket di supporto.|

### <a name="automation-runbook-operator"></a>Operatore runbook di automazione

Un ruolo Operatore runbook di automazione viene concesso nell'ambito del runbook. Un ruolo Operatore runbook di automazione può visualizzare il nome e le proprietà del runbook.  Questo ruolo, combinato con il ruolo "Operatore processo di automazione" consente all'operatore anche di creare e gestire i processi per il runbook. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Elenca i runbook.        |
|Microsoft.Authorization/*/read      | Legge l'autorizzazione.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Legge i ruoli e le assegnazioni di ruoli.         |
|Microsoft.Resources/deployments/*      | Crea e gestisce distribuzioni di gruppi di risorse.         |
|Microsoft.Insights/alertRules/*      | Crea e gestisce regole di avviso.        |
|Microsoft.Support/*      | Crea e gestisce ticket di supporto.        |

### <a name="log-analytics-contributor"></a>Collaboratore di Log Analytics

Un collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log da Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di funzionalità e la configurazione di Diagnostica di Azure in tutte le risorse di Azure. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.Automation/automationAccounts/*|Gestisce gli account di Automazione.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Crea e gestisce le estensioni macchina virtuale.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Elenca le chiavi dell'account di archiviazione classico.|
|Microsoft.Compute/virtualMachines/extensions/*|Crea e gestisce le estensioni macchina virtuale classiche.|
|Microsoft.Insights/alertRules/*|Regole di avviso di lettura, scrittura ed eliminazione.|
|Microsoft.Insights/diagnosticSettings/*|Impostazioni di diagnostica di lettura, scrittura ed eliminazione.|
|Microsoft.OperationalInsights/*|Gestisce i log di Monitoraggio di Azure.|
|Microsoft.OperationsManagement/*|Gestisce le funzionalità di Automazione di Azure nelle aree di lavoro.|
|Microsoft.Resources/deployments/*|Crea e gestisce distribuzioni di gruppi di risorse.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Crea e gestisce distribuzioni di gruppi di risorse.|
|Microsoft.Storage/storageAccounts/listKeys/action|Elenca le chiavi dell'account di archiviazione.|
|Microsoft.Support/*|Crea e gestisce ticket di supporto.|

### <a name="log-analytics-reader"></a>Lettore di Log Analytics

Un ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. La tabella seguente illustra le autorizzazioni concesse o negate per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Gestisce le query nei log di Monitoraggio di Azure.|
|Microsoft.OperationalInsights/workspaces/search/action|Cerca i dati di log di Monitoraggio di Azure.|
|Microsoft.Support/*|Crea e gestisce ticket di supporto.|
|**Non azioni**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Non può leggere le chiavi di accesso condivise.|

### <a name="monitoring-contributor"></a>Collaboratore al monitoraggio

Un ruolo Monitoraggio del ruolo Collaboratore può leggere tutti i dati di monitoraggio e aggiornare le impostazioni di monitoraggio. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.AlertsManagement/alerts/*|Gestisce gli avvisi.|
|Microsoft.AlertsManagement/alertsSummary/*|Gestisce il dashboard degli avvisi.|
|Microsoft.Insights/AlertRules/*|Gestisce le regole di avviso.|
|Microsoft.Insights/components/*|Gestisce i componenti di Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Gestisce le impostazioni di diagnostica.|
|Microsoft.Insights/eventtypes/*|Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività.|
|Microsoft.Insights/LogDefinitions/*|Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. Elencare categorie di log nel log attività.|
|Microsoft.Insights/MetricDefinitions/*|Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa).|
|Microsoft.Insights/Metrics/*|Metriche per una risorsa.|
|Microsoft.Insights/Register/Action|Registra il provider Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Gestisce i test Web di Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gestisce pacchetti di soluzioni dei log di Monitoraggio di Azure.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gestisce le ricerche salvate dei log di Monitoraggio di Azure.|
|Microsoft.OperationalInsights/workspaces/search/action|Cercare aree di lavoro di Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Elencare chiavi per un'area di lavoro Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gestisce le configurazione di dati di archiviazione nei log di Monitoraggio di Azure.|
|Microsoft.Support/*|Crea e gestisce ticket di supporto.|
|Microsoft.WorkloadMonitor/workloads/*|Gestisce i carichi di lavoro.|

### <a name="monitoring-reader"></a>Lettore di monitoraggio

Un ruolo Monitoraggio del ruolo Lettore può leggere tutti i dati di monitoraggio. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge risorse di tutti i tipi, eccetto i segreti.|
|Microsoft.OperationalInsights/workspaces/search/action|Cercare aree di lavoro di Log Analytics.|
|Microsoft.Support/*|Creare e gestire ticket di supporto|

### <a name="user-access-administrator"></a>Amministratore accessi utente

Un amministratore Accesso utenti può gestire l'accesso degli utenti alle risorse di Azure. La tabella seguente illustra le autorizzazioni concesse per il ruolo:

|**Actions**  |**Descrizione**  |
|---------|---------|
|*/lettura|Legge tutte le risorse|
|Microsoft.Authorization/*|Gestire l'autorizzazione|
|Microsoft.Support/*|Creare e gestire ticket di supporto|

## <a name="feature-setup-permissions"></a>Autorizzazioni di configurazione delle funzionalità

Le sezioni seguenti descrivono le autorizzazioni minime necessarie per abilitare le funzionalità Gestione aggiornamenti e Rilevamento modifiche e inventario.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Autorizzazioni per l'abilitazione di Gestione aggiornamenti e Rilevamento modifiche e inventario da una macchina virtuale

|**Azione**  |**Autorizzazione**  |**Ambito minimo**  |
|---------|---------|---------|
|Scrivere una nuova distribuzione      | Microsoft.Resources/deployments/*          |Subscription          |
|Scrivere un nuovo gruppo di risorse      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subscription          |
|Creare una nuova area di lavoro predefinita      | Microsoft.OperationalInsights/workspaces/write         | Resource group         |
|Creare un nuovo account      |  Microsoft.Automation/automationAccounts/write        |Resource group         |
|Collegare un'area di lavoro e un account      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Area di lavoro</br>Account di Automazione
|Creare un'estensione MMA      | Microsoft.Compute/virtualMachines/write         | Macchina virtuale         |
|Creare una ricerca salvata      | Microsoft.OperationalInsights/workspaces/write          | Area di lavoro         |
|Creare una configurazione di ambito      | Microsoft.OperationalInsights/workspaces/write          | Area di lavoro         |
|Controllo dello stato di onboarding - Leggere l'area di lavoro      | Microsoft.OperationalInsights/workspaces/read         | Area di lavoro         |
|Controllo dello stato di onboarding - Leggere la proprietà dell'area di lavoro collegata dell'account     | Microsoft.Automation/automationAccounts/read      | Account di Automazione        |
|Controllo dello stato di onboarding - Leggere la soluzione      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Soluzione         |
|Controllo dello stato di onboarding - Leggere la VM      | Microsoft.Compute/virtualMachines/read         | Macchina virtuale         |
|Controllo dello stato di onboarding - Leggere l'account      | Microsoft.Automation/automationAccounts/read  |  Account di Automazione   |
| Controllo dell'area di lavoro di onboarding per la macchina virtuale<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subscription         |
| Registrare il provider di Log Analytics |Microsoft.Insights/register/action | Subscription|

<sup>1</sup> Questa autorizzazione è necessaria per abilitare le funzionalità tramite l'esperienza del portale per le macchine virtuali.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Autorizzazioni per l'abilitazione di Gestione aggiornamenti e Rilevamento modifiche e inventario da un account di Automazione

|**Azione**  |**Autorizzazione** |**Ambito minimo**  |
|---------|---------|---------|
|Creare una nuova distribuzione     | Microsoft.Resources/deployments/*        | Subscription         |
|Creare un nuovo gruppo di risorse     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subscription        |
|Pannello AutomationOnboarding - Creare una nuova area di lavoro     |Microsoft.OperationalInsights/workspaces/write           | Resource group        |
|Pannello AutomationOnboarding - Leggere un'area di lavoro collegata     | Microsoft.Automation/automationAccounts/read        | Account di Automazione       |
|Pannello AutomationOnboarding - Leggere una soluzione     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Soluzione        |
|Pannello AutomationOnboarding - Leggere un'area di lavoro     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Area di lavoro        |
|Creare un collegamento per un'area di lavoro e un account     | Microsoft.OperationalInsights/workspaces/write        | Area di lavoro        |
|Scrivere un account per Shoebox      | Microsoft.Automation/automationAccounts/write        | Account        |
|Creare/Modificare una ricerca salvata     | Microsoft.OperationalInsights/workspaces/write        | Area di lavoro        |
|Creare/Modificare una configurazione di ambito     | Microsoft.OperationalInsights/workspaces/write        | Area di lavoro        |
| Registrare il provider di Log Analytics |Microsoft.Insights/register/action | Subscription|
|**Passaggio 2: abilitare più macchine virtuali**     |         |         |
|Pannello VMOnboarding - Creare un'estensione MMA     | Microsoft.Compute/virtualMachines/write           | Macchina virtuale        |
|Creare/Modificare una ricerca salvata     | Microsoft.OperationalInsights/workspaces/write           | Area di lavoro        |
|Creare/Modificare una configurazione di ambito  | Microsoft.OperationalInsights/workspaces/write   | Area di lavoro|

## <a name="update-management-permissions"></a>Autorizzazioni di Gestione aggiornamenti

La gestione degli aggiornamenti copre più servizi per fornire il proprio servizio. La tabella seguente illustra le autorizzazioni necessarie per gestire le distribuzioni di gestione degli aggiornamenti:

|**Risorsa**  |**Ruolo**  |**Ambito**  |
|---------|---------|---------|
|Account di Automazione     | Collaboratore di Log Analytics       | Account di Automazione        |
|Account di Automazione    | Collaboratore macchine virtuali        | Gruppo di risorse per l'account        |
|Area di lavoro Log Analytics     | Collaboratore di Log Analytics| Area di lavoro Log Analytics        |
|Area di lavoro Log Analytics |Lettore di Log Analytics| Subscription|
|Soluzione     |Collaboratore di Log Analytics         | Soluzione|
|Macchina virtuale     | Collaboratore macchine virtuali        | Macchina virtuale        |

## <a name="configure-rbac-for-your-automation-account"></a>Configurare il controllo degli accessi in base al ruolo per l'account di Automazione

La sezione seguente illustra come configurare il controllo degli accessi in base al ruolo nell'account di Automazione tramite il [portale di Azure](#configure-rbac-using-the-azure-portal) e [PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Configurare il controllo degli accessi in base al ruolo mediante il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/) e aprire l'account di Automazione nella pagina Account di automazione.
2. Fare clic su **Controllo di accesso (IAM)** per aprire la pagina Controllo di accesso (IAM). È possibile usare questa pagina per aggiungere nuovi utenti, gruppi e applicazioni per gestire il proprio account di Automazione e visualizzare i ruoli esistenti che possono essere configurati per l'account di Automazione.
3. Fare clic sulla scheda **Assegnazioni di ruolo**.

   ![Pulsante Accesso](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Aggiungere un nuovo utente e assegnare un ruolo

1. Nella pagina Controllo di accesso (IAM) fare clic su **+ Aggiungi un'assegnazione di ruolo**. Questa azione apre la pagina Aggiungi un'assegnazione di ruolo in cui è possibile aggiungere un utente, un gruppo o un'applicazione e assegnare un ruolo corrispondente.

2. Selezionare un ruolo dall'elenco di ruoli disponibili. È possibile scegliere uno qualsiasi dei ruoli predefiniti disponibili supportati da un account di Automazione oppure un ruolo personalizzato definito dall'utente.

3. Digitare il nome dell'utente a cui si vogliono concedere le autorizzazioni nel campo **Seleziona**. Scegliere l'utente dall'elenco e fare clic su **Salva**.

   ![Aggiungere utenti](media/automation-role-based-access-control/automation-04-add-users.png)

   L'utente verrà ora aggiunto nella pagina Utenti con il ruolo selezionato assegnato.

   ![Elencare gli utenti](media/automation-role-based-access-control/automation-05-list-users.png)

   È anche possibile assegnare un ruolo all'utente dalla pagina Ruoli.
4. Fare clic su **Ruoli** nella pagina Controllo di accesso (IAM) per aprire la pagina Ruoli. È possibile visualizzare il nome del ruolo e il numero di utenti e gruppi assegnati al ruolo.

    ![Assegnare un ruolo dalla pagina Utenti](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > È possibile impostare il controllo degli accessi in base al ruolo solo nell'ambito dell'account di Automazione e in nessun'altra risorsa al di sotto dell'account di Automazione.

#### <a name="remove-a-user"></a>Rimuovere un utente

È possibile rimuovere l'autorizzazione di accesso per un utente che non gestisce l'account di Automazione o che non lavora più per l'organizzazione. Ecco la procedura da seguire per rimuovere un utente:

1. Nella pagina Controllo di accesso (IAM) selezionare l'utente che si vuole rimuovere e fare clic su **Rimuovi**.
2. Fare clic sul pulsante **Rimuovi** nel pannello dei dettagli dell'assegnazione.
3. Fare clic su **Sì** per confermare la rimozione.

   ![Rimuovere utenti](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Configurare il controllo degli accessi in base al ruolo mediante PowerShell

È possibile configurare l'accesso in base al ruolo per un account di Automazione anche con i [cmdlet di Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) seguenti:

[Get-AzRoleDefinition](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleDefinition?view=azps-3.7.0) elenca tutti i ruoli di Controllo degli accessi in base al ruolo disponibili in Azure Active Directory. È possibile usare questo cmdlet con il parametro `Name` per elencare tutte le azioni che possono essere eseguite da un ruolo specifico.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Di seguito è riportato l'output di esempio:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0) elenca le assegnazioni di ruolo di Controllo degli accessi in base al ruolo di Azure AD nell'ambito specificato. Senza parametri, questo cmdlet restituisce tutte le assegnazioni del ruolo eseguite nell'ambito della sottoscrizione. Usare il parametro `ExpandPrincipalGroups` per elencare le assegnazioni di accesso per l'utente specificato e per i gruppi a cui l'utente appartiene.

**Esempio:** usare il cmdlet seguente per elencare tutti gli utenti e i relativi ruoli all'interno di un account di Automazione.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Di seguito è riportato l'output di esempio:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Usare [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzRoleAssignment?view=azps-3.7.0) per assegnare l'accesso a utenti, gruppi e applicazioni in un determinato ambito.
    
**Esempio:** usare il comando seguente per assegnare il ruolo "Operatore di automazione" a un utente nell'ambito dell'account di Automazione.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Di seguito è riportato l'output di esempio:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Usare [Remove-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzRoleAssignment?view=azps-3.7.0) per rimuovere l'accesso di un utente, un'applicazione o un gruppo specifico da un determinato ambito.

**Esempio:** usare il comando seguente per rimuovere l'utente dal ruolo Operatore di automazione nell'ambito dell'account di Automazione.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Nell'esempio precedente sostituire `sign-in ID of a user you wish to remove`, `SubscriptionID`, `Resource Group Name` e `Automation account name` con i dettagli dell'account. Scegliere **Sì** quando viene chiesto di confermare se si vogliono rimuovere le assegnazioni di ruolo per un utente.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Esperienza utente per il ruolo Operatore di automazione - Account di Automazione

Quando un utente assegnato al ruolo Operatore di automazione nell'ambito dell'account di Automazione visualizza l'account di Automazione a cui è stato assegnato, può vedere solo l'elenco delle pianificazioni, dei runbook e dei processi di runbook creati nell'account di Automazione. L'utente non può visualizzare le definizioni di questi elementi. L'utente può avviare, arrestare, sospendere, riprendere o pianificare il processo del runbook, ma non avrà accesso ad altre risorse di Automazione, tra cui configurazioni, gruppi di ruoli di lavoro ibridi o nodi DSC.

![Nessun accesso alle risorse](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Configurare il controllo degli accessi in base al ruolo per i runbook

Automazione di Azure consente di assegnare il controllo degli accessi in base al ruolo a specifici runbook. A tale scopo, eseguire lo script seguente per aggiungere un utente a un runbook specifico. Questo script può essere eseguito da un amministratore dell'account di Automazione o da un automazione tenant.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Dopo l'esecuzione dello script fare in modo che l'utente acceda al portale di Azure e selezioni **Tutte le risorse**. Nell'elenco l'utente può visualizzare il runbook per cui è stato aggiunto come Operatore runbook di automazione.

![Controllo degli accessi in base al ruolo del runbook nel portale](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Esperienza dell'utente per il ruolo Operatore di automazione - Runbook

Quando un utente assegnato al ruolo Operatore di automazione nell'ambito del runbook visualizza un runbook assegnato, può solo avviare il runbook e visualizzare i processi del runbook.

![Ha accesso solo all'avvio](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul controllo degli accessi in base al ruolo di PowerShell, vedere [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Per informazioni dettagliate sui tipi di runbook, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md).
* Per avviare un runbook, vedere [Avviare un runbook in Automazione di Azure](start-runbooks.md).
