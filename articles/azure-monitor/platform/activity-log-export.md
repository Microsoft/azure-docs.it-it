---
title: Esportare il log attività di Azure
description: Esportare il log attività di Azure nella risorsa di archiviazione per l'archiviazione o hub eventi di Azure per l'esportazione all'esterno di Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80396727"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Esportare il log attività di Azure nell'archiviazione o in hub eventi di Azure

> [!IMPORTANT]
> Il metodo per l'invio del log attività di Azure ad archiviazione di Azure e hub eventi di Azure è stato modificato in [impostazioni di diagnostica](diagnostic-settings.md). Questo articolo descrive il metodo legacy che è in fase di deprecazione. Per un confronto, vedere Aggiornamento per [raccogliere e analizzare i log attività di Azure in monitoraggio di Azure](activity-log-collect.md) .


Il [log attività di Azure](platform-logs-overview.md) fornisce informazioni sugli eventi a livello di sottoscrizione che si sono verificati nella sottoscrizione di Azure. Oltre a visualizzare il log attività nel portale di Azure o copiarlo in un'area di lavoro Log Analytics in cui può essere analizzato con altri dati raccolti da monitoraggio di Azure, è possibile creare un profilo di log per archiviare il log attività in un account di archiviazione di Azure o inviarlo a un hub eventi.

## <a name="archive-activity-log"></a>Archiviare il log attività
L'archiviazione del log attività in un account di archiviazione è utile se si desidera conservare i dati di log con una durata superiore a 90 giorni (con il controllo completo sui criteri di conservazione) per il controllo, l'analisi statica o il backup. Se è necessario conservare gli eventi solo per 90 giorni o meno, non è necessario configurare l'archiviazione in un account di archiviazione, perché gli eventi del log attività vengono conservati nella piattaforma Azure per 90 giorni.

## <a name="stream-activity-log-to-event-hub"></a>Trasmettere il log attività a hub eventi
[Hub eventi di Azure](/azure/event-hubs/) è una piattaforma di streaming di dati e un servizio di inserimento di eventi in grado di ricevere ed elaborare milioni di eventi al secondo. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. È possibile usare la funzionalità di streaming per il log attività in due modi:
* **Trasmettere a sistemi di telemetria e registrazione di terze parti** : in futuro, la funzionalità di trasmissione di Hub eventi di Azure diventerà il meccanismo di invio del log attività a soluzioni di analisi dei log e SIEM di terze parti.
* **Creare una piattaforma di telemetria e registrazione personalizzata** : se si ha già una piattaforma di telemetria personalizzata o si intende crearne una, le caratteristiche di pubblicazione-sottoscrizione altamente scalabili di Hub eventi consentono di inserire il log attività con la massima flessibilità.

## <a name="prerequisites"></a>Prerequisiti

### <a name="storage-account"></a>Account di archiviazione
Se si sta archiviando il log attività, è necessario [creare un account di archiviazione](../../storage/common/storage-account-create.md) , se non ne è già presente uno. Non usare un account di archiviazione esistente con altri dati non di monitoraggio archiviati, in modo da poter controllare meglio l'accesso ai dati di monitoraggio. Se si archiviano anche log e metriche in un account di archiviazione, è possibile scegliere di usare lo stesso account di archiviazione per conservare tutti i dati di monitoraggio in una posizione centrale.

L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della sottoscrizione che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni. 

> [!TIP]
> Vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) per fornire l'accesso a un account di archiviazione dietro una rete virtuale protetta.

### <a name="event-hubs"></a>Hub eventi
Se si sta inviando il log attività a un hub eventi, è necessario [creare un hub eventi](../../event-hubs/event-hubs-create.md) , se non è già presente. Se in precedenza sono stati trasmessi eventi del log attività a questo spazio dei nomi di hub eventi, l'hub eventi verrà riutilizzato.

Il criterio di accesso condiviso definisce le autorizzazioni per il meccanismo di trasmissione. Per lo streaming a hub eventi sono necessarie autorizzazioni di gestione, invio e ascolto. È possibile creare o modificare i criteri di accesso condiviso per lo spazio dei nomi di Hub eventi nel portale di Azure, nella scheda Configura relativa a tale spazio dei nomi.

Per aggiornare il profilo di log del log attività in modo da includere il flusso, è necessario disporre dell'autorizzazione ListKey per la regola di autorizzazione di hub eventi. Lo spazio dei nomi di Hub eventi non deve necessariamente trovarsi nella stessa sottoscrizione in cui vengono creati i log, purché l'utente che configura l'impostazione abbia l'accesso RBAC appropriato a entrambe le sottoscrizioni e queste ultime si trovino entrambe nello stesso tenant AAD.

Trasmettere il log attività a un hub eventi [creando un profilo di log](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Creare un profilo di log
Definire il modo in cui il log attività di Azure viene esportato usando un **profilo di log**. Ogni sottoscrizione di Azure può avere un solo profilo di log. Queste impostazioni possono essere configurate tramite l'opzione **Esporta** nel pannello log attività nel portale. oppure a livello di codice tramite l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx), i cmdlet di PowerShell o l'interfaccia della riga di comando.

Il profilo di log definisce gli elementi seguenti.

**Dove deve essere inviato il log attività.** Attualmente, le opzioni disponibili sono account di archiviazione o hub eventi.

**Le categorie di eventi da inviare.** Il significato della *categoria* nei profili di log e negli eventi del log attività è diverso. Nel profilo di log, *Category* rappresenta il tipo di operazione (Write, DELETE, Action). In un evento del log attività, la proprietà *Category*"* rappresenta l'origine o il tipo di evento (ad esempio, amministrazione, ServiceHealth e avviso).

**Quali aree (posizioni) devono essere esportate.** È necessario includere tutti i percorsi poiché molti eventi nel log attività sono eventi globali.

**Per quanto tempo il log attività deve essere mantenuto nell'account di archiviazione.** Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. In caso contrario, il valore può essere un numero qualsiasi di giorni compreso tra 1 e 365.

Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, i criteri di conservazione non hanno alcun effetto. I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione.


> [!IMPORTANT]
> Se il provider di risorse Microsoft. Insights non è registrato, è possibile che venga visualizzato un errore durante la creazione di un profilo di log. Per registrare questo provider [, vedere provider e tipi di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md) .


### <a name="create-log-profile-using-the-azure-portal"></a>Creare un profilo di log usando il portale di Azure

Creare o modificare un profilo di log con l'opzione **Esporta in hub eventi** nel portale di Azure.

1. Dal menu **monitoraggio di Azure** nella portale di Azure selezionare **log attività**.
3. Fare clic su **impostazioni di diagnostica**.

   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Fare clic sul banner viola per l'esperienza legacy.

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)

3. Nel pannello che viene visualizzato specificare quanto segue:
   * Aree con gli eventi da esportare. È necessario selezionare tutte le aree per assicurarsi di non perdere gli eventi chiave perché il log attività è un log globale (non regionale), quindi la maggior parte degli eventi non dispone di un'area associata.
   * Se si vuole scrivere nell'account di archiviazione:
       * L'account di archiviazione in cui si desidera salvare gli eventi.
       * Il numero di giorni per cui si vogliono conservare questi eventi nell'archivio. (se si impostano 0 giorni, i log vengono conservati all'infinito)
   * Se si vuole scrivere nell'hub eventi:
       * Lo spazio dei nomi del bus di servizio in cui si vuole creare un hub eventi per lo streaming di questi eventi.

     ![Pannello Esporta log di controllo](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Fare clic su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.


### <a name="configure-log-profile-using-powershell"></a>Configurare il profilo di log con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se esiste già un profilo di log, prima di tutto è necessario rimuovere il profilo di log esistente e quindi crearne uno nuovo.

1. Usare `Get-AzLogProfile` per determinare se esiste già un profilo di log.  Se esiste un profilo di log, annotare la proprietà *Name* .

1. Usare `Remove-AzLogProfile` per rimuovere il profilo di log usando il valore della proprietà *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Usare `Add-AzLogProfile` per creare un nuovo profilo di log:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Proprietà | Obbligatoria | Descrizione |
    | --- | --- | --- |
    | Nome |Sì |Nome del profilo di log. |
    | StorageAccountId |No |ID risorsa dell'account di archiviazione in cui deve essere salvato il log attività. |
    | serviceBusRuleId |No |ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui creare gli hub eventi. Si tratta di una stringa nel formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Percorso |Sì |Elenco delimitato da virgole di aree per cui raccogliere eventi del log attività. |
    | RetentionInDays |Sì |Numero di giorni per cui gli eventi devono essere conservati nell'account di archiviazione, tra 1 e 365. Se il valore è zero, i log vengono conservati all'infinito. |
    | Category |No |Elenco delimitato da virgole di categorie di eventi che devono essere raccolti. I valori possibili sono _Write_, _Delete_e _Action_. |

### <a name="example-script"></a>Script di esempio
Di seguito è riportato uno script di PowerShell di esempio per creare un profilo di log che scrive il log attività in un account di archiviazione e in un hub eventi.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurare il profilo di log tramite l'interfaccia della riga di comando

Se esiste già un profilo di log, è innanzitutto necessario rimuovere il profilo di log esistente e quindi crearne uno nuovo.

1. Usare `az monitor log-profiles list` per determinare se esiste già un profilo di log.
2. Usare `az monitor log-profiles delete --name "<log profile name>` per rimuovere il profilo di log usando il valore della proprietà *name*.
3. Usare `az monitor log-profiles create` per creare un nuovo profilo di log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Proprietà | Obbligatoria | Descrizione |
    | --- | --- | --- |
    | name |Sì |Nome del profilo di log. |
    | storage-account-id |Sì |ID risorsa dell'account di archiviazione in cui salvare i log attività. |
    | locations |Sì |Elenco delimitato da spazi di aree per cui raccogliere eventi del log attività. È possibile visualizzare un elenco di tutte le aree per la sottoscrizione tramite `az account list-locations --query [].name`. |
    | days |Sì |Numero di giorni per cui devono essere conservati gli eventi, tra 1 e 365. Se il valore è zero, i log vengono archiviati per un periodo illimitato.  Se è zero, il parametro Enabled deve essere impostato su false. |
    |Enabled | Sì |True o False.  Consente di abilitare o disabilitare i criteri di conservazione.  Se True, il parametro days deve essere un valore maggiore di 0.
    | Categorie |Sì |Elenco delimitato da spazi di categorie di eventi che devono essere raccolti. I valori possibili sono Write, Delete e Action. |



## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul log attività](../../azure-resource-manager/management/view-activity-logs.md)
* [Raccogliere log attività nei log di monitoraggio di Azure](activity-log-collect.md)
