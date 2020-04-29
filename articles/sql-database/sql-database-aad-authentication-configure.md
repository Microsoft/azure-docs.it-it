---
title: Configurare l'autenticazione di Azure Active Directory
description: Informazioni su come connettersi a database SQL, istanza gestita e analisi sinapsi di Azure usando l'autenticazione di Azure Active Directory, dopo la configurazione di Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 0e244ea185011bbb7d9f0facad399bb9b577bbc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419858"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurare e gestire l'autenticazione di Azure Active Directory con SQL

Questo articolo illustra come creare e popolare Azure AD e quindi usare Azure AD con il [database SQL di Azure (database SQL)](sql-database-technical-overview.md), [istanza gestita (mi)](sql-database-managed-instance.md)e [Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Per una panoramica vedere [Autenticazione di Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Questo articolo si applica al server SQL di Azure e sia al database SQL che alla sinapsi di Azure. Per semplicità, il database SQL viene usato quando si fa riferimento sia al database SQL che alla sinapsi di Azure.

> [!IMPORTANT]  
> La connessione a SQL Server in esecuzione in una VM di Azure non è supportata con un account Azure Active Directory. Usare un account Active Directory di dominio.

## <a name="azure-ad-authentication-methods"></a>Metodi di autenticazione di Azure AD

Azure AD autenticazione supporta i metodi di autenticazione seguenti:

- Azure AD le identità solo cloud
- Azure AD identità ibride che supportano:
  - Autenticazione cloud con due opzioni abbinate a seamless Single Sign-On (SSO)
    - Autenticazione dell'hash della password Azure AD
    - Autenticazione pass-through Azure AD
  - Autenticazione federata

Per ulteriori informazioni sui metodi di autenticazione Azure AD e su quello da scegliere, vedere l'articolo:
- [Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD](../active-directory/hybrid/choose-ad-authn.md)

Per ulteriori informazioni su Azure AD identità ibride, l'installazione e la sincronizzazione, vedere gli articoli seguenti:

- Autenticazione dell'hash delle password: [implementare la sincronizzazione dell'hash delle password con Azure ad Connect sincronizzazione](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticazione pass-through: [autenticazione pass-through Azure Active Directory](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticazione federata- [distribuzione di Active Directory Federation Services in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure ad Connect e Federazione](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Sono supportati tutti i metodi di autenticazione per database SQL (database singolo e pool di database), istanza gestita e sinapsi di Azure.

## <a name="create-and-populate-an-azure-ad"></a>Creare e popolare un'istanza di Azure AD

Creare un'istanza di Azure AD e popolarla con utenti e gruppi. Azure AD può essere il dominio gestito di Azure AD iniziale. Azure AD può anche essere un set di servizi di dominio Active Directory locali federato con Azure AD.

Per altre informazioni, consultare [Integrazione delle identità locali con Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Aggiungere un nome di dominio personalizzato ad Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Windows Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Nuovo supporto per la federazione con Active Directory di Windows Server in Windows Azure), [Amministrazione della directory di Azure AD](../active-directory/fundamentals/active-directory-administer.md) e [Gestire Azure AD con Windows PowerShell](/powershell/azure/overview) e [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory

1. Associare la sottoscrizione di Azure ad Azure Active Directory impostando la directory come directory attendibile per la sottoscrizione di Azure che ospita il database. Per altri dettagli, vedere [Associare le sottoscrizioni di Azure ad Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Usare il controllo per cambiare directory nel portale di Azure per passare alla sottoscrizione associata al dominio.

   > [!IMPORTANT]
   > Ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. Questa relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione tra la sottoscrizione e tutte le altre risorse in Azure, ad esempio siti Web, database e così via, le quali sono da considerarsi più come risorse figlio di una sottoscrizione. Se la sottoscrizione scade, non sarà più possibile accedere a tutte queste altre risorse associate alla sottoscrizione anche se la directory rimane in Azure, quindi sarà possibile associarvi un'altra sottoscrizione e continuare a gestire gli utenti della directory. Per altre informazioni sulle risorse, vedere [Informazioni sull'accesso alle risorse di Azure](../active-directory/active-directory-b2b-admin-add-users.md). Per altre informazioni su questa relazione attendibile, vedere [Associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Creare un amministratore di Azure Active Directory per il server SQL di Azure

Ogni server SQL di Azure (che ospita un database SQL o una sinapsi di Azure) inizia con un account amministratore di server singolo che è l'amministratore dell'intero server SQL di Azure. È necessario creare un secondo amministratore del server SQL, cioè un account Azure AD. Questa entità viene creata come un utente di database indipendente nel database master. Per quando riguarda gli amministratori, gli account amministratore del server sono membri del ruolo **db_owner** in ogni database utente e ogni database utente viene immesso come utente **dbo**. Per altre informazioni sugli account amministratore del server, vedere l'articolo relativo alla [gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).

Quando si usa Azure Active Directory con la replica geografica, l'amministratore di Azure Active Directory deve essere configurato sia per i server primari che per quelli secondari. Se un server non ha un amministratore di Azure Active Directory, gli utenti e gli account di accesso di Azure Active Directory ricevono un messaggio di errore "Impossibile connettersi al server".

> [!NOTE]
> Gli utenti che non hanno un account Azure Active Directory, incluso l'account amministratore di Azure SQL Server, non possono creare utenti di Azure Active Directory, perché non hanno l'autorizzazione per convalidare con Azure Active Directory gli utenti di database proposti.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Effettuare il provisioning di un amministratore Azure Active Directory per l'istanza gestita

> [!IMPORTANT]
> Eseguire questa procedura solo se si esegue il provisioning di un'istanza gestita. Questa operazione può essere eseguita solo dall'amministratore globale/aziendale o da un amministratore del ruolo con privilegi in Azure AD. Di seguito viene descritta la procedura da seguire per concedere le autorizzazioni agli utenti con privilegi diversi nella directory.

> [!NOTE]
> Per gli amministratori Azure AD per MI creato prima della disponibilità generale, ma continua a funzionare dopo la versione GA, non esiste alcuna modifica funzionale al comportamento esistente. Per ulteriori informazioni, vedere la sezione [nuova Azure ad funzionalità di amministrazione per mi](#new-azure-ad-admin-functionality-for-mi) .

L'istanza gestita deve avere le autorizzazioni per leggere Azure AD per eseguire correttamente le attività, ad esempio l'autenticazione degli utenti tramite l'appartenenza ai gruppi di sicurezza o la creazione di nuovi utenti. Per eseguire questa operazione, è necessario concedere le autorizzazioni per l'istanza gestita per leggere Azure AD. Per eseguire questa operazione sono disponibili due metodi. È possibile usare il portale e PowerShell. I passaggi seguenti illustrano entrambi i metodi.

1. Nell'angolo in alto a destra del portale di Azure scegliere la connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory.

2. Scegliere la directory corretta come directory predefinita di Azure AD.

   Questo passaggio collega la sottoscrizione associata ad Active Directory con Istanza gestita, verificando che venga usata la stessa sottoscrizione per Azure AD e per l'istanza gestita.

3. Passare a Istanza gestita e selezionare l'istanza da usare per l'integrazione con Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selezionare il banner nella parte superiore della pagina di amministrazione di Active Directory e concedere l'autorizzazione all'utente corrente. Se si è connessi come amministratore globale/aziendale in Azure AD, è possibile eseguire questa operazione dalla portale di Azure o usando PowerShell con lo script seguente.

    ![concessione di autorizzazioni-portale](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Al termine dell'operazione, nell'angolo in alto a destra verrà visualizzata la notifica seguente:

    ![esito positivo](./media/sql-database-aad-authentication/success.png)

6. A questo punto è possibile scegliere l'amministratore di Azure AD per l'istanza gestita. Nella pagina Amministratore di Active Directory selezionare il comando **Imposta amministratore**.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Nella pagina di amministrazione di Azure Active Directory, cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi scegliere **Seleziona**.

   La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Vedere l'elenco degli amministratori supportati in [Funzionalità e limitazioni di Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Il controllo degli accessi in base al ruolo (RBAC) si applica solo al portale di Azure e non viene propagato a SQL Server.

    ![Aggiungere Azure Active Directory amministratore](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Nella parte superiore della pagina Amministratore di Active Directory selezionare **Salva**.

    ![Salva](./media/sql-database-aad-authentication/save.png)

    Il processo di modifica dell'amministratore può richiedere alcuni minuti. Al termine del processo, nella casella Amministratore di Active Directory verrà visualizzato il nome del nuovo amministratore.

Dopo aver eseguito il provisioning di un amministratore Azure AD per l'istanza gestita, è possibile iniziare a creare Azure AD entità server (account di accesso) con la sintassi <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create Login</a> . Per ulteriori informazioni, vedere [Cenni preliminari sulle istanze gestite](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Per rimuovere un amministratore in un secondo momento, nella parte superiore della pagina Amministratore di Active Directory scegliere **Rimuovi amministratore** e quindi **Salva**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nuove funzionalità di amministrazione di Azure AD per MI

Nella tabella seguente sono riepilogate le funzionalità per l'anteprima pubblica Azure AD amministratore di accesso per MI, rispetto a una nuova funzionalità fornita con GA per Azure AD account di accesso.

| Azure AD amministratore di accesso per MI durante l'anteprima pubblica | Funzionalità GA per Azure AD admin per MI |
| --- | ---|
| Si comporta in modo analogo a Azure AD amministratore per il database SQL, che Abilita l'autenticazione Azure AD, ma l'amministratore Azure AD non è in grado di creare account di accesso SQL o Azure AD nel database master per MI. | Azure AD amministratore dispone dell'autorizzazione sysadmin e può creare gli account di accesso di AAD e SQL nel database master per MI. |
| Non è presente nella vista sys. server_principals | È presente nella vista sys. server_principals |
| Consente la configurazione di singoli utenti Guest Azure AD come Azure AD amministratore per MI. Per ulteriori informazioni, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../active-directory/b2b/add-users-administrator.md). | Richiede la creazione di un gruppo di Azure AD con utenti guest come membri per configurare questo gruppo come amministratore Azure AD per MI. Per ulteriori informazioni, vedere [Azure ad supporto business to business](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Come procedura consigliata per gli amministratori di Azure AD esistenti per MI creato prima di GA e continuare a lavorare dopo la versione GA, reimpostare l'amministratore di Azure AD utilizzando l'opzione portale di Azure "Rimuovi amministratore" e "imposta amministratore" per lo stesso utente o gruppo di Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Problemi noti relativi all'accesso Azure AD GA per MI

- Se nel database master è presente un account di accesso Azure AD per MI, creato con il comando `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`T-SQL, non può essere configurato come amministratore Azure ad per mi. Si verificherà un errore durante l'impostazione dell'account di accesso come amministratore Azure AD usando i comandi portale di Azure, PowerShell o CLI per creare l'account di accesso Azure AD.
  - Per poter creare l'account come amministratore di Azure AD, è necessario `DROP LOGIN [myaadaccount]`che l'account di accesso venga eliminato nel database master utilizzando il comando.
  - Configurare l'account amministratore Azure AD nel portale di Azure dopo che ha avuto `DROP LOGIN` esito positivo. 
  - Se non è possibile configurare l'account amministratore di Azure AD, archiviare il database master dell'istanza gestita per l'account di accesso. Usare il comando seguente: `SELECT * FROM sys.server_principals`
  - La configurazione di un amministratore Azure AD per MI creerà automaticamente un account di accesso nel database master per l'account. La rimozione dell'amministratore di Azure AD eliminerà automaticamente l'account di accesso dal database master.

- I singoli utenti Guest Azure AD non sono supportati come amministratori Azure AD per MI. È necessario che gli utenti Guest facciano parte di un gruppo di Azure AD da configurare come Azure AD amministratore. Attualmente, il pannello portale di Azure non disabilita gli utenti guest per un altro Azure AD, consentendo agli utenti di proseguire con la configurazione dell'amministratore. Se si salvano gli utenti guest come amministratore Azure AD, l'installazione avrà esito negativo.
  - Se si desidera impostare un utente guest come amministratore di Azure AD per MI, includere l'utente guest in un gruppo di Azure AD e impostare questo gruppo come amministratore di Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell per istanza gestita di SQL

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell (RM) è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Il modulo AzureRM continuerà a ricevere correzioni di bug fino ad almeno il 2020 dicembre.  Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione al nuovo Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Per eseguire il provisioning di un amministratore di Azure AD, eseguire i comandi di Azure PowerShell seguenti:

- Connect-AzAccount
- Select-AzSubscription

Cmdlet usati per il provisioning e la gestione di Azure AD amministratore per l'istanza gestita di SQL:

| Nome del cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Effettua il provisioning di un amministratore Azure AD per l'istanza gestita di SQL nella sottoscrizione corrente. (Deve essere dalla sottoscrizione corrente)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Rimuove un amministratore Azure AD per l'istanza gestita di SQL nella sottoscrizione corrente. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Restituisce informazioni su un amministratore Azure AD per l'istanza gestita di SQL nella sottoscrizione corrente.|

Il comando seguente ottiene informazioni su un amministratore Azure AD per un'istanza gestita denominata ManagedInstance01 associata a un gruppo di risorse denominato ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Il comando seguente effettua il provisioning di un gruppo di amministratori Azure AD denominato DBA per l'istanza gestita denominata ManagedInstance01. Questo server è associato al gruppo di risorse ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Il comando seguente rimuove l'amministratore di Azure AD per l'istanza gestita denominata ManagedInstanceName01 associata al gruppo di risorse ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È anche possibile eseguire il provisioning di un amministratore Azure AD per l'istanza gestita di SQL chiamando i comandi CLI seguenti:

| Comando | Descrizione |
| --- | --- |
|[AZ SQL mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Effettua il provisioning di un amministratore Azure Active Directory per l'istanza gestita di SQL. (Deve essere dalla sottoscrizione corrente) |
|[AZ SQL mi ad-admin Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Rimuove un amministratore Azure Active Directory per l'istanza gestita di SQL. |
|[elenco AZ SQL mi ad-admin](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Restituisce informazioni su un amministratore Azure Active Directory attualmente configurato per l'istanza gestita di SQL. |
|[AZ SQL mi ad-Admin Update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aggiorna l'amministratore Active Directory per un'istanza gestita di SQL. |

Per ulteriori informazioni sui comandi dell'interfaccia della riga di comando, vedere [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Effettuare il provisioning di un amministratore di Azure Active Directory per il server di database SQL di Azure

> [!IMPORTANT]
> Seguire questa procedura solo se si esegue il provisioning di un server di database SQL di Azure o di Azure sinapsi Analytics.

Le due procedure seguenti illustrano come eseguire il provisioning di un amministratore di Azure Active Directory per il server SQL Azure usando il portale di Azure e PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Nell'angolo in alto a destra del [portale di Azure](https://portal.azure.com/) scegliere la connessione per visualizzare un elenco a discesa delle possibili directory di Active Directory. Scegliere la directory corretta come directory predefinita di Azure AD. Questo passaggio collega la directory di Active Directory associata alla sottoscrizione con il server SQL di Azure, assicurando che la stessa sottoscrizione venga usata per Azure AD e per SQL Server. Il server SQL di Azure può ospitare il database SQL di Azure o la sinapsi di Azure.

    ![choose-ad][8]

2. Cercare e selezionare **SQL Server**.

    ![Cercare e selezionare SQL Server](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > In questa pagina, prima di selezionare **SQL Server** è possibile selezionare la **stella** accanto al nome per *aggiungere ai preferiti* la categoria e aggiungere **SQL Server**alla barra di navigazione sinistra.

3. Nella pagina **SQL Server**, selezionare **Amministratore di Active Directory**.

4. Nella pagina **Amministratore di Active Directory** selezionare **Imposta amministratore**.

    ![Server SQL impostati Active Directory amministratore](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Nella pagina **Aggiungi amministratore** cercare un utente, selezionare l'utente o il gruppo da impostare come amministratore e quindi selezionare **Seleziona**. La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Gli utenti e i gruppi non disponibili (in grigio) non possono essere selezionati, perché non sono supportati come amministratori di Azure AD. Vedere l'elenco degli amministratori supportati nella sezione **Azure ad funzionalità e limitazioni** di [usare l'autenticazione Azure Active Directory per l'autenticazione con il database SQL o la sinapsi di Azure](sql-database-aad-authentication.md). Il controllo degli accessi in base al ruolo (RBAC) si applica solo al portale e non viene propagato ai SQL Server.

    ![Selezionare Azure Active Directory amministratore](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Nella parte superiore della pagina **amministratore di Active Directory** selezionare **Salva**.

    ![Salvare l'impostazione dell'amministratore](./media/sql-database-aad-authentication/save-admin.png)

Il processo di modifica dell'amministratore può richiedere alcuni minuti. Il nuovo amministratore è quindi visualizzato nella casella **Amministratore di Active Directory** .

   > [!NOTE]
   > Quando si configura l'amministratore di Azure AD il nuovo nome dell'amministratore, utente o gruppo, non può essere già presente nel database master virtuale come un utente dell'autenticazione del server SQL. Se presente, l’impostazione dell’amministratore di Microsoft Azure avrà esito negativo; eseguire il rollback della creazione e indicare che tale (nome) di amministratore già esiste. Poiché tale utente dell'autenticazione del server SQL non è parte di Azure AD, qualsiasi tentativo di connettersi al server mediante l'autenticazione di Azure AD ha esito negativo.

Per rimuovere un amministratore in un secondo momento, nella parte superiore della pagina **Amministratore di Active Directory** scegliere **Rimuovi amministratore** e quindi **Salva**.

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>PowerShell per il database SQL di Azure e la sinapsi di Azure

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview). Per eseguire il provisioning di un amministratore di Azure AD, eseguire i comandi di Azure PowerShell seguenti:

- Connect-AzAccount
- Select-AzSubscription

Cmdlet usati per il provisioning e la gestione di Azure AD amministratore per il database SQL di Azure e la sinapsi di Azure:

| Nome del cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Effettua il provisioning di un amministratore Azure Active Directory per Azure SQL Server o la sinapsi di Azure. (Deve essere dalla sottoscrizione corrente) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Rimuove un amministratore Azure Active Directory per il server di Azure SQL o la sinapsi di Azure. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Restituisce informazioni su un amministratore Azure Active Directory attualmente configurato per il server di Azure SQL o la sinapsi di Azure. |

Usare il comando Get-Help di PowerShell per visualizzare altre informazioni per ognuno di questi comandi. Ad esempio: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Lo script seguente effettua il provisioning di **DBA_Group** un gruppo di amministratori `40b79501-b343-44ed-9ce7-da4c8cc7353f`Azure ad denominato DBA_Group (ID oggetto) per il server **demo_server** in un gruppo di risorse denominato **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Il parametro di input **DisplayName** accetta il nome visualizzato di Azure AD o il nome dell'entità utente. Ad esempio, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Per i gruppi di Azure AD è supportato solo il nome visualizzato di Azure AD.

> [!NOTE]
> Il comando di Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` non impedisce di eseguire il provisioning degli amministratori di Azure AD per gli utenti non supportati. È possibile eseguire il provisioning di un utente non supportato il quale non può tuttavia connettersi a un database.

L'esempio seguente usa il valore **ObjectID**opzionale:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Il valore **ObjectID** di Azure AD è obbligatorio quando **DisplayName** non è univoco. Per recuperare i valori **ObjectID** e **DisplayName**, usare la sezione Active Directory del portale di Azure classico e visualizzare le proprietà di un utente o di un gruppo.

L'esempio seguente restituisce informazioni sull'amministratore di Azure AD corrente per Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

L'esempio seguente illustra come rimuovere un amministratore di Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile effettuare il provisioning di un amministratore Azure AD chiamando i comandi CLI seguenti:

| Comando | Descrizione |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Effettua il provisioning di un amministratore Azure Active Directory per Azure SQL Server o la sinapsi di Azure. (Deve essere dalla sottoscrizione corrente) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Rimuove un amministratore Azure Active Directory per il server di Azure SQL o la sinapsi di Azure. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Restituisce informazioni su un amministratore Azure Active Directory attualmente configurato per il server di Azure SQL o la sinapsi di Azure. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aggiorna l'amministratore Active Directory per un server di Azure SQL o una sinapsi di Azure. |

Per ulteriori informazioni sui comandi dell'interfaccia della riga di comando, vedere [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> È anche possibile effettuare il provisioning di un amministratore di Azure Active Directory usando le API REST. Per altre informazioni, vedere [Service Management REST API Reference and Operations for Azure SQL Database](/rest/api/sql/) (Informazioni di riferimento e operative sull'API REST di gestione dei servizi per le operazioni del database SQL di Azure)

## <a name="configure-your-client-computers"></a>Configurare i computer client

In tutti i computer client, da cui le applicazioni o gli utenti si connettono al database SQL di Azure o a sinapsi di Azure usando Azure AD identità, è necessario installare il software seguente:

- .NET Framework 4,6 o versione successiva [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)da.
- Azure Active Directory libreria di autenticazione per SQL Server (*adal. DLL*). Di seguito sono riportati i collegamenti di download per installare la versione più recente di SSMS, ODBC e driver di OLE DB che contiene *adal. *Libreria di dll.
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC Driver 17 for SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB driver 18 per SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

È possibile soddisfare questi requisiti tramite:

- L'installazione della versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) soddisfa il requisito .NET Framework 4,6.
    - SSMS installa la versione x86 di *adal. DLL*.
    - SSDT installa la versione amd64 di *adal. DLL*.
    - La versione più recente di Visual Studio per i [download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) soddisfa i requisiti di .NET Framework 4,6, ma non installa la versione amd64 richiesta di *adal. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD

> [!IMPORTANT]
> Istanza gestita supporta ora Azure AD entità server (account di accesso), che consente di creare account di accesso da Azure AD utenti, gruppi o applicazioni. Azure AD entità server (account di accesso) consente di eseguire l'autenticazione all'istanza gestita senza richiedere la creazione di utenti del database come utente di database indipendente. Per ulteriori informazioni, vedere [Cenni preliminari sulle istanze gestite](sql-database-managed-instance.md#azure-active-directory-integration). Per la sintassi sulla creazione di entità server (account di accesso) di Azure AD, vedere <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

L'autenticazione di Azure Active Directory richiede la creazione di utenti del database come utenti di database indipendente. Un utente di database indipendente basato su un'identità di Azure AD è un utente di database che non può accedere al database master e del quale viene eseguito il mapping a un'identità nella directory di Azure AD associata al database. L'identità di Azure AD può essere un singolo account utente o un gruppo. Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Gli utenti del database, tranne gli amministratori, non possono essere creati tramite il portale di Azure. I ruoli RBAC non vengono propagati a SQL Server, database SQL o sinapsi di Azure. I ruoli Controllo degli accessi in base al ruolo di Azure vengono usati per la gestione delle risorse di Azure e non si applicano alle autorizzazioni di database. Ad esempio, il ruolo **collaboratore SQL Server** non concede l'accesso per connettersi al database SQL o alla sinapsi di Azure. L'autorizzazione di accesso deve essere concessa direttamente nel database tramite istruzioni Transact-SQL.

> [!WARNING]
> Caratteri speciali, ad esempio i due punti `:` o la e commerciale `&`, quando vengono inclusi come nomi utente in istruzioni T-SQL CREATE LOGIN e CREATE USER, non sono supportati.

Per creare un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD come utente con almeno l'autorizzazione **ALTER ANY USER** . Usare quindi la sintassi Transact-SQL seguente:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* può essere il nome dell'entità utente di un utente di Azure AD o il nome visualizzato di un gruppo di Azure AD.

**Esempi:** per creare un utente di database indipendente che rappresenta un utente di dominio gestito o federato di Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un gruppo di dominio federato o di Azure AD, specificare il nome visualizzato di un gruppo di sicurezza:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Per creare un utente di database indipendente che rappresenta un'applicazione che si connette usando un token di Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Questo comando richiede che SQL Access Azure AD (il "provider esterno") per conto dell'utente connesso. Talvolta si verificheranno circostanze che determinano la restituzione di un'eccezione a SQL da parte di Azure AD. In questi casi, l'utente visualizzerà l'errore SQL 33134, che dovrebbe contenere il messaggio di errore specifico di AAD. Nella maggior parte dei casi, l'errore indicherà che l'accesso è stato negato o che l'utente deve registrarsi in multi-factor authentication per accedere alla risorsa o che l'accesso tra le applicazioni di terze parti deve essere gestito tramite la preautorizzazione. Nei primi due casi, il problema è in genere causato dai criteri di accesso condizionale impostati nel tenant AAD dell'utente: impediscono all'utente di accedere al provider esterno. L'aggiornamento dei criteri della CA per consentire l'accesso all'applicazione ' 00000002-0000-0000-C000-000000000000' (ID applicazione del API Graph AAD) dovrebbe risolvere il problema. Nel caso in cui l'errore indichi che l'accesso tra le applicazioni di terze parti deve essere gestito tramite la preautorizzazione, il problema è dovuto al fatto che l'utente ha eseguito l'accesso come entità servizio. Il comando deve avere esito positivo se viene eseguito da un utente.

> [!TIP]
> È possibile creare un utente direttamente da Azure Active Directory solo se si usa l'Azure Active Directory associata alla sottoscrizione di Azure. Tuttavia, i membri di altre directory di Active Directory che sono utenti importati nell'Active Directory associata (chiamati utenti esterni) possono essere aggiunti a un gruppo Active Directory nell'Active Directory tenant. Tramite la creazione di un utente del database indipendente per il gruppo AD, gli utenti dell'Active Directory esterna possono ottenere l'accesso al database SQL.

Per altre informazioni sulla creazione di utenti di database indipendente basati su identità di Azure Active Directory, vedere [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> La rimozione dell'amministratore di Azure Active Directory per il server di Azure SQL impedisce agli utenti dell'autenticazione di Azure AD di connettersi al server. Se necessario, un amministratore del database SQL può eliminare manualmente gli utenti di Azure AD inutilizzabili.

> [!NOTE]
> Se si riceve un messaggio di **timeout della connessione scaduto**, potrebbe essere necessario impostare il parametro `TransparentNetworkIPResolution` della stringa di connessione su false. Per altre informazioni, vedere [Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Problema di timeout della connessione con .NET Framework 4.6.1 - TransparentNetworkIPResolution).

Quando si crea un database utente, questo riceve l'autorizzazione **CONNECT** e può connettersi al database come membro del ruolo **PUBLIC**. Inizialmente le sole autorizzazioni disponibili per l'utente sono quelle concesse al ruolo **PUBLIC** o quelle concesse a qualsiasi gruppo di Azure AD di cui è membro. Dopo avere effettuato il provisioning di un utente di database indipendente basato su Azure AD, è possibile concedere all'utente altre autorizzazioni, esattamente come si concede un'autorizzazione a qualsiasi altro tipo di utente. In genere si concedono autorizzazioni ai ruoli del database e si aggiungono gli utenti ai ruoli. Per altre informazioni, vedere l'articolo relativo alle [nozioni di base delle autorizzazioni per il motore di database](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Per altre informazioni sui ruoli speciali del database SQL, vedere [Gestione di database e account di accesso nel database SQL di Azure](sql-database-manage-logins.md).
Un account utente di dominio federato importato in un dominio gestito come utente esterno deve usare l'identità del dominio gestito.

> [!NOTE]
> Gli utenti di Azure AD sono contrassegnati nei metadati del database con il tipo E (EXTERNAL_USER). I gruppi sono contrassegnati con il tipo X (EXTERNAL_GROUPS). Per altre informazioni, vedere [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-azure-synapse-by-using-ssms-or-ssdt"></a>Connettersi al database utente o alla sinapsi di Azure tramite SSMS o SSDT  

Per verificare che l'amministratore di Azure AD sia configurato correttamente, connettersi al database **master** con l'account amministratore di Azure AD.
Per effettuare il provisioning di un utente di database indipendente basato su Azure AD, diverso dall'amministratore del server proprietario del database, connettersi al database con un'identità di Azure AD che abbia accesso al database.

> [!IMPORTANT]
> Il supporto per l'autenticazione di Azure Active Directory è disponibile con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015. La versione di agosto 2016 di SQL Server Management Studio include anche il supporto per l'autenticazione universale di Active Directory, che consente agli amministratori di richiedere la Multi-Factor Authentication mediante chiamata telefonica, SMS, smart card con pin, o notifica dell'app per dispositivi mobili.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Uso di un'identità di Azure AD per la connessione con SSMS o SSDT

Le procedure seguenti mostrano come connettersi a un database SQL con un'identità di Azure Active Directory usando SQL Server Management Studio o SQL Server Database Tools. 

### <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Utilizzare questo metodo se si è connessi a Windows con le credenziali di Azure Active Directory da un dominio federato o un dominio gestito configurato per l'Single Sign-On trasparente per l'autenticazione pass-through e di hash delle password. Per altre informazioni, vedere [Azure Active Directory accesso Single Sign-on](../active-directory/hybrid/how-to-connect-sso.md)facile.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti a motore di database**), nella casella **autenticazione** selezionare **Azure Active Directory-Integrated**. La password non è necessaria e non può essere immessa, perché per la connessione vengono presentate le credenziali esistenti.

    ![Selezionare Autenticazione integrata di Active Directory][11]

2. Scegliere il pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connect to database** (Connetti al database). Per altre informazioni, vedere l'articolo [autenticazione](sql-database-ssms-mfa-authentication.md#azure-ad-domain-name-or-tenant-id-parameter) a più fattori di AAD sulle differenze tra le proprietà di connessione per SSMS 17. x e 18. x. 

    ![Selezionare il nome del database][13]

### <a name="active-directory-password-authentication"></a>Autenticazione della password di Active Directory

Usare questo metodo per connettersi con il nome dell'entità di Azure AD tramite il dominio gestito di Azure AD. È anche possibile usarlo per gli account federati senza accesso al dominio, ad esempio quando si lavora in modalità remota.

Usare questo metodo per eseguire l'autenticazione al database SQL o a MI con Azure AD utenti di identità solo cloud o a coloro che usano Azure AD identità ibride. Questo metodo supporta gli utenti che desiderano utilizzare le credenziali di Windows, ma il computer locale non è aggiunto al dominio, ad esempio utilizzando accesso remoto. In questo caso, un utente di Windows può indicare il proprio account di dominio e la relativa password ed è in grado di eseguire l'autenticazione al database SQL, a MI o a sinapsi di Azure.

1. Avviare Management Studio o Data Tools e nella finestra di dialogo **Connetti al server** (o **Connetti a motore di database**), nella casella **autenticazione** selezionare **Azure Active Directory-password**.

2. Nella casella **nome utente** Digitare il nome utente Azure Active Directory nel formato nome utente **\@Domain.com**. I nomi utente devono essere un account di Azure Active Directory o un account di un dominio gestito o federato con Azure Active Directory.

3. Nella casella **password** Digitare la password utente per l'account Azure Active Directory o per l'account di dominio gestito/federato.

    ![Selezionare Autenticazione della password di Active Directory][12]

4. Scegliere il pulsante **Opzioni**, quindi nella pagina **Proprietà connessione** digitare il nome del database utente a cui si desidera connettersi nella casella **Connect to database** (Connetti al database). Vedere il grafico nell'opzione precedente.

### <a name="active-directory-interactive-authentication"></a>Autenticazione interattiva di Active Directory

Utilizzare questo metodo per l'autenticazione interattiva con o senza multi-factor authentication, con la richiesta di password in modo interattivo. Questo metodo può essere usato per eseguire l'autenticazione al database SQL, MI e a sinapsi di Azure per Azure AD gli utenti di identità solo cloud oppure quelli che usano Azure AD identità ibride.

Per altre informazioni, vedere [uso dell'autenticazione di AAD a più fattori con il database SQL di Azure e Azure sinapsi Analytics (supporto di SSMS per multi-factor authentication)](sql-database-ssms-mfa-authentication.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Utilizzo di un'identità di Azure Active Directory per connettersi da un'applicazione client

Le procedure seguenti mostrano come connettersi a un database SQL con un'identità di Azure Active Directory da un'applicazione client.

### <a name="active-directory-integrated-authentication"></a>Autenticazione integrata di Active Directory

Per usare l'autenticazione integrata di Windows, è necessario che il Active Directory del dominio sia federato con Azure Active Directory oppure che sia un dominio gestito configurato per Single Sign-On Seamless per l'autenticazione pass-through o con hash delle password. Per altre informazioni, vedere [Azure Active Directory accesso Single Sign-on](../active-directory/hybrid/how-to-connect-sso.md)facile.

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) per l'autenticazione integrata di Windows non è supportato per l'accesso Single Sign-On facile per l'autenticazione pass-through e con hash delle password.

L'applicazione client (o un servizio) che si connette al database deve essere in esecuzione in un computer aggiunto a un dominio con le credenziali di dominio di un utente.

Per connettersi a un database usando l'autenticazione integrata e un'identità Azure AD, la parola chiave Authentication nella stringa di connessione del database deve `Active Directory Integrated`essere impostata su. L'esempio di codice C# seguente usa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

La parola chiave `Integrated Security=True` della stringa di connessione non è supportata per la connessione al database SQL di Azure. Quando si esegue una connessione ODBC, è necessario rimuovere gli spazi e impostare l'autenticazione su 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticazione della password di Active Directory

Per connettersi a un database usando Azure AD account utente di identità solo cloud o quelli che usano Azure AD identità ibride, la parola chiave di autenticazione deve essere impostata `Active Directory Password`su. La stringa di connessione deve contenere le parole chiave e i valori User ID/UID e Password/PWD. L'esempio di codice C# seguente usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Per altre informazioni sui metodi di autenticazione di Azure AD, usare gli esempi di codice dimostrativo disponibili in [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)(Dimostrazione GitHub dell'autenticazione di Azure AD).

## <a name="azure-ad-token"></a>Token Azure AD

Questo metodo di autenticazione consente ai servizi di livello intermedio di ottenere [token Web JSON (JWT)](../active-directory/develop/id-tokens.md) per connettersi al database SQL di Azure o a sinapsi di Azure ottenendo un token da Azure Active Directory (AAD). Questo metodo consente diversi scenari di applicazione, tra cui identità del servizio, entità servizio e applicazioni che utilizzano l'autenticazione basata su certificati. Per usare l'autenticazione tramite token di Azure AD, è necessario completare quattro passaggi fondamentali:

1. Registrare l'applicazione con Azure Active Directory e ottenere l'ID client per il codice.
2. Creare un utente del database che rappresenta l'applicazione. (Completato in precedenza al passaggio 6).
3. Creare un certificato nel computer client che esegue l'applicazione.
4. Aggiungere il certificato come chiave per l'applicazione.

Stringa di connessione di esempio:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Per altre informazioni, vedere [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)(Blog sulla sicurezza del server SQL). Per informazioni sull'aggiunta di un certificato, vedere [Introduzione all'autenticazione basata su certificati di Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Le istruzioni seguenti eseguono la connessione usando la versione 13.1 di sqlcmd disponibile in [Download Center](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd`con il `-G` comando non funziona con le identità di sistema e richiede un account di accesso dell'entità utente.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Risoluzione dei problemi Autenticazione di Azure AD

Informazioni aggiuntive sulla risoluzione dei problemi relativi a Autenticazione di Azure AD sono disponibili nel Blog seguente:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica degli account di accesso, degli utenti, dei ruoli del database e delle autorizzazioni nel database SQL, vedere account di [accesso, utenti, ruoli del database e account utente](sql-database-manage-logins.md).
- Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx).
- Per altre informazioni sui ruoli del database, vedere [Ruoli a livello di database](https://msdn.microsoft.com/library/ms189121.aspx).
- Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](sql-database-firewall-configure.md).

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
