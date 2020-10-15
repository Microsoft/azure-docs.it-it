---
title: Azure Key Vault - Come usare la funzionalità di eliminazione temporanea con l'interfaccia della riga di comando
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per usare la funzionalità di eliminazione temporanea di Azure Key Vault che consente il ripristino degli insiemi di credenziali delle chiavi e degli oggetti dell'insieme di credenziali delle chiavi.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.openlocfilehash: da821da08594180b9dd94728252e1a43c04fbde2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531662"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando

La funzionalità di eliminazione temporanea di Azure Key Vault consente il recupero di insiemi di credenziali e di oggetti di insiemi di credenziali eliminati. In particolare, l'eliminazione temporanea viene applicata negli scenari seguenti:

- Supporto per l'eliminazione reversibile di un insieme di credenziali delle chiavi
- Supporto per l'eliminazione reversibile di oggetti di insiemi di credenziali delle chiavi: chiavi, segreti e certificati

## <a name="prerequisites"></a>Prerequisiti

- Interfaccia della riga di comando di Azure: se non è ancora installata nell'ambiente di lavoro, vedere [Gestire Key Vault tramite l'interfaccia della riga di comando di Azure](manage-with-cli2.md).

Per informazioni sui comandi dell'interfaccia della riga di comando relativi a Key Vault, vedere la [Documentazione sull'interfaccia della riga di comando di Azure per Key Vault](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Le operazioni di Key Vault vengono gestite separatamente tramite autorizzazioni del controllo degli accessi in base al ruolo, come indicato di seguito:

| Operazione | Descrizione | Autorizzazione utente |
|:--|:--|:--|
|Elenco|Elenca gli insiemi di credenziali delle chiavi eliminati.|Microsoft.KeyVault/deletedVaults/read|
|Recupera|Recupera un insieme di credenziali delle chiavi eliminato.|Microsoft.KeyVault/vaults/write|
|Ripulisci|Rimuove in modo permanente un insieme di credenziali delle chiavi eliminato e tutti i relativi contenuti.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Per altre informazioni sulle autorizzazioni e il controllo degli accessi, vedere [Proteggere l'insieme di credenziali delle chiavi](secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Abilitazione della funzione di eliminazione temporanea

La funzione di eliminazione temporanea viene abilitata per consentire il recupero di un insieme di credenziali delle chiavi eliminato o degli oggetti archiviati in un insieme di credenziali delle chiavi.

> [!IMPORTANT]
> L'abilitazione dell'eliminazione temporanea per un insieme di credenziali delle chiavi è un'azione irreversibile. Dopo che la proprietà di eliminazione temporanea è stata impostata su "true", non può essere modificata o rimossa.  

### <a name="existing-key-vault"></a>Insieme di credenziali delle chiavi esistente

Per un insieme di credenziali delle chiavi esistente denominato ContosoVault, è possibile abilitare l'eliminazione temporanea come indicato di seguito. 

```azurecli
az keyvault update -n ContosoVault --enable-soft-delete true
```

### <a name="new-key-vault"></a>Insieme di credenziali delle chiavi nuovo

L'eliminazione temporanea è abilitata automaticamente in tutti gli insiemi di credenziali delle chiavi per impostazione predefinita. A partire dal 31 dicembre 2020 non sarà più possibile creare un nuovo insieme di credenziali delle chiavi senza l'eliminazione temporanea abilitata.

### <a name="verify-soft-delete-enablement"></a>Verificare l'abilitazione della funzione di eliminazione temporanea

Per verificare che in un insieme di credenziali delle chiavi sia abilitata la funzione di eliminazione temporanea, eseguire il comando *show* e controllare se l'attributo "Soft Delete Enabled?" ("Eliminazione temporanea abilitata?") è presente:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Eliminazione di un insieme di credenziali delle chiavi protetto dalla funzione di eliminazione temporanea

Il comportamento del comando per eliminare un insieme di credenziali delle chiavi è diverso a seconda che l'eliminazione temporanea sia abilitata o meno.

> [!IMPORTANT]
>Se si esegue il comando seguente per un insieme di credenziali delle chiavi in cui la funzione di eliminazione temporanea non è abilitata, l'insieme di credenziali delle chiavi e tutti i relativi contenuti verranno eliminati in modo permanente, senza possibilità di recupero.

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Come la funzione di eliminazione temporanea protegge gli insiemi di credenziali delle chiavi

Con la funzione di eliminazione temporanea abilitata:

- Un insieme di credenziali delle chiavi viene rimosso dal relativo gruppo di risorse e inserito in uno spazio dei nomi riservato associato al percorso in cui è stato creato. 
- Gli oggetti eliminati, come chiavi, segreti e certificati, risultano non accessibili finché l'insieme di credenziali delle chiavi in cui sono contenuti è in stato "eliminato". 
- Il nome DNS per un insieme di credenziali delle chiavi eliminato è riservato e non è quindi possibile creare un nuovo insieme di credenziali delle chiavi con lo stesso nome.  

È possibile visualizzare gli insiemi di credenziali delle chiavi in stato "eliminato" associati alla propria sottoscrizione usando il comando seguente:

```azurecli
az keyvault list-deleted
```
- L'*ID* può essere usato per identificare la risorsa durante il recupero o l'eliminazione definitiva. 
- L'*ID risorsa* è l'ID risorsa originale di questo insieme di credenziali. Poiché l'insieme di credenziali delle chiavi è ora in stato "eliminato", non è presente alcuna risorsa con questo ID. 
- La *data di eliminazione definitiva programmata* indica il momento in cui l'insieme di credenziali verrà eliminato in modo definitivo se non viene eseguita alcuna operazione. Il periodo di memorizzazione predefinito usato per calcolare il campo *Scheduled Purge Date* (Data eliminazione definitiva programmata) è di 90 giorni.

## <a name="recovering-a-key-vault"></a>Recupero di un insieme di credenziali delle chiavi

Per recuperare un insieme di credenziali delle chiavi, si specifica il nome, il gruppo di risorse e il percorso. Annotare il percorso e il gruppo di risorse dell'insieme di credenziali delle chiavi eliminato, perché saranno necessari nel processo di recupero.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Quando viene recuperato un insieme di credenziali delle chiavi, viene creata una nuova risorsa con l'ID risorsa originale dell'insieme di credenziali delle chiavi. Se il gruppo di risorse originale viene rimosso, ne deve essere creato uno con lo stesso nome prima di tentare il recupero.

## <a name="deleting-and-purging-key-vault-objects"></a>Eliminazione e pulizia di oggetti di insiemi di credenziali delle chiavi

Il comando seguente elimina la chiave 'ContosoFirstKey' in un insieme di credenziali delle chiavi denominato 'ContosoVault' in cui è abilitata l'eliminazione temporanea:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Se in un insieme di credenziali delle chiavi è abilitata la funzione di eliminazione temporanea, una chiave eliminata continua a essere visualizzata come eliminata a meno che non venga elencata o recuperata in modo esplicito. La maggior parte delle operazioni eseguite su una chiave in stato "eliminato" ha esito negativo. Una chiave eliminata, infatti, può essere solo elencata, recuperata o eliminata in modo definitivo. 

Per richiedere di compilare un elenco delle chiavi eliminate in un insieme di credenziali delle chiavi, usare il comando seguente:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Stato di transizione 

Quando si elimina una chiave in un insieme di credenziali delle chiavi con eliminazione temporanea abilitata, per completare la transizione possono essere necessari alcuni secondi. Durante questa transizione, è possibile che la chiave non risulti in stato attivo o in stato eliminato. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilizzo della funzione di eliminazione temporanea con gli oggetti di un insieme di credenziali delle chiavi

Analogamente agli insiemi di credenziali delle chiavi, anche una chiave, un segreto o un certificato eliminato rimane in stato "eliminato" per un massimo di 90 giorni, a meno che non si scelga di recuperarlo o eliminarlo in modo definitivo.

#### <a name="keys"></a>Chiavi

Per recuperare una chiave eliminata temporaneamente:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Per eliminare definitivamente (ripulire) una chiave eliminata temporaneamente:

> [!IMPORTANT]
> Dopo essere stata eliminata in modo definitivo, una chiave non è più recuperabile. 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

Alle azioni di **recupero** e **pulizia** sono associate autorizzazioni specifiche nei criteri di accesso dell'insieme di credenziali delle chiavi. Per poter eseguire un'azione **recover** o **purge**, quindi, un utente o un'entità servizio deve avere la relativa autorizzazione per la chiave o il segreto. Per impostazione predefinita, **purge** non viene aggiunta ai criteri di accesso dell'insieme di credenziali delle chiavi se viene usato il collegamento "all" per concedere tutte le autorizzazioni. L'autorizzazione **purge** deve essere concessa esplicitamente. 

#### <a name="set-a-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Il comando seguente, concede a user@contoso.com l'autorizzazione per usare diverse operazioni sulle chiavi in *ContosoVault*, tra cui **purge**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Se si ha un insieme di credenziali delle chiavi in cui è stata appena abilitata la funzione di eliminazione temporanea, è possibile che non si disponga delle autorizzazioni di **recupero** ed **eliminazione definitiva**.

#### <a name="secrets"></a>Segreti

Come le chiavi, i segreti vengono gestiti con comandi specifici:

- Eliminare un segreto denominato SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Elencare tutti i segreti eliminati in un insieme di credenziali delle chiavi: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Ripristinare un segreto in stato "eliminato": 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Eliminare in modo definitivo un segreto in stato "eliminato": 

  > [!IMPORTANT]
  > Dopo essere stato eliminato in modo definitivo, un segreto non è più recuperabile. 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Pulizia di un insieme di credenziali delle chiavi protetto dalla funzione di eliminazione temporanea

> [!IMPORTANT]
> Dopo l'operazione di pulizia, un insieme di credenziali delle chiavi o un oggetto dell'insieme non è più recuperabile.

La funzione di pulizia viene usata per eliminare in modo permanente un oggetto dell'insieme di credenziali delle chiavi, o un intero insieme, che in precedenza è stato eliminato temporaneamente. Come illustrato nella sezione precedente, gli oggetti archiviati in un insieme di credenziali delle chiavi con la funzionalità di eliminazione temporanea abilitata, possono passare attraverso più stati:

- **Attivo**: prima dell'eliminazione.
- **Eliminato temporaneamente**: dopo l'eliminazione, può essere recuperato e ripristinato allo stato attivo.
- **Eliminato definitivamente**: dopo la pulizia, non può più essere recuperato.

Lo stesso vale per l'insieme di credenziali delle chiavi. Per eliminare in modo definitivo un insieme di credenziali delle chiavi eliminato temporaneamente e il relativo contenuto, è necessario ripulire l'intero insieme di credenziali delle chiavi.

### <a name="purging-a-key-vault"></a>Pulizia di un insieme di credenziali delle chiavi

Quando un insieme di credenziali delle chiavi viene ripulito, ne viene eliminato in modo definitivo l'intero contenuto, inclusi certificati, chiavi e segreti. Per ripulire un insieme di credenziali delle chiavi eliminato temporaneamente, usare il comando `az keyvault purge`. È possibile trovare il percorso degli insiemi di credenziali delle chiavi eliminati nella sottoscrizione usando il comando `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Autorizzazioni di eliminazione definitiva necessarie
- Per ripulire un insieme di credenziali delle chiavi eliminato, l'utente necessita dell'autorizzazione di Controllo degli accessi in base al ruolo per l'operazione *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Per elencare un insieme di credenziali delle chiavi eliminato, l'utente necessita dell'autorizzazione di Controllo degli accessi in base al ruolo per l'operazione *Microsoft.KeyVault/deletedVaults/read*. 
- Per impostazione predefinita, solo un amministratore della sottoscrizione ha queste autorizzazioni. 

### <a name="scheduled-purge"></a>Eliminazione temporanea programmata

Quando si elencano gli oggetti di un insieme di credenziali eliminato, viene indicato anche quando ne è programmata l'eliminazione definitiva da Key Vault. La *data eliminazione definitiva programmata* indica il momento in cui un oggetto dell'insieme di credenziali delle chiavi verrà eliminato in modo definitivo se non viene eseguita alcuna operazione. Per impostazione predefinita, il periodo di memorizzazione di un oggetto di un insieme di credenziali delle chiavi eliminato è di 90 giorni.

>[!IMPORTANT]
>Un oggetto di un insieme di credenziali delle chiavi eliminato in modo permanente, attivato dal campo *Scheduled Purge Date* (Data eliminazione definitiva programmata), viene eliminato in modo definitivo e non è più recuperabile.

## <a name="enabling-purge-protection"></a>Abilitazione del flag di protezione dall'eliminazione

Quando la protezione dall'eliminazione è attivata, un insieme di credenziali o un oggetto nello stato eliminato non può essere ripulito finché non ha superato il periodo di conservazione di 90 giorni. Tale insieme di credenziali o oggetto può essere comunque ripristinato. Questa funzionalità offre maggiore sicurezza che un oggetto o un insieme di credenziali non venga mai eliminato definitivamente prima che sia trascorso il periodo di conservazione.

È possibile abilitare la protezione dall'eliminazione solo se è abilitata anche l'eliminazione temporanea. La disabilitazione della protezione dall'eliminazione non è supportata.

Per attivare l'eliminazione temporanea e la protezione dall'eliminazione durante la creazione di un insieme di credenziali, usare il comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create):

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Per aggiungere la protezione dall'eliminazione a un insieme di credenziali esistente (per cui è già abilitata l'eliminazione temporanea), usare il comando [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update):

```azurecli
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Altre risorse

- Per una panoramica della funzionalità di eliminazione temporanea di Key Vault, vedere [Panoramica della funzionalità di eliminazione temporanea di Azure Key Vault](soft-delete-overview.md).
- Per una panoramica generale dell'uso di Azure Key Vault, vedere [Cos'è Azure Key Vault?](overview.md).

