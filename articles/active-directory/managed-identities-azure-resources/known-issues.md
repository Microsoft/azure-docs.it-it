---
title: Domande frequenti e problemi noti con le identità gestite - Azure AD
description: Problemi noti relativi alle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266533"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Domande frequenti e problemi noti nell'uso di identità gestite per le risorse di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Le identità gestite per le risorse di Azure funzionano con i Servizi cloud di Azure?

No, non sono previste iniziative per supportare le identità gestite per le risorse di Azure nei Servizi cloud di Azure.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Le identità gestite per le risorse di Azure funzionano con Active Directory Authentication Library (ADAL) o con Microsoft Authentication Library (MSAL)?

No, le identità gestite per le risorse di Azure non sono ancora integrate con ADAL o MSAL. Per informazioni dettagliate sull'acquisizione di un token per le identità gestite per le risorse di Azure usando l'endpoint REST, vedere [Come usare le identità gestite per le risorse](how-to-use-vm-token.md)di Azure in una macchina virtuale di Azure per acquisire un token di accesso.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Cosa si intende per limite di sicurezza delle identità gestite per le risorse di Azure?

Il limite di sicurezza dell'identità è la risorsa a cui è collegata. Ad esempio, il limite di sicurezza per una macchina virtuale con le identità gestite per le risorse di Azure abilitate è la macchina virtuale. Qualsiasi codice in esecuzione su quella macchina virtuale può chiamare l'endpoint delle identità gestite per le risorse di Azure e richiedere i token. L'esperienza è simile a quella con altre risorse che supportano le identità gestite per le risorse di Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Quale identità sarà predefinita da IMDS se non si specifica l'identità nella richiesta?

- Se è abilitata l'identità gestita assegnata dal sistema e non viene specificata nessuna identità nella richiesta, IMDS userà come predefinita l'identità gestita assegnata dal sistema.
- Se non è abilitata l'identità gestita assegnata dal sistema ed esiste solo un'identità gestita assegnata dall'utente, IMDS userà come predefinita quella singola identità gestita assegnata dall'utente. 
- Se non è abilitata l'identità gestita assegnata dal sistema ed esistono più identità gestite assegnate dall'utente, è necessario allora specificare un'identità gestita nella richiesta.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>È consigliabile usare le identità gestite per l'endpoint IMDS delle risorse di Azure o l'endpoint di estensione della macchina virtuale?

Quando si usano le identità gestite per le risorse di Azure con le macchine virtuali, è consigliabile usare l'endpoint IMDS.When using managed identities for Azure resources with VMs, we recommend using the IMDS endpoint. Il Servizio metadati dell'istanza di Azure è un endpoint REST accessibile a tutte le macchine virtuali IaaS create tramite Azure Resource Manager. 

Alcuni vantaggi dell'uso delle identità gestite per le risorse di Azure nel servizio metadati dell'istanza sono:
- Tutti i sistemi operativi supportati da IaaS di Azure possono usare le identità gestite per le risorse di Azure sul servizio metadati dell'istanza.
- Non è più necessario installare un'estensione nella macchina virtuale per abilitare le identità per le risorse di Azure. 
- I certificati usati dalle identità gestite per le risorse di Azure non sono più presenti nella macchina virtuale.
- L'endpoint IMDS è un indirizzo IP non instradabile noto disponibile solo dalla macchina virtuale.
- È possibile assegnare 1000 identità gestite assegnate dall'utente a una singola macchina virtuale. 

L'estensione della macchina virtuale delle identità gestite per le risorse di Azure è ancora disponibile. tuttavia, non stiamo più sviluppando nuove funzionalità su di esso. È consigliabile passare per utilizzare l'endpoint IMDS. 

Alcune delle limitazioni relative all'uso dell'endpoint di estensione della macchina virtuale sono:Some of the limitations of using the VM extension endpoint are:
- Supporto limitato per le distribuzioni Linux: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
- Solo 32 identità gestite assegnate dall'utente possono essere assegnate alla macchina virtuale.


Nota: l'estensione della macchina virtuale delle identità gestite per le risorse di Azure non sarà supportata a gennaio 2019.Note: The managed identities for Azure resources VM extension will be out of support in January 2019. 

Per altre informazioni sul servizio metadati dell'istanza di Azure, vedere la [documentazione di IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Le identità gestite saranno ricreate automaticamente se si sposta una sottoscrizione in un'altra directory?

No. Se si sposta una sottoscrizione in un'altra directory, sarà necessario ricrearla manualmente e concedere nuovamente le assegnazioni di ruolo del Controllo degli accessi in base al ruolo di Azure.
- Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
- Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegare nuovamente alle risorse necessarie (ad esempio macchine virtuali)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>È possibile usare un'identità gestita per accedere a risorse in tenant/directory diversi?

No. Le identità gestite attualmente non supportano gli scenari tra directory. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quali autorizzazioni di Controllo degli accessi in base al ruolo di Azure sono necessarie per l'identità gestita in una risorsa? 

- Identità gestita assegnata dal sistema: sono necessarie autorizzazioni di scrittura sulla risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Questa azione è inclusa nei ruoli predefiniti specifici delle risorse, ad esempio [Collaboratore macchina virtuale](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Identità gestita assegnata dall'utente: sono necessarie autorizzazioni di scrittura sulla risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Oltre all'assegnazione di ruolo [Operatore identità gestita](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) sull'identità gestita.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Come riavviare l'estensione relativa alle identità gestite per le risorse di Azure
In Windows e alcune versioni di Linux, se si arresta l'estensione, è possibile usare il cmdlet seguente per riavviarla manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Dove: 
- Il nome e il tipo di estensione per Windows è: ManagedIdentityExtensionForWindows
- Il nome e il tipo di estensione per Linux è: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemi noti

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Lo "Script di automazione" restituisce un errore se si tenta di esportare lo schema per l'estensione relativa alle identità gestite per le risorse di Azure

Se le identità gestite per le risorse di Azure sono abilitate su una macchina virtuale, viene visualizzato l'errore seguente quando si tenta di usare la funzionalità "Script di automazione" per la macchina virtuale o per il relativo gruppo di risorse:

![Errore di esportazione dello script di automazione relativo alle identità gestite per le risorse di Azure](./media/msi-known-issues/automation-script-export-error.png)

L'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019) non supporta attualmente la possibilità di esportare lo schema in un modello di gruppo di risorse. Di conseguenza, il modello generato non mostra i parametri di configurazione per abilitare le identità gestite per le risorse di Azure sulla risorsa. È possibile aggiungere manualmente queste sezioni seguendo gli esempi in [Configurare identità gestite per le risorse di Azure in una macchina virtuale di Azure usando modelli](qs-configure-template-windows-vm.md).

Quando la funzione di esportazione dello schema sarà disponibile per l'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019), sarà elencata in [Esportazione di gruppi di risorse contenenti estensioni della macchina virtuale](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La macchina virtuale non viene avviata dopo essere stata spostata dal gruppo di risorse o dalla sottoscrizione

Se si sposta una macchina virtuale in esecuzione, questa continuerà a eseguire durante lo spostamento. Tuttavia, dopo lo spostamento, se la macchina virtuale viene arrestata e riavviata, non si avvierà. Questo problema si verifica perché la macchina virtuale non aggiorna il riferimento alle identità gestite per le risorse di Azure e continua a puntare a questa nel gruppo di risorse precedente.

**Soluzione** 
 
Attivare un aggiornamento nella macchina virtuale così da poter ottenere i valori corretti per le identità gestite per le risorse di Azure. È possibile apportare una modifica alla proprietà della macchina virtuale per aggiornare il riferimento alle identità gestite per le risorse di Azure. Ad esempio, è possibile impostare un nuovo valore di tag nella macchina virtuale con il comando seguente:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Questo comando imposta un nuovo tag "fixVM" con valore 1 nella macchina virtuale. 
 
Impostando questa proprietà, la macchina virtuale si aggiorna inserendo l'URI risorsa corretto delle identità gestite per le risorse di Azure. La macchina virtuale dovrebbe a questo punto avviarsi. 
 
Dopo aver avviato la macchina virtuale, il tag può essere rimosso tramite il seguente comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Il provisioning dell'estensione VM non riesce

Il provisioning dell'estensione VM potrebbe non riuscire a causa di errori di ricerca DNS. Riavviare la macchina virtuale e riprovare.
 
> [!NOTE]
> La deprecazione dell'endpoint dell'estensione della macchina virtuale è prevista per gennaio 2019. Si consiglia di passare all'uso dell'endpoint IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Trasferimento di una sottoscrizione tra directory di Azure AD

Le identità gestite non vengono aggiornate quando una sottoscrizione viene spostata/trasferita a un'altra directory. Di conseguenza, tutte le identità gestite assegnate dal sistema o assegnate dall'utente saranno interrotte. 

Soluzione alternativa per le identità gestite in una sottoscrizione spostata in un'altra directory:

 - Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
 - Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegare nuovamente alle risorse necessarie (ad esempio macchine virtuali)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Spostamento di un'identità gestita assegnata dall'utente in un gruppo di risorse/sottoscrizione diverso

Lo spostamento di un'identità gestita assegnata dall'utente a un gruppo di risorse diverso causerà l'interruzione dell'identità. Di conseguenza, le risorse (ad esempio la macchina virtuale) che usano tale identità non saranno in grado di richiedere token per tale IDentità. 
