---
title: Eseguire l'autenticazione con Azure Key Vault
description: Informazioni su come eseguire l'autenticazione con Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 8a8fe4ed0c24d2ccda5fb844005a33a93e85a169
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365519"
---
# <a name="authenticate-to-azure-key-vault"></a>Eseguire l'autenticazione con Azure Key Vault

Azure Key Vault consente di archiviare i segreti e controllarne la distribuzione in un repository cloud sicuro e centralizzato, che evita di dover archiviare le credenziali nelle applicazioni. Le applicazioni devono solo eseguire l'autenticazione con Key Vault in fase di esecuzione per accedere a tali segreti.

## <a name="app-identity-and-security-principals"></a>Identità ed entità di sicurezza dell'app

L'autenticazione con Key Vault funziona in combinazione con [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), che è responsabile dell'autenticazione dell'identità di una determinata **entità di sicurezza**.

Un'entità di sicurezza è un oggetto che rappresenta un utente, un gruppo, un servizio o un'applicazione che richiede l'accesso alle risorse di Azure. Azure assegna un **ID oggetto** univoco a ogni entità di sicurezza.

* L'entità di sicurezza di un **utente** identifica un soggetto che ha un profilo in Azure Active Directory.

* L'entità di sicurezza di un **gruppo** identifica un set di utenti creati in Azure Active Directory. Tutti i ruoli o le autorizzazioni assegnati al gruppo vengono concessi a tutti gli utenti all'interno del gruppo.

* Un'**entità servizio** è un tipo di entità di sicurezza che identifica un'applicazione o un servizio, ovvero una porzione di codice anziché un utente o un gruppo. L'ID oggetto di un'entità servizio è noto come **ID client** e funge da nome utente. Il **segreto client** dell'entità servizio funge da password.

Per le applicazioni esistono due modi per ottenere un'entità servizio:

* Consigliato: abilitare un'**identità gestita** assegnata dal sistema per l'applicazione.

    Con l'identità gestita, Azure gestisce internamente l'entità servizio dell'applicazione e autentica automaticamente l'applicazione per altri servizi di Azure. L'identità gestita è disponibile per le applicazioni distribuite in un'ampia gamma di servizi.

    Per altre informazioni, vedere [Panoramica delle identità gestite](../../active-directory/managed-identities-azure-resources/overview.md). Vedere anche l'articolo [Servizi di Azure che supportano l'identità gestita](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), che contiene collegamenti ad articoli che descrivono come abilitare l'identità gestita per servizi specifici, ad esempio Servizio app, Funzioni di Azure, Macchine virtuali e così via.

* Se non è possibile usare l'identità gestita, **registrare** invece l'applicazione nel tenant di Azure AD, come descritto in [Avvio rapido: Registrare un'applicazione con la piattaforma delle identità di Azure](../../active-directory/develop/quickstart-register-app.md). La registrazione crea anche un secondo oggetto applicazione che identifica l'app in tutti i tenant.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autorizzare un'entità di sicurezza per l'accesso a Key Vault

Key Vault funziona con due livelli distinti di autorizzazione:

- I **criteri di accesso** controllano se un utente, un gruppo o un'entità servizio è autorizzato ad accedere a segreti, chiavi e certificati *all'interno* di una risorsa di Key Vault esistente (anche dette operazioni sul "piano dati"). I criteri di accesso vengono in genere concessi a utenti, gruppi e applicazioni.

    Per assegnare i criteri di accesso, vedere gli articoli seguenti:

    - [Azure portal](assign-access-policy-portal.md)
    - [Interfaccia della riga di comando di Azure](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- Le **autorizzazioni per i ruoli** controllano se un utente, un gruppo o un'entità servizio è autorizzato a creare, eliminare e gestire in altro modo una risorsa di Key Vault (anche dette operazioni sul "piano di gestione"). Questi ruoli vengono spesso concessi solo agli amministratori.
 
    Per assegnare e gestire i ruoli, vedere gli articoli seguenti:

    - [Azure portal](../../role-based-access-control/role-assignments-portal.md)
    - [Interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Per informazioni generali sui ruoli, vedere [Che cos'è il controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> Per maggiore sicurezza, seguire sempre il principio dei privilegi minimi e concedere solo i criteri di accesso e i ruoli più specifici necessari. 
    
## <a name="configure-the-key-vault-firewall"></a>Configurare il firewall di Key Vault

Per impostazione predefinita, Key Vault consente l'accesso alle risorse tramite indirizzi IP pubblici. Per maggiore sicurezza, è anche possibile limitare l'accesso a specifici intervalli IP, endpoint di servizio, reti virtuali o endpoint privati.

Per altre informazioni, vedere [Accedere ad Azure Key Vault protetto da firewall](./access-behind-firewall.md).


## <a name="the-key-vault-authentication-flow"></a>Flusso di autenticazione di Key Vault

1. Un'entità servizio richiede di eseguire l'autenticazione con Azure AD, ad esempio:
    * Un utente accede al portale di Azure usando un nome utente e una password.
    * Un'applicazione richiama un'API REST di Azure, presentando un ID client e un segreto o un certificato client.
    * Una risorsa di Azure, ad esempio una macchina virtuale con un'identità gestita, contatta l'endpoint REST del [servizio metadati dell'istanza di Azure (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) per ottenere un token di accesso.

1. Se l'autenticazione con Azure AD riesce, all'entità servizio verrà concesso un token OAuth.

1. L'entità servizio effettua una chiamata all'API REST di Key Vault tramite l'endpoint (URI) di Key Vault.

1. Il firewall di Key Vault controlla i criteri seguenti. Se viene soddisfatto un criterio, la chiamata è consentita. In caso contrario, la chiamata viene bloccata e viene restituita una risposta di operazione non consentita.

    * Il firewall viene disabilitato e l'endpoint pubblico di Key Vault è raggiungibile dalla rete Internet pubblica.
    * Il chiamante è un [servizio attendibile di Key Vault](./overview-vnet-service-endpoints.md#trusted-services) e ciò consente di evitare il firewall.
    * Il chiamante è elencato nel firewall in base a indirizzo IP, rete virtuale o endpoint di servizio.
    * Il chiamante può raggiungere Key Vault tramite una connessione di collegamento privato configurata.    

1. Se il firewall consente la chiamata, Key Vault chiama Azure AD per convalidare il token di accesso dell'entità servizio.

1. Key Vault controlla se l'entità servizio ha i criteri di accesso necessari per l'operazione richiesta. In caso contrario, Key Vault restituisce una risposta di operazione non consentita.

1. Key Vault esegue l'operazione richiesta e restituisce il risultato.

Il diagramma seguente illustra il processo di un'applicazione che chiama un'API Key Vault "Get Secret":

![Flusso di autenticazione di Azure Key Vault](../media/authentication/authentication-flow.png)

> [!NOTE]
> I client Key Vault SDK per segreti, certificati e chiavi effettuano una chiamata aggiuntiva a Key Vault senza token di accesso, che restituisce una risposta 401 per recuperare le informazioni sul tenant. Per altre informazioni, vedere [Autenticazione, richieste e risposte](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Esempi di codice

La tabella seguente contiene collegamenti a diversi articoli che illustrano come usare Key Vault nel codice dell'applicazione usando le librerie di Azure SDK per il linguaggio in questione. Sono incluse per praticità altre interfacce, come l'interfaccia della riga di comando di Azure e il portale di Azure.

| Segreti di Key Vault | Chiavi di Key Vault | Certificati Key Vault |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Azure portal](../secrets/quick-create-portal.md) | [Azure portal](../keys/quick-create-portal.md) | [Azure portal](../certificates/quick-create-portal.md) |
| [Interfaccia della riga di comando di Azure](../secrets/quick-create-cli.md) | [Interfaccia della riga di comando di Azure](../keys/quick-create-cli.md) | [Interfaccia della riga di comando di Azure](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [Modello ARM](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi relativi ai criteri di accesso di Key Vault](troubleshooting-access-issues.md)
- [Codici di errore dell'API REST per Key Vault](rest-error-codes.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](developers-guide.md)
- [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../../role-based-access-control/overview.md)
