---
title: Creare una condivisione file di Azure
titleSuffix: Azure Files
description: Come creare una condivisione file di Azure usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 24bee926d84c7a5be3f19c39d39285c2cd486824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211023"
---
# <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Per creare una condivisione file di Azure, è necessario rispondere a tre domande sul modo in cui verrà usata:

- **Quali sono i requisiti di prestazioni per la condivisione file di Azure?**  
    File di Azure offre condivisioni file standard ospitate su hardware basato su disco rigido (basato su HDD) e condivisioni file Premium, che sono ospitate su hardware a stato solido basato su disco (basato su SSD).

- **Quali sono i requisiti di ridondanza per la condivisione file di Azure?**  
    Le condivisioni file standard offrono l'archiviazione con ridondanza locale (con ridondanza locale), con ridondanza della zona (ZRS), con ridondanza geografica (GRS) o con ridondanza geografica (GZRS), tuttavia la funzionalità di condivisione file di grandi dimensioni è supportata solo nelle condivisioni file con ridondanza locale e con ridondanza della zona. Le condivisioni file Premium non supportano alcuna forma di ridondanza geografica.

    Le condivisioni file Premium sono disponibili con ridondanza locale e ridondanza della zona in un subset di aree. Per scoprire se le condivisioni file Premium sono attualmente disponibili nella propria area geografica, vedere la pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=storage) per Azure. Per informazioni sulle aree che supportano ZRS, vedere [ridondanza di archiviazione di Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

- **Quale dimensione è necessaria per la condivisione file?**  
    Negli account di archiviazione con ridondanza locale e di zona, le condivisioni file di Azure possono estendersi fino a 100 TiB, tuttavia negli account di archiviazione con ridondanza geografica e area geografica, le condivisioni file di Azure possono estendersi solo fino a 5 

Per ulteriori informazioni su queste tre scelte, vedere [pianificazione di una distribuzione di file di Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Prerequisiti
- In questo articolo si presuppone che sia già stata creata una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Se si intende usare Azure PowerShell, [installare l'ultima versione](/powershell/azure/install-az-ps).
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Le condivisioni file di Azure vengono distribuite in *account di archiviazione*, ovvero oggetti di primo livello che rappresentano un pool di archiviazione condiviso. Questo pool di archiviazione può essere usato per distribuire più condivisioni file. 

Azure supporta più tipi di account di archiviazione per diversi scenari di archiviazione che i clienti possono avere, ma esistono due tipi principali di account di archiviazione per File di Azure. Il tipo di account di archiviazione che è necessario creare varia a seconda che si desideri creare una condivisione file standard o una condivisione file Premium: 

- **Account di archiviazione per utilizzo generico v2**: gli account di archiviazione per utilizzo generico v2 consentono di distribuire condivisioni file di Azure in hardware standard o basato su disco rigido (HDD). Oltre a archiviare le condivisioni file di Azure, gli account di archiviazione per utilizzo generico v2 possono archiviare altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. Le condivisioni file possono essere distribuite nei livelli di transazione ottimizzata (impostazione predefinita), ad accesso frequente o ad accesso sporadico.

- **Account di archiviazione FileStorage**: gli account di archiviazione FileStorage consentono di distribuire condivisioni file di Azure in hardware Premium o basato su unità SSD. Gli account FileStorage possono essere usati solo per archiviare le condivisioni file di Azure. Non è infatti possibile distribuire altre risorse di archiviazione (contenitori BLOB, code, tabelle e così via) in un account FileStorage.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un account di archiviazione tramite il portale di Azure, selezionare **+ Crea una risorsa** dal dashboard. Nella finestra di ricerca di Azure Marketplace risultante cercare **account di archiviazione** e selezionare il risultato della ricerca risultante. Verrà quindi illustrata una pagina di panoramica per gli account di archiviazione. Selezionare **Crea** per procedere con la creazione guidata dell'account di archiviazione.

![Screenshot dell'opzione di creazione rapida dell'account di archiviazione in un browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Nozioni di base
La prima sezione da completare per creare un account di archiviazione è denominata **nozioni di base**. Contiene tutti i campi obbligatori per creare un account di archiviazione. Per creare un account di archiviazione GPv2, verificare che il pulsante di opzione **prestazioni** sia impostato su *standard* e che l'elenco a discesa **tipo di account** sia selezionato per *archiviazione V2 (utilizzo generico v2)*.

![Screenshot del pulsante di opzione prestazioni con tipo di account e selezionato standard con archiviazione V2 selezionato](media/storage-how-to-create-file-share/create-storage-account-1.png)

Per creare un account di archiviazione filestorage, verificare che il pulsante di opzione **prestazioni** sia impostato su *Premium* e che l'elenco a discesa **tipo di account** sia selezionato per *filestorage*.

![Screenshot del pulsante di opzione prestazioni con l'opzione Premium selezionata e il tipo di account con filestorage selezionato](media/storage-how-to-create-file-share/create-storage-account-2.png)

Gli altri campi di base sono indipendenti dalla scelta dell'account di archiviazione:
- **Nome dell'account di archiviazione**: il nome della risorsa dell'account di archiviazione da creare. Questo nome deve essere globalmente univoco, ma in caso contrario può avere qualsiasi nome desiderato. Il nome dell'account di archiviazione verrà usato come nome del server quando si monta una condivisione file di Azure tramite SMB.
- **Location**: area per l'account di archiviazione in cui eseguire la distribuzione. Può trattarsi dell'area associata al gruppo di risorse o di qualsiasi altra area disponibile.
- **Replica**: Sebbene sia etichettata come replica, questo campo significa effettivamente **ridondanza**; Questo è il livello di ridondanza desiderato: ridondanza locale (con ridondanza locale), ridondanza della zona (ZRS), ridondanza geografica (GRS) e ridondanza della zona geografica (GZRS). Questo elenco a discesa contiene anche la ridondanza geografica e accesso in lettura (RA-GRS) e la ridondanza della zona geografica con accesso in lettura (RA-GZRS), che non si applicano alle condivisioni file di Azure. tutte le condivisioni file create in un account di archiviazione con questi oggetti selezionati saranno in realtà con ridondanza geografica o con ridondanza della zona geografica, rispettivamente. 

#### <a name="networking"></a>Rete
La sezione rete consente di configurare le opzioni di rete. Queste impostazioni sono facoltative per la creazione dell'account di archiviazione e possono essere configurate in un secondo momento, se necessario. Per ulteriori informazioni su queste opzioni, vedere [file di Azure considerazioni sulla rete](storage-files-networking-overview.md).

#### <a name="data-protection"></a>Protezione dei dati
La sezione protezione dei dati consente di configurare i criteri di eliminazione temporanea per le condivisioni file di Azure nell'account di archiviazione. Altre impostazioni correlate all'eliminazione temporanea per i BLOB, i contenitori, il ripristino temporizzato per i contenitori, il controllo delle versioni e il feed delle modifiche si applicano solo all'archivio BLOB di Azure.

#### <a name="advanced"></a>Avanzato
La sezione Advanced contiene alcune importanti impostazioni per le condivisioni file di Azure:

- **Trasferimento sicuro obbligatorio**: questo campo indica se l'account di archiviazione richiede la crittografia in transito per la comunicazione con l'account di archiviazione. Se è necessario il supporto SMB 2,1, è necessario disabilitare questa operazione.
- **Condivisioni file di grandi dimensioni**: questo campo Abilita l'account di archiviazione per le condivisioni file estese fino a 100 tib. L'abilitazione di questa funzionalità limiterà l'account di archiviazione alle opzioni di archiviazione con ridondanza locale e con ridondanza della zona. Dopo che un account di archiviazione GPv2 è stato abilitato per le condivisioni file di grandi dimensioni, non è possibile disabilitare la funzionalità di condivisione file di grandi dimensioni. Gli account di archiviazione filestorage (account di archiviazione per le condivisioni file Premium) non hanno questa opzione, perché tutte le condivisioni file Premium possono essere scalate fino a 100 TiB. 

![Screenshot delle impostazioni avanzate principali che si applicano a File di Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

Le altre impostazioni disponibili nella scheda Avanzate (spazio dei nomi gerarchico per Azure Data Lake storage gen 2, livello BLOB predefinito, NFSv3 per l'archiviazione BLOB e così via) non si applicano ai File di Azure.

> [!Important]  
> La selezione del livello di accesso BLOB non influisce sul livello della condivisione file.

#### <a name="tags"></a>Tag
I tag sono coppie nome-valore che consentono di classificare le risorse e visualizzare dati di fatturazione consolidati tramite l'applicazione dello stesso tag a più risorse e gruppi di risorse. Si tratta di un parametro facoltativo che può essere applicato dopo la creazione dell'account di archiviazione.

#### <a name="review--create"></a>Rivedi e crea
Il passaggio finale per creare l'account di archiviazione consiste nel selezionare il pulsante **Crea** nella scheda **Verifica + crea** . Questo pulsante non sarà disponibile se tutti i campi obbligatori per un account di archiviazione non vengono riempiti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per creare un account di archiviazione con PowerShell, si userà il `New-AzStorageAccount` cmdlet. Questo cmdlet dispone di numerose opzioni. vengono visualizzate solo le opzioni obbligatorie. Per ulteriori informazioni sulle opzioni avanzate, vedere la [ `New-AzStorageAccount` documentazione del cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Per semplificare la creazione dell'account di archiviazione e della successiva condivisione file, si archivieranno diversi parametri nelle variabili. È possibile sostituire il contenuto della variabile con qualsiasi valore desiderato. si noti tuttavia che il nome dell'account di archiviazione deve essere globalmente univoco.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Per creare un account di archiviazione in grado di archiviare condivisioni file di Azure standard, viene usato il comando seguente. Il `-SkuName` parametro è correlato al tipo di ridondanza desiderato; se si desidera un account di archiviazione con ridondanza geografica o con ridondanza geografica, è necessario rimuovere anche il `-EnableLargeFileShare` parametro.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Per creare un account di archiviazione in grado di archiviare le condivisioni file di Azure Premium, viene usato il comando seguente. Si noti che il `-SkuName` parametro è stato modificato in modo da includere sia sia `Premium` il livello di ridondanza desiderato di ridondanza locale ( `LRS` ). Il `-Kind` parametro è `FileStorage` anziché `StorageV2` perché è necessario creare condivisioni file Premium in un account di archiviazione filestorage anziché in un account di archiviazione GPv2.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per creare un account di archiviazione usando l'interfaccia della riga di comando di Azure, si userà il comando AZ storage account create. Questo comando ha molte opzioni. vengono visualizzate solo le opzioni obbligatorie. Per ulteriori informazioni sulle opzioni avanzate, vedere la [ `az storage account create` documentazione del comando](/cli/azure/storage/account).

Per semplificare la creazione dell'account di archiviazione e della successiva condivisione file, si archivieranno diversi parametri nelle variabili. È possibile sostituire il contenuto della variabile con qualsiasi valore desiderato. si noti tuttavia che il nome dell'account di archiviazione deve essere globalmente univoco.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Per creare un account di archiviazione in grado di archiviare condivisioni file di Azure standard, viene usato il comando seguente. Il `--sku` parametro è correlato al tipo di ridondanza desiderato; se si desidera un account di archiviazione con ridondanza geografica o con ridondanza geografica, è necessario rimuovere anche il `--enable-large-file-share` parametro.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Per creare un account di archiviazione in grado di archiviare le condivisioni file di Azure Premium, viene usato il comando seguente. Si noti che il `--sku` parametro è stato modificato in modo da includere sia sia `Premium` il livello di ridondanza desiderato di ridondanza locale ( `LRS` ). Il `--kind` parametro è `FileStorage` anziché `StorageV2` perché è necessario creare condivisioni file Premium in un account di archiviazione filestorage anziché in un account di archiviazione GPv2.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Creare la condivisione file
Dopo aver creato l'account di archiviazione, è possibile creare la condivisione file. Questo processo è essenzialmente lo stesso, indipendentemente dal fatto che si stia usando una condivisione file Premium o una condivisione file standard. È necessario considerare le differenze seguenti.

Le condivisioni file standard possono essere distribuite in uno dei livelli standard: Transaction Optimized (default), Hot o cool. Si tratta di un livello per condivisione file che non è influenzato dal **livello di accesso BLOB** dell'account di archiviazione. questa proprietà è relativa solo all'archiviazione BLOB di Azure, ma non è correlata a file di Azure. È possibile modificare il livello della condivisione in qualsiasi momento dopo la distribuzione. Le condivisioni file Premium non possono essere convertite direttamente in un livello standard.

> [!Important]  
> È possibile spostare le condivisioni file tra livelli all'interno di tipi di account di archiviazione GPv2 (ottimizzato per le transazioni, ad accesso frequente e ad accesso sporadico). Gli spostamenti delle condivisioni tra livelli comportano transazioni: il passaggio da un livello ad accesso più frequente a uno ad accesso più sporadico comporta l'addebito per le transazioni di scrittura del livello più sporadico per ogni file della condivisione, mentre un passaggio da un livello più sporadico a uno più frequente comporterà l'addebito delle transazioni di lettura del livello ad accesso sporadico per ogni file della condivisione.

La proprietà **quota** indica un valore leggermente diverso tra le condivisioni file Premium e standard:

- Per le condivisioni file standard, si tratta di un limite superiore della condivisione file di Azure, oltre il quale gli utenti finali non possono andare. Se non si specifica una quota, la condivisione file standard può estendersi fino a 100 TiB (o 5 TiB se la proprietà large file Shares non è impostata per un account di archiviazione).

- Per le condivisioni file Premium, quota indica le **dimensioni con provisioning**. Le dimensioni di cui è stato effettuato il provisioning corrispondono alla quantità per la quale verrà fatturato, indipendentemente dall'utilizzo effettivo. Per altre informazioni su come pianificare una condivisione file Premium, vedere [provisioning di condivisioni file Premium](understanding-billing.md#provisioned-model).

# <a name="portal"></a>[Portale](#tab/azure-portal)
Se è stato appena creato l'account di archiviazione, è possibile accedervi dalla schermata di distribuzione selezionando **Vai a risorsa**. Una volta nell'account di archiviazione, selezionare il riquadro **condivisioni file** con etichetta (è anche possibile passare alle **condivisioni file** tramite il sommario per l'account di archiviazione).

![Screenshot del riquadro condivisioni file](media/storage-how-to-create-file-share/create-file-share-1.png)

Nell'elenco delle condivisioni file dovrebbero essere visualizzate tutte le condivisioni file create in precedenza in questo account di archiviazione. una tabella vuota se non sono ancora state create condivisioni file. Selezionare **+ condivisione file** per creare una nuova condivisione file.

Il pannello nuova condivisione file verrà visualizzato sullo schermo. Completare i campi nel pannello nuova condivisione file per creare una condivisione file:

- **Nome**: il nome della condivisione file da creare.
- **Quota**: la quota della condivisione file per le condivisioni file standard. dimensioni del provisioning della condivisione file per le condivisioni file Premium.
- **Livelli**: il livello selezionato per una condivisione file. Questo campo è disponibile solo in un **account di archiviazione per utilizzo generico (GPv2)**. È possibile scegliere transazione ottimizzata, ad accesso frequente o ad accesso sporadico. Il livello della condivisione può essere modificato in qualsiasi momento. È consigliabile scegliere il livello più caldo possibile durante una migrazione, per ridurre al minimo le spese di transazione e quindi passare a un livello inferiore, se necessario, al termine della migrazione.

Selezionare **Crea** per completare la creazione della nuova condivisione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
È possibile creare una condivisione file di Azure con il [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet. I comandi di PowerShell riportati di seguito presuppongono che siano state impostate le variabili `$resourceGroupName` e `$storageAccountName` come definito in precedenza nella sezione Creazione di un account di archiviazione con Azure PowerShell. 

Nell'esempio seguente viene illustrata la creazione di una condivisione file con un livello esplicito utilizzando il `-AccessTier` parametro. A tale scopo, è necessario usare il modulo di anteprima AZ. storage come indicato nell'esempio. Se non si specifica un livello, perché si sta usando il modulo GA AZ. storage o perché non è stato incluso questo comando, il livello predefinito per le condivisioni file standard è ottimizzato per le transazioni.

> [!Important]  
> Per le condivisioni file Premium, il `-QuotaGiB` parametro fa riferimento alla dimensione con provisioning della condivisione file. La dimensione di cui è stato effettuato il provisioning della condivisione file è la quantità per la quale verrà fatturato, indipendentemente dall'utilizzo. Le condivisioni file standard vengono fatturate in base all'utilizzo anziché alle dimensioni con provisioning.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
È possibile creare una condivisione file di Azure con il [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) comando. I comandi dell'interfaccia della riga di comando di Azure seguenti presuppongono che siano state impostate le variabili `$resourceGroupName` e `$storageAccountName` come definito sopra nella sezione Creazione di un account di archiviazione con l'interfaccia della riga

> [!Important]  
> Per le condivisioni file Premium, il `--quota` parametro fa riferimento alla dimensione con provisioning della condivisione file. La dimensione di cui è stato effettuato il provisioning della condivisione file è la quantità per la quale verrà fatturato, indipendentemente dall'utilizzo. Le condivisioni file standard vengono fatturate in base all'utilizzo anziché alle dimensioni con provisioning.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per informazioni dettagliate su come denominare le condivisioni e i file, vedere [Naming and referencing shares, directories, files, and metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Denominazione e riferimento a condivisioni, directory, file e metadati).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Modifica del livello di una condivisione file di Azure
Le condivisioni file distribuite nell' **account di archiviazione per utilizzo generico V2 (GPv2)** possono trovarsi nei livelli di transazione ottimizzata, ad accesso frequente o sporadico. È possibile modificare il livello della condivisione file di Azure in qualsiasi momento, in base ai costi delle transazioni, come descritto in precedenza.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Nella pagina principale dell'account di archiviazione selezionare **condivisioni file**  selezionare il riquadro **condivisioni file** con etichetta. è anche possibile passare a **condivisioni file** tramite il sommario per l'account di archiviazione.

![Screenshot del riquadro condivisioni file](media/storage-how-to-create-file-share/create-file-share-1.png)

Nell'elenco tabella delle condivisioni file selezionare la condivisione file per cui si desidera modificare il livello. Nella pagina Panoramica della condivisione file selezionare **modifica livello** dal menu.

![Screenshot della pagina Panoramica della condivisione file con il pulsante Cambia livello evidenziato](media/storage-how-to-create-file-share/change-tier-0.png)

Nella finestra di dialogo risultante selezionare il livello desiderato: transazione ottimizzata, a caldo o ad accesso sporadico.

![Screenshot della finestra di dialogo del livello di modifica](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Il cmdlet di PowerShell seguente presuppone che siano state impostate `$resourceGroupName` le `$storageAccountName` variabili,, `$shareName` come descritto nelle sezioni precedenti di questo documento.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Il comando dell'interfaccia della riga di comando di Azure seguente presuppone che siano state impostate le `$resourceGroupName` `$storageAccountName` variabili, e, `$shareName` come descritto nelle sezioni precedenti di questo documento.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di file di Azure](storage-files-planning.md) o [pianificare una distribuzione di sincronizzazione file di Azure](storage-sync-files-planning.md). 
- [Panoramica sulla rete](storage-files-networking-overview.md).
- Connettere e montare una condivisione file in [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md).