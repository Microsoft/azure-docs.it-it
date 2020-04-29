---
title: Crittografare gli asset protetti in Automazione di Azure
description: Automazione di Azure protegge le risorse protette usando più livelli di crittografia. Per impostazione predefinita, la crittografia viene eseguita usando chiavi gestite da Microsoft. I clienti possono configurare gli account di automazione in modo da usare chiavi gestite dal cliente per la crittografia. Questo articolo descrive i dettagli di entrambe le modalità di crittografia e il modo in cui è possibile spostarsi tra i due.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 594bac257c2b9739f1ece276c881348b35d2f704
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604822"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Crittografare gli asset protetti in Automazione di Azure

Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset sono protetti in automazione di Azure usando più livelli di crittografia. In base alla chiave di primo livello utilizzata per la crittografia, sono disponibili due modelli per la crittografia:
-    Uso delle chiavi gestite da Microsoft
-    Uso delle chiavi gestite dal cliente

## <a name="microsoft-managed-keys"></a>Chiavi gestite da Microsoft

Per impostazione predefinita, l'account di automazione di Azure usa chiavi gestite da Microsoft.

Ogni asset sicuro viene crittografato e archiviato in automazione di Azure tramite una chiave univoca (chiave di crittografia dei dati) generata per ogni account di automazione. Queste chiavi vengono crittografate e archiviate in automazione di Azure usando anche un'altra chiave univoca generata per ogni account denominato chiave di crittografia dell'account (AEK). Queste chiavi di crittografia dell'account vengono crittografate e archiviate in automazione di Azure tramite chiavi gestite da Microsoft. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Chiavi gestite dal cliente con Key Vault (anteprima)

È possibile gestire la crittografia degli asset protetti per l'account di automazione con le proprie chiavi. Quando si specifica una chiave gestita dal cliente al livello dell'account di automazione, questa chiave viene usata per proteggere e controllare l'accesso alla chiave di crittografia dell'account per l'account di automazione. Questa operazione viene a sua volta usata per crittografare e decrittografare tutti gli asset protetti. Le chiavi gestite dal cliente offrono maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere gli asset protetti.

Utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi.  Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Abilitare le chiavi gestite dal cliente per un account di automazione

Quando si Abilita la crittografia con chiavi gestite dal cliente per un account di automazione, automazione di Azure esegue il wrapping della chiave di crittografia dell'account con la chiave gestita dal cliente nell'insieme di credenziali delle chiavi associato. L'abilitazione delle chiavi gestite dal cliente non influisca sulle prestazioni e l'account viene crittografato con la nuova chiave immediatamente, senza alcun ritardo.

Un nuovo account di automazione è sempre crittografato con le chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente al momento della creazione dell'account. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata all'account di automazione. L'identità gestita è disponibile solo dopo la creazione dell'account di archiviazione.

Quando si modifica la chiave usata per la crittografia di asset sicura di automazione di Azure, abilitando o disabilitando le chiavi gestite dal cliente, aggiornando la versione della chiave o specificando una chiave diversa, la crittografia della chiave di crittografia dell'account viene modificata, ma non è necessario crittografare di nuovo le risorse sicure nell'account di automazione di Azure.

Nelle tre sezioni seguenti vengono descritti i meccanismi di abilitazione delle chiavi gestite dal cliente per un account di automazione. 

> [!NOTE] 
> Per abilitare le chiavi gestite dal cliente, è necessario eseguire chiamate API REST di automazione di Azure usando l'API versione 2020-01-13-Preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Prerequisiti per l'uso delle chiavi gestite dal cliente in automazione di Azure

Prima di abilitare le chiavi gestite dal cliente per un account di automazione, è necessario verificare che siano soddisfatti i seguenti prerequisiti:

 - La chiave cliente è archiviata in un Azure Key Vault. 
 - Abilitare sia l' **eliminazione** temporanea che **non ripulire** le proprietà nell'insieme di credenziali delle chiavi. Queste funzionalità sono necessarie per consentire il ripristino delle chiavi in caso di eliminazione accidentale.
 - Con la crittografia di automazione di Azure sono supportate solo le chiavi RSA. Per ulteriori informazioni sulle chiavi, vedere [informazioni su Azure Key Vault chiavi, segreti e certificati](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- L'account di automazione e l'insieme di credenziali delle chiavi possono trovarsi in sottoscrizioni diverse, ma devono trovarsi nello stesso tenant Azure Active Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Assegnare un'identità all'account di automazione

Per usare chiavi gestite dal cliente con un account di automazione, l'account di automazione deve eseguire l'autenticazione nell'insieme di credenziali delle chiavi che archivia le chiavi gestite dal cliente. Automazione di Azure Usa identità gestite assegnate dal sistema per autenticare l'account con Azure Key Vault. Per altre informazioni sulle identità gestite, vedere [che cosa sono le identità gestite per le risorse di Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Configurare un'identità gestita assegnata dal sistema per l'account di automazione usando la chiamata all'API REST seguente:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo della richiesta:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

L'identità assegnata dal sistema per l'account di automazione viene restituita in una risposta simile alla seguente:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso Key Vault

Dopo che un'identità gestita è stata assegnata all'account di automazione, è possibile configurare l'accesso all'insieme di credenziali delle chiavi che archivia le chiavi gestite dal cliente. Automazione di Azure richiede **Get**, **Recover**, **wrapKey**, **UnwrapKey** sulle chiavi gestite dal cliente.

È possibile impostare un criterio di accesso di questo tipo usando la chiamata all'API REST seguente:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Corpo della richiesta:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> È necessario fornire i campi **tenantId** e **ObjectID** con i valori **Identity. tenantId** e **Identity. PrincipalId** rispettivamente dalla risposta dell'identità gestita per l'account di automazione.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Modificare la configurazione dell'account di automazione per usare la chiave gestita dal cliente

Infine, è possibile cambiare l'account di automazione da chiavi gestite da Microsoft alle chiavi gestite dal cliente, usando la chiamata all'API REST seguente:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo della richiesta:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Risposta di esempio

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Gestire il ciclo di vita delle chiavi gestite dal cliente

### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare l'account di automazione per usare il nuovo URI della chiave.

La rotazione della chiave non attiva la nuova crittografia degli asset protetti nell'account di automazione. Non sono necessarie altre azioni.

### <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) o l'interfaccia della riga di comando di [Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca in modo efficace l'accesso a tutti gli asset protetti nell'account di automazione, in quanto la chiave di crittografia non è accessibile da automazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'insieme di credenziali delle chiavi di Azure](../key-vault/general/overview.md)
- [Asset di tipo certificato in Automazione di Azure](shared-resources/certificates.md)
- [Asset credenziali in Automazione di Azure](shared-resources/credentials.md)
- [Asset di tipo variabile in Automazione di Azure](shared-resources/variables.md)
