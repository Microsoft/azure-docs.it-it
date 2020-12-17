---
title: Prerequisiti del provisioning cloud di Azure AD Connect in Azure AD
description: Questo articolo descrive i prerequisiti e i requisiti hardware previsti per il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4956d11ea2a4b011a792827357c3f4627058ead9
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97651991"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Prerequisiti del provisioning cloud di Azure AD Connect
Questo articolo fornisce indicazioni su come scegliere e usare il provisioning cloud di Azure Active Directory (Azure AD) Connect come soluzione di identità.

## <a name="cloud-provisioning-agent-requirements"></a>Requisiti dell'agente di provisioning cloud
Per usare il provisioning cloud di Azure AD Connect, è necessario quanto segue:

- Credenziali di amministratore di dominio o di amministratore dell'organizzazione per creare l'Azure AD Connect Cloud Sync gMSA (account del servizio gestito del gruppo) per eseguire il servizio Agent. 
- Un account amministratore di identità ibrido per il tenant di Azure AD che non è un utente Guest.
- Un server locale per l'agente di provisioning con Windows 2012 R2 o versione successiva.  Questo server deve essere un server di livello 0 basato sul [modello di livello amministrativo Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Configurazione del firewall locale.

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Un account del servizio gestito del gruppo è un account di dominio gestito che offre la gestione automatica delle password, la gestione semplificata del nome dell'entità servizio (SPN), la possibilità di delegare la gestione ad altri amministratori e anche di estendere questa funzionalità su più server.  Azure AD Connect Cloud Sync supporta e usa un gMSA per l'esecuzione dell'agente.  Verranno richieste le credenziali amministrative durante l'installazione, per creare questo account.  L'account verrà visualizzato come (domain\provAgentgMSA $).  Per altre informazioni su un gMSA, vedere [account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>Prerequisiti per gMSA:
1.  È necessario aggiornare lo schema Active Directory nella foresta del dominio gMSA a Windows Server 2012
2.  [Moduli amministrazione remota di PowerShell](/windows-server/remote/remote-server-administration-tools) in un controller di dominio
3.  Almeno un controller di dominio nel dominio deve eseguire Windows Server 2012.
4.  Un server aggiunto a un dominio in cui è installato l'agente deve essere Windows Server 2012 o versione successiva.

Per i passaggi relativi all'aggiornamento di un agente esistente per l'uso di un account gMSA, vedere [account del servizio gestito del gruppo](how-to-install.md#group-managed-service-accounts).

### <a name="in-the-azure-active-directory-admin-center"></a>Nell'interfaccia di amministrazione di Azure Active Directory

1. Creare un account amministratore di identità ibrido solo cloud nel tenant del Azure AD. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore di identità ibrido solo cloud](../fundamentals/add-users-azure-active-directory.md). Il completamento di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
1. Aggiungere uno o più [nomi di dominio personalizzati](../fundamentals/add-custom-domain.md) al tenant di Azure AD. Gli utenti possono accedere usando uno di questi nomi di dominio.

### <a name="in-your-directory-in-active-directory"></a>Nella directory personale di Active Directory

Eseguire lo [strumento IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) per preparare gli attributi di directory per la sincronizzazione.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server host aggiunto al dominio che esegue Windows Server 2012 R2 o versione successiva con almeno 4 GB di RAM e il runtime di .NET 4.7.1 o versione successiva.

    >[!NOTE]
    > Tenere presente che la definizione di un filtro di ambito comporta un costo di memoria per il server host.  Se non viene utilizzato alcun filtro di ambito, non è previsto alcun costo aggiuntivo per la memoria. Il valore minimo di 4 GB supporterà la sincronizzazione per un massimo di 12 unità organizzative definite nel filtro di ambito. Se è necessario sincronizzare altre unità organizzative, sarà necessario aumentare la quantità minima di memoria. Usare la tabella seguente come guida:
    >
    >
    > | Numero di unità organizzative nel filtro di ambito| memoria minima richiesta|
    > | --- | --- |
    > | 12 | 4 GB |
    > | 18 | 5,5 GB|
    > | 28 | PIÙ DI 10 GB|
    >
    > 

2. I criteri di esecuzione di PowerShell nel server locale devono essere impostati su Undefined o RemoteSigned.

3. Se è presente un firewall tra i server e Azure AD, è necessario configurare gli elementi seguenti:

   - Assicurarsi che gli agenti possano effettuare richieste *in uscita* ad Azure AD sulle porte seguenti:

      | Numero della porta | Uso |
      | --- | --- |
      | **80** | Scarica gli elenchi di revoche di certificati (CRL) durante la convalida del certificato TLS/SSL.  |
      | **443** | Gestisce tutte le comunicazioni in uscita con il servizio. |
      |**8082**|Obbligatorio per l'installazione e se si desidera configurare l'API di amministrazione.  Questa porta può essere rimossa dopo l'installazione dell'agente e se non si prevede di usare l'API.   |
      | **8080** (facoltativo) | Se la porta 443 non è disponibile, gli agenti di autenticazione segnalano il proprio stato ogni dieci minuti attraverso la porta 8080. Lo stato viene visualizzato nel portale di Azure AD. |

   - Se il firewall applica regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizi di rete.
   - Se il firewall o il proxy consente di specificare suffissi sicuri, aggiungere connessioni a \*.msappproxy.net e \*.servicebus.windows.net. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana.
   - Gli agenti devono poter accedere a login.windows.net e login.microsoftonline.net per la registrazione iniziale. Aprire il firewall anche per questi URL.
   - Per la convalida del certificato, sbloccare questi URL: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 e www\.microsoft.com:80. Poiché vengono usati per la convalida del certificato con altri prodotti Microsoft, questi URL potrebbero essere già sbloccati.

>[!NOTE]
> L'installazione dell'agente di provisioning cloud in Windows Server Core non è supportata.

### <a name="additional-requirements"></a>Requisiti aggiuntivi

- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisiti TLS

>[!NOTE]
>Transport Layer Security (TLS) è un protocollo che garantisce comunicazioni sicure. Eventuali modifiche alle impostazioni TLS influiscono sull'intera foresta. Per altre informazioni, vedere [Aggiornare per abilitare TLS 1.1 e TLS 1.2 come protocolli di protezione predefiniti in WinHTTP in Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Nel server Windows che ospita l'agente di provisioning di Azure AD Connect Cloud deve essere abilitato il protocollo TLS 1.2 per poter eseguire l'installazione.

Per abilitare il protocollo TLS 1.2, seguire questa procedura.

1. Impostare le chiavi del Registro di sistema seguenti:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Riavviare il server.

## <a name="known-limitations"></a>Limitazioni note
Di seguito sono riportate le limitazioni note:

### <a name="delta-synchronization"></a>Sincronizzazione differenziale

- Il filtro di ambito gruppo per la sincronizzazione Delta non supporta più di 1500 membri.
- Quando si elimina un gruppo usato come parte di un filtro di ambito gruppo, gli utenti che sono membri del gruppo non vengono eliminati. 
- Quando si rinomina l'unità organizzativa o il gruppo nell'ambito, la sincronizzazione Delta non rimuoverà gli utenti.

### <a name="provisioning-logs"></a>Log di provisioning
- I log di provisioning non distinguono chiaramente tra le operazioni di creazione e aggiornamento.  È possibile che venga visualizzata un'operazione di creazione per un aggiornamento e un'operazione di aggiornamento per un oggetto create.

### <a name="group-re-naming-or-ou-re-naming"></a>Ridenominazione di gruppi o ridenominazione di unità organizzative
- Se si rinomina un gruppo o un'unità organizzativa in Active Directory nell'ambito di una determinata configurazione, il processo di provisioning nel cloud non sarà in grado di riconoscere la modifica del nome in Active Directory. Il processo non entra in quarantena e rimarrà integro.


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)