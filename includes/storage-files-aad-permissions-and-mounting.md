---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95553751"
---
## <a name="assign-access-permissions-to-an-identity"></a>Assegnare le autorizzazioni di accesso a un'identità

Per accedere alle risorse File di Azure con l'autenticazione basata su identità, un'identità (un utente, un gruppo o un'entità servizio) deve disporre delle autorizzazioni necessarie a livello di condivisione. Questo processo è simile alla specifica delle autorizzazioni di condivisione di Windows, in cui è possibile specificare il tipo di accesso di un determinato utente a una condivisione file. Le istruzioni dettagliate fornite in questa sezione spiegano come assegnare a un'identità le autorizzazioni di lettura, scrittura o eliminazione per una condivisione file. 

Sono stati introdotti tre ruoli predefiniti di Azure per la concessione di autorizzazioni a livello di condivisione agli utenti:

- **Storage file data SMB Share Reader** consente l'accesso in lettura alle condivisioni file di archiviazione di Azure tramite SMB.
- Il **collaboratore condivisione SMB per i dati dei file di archiviazione** consente l'accesso in lettura, scrittura ed eliminazione nelle condivisioni file di archiviazione di Azure tramite SMB.
- **Storage file data SMB Share collaboratore con privilegi elevati** consente di leggere, scrivere, eliminare e modificare le autorizzazioni NTFS nelle condivisioni file di archiviazione di Azure tramite SMB.

> [!IMPORTANT]
> Il controllo amministrativo completo di una condivisione file, inclusa la possibilità di assumere la proprietà di un file, richiede l'uso della chiave dell'account di archiviazione. Il controllo amministrativo non è supportato con le credenziali di Azure AD.

È possibile usare la portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per assegnare i ruoli predefiniti all'identità Azure AD di un utente per concedere autorizzazioni a livello di condivisione. Tenere presente che l'assegnazione del ruolo di Azure a livello di condivisione può richiedere del tempo. 

> [!NOTE]
> Ricordarsi di [sincronizzare le credenziali di servizi di dominio Active Directory per Azure ad](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) se si prevede di usare servizi di dominio Active Directory locali per l'autenticazione. La sincronizzazione dell'hash delle password da servizi di dominio Active Directory a Azure AD è facoltativa. L'autorizzazione a livello di condivisione verrà concessa al Azure AD identità sincronizzata da servizi di dominio Active Directory locale.

L'indicazione generale prevede l'uso dell'autorizzazione a livello di condivisione per la gestione degli accessi di alto livello per un gruppo di Active Directory che rappresenta un gruppo di utenti e identità, quindi sfrutta le autorizzazioni NTFS per il controllo di accesso granulare a livello di directory/file. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Assegnare un ruolo di Azure a un'identità di Active Directory

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per assegnare un ruolo di Azure a un'identità di Azure AD, usando il [portale di Azure](https://portal.azure.com), seguire questa procedura:

1. Nella portale di Azure passare alla condivisione file o [creare una condivisione file](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **Aggiungi un'assegnazione di ruolo**
4. Nel pannello **Aggiungi assegnazione ruolo** selezionare il ruolo predefinito appropriato (Storage file data SMB Share Reader, storage file data SMB condivisione Contributor) dall'elenco dei **ruoli** . Lasciare **assegna accesso a** con l'impostazione predefinita: **Azure ad utente, gruppo o entità servizio**. Selezionare la destinazione Azure AD identità in base al nome o all'indirizzo di posta elettronica.
5. Selezionare **Salva** per completare l'operazione di assegnazione di ruolo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

L'esempio di PowerShell seguente illustra come assegnare un ruolo di Azure a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione di ruoli di Azure con PowerShell, vedere [gestire l'accesso con RBAC e Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, comprese le parentesi, con valori personalizzati.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
  
Il comando seguente dell'interfaccia della riga di comando 2,0 illustra come assegnare un ruolo di Azure a un'identità di Azure AD, in base al nome di accesso. Per altre informazioni sull'assegnazione di ruoli di Azure con l'interfaccia della [riga di comando di Azure, vedere Manage Access by using RBAC and Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Prima di eseguire lo script di esempio seguente, ricordarsi di sostituire i valori segnaposto, comprese le parentesi, con valori personalizzati.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Configurare le autorizzazioni NTFS tramite SMB

Dopo aver assegnato le autorizzazioni a livello di condivisione con RBAC, è necessario assegnare le autorizzazioni NTFS appropriate a livello di radice, directory o file. Si pensi a autorizzazioni a livello di condivisione come Gatekeeper di alto livello che determina se un utente può accedere alla condivisione. Mentre le autorizzazioni NTFS agiscono a un livello più granulare per determinare le operazioni che l'utente può eseguire a livello di directory o di file.

File di Azure supporta il set completo di autorizzazioni NTFS di base e avanzate. È possibile visualizzare e configurare le autorizzazioni NTFS per directory e file in una condivisione file di Azure montando la condivisione e quindi usando Esplora file di Windows o eseguendo il comando Windows [icacls](/windows-server/administration/windows-commands/icacls) o [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Per configurare NTFS con autorizzazioni superuser, è necessario montare la condivisione usando la chiave dell'account di archiviazione della macchina virtuale aggiunta al dominio. Seguire le istruzioni nella sezione successiva per montare una condivisione file di Azure dal prompt dei comandi e configurare di conseguenza le autorizzazioni NTFS.

I set di autorizzazioni seguenti sono supportati per la directory radice di una condivisione file:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montare una condivisione file dal prompt dei comandi

Usare il comando di Windows **net use** per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto nell'esempio seguente con valori personalizzati. Per altre informazioni sul montaggio di condivisioni file, vedere [usare una condivisione file di Azure con Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se si verificano problemi durante la connessione a File di Azure, vedere [lo strumento per la risoluzione dei problemi pubblicato per file di Azure errori di montaggio in Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Vengono inoltre fornite [indicazioni](../articles/storage/files/storage-files-faq.md#on-premises-access) per aggirare gli scenari in cui la porta 445 è bloccata. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurare le autorizzazioni NTFS con Esplora file di Windows

Utilizzare Esplora file di Windows per concedere l'autorizzazione completa a tutte le directory e i file nella condivisione file, inclusa la directory radice.

1. Aprire Esplora file di Windows e fare clic con il pulsante destro del mouse su file/directory e selezionare **Proprietà**.
2. Selezionare la scheda **Sicurezza**.
3. Selezionare **modifica.** per modificare le autorizzazioni.
4. È possibile modificare le autorizzazioni degli utenti esistenti o selezionare **Aggiungi...** per concedere le autorizzazioni ai nuovi utenti.
5. Nella finestra di messaggio di richiesta per l'aggiunta di nuovi utenti, immettere il nome utente di destinazione a cui si vuole concedere l'autorizzazione nella casella **immettere i nomi degli oggetti da selezionare** e selezionare **Controlla nomi** per trovare il nome UPN completo dell'utente di destinazione.
7.    Selezionare **OK**.
8.    Nella scheda **sicurezza** selezionare tutte le autorizzazioni che si desidera concedere al nuovo utente.
9.    Selezionare **Applica**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurare le autorizzazioni NTFS con icacls

Usare il comando seguente di Windows per concedere autorizzazioni complete a tutti i file e le sottodirectory nella condivisione file, inclusa la directory radice. Ricordarsi di sostituire i valori segnaposto nell'esempio con i valori personalizzati.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Per ulteriori informazioni su come utilizzare icacls per impostare le autorizzazioni NTFS e sui diversi tipi di autorizzazioni supportate, vedere [la Guida di riferimento alla riga di comando per icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montare una condivisione file da una macchina virtuale aggiunta a dominio

Il processo seguente verifica che la condivisione file e le autorizzazioni di accesso siano state configurate correttamente e che sia possibile accedere a una condivisione file di Azure da una macchina virtuale aggiunta a un dominio. Tenere presente che l'assegnazione del ruolo di Azure a livello di condivisione può richiedere del tempo. 

Accedere alla macchina virtuale usando l'identità Azure AD a cui sono state concesse le autorizzazioni, come illustrato nella figura seguente. Se è stata abilitata l'autenticazione di servizi di dominio Active Directory locale per File di Azure, usare le credenziali di Active Directory Domain Services. Per l'autenticazione Azure AD DS, accedere con Azure AD credenziali.

![Screenshot che mostra la schermata di accesso ad Azure AD per l'autenticazione utente](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Usare il comando seguente per montare la condivisione file di Azure. Ricordarsi di sostituire i valori segnaposto con i valori personalizzati. Poiché è stata eseguita l'autenticazione, non è necessario specificare la chiave dell'account di archiviazione, le credenziali di servizi di dominio Active Directory locale o le credenziali Azure AD DS. L'esperienza Single Sign-on è supportata per l'autenticazione con servizi di dominio Active Directory locale o Azure AD DS. Se si verificano problemi di montaggio con le credenziali di servizi di dominio Active Directory, fare riferimento a [risoluzione dei problemi file di Azure in Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) per informazioni aggiuntive.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```