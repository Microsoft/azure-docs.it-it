---
title: Ruoli, autorizzazioni e sicurezza in monitoraggio di Azure
description: Informazioni su come usare le autorizzazioni e i ruoli predefiniti di monitoraggio di Azure per limitare l'accesso alle risorse di monitoraggio.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 7d92cbc25411f5cc2d528ccf6ecec4539494d380
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533275"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Ruoli, autorizzazioni e sicurezza in monitoraggio di Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Molti team hanno bisogno di regolare rigorosamente l'accesso ai dati e alle impostazioni di monitoraggio. Ad esempio, se si dispone di membri del team che lavorano esclusivamente sul monitoraggio (tecnici del supporto, tecnici DevOps) o si usa un provider di servizi gestiti, si consiglia di concedere loro l'accesso ai dati di monitoraggio solo limitandone la possibilità di creare, modificare o eliminare le risorse. Questo articolo illustra come applicare rapidamente un ruolo di Azure di monitoraggio predefinito a un utente in Azure o creare un ruolo personalizzato per un utente che necessita di autorizzazioni di monitoraggio limitate. Vengono poi esposte alcune considerazioni sulla sicurezza per le risorse legate al monitoraggio di Azure e viene illustrato come è possibile limitare l'accesso ai dati che contengono.

## <a name="built-in-monitoring-roles"></a>Ruoli di monitoraggio predefiniti
I ruoli predefiniti del monitoraggio di Azure consentono di limitare l'accesso alle risorse in una sottoscrizione e allo stesso tempo consentire ai responsabili del monitoraggio dell'infrastruttura di ottenere e configurare i dati necessari. Monitoraggio di Azure offre due ruoli predefiniti: Un lettore di monitoraggio e un collaboratore al monitoraggio.

### <a name="monitoring-reader"></a>Lettore di monitoraggio
Le persone a cui è assegnato il ruolo di lettore di monitoraggio possono visualizzare tutti i dati di monitoraggio in una sottoscrizione ma non possono modificare alcuna risorsa o impostazione relativa alle risorse di monitoraggio. Questo ruolo è appropriato per gli utenti di un'organizzazione, ad esempio tecnici del supporto o delle operazioni, che devono essere in grado di:

* Visualizzare i dashboard di monitoraggio nel portale e creare dashboard di monitoraggio privati.
* Visualizzare le regole di avviso definite in [Avvisi di Azure](alerts-overview.md)
* Eseguire query per le metriche usando l'[API REST di Monitoraggio di Azure](/rest/api/monitor/metrics), i [cmdlet di PowerShell](../samples/powershell-samples.md) o l'[interfaccia della riga di comando multipiattaforma](../samples/cli-samples.md).
* Eseguire query per il registro attività usando il portale, l'API REST di monitoraggio di Azure, i cmdlet di PowerShell o l'interfaccia della riga di comando multipiattaforma.
* Visualizzare le [impostazioni di diagnostica](diagnostic-settings.md) per una risorsa.
* Visualizzare il [profilo di registro](./activity-log.md#legacy-collection-methods) per una sottoscrizione.
* Visualizzare le impostazioni di scalabilità automatica.
* Visualizzare impostazioni e attività di avviso.
* Accedere ai dati di Application Insights e visualizzarli in AI Analytics.
* Cercare i dati dell'area di lavoro Log Analytics, inclusi i dati sull'uso dell'area di lavoro.
* Visualizzare i gruppi di gestione di Log Analytics.
* Recuperare lo schema di ricerca nell'area di lavoro Log Analytics.
* Elencare i Monitoring Pack nell'area di lavoro Log Analytics.
* Recuperare ed eseguire le ricerche salvate nell'area di lavoro Log Analytics.
* Recuperare la configurazione di archiviazione dell'area di lavoro Log Analytics.

> [!NOTE]
> Questo ruolo non concede l'accesso in lettura ai dati del registro che sono stati trasmessi a un hub eventi o archiviati in un account di archiviazione. [vedere di seguito](#security-considerations-for-monitoring-data) .
> 
> 

### <a name="monitoring-contributor"></a>Collaboratore al monitoraggio
Le persone a cui è assegnato il ruolo di collaboratore al monitoraggio possono visualizzare tutti i dati di monitoraggio in una sottoscrizione e creare o modificare le impostazioni, ma non possono modificare altre risorse. Questo ruolo è un soprainsieme del ruolo di lettore di monitoraggio ed è adatto ai membri del team di monitoraggio di un'organizzazione o ai fornitori di servizi gestiti che, oltre alle autorizzazioni di cui sopra, devono essere in grado di:

* Pubblicare dashboard di monitoraggio come dashboard condivisi.
* Configurare le [impostazioni di diagnostica](diagnostic-settings.md) per una risorsa.\*
* Impostare il [profilo di registro](./activity-log.md#legacy-collection-methods) per una sottoscrizione.\*
* Configurare le attività e le impostazioni delle regole di avviso tramite [Avvisi di Azure](alerts-overview.md).
* Creare componenti e test Web di Application Insights.
* Elencare le chiavi condivise dell'area di lavoro Log Analytics.
* Abilitare o disabilitare i Monitoring Pack nell'area di lavoro Log Analytics.
* Creare, eliminare ed eseguire le ricerche salvate nell'area di lavoro Log Analytics.
* Creare ed eliminare la configurazione di archiviazione dell'area di lavoro Log Analytics.

\* per configurare un profilo di registro o un'impostazione di diagnostica, è necessario che all'utente sia concessa separatamente anche l'autorizzazione ListKeys nella risorsa di destinazione (account di archiviazione o spazio dei nomi dell'hub eventi).

> [!NOTE]
> Questo ruolo non concede l'accesso in lettura ai dati del registro che sono stati trasmessi a un hub eventi o archiviati in un account di archiviazione. [vedere di seguito](#security-considerations-for-monitoring-data) .
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Monitoraggio delle autorizzazioni e dei ruoli personalizzati di Azure
Se i ruoli predefiniti precedenti non soddisfano le esigenze esatte del team, è possibile [creare un ruolo personalizzato di Azure](../../role-based-access-control/custom-roles.md) con autorizzazioni più granulari. Di seguito sono riportate le più comuni operazioni nel Controllo degli accessi in base al ruolo di monitoraggio di Azure con le relative descrizioni.

| Operazione | Descrizione |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Gruppi di azioni di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Avvisi del log attività di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Regole di avviso di lettura, scrittura ed eliminazione (da versione classica degli avvisi). |
| Microsoft.Insights/AlertRules/Incidents/Read |Elenco degli eventi imprevisti (cronologia della regola di avviso attivata) per le regole di avviso. Si applica solo al portale. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Impostazioni di scalabilità automatica di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Impostazioni di diagnostica di lettura, scrittura ed eliminazione. |
| Microsoft.Insights/EventCategories/Read |Enumerazione di tutte le categorie possibili nel log attività. Operazione usata dal portale di Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. |
| Microsoft.Insights/eventtypes/values/Read |Elenco degli eventi dei registri attività (eventi di gestione) in una sottoscrizione. Questa autorizzazione è applicabile sia all'accesso programmatico che all'accesso al portale per il registro attività. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Impostazioni di diagnostica di lettura, scrittura ed eliminazione per i log del flusso di rete. |
| Microsoft.Insights/LogDefinitions/Read |Questa autorizzazione è necessaria per gli utenti che hanno bisogno dell'accesso ai log attività tramite il portale. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Profili di log di lettura, scrittura ed eliminazione (streaming del log attività nell'hub eventi o nell'account di archiviazione). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Avvisi delle metriche near real time di lettura, scrittura ed eliminazione |
| Microsoft.Insights/MetricDefinitions/Read |Definizioni delle metriche (elenco dei tipi di metriche disponibili per una risorsa). |
| Microsoft.Insights/Metrics/Read |Metriche per una risorsa. |
| Microsoft.Insights/Register/Action |Registrazione del provider di risorse di Monitoraggio di Azure. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Avvisi di lettura, scrittura ed eliminazione del log nel Monitoraggio di Azure. |



> [!NOTE]
> L'accesso ad avvisi, impostazioni di diagnostica e metriche per una risorsa richiede che l'utente disponga dell'accesso in lettura per il tipo di risorsa e l'ambito di tale risorsa. La creazione ("scrittura") di un'impostazione di diagnostica o di un profilo di registro che archivia in un account di archiviazione o trasmette a un hub eventi richiede che l'utente disponga anche dell'autorizzazione ListKeys nella risorsa di destinazione.
> 
> 

Ad esempio, usando la tabella precedente, è possibile creare un ruolo personalizzato di Azure per un "lettore di log attività" simile al seguente:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considerazioni sulla sicurezza per i dati sul monitoraggio
I dati sul monitoraggio dei dati, in particolare i file di registro, possono contenere informazioni sensibili, come indirizzi IP o nomi utente. I dati sul monitoraggio di Azure sono forniti in tre forme base:

1. Registro attività, che descrive tutte le azioni del piano di controllo nella sottoscrizione di Azure.
2. log di risorsa, cioè log generati da una risorsa.
3. Metriche, generate dalle risorse.

Tutti e tre questi tipi di dati possono essere archiviati in un account di archiviazione o trasmessi a un hub eventi, che sono entrambi risorse di Azure di scopo generico. Poiché si tratta di risorse di scopo generico, la creazione, l'eliminazione e l'accesso sono operazioni privilegiate e riservate agli amministratori. Si consiglia di usare le procedure seguenti con le risorse relative al monitoraggio per evitare un uso improprio:

* Usare un account di archiviazione singolo e dedicato per il monitoraggio dei dati. Se è necessario separare i dati di monitoraggio in più account di archiviazione, non condividere mai l'uso di un account di archiviazione tra dati relativi al monitoraggio e non relativi al monitoraggio, perché in questo modo si potrebbe inavvertitamente fornire l'accesso ai dati non relativi al monitoraggio a chi ha bisogno solo dei dati di monitoraggio (ad esempio SIEM di terze parti).
* Usare un singolo bus di servizio o spazio dei nomi dell'hub eventi dedicato in tutte le impostazioni di diagnostica per lo stesso motivo specificato sopra.
* Limitare l'accesso agli hub eventi o agli account di archiviazione relativi al monitoraggio tenendoli in un gruppo di risorse separato e [usare l'ambito](../../role-based-access-control/overview.md#scope) nei ruoli di monitoraggio per limitare l'accesso solo a tale gruppo di risorse.
* Non concedere mai l'autorizzazione ListKeys ad account di archiviazione o hub eventi nell'ambito della sottoscrizione quando un utente ha bisogno solo dell'accesso ai dati di monitoraggio. Piuttosto, assegnare queste autorizzazioni all'utente nell'ambito di una risorsa o di un gruppo di risorse (se si dispone di un gruppo di risorse di monitoraggio dedicato).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitare l'accesso agli account di archiviazione relativi al monitoraggio
Quando un utente o un'applicazione richiede l'accesso ai dati di monitoraggio in un account di archiviazione, è necessario [generare una firma di accesso condiviso per l'account](/rest/api/storageservices/create-account-sas) nell'account di archiviazione che contiene i dati di monitoraggio con accesso in sola lettura a livello di servizio all'archivio BLOB. In PowerShell, potrebbe avere un aspetto simile al seguente:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

A questo punto è possibile fornire il token all'entità che ha bisogno di leggere da quell'account di archiviazione; tale token può elencare e leggere da tutti i BLOB nell'account di archiviazione.

In alternativa, se è necessario verificare l'autorizzazione con il Controllo degli accessi in base al ruolo, è possibile concedere a tale entità l'autorizzazione Microsoft.Storage/storageAccounts/listkeys/action su quel particolare account di archiviazione. Questo è necessario per gli utenti che devono essere in grado di configurare un'impostazione di diagnostica o un profilo di registro per l'archiviazione in un account di archiviazione. Ad esempio, è possibile creare il ruolo personalizzato di Azure seguente per un utente o un'applicazione che deve leggere solo da un account di archiviazione:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> L'autorizzazione ListKeys consente all'utente di elencare le chiavi dell'account di archiviazione primario e secondario. Queste chiavi concedono all'utente tutte le autorizzazioni accettate (lettura, scrittura, creazione di BLOB, eliminazione di BLOB e così via) in tutti i servizi accettati (BLOB, coda, tabella, file) di tale account di archiviazione. Se possibile, si consiglia di usare una firma di accesso condiviso dell'account come descritto sopra.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitare l'accesso agli hub eventi relativi al monitoraggio
Un modello simile può essere seguito con gli hub eventi, tuttavia è innanzitutto necessario creare una regola di autorizzazione di ascolto dedicata. Se si desidera concedere l'accesso a un'applicazione che ha bisogno solo di ascoltare gli hub eventi relativi al monitoraggio, procedere come segue:

1. Creare un criterio di accesso condiviso negli hub eventi creati per trasmettere i dati di monitoraggio solo con attestazioni di ascolto. Questa operazione può essere eseguita nel portale. Ad esempio, è possibile chiamarlo "monitoringReadOnly". Se possibile, si dovrà fornire la chiave direttamente al consumatore e ignorare il passaggio successivo.
2. Se il consumatore deve essere in grado di ottenere la chiave ad hoc, concedere all'utente l'azione ListKeys per l'hub eventi. Questo è necessario anche per gli utenti che devono essere in grado di configurare un'impostazione di diagnostica o un profilo di registro per trasmettere agli hub eventi. Ad esempio, è possibile creare una regola nel Controllo degli accessi in base al ruolo:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitoraggio all'interno di una rete virtuale protetta

Monitoraggio di Azure deve accedere alle risorse di Azure per fornire i servizi abilitati. Se si vuole eseguire il monitoraggio delle risorse di Azure durante la fase di protezione dall'accesso alla rete Internet pubblica, è possibile abilitare le impostazioni seguenti.

### <a name="secured-storage-accounts"></a>Account di archiviazione protetti 

I dati di monitoraggio vengono spesso scritti in un account di archiviazione. È possibile assicurarsi che i dati copiati in un account di archiviazione non siano accessibili da utenti non autorizzati. Per una maggiore sicurezza, è possibile bloccare l'accesso alla rete per consentire solo alle risorse autorizzate e ai servizi Microsoft attendibili di accedere a un account di archiviazione limitando un account di archiviazione all'uso di "reti selezionate".
![Finestra di dialogo delle impostazioni di Archiviazione di Azure](./media/roles-permissions-security/secured-storage-example.png): Monitoraggio di Azure viene considerato uno di tali "servizi Microsoft attendibili". Se si consente a tali servizi Microsoft attendibili di accedere alla risorsa di archiviazione protetta, Monitoraggio di Azure potrà accedere all'account di archiviazione protetto abilitando la scrittura dei log di risorsa, log attività e metriche di Monitoraggio di Azure nell'account di archiviazione in queste condizioni protette. Ciò consentirà anche a Log Analytics di leggere i log dalla risorsa di archiviazione protetta.   


Per altre informazioni, vedere [Sicurezza di rete di Archiviazione di Azure](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Passaggi successivi
* [Controllo degli accessi in base al ruolo e autorizzazioni in Resource Manager](../../role-based-access-control/overview.md)
* [Panoramica sul monitoraggio in Azure](../overview.md)

