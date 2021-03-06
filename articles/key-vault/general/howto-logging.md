---
title: Come abilitare la registrazione Azure Key Vault
description: Come abilitare la registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure fornito dall'utente.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 62035b2fe6c3db71e392a05946ea3f230dfa030e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604627"
---
# <a name="how-to-enable-key-vault-logging"></a>Come abilitare la registrazione di Key Vault

Dopo aver creato una o più insiemi di credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. Per informazioni dettagliate sulla funzionalità, vedere [Key Vault registrazione](logging.md).

Cosa viene registrato:

* Tutte le richieste API REST autenticate, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide.
* Operazioni sull'insieme di credenziali delle chiavi stesso, tra cui creazione, eliminazione e impostazione di criteri di accesso all'insieme di credenziali delle chiavi, nonché aggiornamento degli attributi dell'insieme di credenziali delle chiavi, quali i tag.
* Operazioni su chiavi e segreti nell'insieme di credenziali delle chiavi, tra cui:
  * Creazione, modifica o eliminazione di queste chiavi o segreti.
  * Firma, verifica, crittografia, decrittografia, wrapping e annullamento del wrapping delle chiavi, recupero di segreti ed elenco di chiavi e segreti (e delle relative versioni).
* Richieste non autenticate che generano una risposta 401. Alcuni esempi sono le richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.  
* Eventi di notifica di modifica di Griglia di eventi per i criteri di accesso all'insieme di credenziali delle chiavi vicini alla scadenza o scaduti (l'evento su nuove versioni non viene registrato). Gli eventi vengono registrati indipendentemente dalla disponibilità di una sottoscrizione di eventi creata nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Schema di eventi di Griglia di eventi per Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:

* Insieme di credenziali delle chiavi esistente e già in uso.  
* Ambiente [Azure cloud Shell](https://shell.azure.com) bash
* Spazio di archiviazione sufficiente in Azure per i log dell'insieme di credenziali delle chiavi.

I comandi di questa guida sono formattati per [cloud Shell](https://shell.azure.com) con bash come ambiente.

## <a name="connect-to-your-key-vault-subscription"></a>Connettersi alla sottoscrizione di Key Vault

Il primo passaggio per la configurazione della registrazione delle chiavi è la connessione alla sottoscrizione che contiene l'insieme di credenziali delle chiavi. Questo è particolarmente importante se sono presenti più sottoscrizioni associate all'account.

Con l'interfaccia della riga di comando di Azure è possibile visualizzare tutte le sottoscrizioni usando il comando [AZ account list](/cli/azure/account#az_account_list) e quindi connettersi a una usando [AZ account set](/cli/azure/account#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Con Azure PowerShell è possibile elencare prima le sottoscrizioni usando il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) e quindi connettersi a una usando il cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Creare un account di archiviazione per i log

Sebbene sia possibile usare un account di archiviazione esistente per i log, verrà creato un nuovo account di archiviazione dedicato ai log Key Vault. 

Per rendere la gestione ancora più facile, si userà anche lo stesso gruppo di risorse che contiene l'insieme di credenziali delle chiavi. Nella [Guida introduttiva dell'interfaccia](quick-create-cli.md) della riga di comando di Azure e [Azure PowerShell avvio rapido](quick-create-powershell.md)questo gruppo di risorse è denominato **myResourceGroup** e il percorso è *eastus*. Sostituire questi valori con quelli personalizzati, come applicabile. 

È anche necessario specificare un nome di account di archiviazione. I nomi degli account di archiviazione devono essere univoci, con una lunghezza compresa tra 3 e 24 caratteri e usare solo numeri e lettere minuscole.  Infine, verrà creato un account di archiviazione dello SKU "Standard_LRS".

Con l'interfaccia della riga di comando di Azure usare il comando [AZ storage account create](/cli/azure/storage/account#az_storage_account_create) . 

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Con Azure PowerShell, usare il cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Sarà necessario specificare il percorso corrispondente al gruppo di risorse.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

In entrambi i casi, prendere nota dell'"ID" dell'account di archiviazione. L'operazione dell'interfaccia della riga di comando di Azure restituisce l'ID nell'output. Per ottenere "ID" con Azure PowerShell, usare [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) e assegnare l'output a una variabile $SA. È quindi possibile visualizzare l'account di archiviazione con $sa. ID. ("$Sa. Verrà usata anche la proprietà Context, più avanti in questo articolo.

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

Il "ID" dell'account di archiviazione sarà nel formato "/subscriptions/<your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<il nome-Unique-storage-account-name>".

> [!NOTE]
> Se si decide di usare un account di archiviazione esistente, è necessario usare la stessa sottoscrizione dell'insieme di credenziali delle chiavi e usare il modello di distribuzione Azure Resource Manager, anziché il modello di distribuzione classica.

## <a name="obtain-your-key-vault-resource-id"></a>Ottenere l'ID risorsa dell'insieme di credenziali delle chiavi

Nella [Guida introduttiva dell'interfaccia](quick-create-cli.md) della riga di comando e in [PowerShell](quick-create-powershell.md)è stata creata una chiave con un nome univoco.  Usare nuovamente questo nome nei passaggi seguenti.  Se non è possibile ricordare il nome dell'insieme di credenziali delle chiavi, è possibile usare l'interfaccia della riga di comando di Azure [AZ Key Vault list](/cli/azure/keyvault#az_keyvault_list) oppure il cmdlet Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) per elencarli.

Usare il nome dell'insieme di credenziali delle chiavi per trovare il relativo ID risorsa.  Con l'interfaccia della riga di comando di Azure usare il comando [AZ Vault Show](/cli/azure/keyvault#az_keyvault_show) .

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Con Azure PowerShell, usare il cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) .

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

L'ID di risorsa per l'insieme di credenziali delle chiavi sarà nel formato "/subscriptions/<your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<Your-Unique-Key Vault-Name>". Si noti che per il passaggio successivo.

## <a name="enable-logging"></a>Abilitare la registrazione

È possibile abilitare la registrazione per Key Vault usando l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare l'interfaccia della riga di comando [AZ monitor Diagnostic-Settings create](/cli/azure/monitor/diagnostic-settings) con l'ID dell'account di archiviazione e l'ID della risorsa dell'insieme di credenziali delle chiavi.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Facoltativamente, è possibile impostare un criterio di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente dopo un periodo di tempo specificato. Ad esempio, è possibile impostare un criterio di conservazione che elimini automaticamente i log anteriori a 90 giorni.

Con l'interfaccia della riga di comando di Azure, usare il comando [AZ monitor Diagnostic-Settings Update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) . 

```azurecli-interactive
az monitor diagnostic-settings update --name "Key vault retention policy" --resource "<key-vault-resource-id>" --set retentionPolicy.days=90
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Usare il cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) con il flag **-Enabled** impostato su **$true** e la categoria impostata su `AuditEvent` (l'unica categoria per la registrazione Key Vault):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Facoltativamente, è possibile impostare un criterio di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente dopo un periodo di tempo specificato. Ad esempio, è possibile impostare un criterio di conservazione che elimini automaticamente i log anteriori a 90 giorni.

Con Azure PowerShell, usare il cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) .

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Per configurare le impostazioni di diagnostica nel portale, seguire questa procedura.

1. Selezionare le impostazioni di diagnostica dal menu del pannello della risorsa.

    :::image type="content" source="../media/diagnostics-portal-1.png" alt-text="Portale di diagnostica 1":::

1. Fare clic su "+ Aggiungi impostazione di diagnostica"

    :::image type="content" source="../media/diagnostics-portal-2.png" alt-text="Portale di diagnostica 2":::
 
1. Selezionare un nome per chiamare l'impostazione di diagnostica. Per configurare la registrazione per monitoraggio di Azure per Key Vault, selezionare l'opzione "AuditEvent" e "Invia a Log Analytics area di lavoro". Scegliere quindi la sottoscrizione e l'area di lavoro Log Analytics a cui si vogliono inviare i log.

    :::image type="content" source="../media/diagnostics-portal-3.png" alt-text="Portale di diagnostica 3":::

    In caso contrario, selezionare le opzioni relative ai log che si desidera selezionare

1. Dopo aver selezionato le opzioni desiderate, selezionare Salva.

    :::image type="content" source="../media/diagnostics-portal-4.png" alt-text="Portale di diagnostica 4":::

---

## <a name="access-your-logs"></a>Accedere ai log

Key Vault log vengono archiviati nel contenitore "Insights-logs-AuditEvent" nell'account di archiviazione specificato. Per visualizzare i log, è necessario scaricare i BLOB.

Per prima cosa, elencare tutti i BLOB nel contenitore.  Con l'interfaccia della riga di comando di Azure usare il comando [AZ storage BLOB list](/cli/azure/storage/blob#az_storage_blob_list) .

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Con Azure PowerShell, usare l'elenco [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) per tutti i BLOB in questo contenitore, immettere:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Come si vedrà dall'output del comando dell'interfaccia della riga di comando di Azure o dal cmdlet di Azure PowerShell, il nome dei BLOB è nel formato `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . I valori di data e ora sono nel formato UTC.

Poiché è possibile usare lo stesso account di archiviazione per raccogliere i log per più risorse, l'ID completo della risorsa nel nome del BLOB è utile per accedere solo ai BLOB necessari o per scaricarli. Prima di procedere, si vedrà però come scaricare tutti i BLOB.

Con l'interfaccia della riga di comando di Azure, usare il comando [AZ storage BLOB download](/cli/azure/storage/blob#az_storage_blob_download) , passare i nomi dei BLOB e il percorso del file in cui si desidera salvare i risultati.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Con Azure PowerShell, usare il cmdlet [gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob) per ottenere un elenco di BLOB, quindi inviarlo tramite pipe al cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) per scaricare i log nel percorso scelto.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Quando si esegue questo secondo cmdlet in PowerShell, il **/** delimitatore nei nomi dei BLOB crea una struttura di cartelle completa nella cartella di destinazione. Questa struttura verrà usata per scaricare e archiviare i BLOB come file.

Per scaricare BLOB in modo selettivo, usare caratteri jolly. Ad esempio:

* Se sono disponibili più insiemi di credenziali delle chiavi e si vogliono scaricare i log per un solo insieme di credenziali delle chiavi denominato CONTOSOKEYVAULT3, usare:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se sono disponibili più gruppi di risorse e si vogliono scaricare i log per un solo gruppo di risorse, usare `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Per scaricare tutti i log per il mese di gennaio 2019, usare `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

A questo punto si può iniziare a osservare il contenuto dei log. Prima di procedere, tuttavia, è necessario esaminare altri due comandi:

Per informazioni dettagliate su come leggere i log, vedere [Key Vault registrazione: interpretare i log di Key Vault](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Usare i log di Monitoraggio di Azure

È possibile usare la soluzione Key Vault nei log di Monitoraggio di Azure per esaminare i log `AuditEvent` di Key Vault. Nei log di Monitoraggio di Azure è possibile usare le query di log per analizzare i dati e ottenere le informazioni necessarie.

Per altre informazioni, anche su come configurare la soluzione, vedere [Azure Key Vault nei log di Monitoraggio di Azure](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni concettuali, incluse le modalità di interpretazione dei log Key Vault, vedere [Key Vault registrazione](logging.md)
- Per un'esercitazione sull'uso di Azure Key Vault in un'applicazione Web .NET, vedere [Usare Azure Key Vault da un'app Web](tutorial-net-create-vault-azure-web-app.md).
- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](developers-guide.md).
