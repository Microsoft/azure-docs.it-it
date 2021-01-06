---
title: Panoramica di Azure Key Vault Recovery | Microsoft Docs
description: Key Vault funzionalità di ripristino sono progettate per evitare l'eliminazione accidentale o dannosa dell'insieme di credenziali delle chiavi e dei segreti, delle chiavi e dei certificati archiviati all'interno di Key Vault.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
manager: rkarlin
ms.date: 09/30/2020
ms.openlocfilehash: a9a783333d8edd40556b7744eb7cf7dfb52953a7
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97933983"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Gestione del ripristino Azure Key Vault con eliminazione e ripulitura soft

Questo articolo illustra due funzionalità di ripristino di Azure Key Vault, l'eliminazione temporanea e la protezione di ripulitura. Questo documento offre una panoramica di queste funzionalità e illustra come gestirle tramite il portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell.

Per ulteriori informazioni su Key Vault, vedere.
- [Panoramica di Key Vault](overview.md)
- [Cenni preliminari su chiavi, segreti e certificati Azure Key Vault](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/dotnet)
* [Modulo di PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
* Un insieme di credenziali delle chiavi. È possibile crearne uno con il [portale di Azure](../general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="what-are-soft-delete-and-purge-protection"></a>Che cosa sono la protezione eliminazione temporanea e ripulitura

La protezione per l' [eliminazione](soft-delete-overview.md) e la ripulitura temporanea sono due diverse funzionalità di ripristino di Key Vault.

> [!IMPORTANT]
> L'attivazione dell'eliminazione temporanea è fondamentale per garantire che gli insiemi di credenziali delle chiavi e le credenziali siano protetti da eliminazioni accidentali. Tuttavia, l'attivazione dell'eliminazione temporanea è considerata una modifica sostanziale perché potrebbe essere necessario modificare la logica dell'applicazione o fornire autorizzazioni aggiuntive per le entità servizio. Prima di attivare l'eliminazione temporanea usando le istruzioni riportate di seguito, assicurarsi che l'applicazione sia compatibile con la modifica usando questo documento [ **qui**.](soft-delete-change.md)

L' **eliminazione** temporanea è progettata per impedire l'eliminazione accidentale di Key Vault e chiavi, segreti e certificati archiviati all'interno di Key Vault. Si pensi all'eliminazione temporanea come un cestino. Quando si elimina un insieme di credenziali delle chiavi o un oggetto dell'insieme di credenziali delle chiavi, questo resterà reversibile per un periodo di conservazione configurabile dall'utente o per un valore predefinito di 90 giorni. Gli insiemi di credenziali delle chiavi nello stato di eliminazione temporanea possono anche essere eliminati, il che **significa che vengono** eliminati definitivamente. In questo modo è possibile ricreare gli insiemi di credenziali delle chiavi e gli oggetti dell'insieme di credenziali delle chiavi con lo stesso nome. Il recupero e l'eliminazione di insiemi di credenziali delle chiavi e di oggetti richiedono autorizzazioni per i criteri di accesso elevati. **Una volta abilitata l'eliminazione temporanea, non è possibile disabilitarla.**

È importante notare che i nomi dell'insieme di credenziali delle **chiavi sono univoci a livello globale**, quindi non sarà possibile creare un insieme di credenziali delle chiavi con lo stesso nome di un insieme di credenziali delle chiavi nello stato Soft deleted. Analogamente, i nomi di chiavi, segreti e certificati sono univoci all'interno di un insieme di credenziali delle chiavi. Non sarà possibile creare un segreto, una chiave o un certificato con lo stesso nome di un altro nello stato di eliminazione temporanea.

L' **eliminazione della protezione** è progettata per impedire l'eliminazione di Key Vault, chiavi, segreti e certificati da parte di utenti malintenzionati. Considerarlo come cestino con un blocco basato su tempo. È possibile ripristinare gli elementi in qualsiasi momento durante il periodo di memorizzazione configurabile. **Non sarà possibile eliminare o eliminare definitivamente un insieme di credenziali delle chiavi finché non trascorre il periodo di conservazione.** Una volta trascorso il periodo di memorizzazione, l'insieme di credenziali delle chiavi o l'oggetto Key Vault verrà eliminato automaticamente.

> [!NOTE]
> L'eliminazione della protezione è progettata in modo che nessun ruolo o autorizzazione di amministratore possa ignorare, disabilitare o aggirare l'eliminazione della protezione. **Una volta abilitata l'eliminazione, la protezione non può essere disabilitata o sostituita da chiunque includa Microsoft.** Ciò significa che è necessario ripristinare un insieme di credenziali delle chiavi eliminato oppure attendere che il periodo di conservazione venga trascorso prima di riusare il nome dell'insieme di credenziali delle chiavi.

Per ulteriori informazioni sull'eliminazione temporanea, vedere la [Panoramica di Azure Key Vault soft-delete](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Verificare se l'eliminazione temporanea è abilitata in un insieme di credenziali delle chiavi e abilitare l'eliminazione temporanea

1. Accedere al portale di Azure.
1. Selezionare l'insieme di credenziali delle chiavi.
1. Fare clic sul pannello "proprietà".
1. Verificare se il pulsante di opzione accanto a soft-delete è impostato su "Abilita ripristino".
1. Se l'eliminazione temporanea non è abilitata nell'insieme di credenziali delle chiavi, fare clic sul pulsante di opzione per abilitare l'eliminazione temporanea e fare clic su "Salva".

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="In proprietà, l'eliminazione temporanea è evidenziata, così come il valore per abilitarla.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Concedi l'accesso a un'entità servizio per eliminare e recuperare i segreti eliminati

1. Accedere al portale di Azure.
1. Selezionare l'insieme di credenziali delle chiavi.
1. Fare clic sul pannello "criteri di accesso".
1. Nella tabella individuare la riga dell'entità di sicurezza a cui si vuole concedere l'accesso o aggiungere una nuova entità di sicurezza.
1. Fare clic sull'elenco a discesa per chiavi, certificati e segreti.
1. Scorrere fino alla fine dell'elenco a discesa e fare clic su "Ripristina" e "Ripulisci".
1. Per eseguire la maggior parte delle operazioni, le entità di sicurezza necessitano anche della funzionalità Get ed List.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="Nel riquadro di spostamento a sinistra vengono evidenziati i criteri di accesso. Nei criteri di accesso viene visualizzato l'elenco a discesa posizioni segrete e vengono selezionati quattro elementi: Get, List, Recover ed Purge.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Elencare, ripristinare o eliminare un insieme di credenziali delle chiavi eliminato temporaneamente

1. Accedere al portale di Azure.
1. Fare clic sulla barra di ricerca nella parte superiore della pagina.
1. In "servizi recenti" fare clic su "Key Vault". Non fare clic su un singolo insieme di credenziali delle chiavi.
1. Nella parte superiore della schermata fare clic sull'opzione "Gestisci insiemi di credenziali eliminati".
1. Sul lato destro dello schermo viene aperto un riquadro del contesto.
1. Selezionare la propria sottoscrizione.
1. Se l'insieme di credenziali delle chiavi è stato eliminato temporaneamente, verrà visualizzato nel riquadro contesto a destra.
1. Se sono presenti troppi insiemi di credenziali, è possibile fare clic su "carica altro" nella parte inferiore del riquadro contesto oppure usare l'interfaccia della riga di comando o PowerShell per ottenere i risultati.
1. Dopo aver individuato l'insieme di credenziali che si vuole ripristinare o eliminare, selezionare la casella di controllo accanto.
1. Se si vuole ripristinare l'insieme di credenziali delle chiavi, selezionare l'opzione Ripristina nella parte inferiore del riquadro del contesto.
1. Selezionare l'opzione Ripulisci se si vuole eliminare definitivamente l'insieme di credenziali delle chiavi.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Negli insiemi di credenziali delle chiavi, l'opzione Gestisci insiemi di credenziali eliminati è evidenziata.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="In Gestisci insiemi di credenziali delle chiavi eliminati, l'unico insieme di credenziali delle chiavi elencato è evidenziato e selezionato e il pulsante Ripristina è evidenziato.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Elencare, recuperare o eliminare i segreti, le chiavi e i certificati eliminati temporaneamente

1. Accedere al portale di Azure.
1. Selezionare l'insieme di credenziali delle chiavi.
1. Selezionare il pannello corrispondente al tipo di segreto che si vuole gestire (chiavi, segreti o certificati).
1. Nella parte superiore della schermata fare clic su "Gestisci eliminati (chiavi, segreti o certificati)
1. Verrà visualizzato un riquadro del contesto sul lato destro dello schermo.
1. Se il segreto, la chiave o il certificato non è visualizzato nell'elenco, non è nello stato di eliminazione temporanea.
1. Selezionare il segreto, la chiave o il certificato che si vuole gestire.
1. Selezionare l'opzione per il ripristino o l'eliminazione nella parte inferiore del riquadro del contesto.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="In chiavi, l'opzione Gestisci chiavi eliminate è evidenziata.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Verificare se per un insieme di credenziali delle chiavi è abilitata l'eliminazione temporanea

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi

    Per tutti i nuovi insiemi di credenziali delle chiavi l'eliminazione temporanea è abilitata per impostazione predefinita. Se è attualmente disponibile un insieme di credenziali delle chiavi in cui non è abilitata l'eliminazione temporanea, usare il comando seguente per abilitare l'eliminazione temporanea.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Elimina Key Vault (reversibile se l'eliminazione temporanea è abilitata)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Elencare tutti gli insiemi di credenziali delle chiavi eliminati temporaneamente

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Ripristinare l'insieme di credenziali delle chiavi eliminate temporaneamente

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Elimina l'insieme di credenziali delle chiavi eliminato temporaneamente **(avviso! Questa operazione ELIMINerà definitivamente l'** insieme di credenziali delle chiavi)

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Abilita ripulitura-protezione su Key Vault

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certificati (CLI)

* Concessione dell'accesso per l'eliminazione e il ripristino dei certificati

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Eliminazione di un certificato

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Elencare i certificati eliminati

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Ripristina certificato eliminato

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Elimina il certificato eliminato temporaneamente **(avviso. Questa operazione ELIMINerà definitivamente il certificato** .

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Chiavi (CLI)

* Concedi l'accesso per eliminare e recuperare le chiavi

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Tasto CANC

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Elencare le chiavi eliminate

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Ripristina chiave eliminata

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Elimina la chiave eliminata temporaneamente **(avviso. Questa operazione ELIMINerà definitivamente la chiave)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Segreti (CLI)

* Concedi l'accesso per eliminare e ripristinare i segreti

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Elimina segreto

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Elencare i segreti eliminati

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Ripristinare il segreto eliminato

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Elimina il segreto eliminato temporaneamente **(avviso. Questa operazione ELIMINerà definitivamente il segreto**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Verificare se per un insieme di credenziali delle chiavi è abilitata l'eliminazione temporanea

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Elimina Key Vault

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Elencare tutti gli insiemi di credenziali delle chiavi eliminati temporaneamente

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Ripristinare l'insieme di credenziali delle chiavi eliminate temporaneamente

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Eliminare l'insieme di credenziali delle chiavi eliminate temporaneamente **(avviso! Questa operazione ELIMINerà definitivamente l'** insieme di credenziali delle chiavi)

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Abilita ripulitura-protezione su Key Vault

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certificati (PowerShell)

* Concedere le autorizzazioni per ripristinare ed eliminare i certificati

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Eliminare un certificato

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Elencare tutti i certificati eliminati in un insieme di credenziali delle chiavi

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Ripristinare un certificato nello stato eliminato

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Elimina un certificato eliminato temporaneamente **(avviso. Questa operazione ELIMINerà definitivamente il certificato** .

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Chiavi (PowerShell)

* Concedere le autorizzazioni per il recupero e l'eliminazione delle chiavi

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Eliminare una chiave

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Elencare tutti i certificati eliminati in un insieme di credenziali delle chiavi

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Per ripristinare una chiave eliminata temporaneamente

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Elimina una chiave eliminata temporaneamente **(avviso. Questa operazione ELIMINerà definitivamente la chiave)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Segreti (PowerShell)

* Concedere le autorizzazioni per il recupero e l'eliminazione dei segreti

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Eliminare un segreto denominato sqlpassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Elencare tutti i segreti eliminati in un insieme di credenziali delle chiavi

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Ripristinare un segreto nello stato Deleted

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Elimina un segreto in stato eliminato **(avviso! Questa operazione ELIMINerà definitivamente la chiave)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Passaggi successivi

- [Cmdlet di Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault)
- [Comandi dell'interfaccia della riga di comando Key Vault Azure](https://docs.microsoft.com/cli/azure/keyvault)
- [Backup di Azure Key Vault](backup.md)
- [Come abilitare la registrazione di Key Vault](howto-logging.md)
- [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- [Procedure consigliate per usare un insieme di credenziali delle chiavi](security-overview.md)
