---
title: Domande frequenti e problemi noti relativi alle identità gestite - Azure AD
description: Problemi noti relativi alle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/04/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f1be2e64435cb0bcdb369a398a9a65fc3714fb2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008537"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Domande frequenti e problemi noti nell'uso di identità gestite per le risorse di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Domande frequenti (FAQ)

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Come è possibile trovare le risorse con un'identità gestita?

È possibile trovare l'elenco delle risorse che hanno un'identità gestita assegnata dal sistema usando il comando dell'interfaccia della riga di comando di Azure seguente: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>Le identità gestite hanno un oggetto app di supporto?

No. Le identità gestite e le registrazioni App Azure AD non sono la stessa cosa nella directory. 

Registrazioni app hanno due componenti: un oggetto applicazione e un oggetto entità servizio. Le identità gestite per le risorse di Azure hanno solo uno di questi componenti: un oggetto entità servizio. 

Le identità gestite non hanno un oggetto applicazione nella directory, ovvero ciò che viene comunemente usato per concedere le autorizzazioni dell'app per MS Graph. Al contrario, le autorizzazioni di Microsoft Graph per le identità gestite devono essere concesse direttamente all'entità servizio.  

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>È possibile usare la stessa identità gestita in più aree?

In breve, sì, è possibile usare le identità gestite assegnate dall'utente in più di un'area di Azure. La risposta più lunga è che, mentre le identità gestite assegnate dall'utente vengono create come risorse internazionali, l' [entità servizio](../develop/app-objects-and-service-principals.md#service-principal-object) (SPN) associata creata in Azure ad è disponibile a livello globale. L'entità servizio può essere usata da qualsiasi area di Azure e la relativa disponibilità dipende dalla disponibilità di Azure AD. Se, ad esempio, è stata creata un'identità gestita assegnata dall'utente nell'area South-Central e tale area non è più disponibile, questo problema influisca solo sulle attività del [piano di controllo](../../azure-resource-manager/management/control-plane-and-data-plane.md) sull'identità gestita stessa.  Le attività eseguite dalle risorse già configurate per l'utilizzo delle identità gestite non saranno interessate.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Le identità gestite per le risorse di Azure funzionano con i Servizi cloud di Azure?

No, non sono previste iniziative per supportare le identità gestite per le risorse di Azure nei Servizi cloud di Azure.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Quali sono le credenziali associate a un'identità gestita? Quanto tempo è valido e con quale frequenza viene ruotato?

> [!NOTE]
> Il modo in cui le identità gestite si autenticano è un dettaglio di implementazione interno soggetto a modifiche senza preavviso.

Le identità gestite usano l'autenticazione basata su certificati. Le credenziali di ogni identità gestita hanno una scadenza di 90 giorni ed è stato eseguito il rollback dopo 45 giorni.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Cosa si intende per limite di sicurezza delle identità gestite per le risorse di Azure?

Il limite di sicurezza dell'identità è la risorsa a cui è collegata. Ad esempio, il limite di sicurezza per una macchina virtuale con le identità gestite per le risorse di Azure abilitate è la macchina virtuale. Qualsiasi codice in esecuzione su quella macchina virtuale può chiamare l'endpoint delle identità gestite per le risorse di Azure e richiedere i token. L'esperienza è simile a quella con altre risorse che supportano le identità gestite per le risorse di Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Quale identità sarà predefinita da IMDS se non si specifica l'identità nella richiesta?

- Se è abilitata l'identità gestita assegnata dal sistema e non viene specificata nessuna identità nella richiesta, IMDS userà come predefinita l'identità gestita assegnata dal sistema.
- Se non è abilitata l'identità gestita assegnata dal sistema ed esiste solo un'identità gestita assegnata dall'utente, IMDS userà come predefinita quella singola identità gestita assegnata dall'utente. 
- Se non è abilitata l'identità gestita assegnata dal sistema ed esistono più identità gestite assegnate dall'utente, è necessario allora specificare un'identità gestita nella richiesta.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Le identità gestite saranno ricreate automaticamente se si sposta una sottoscrizione in un'altra directory?

No. Se si sposta una sottoscrizione in un'altra directory, sarà necessario ricrearla manualmente e concedere nuovamente le assegnazioni di ruolo di Azure.
- Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
- Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegarle nuovamente alle risorse necessarie, ad esempio macchine virtuali.

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>È possibile usare un'identità gestita per accedere a risorse in tenant/directory diversi?

No. Le identità gestite attualmente non supportano gli scenari tra directory. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quali autorizzazioni di controllo degli accessi in base al ruolo di Azure sono necessarie per un'identità gestita in una risorsa? 

- Identità gestita assegnata dal sistema: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Questa azione è inclusa in ruoli predefiniti specifici della risorsa come [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Identità gestita assegnata dall'utente: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Oltre all'assegnazione di ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator) per l'identità gestita.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Ricerca per categorie impedire la creazione di identità gestite assegnate dall'utente?

È possibile impedire agli utenti di creare identità gestite assegnate dall'utente usando [criteri di Azure](../../governance/policy/overview.md)

- Passare alla [portale di Azure](https://portal.azure.com) e passare a **criterio**.
- Scegli **definizioni**
- Selezionare **+ definizione criteri** e immettere le informazioni necessarie.
- Nella sezione della regola dei criteri incollare

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {}
}

```

Dopo aver creato il criterio, assegnarlo al gruppo di risorse che si vuole usare.

- Passare a gruppi di risorse.
- Trovare il gruppo di risorse usato per il test.
- Scegliere **criteri** dal menu a sinistra.
- Selezionare **assegna criterio**
- Nella sezione **nozioni di base** fornire:
    - **Ambito** Il gruppo di risorse usato per il test
    - **Definizione dei criteri**: il criterio creato in precedenza.
- Lasciare le impostazioni predefinite per tutte le altre impostazioni e scegliere **Verifica + crea**

A questo punto, qualsiasi tentativo di creare un'identità gestita assegnata dall'utente nel gruppo di risorse avrà esito negativo.

  ![Violazione dei criteri](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>Problemi noti

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Lo "Script di automazione" restituisce un errore se si tenta di esportare lo schema per l'estensione relativa alle identità gestite per le risorse di Azure

Se le identità gestite per le risorse di Azure sono abilitate su una macchina virtuale, viene visualizzato l'errore seguente quando si tenta di usare la funzionalità "Script di automazione" per la macchina virtuale o per il relativo gruppo di risorse:

![Errore di esportazione dello script di automazione relativo alle identità gestite per le risorse di Azure](./media/msi-known-issues/automation-script-export-error.png)

L'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019) non supporta attualmente la possibilità di esportare lo schema in un modello di gruppo di risorse. Di conseguenza, il modello generato non mostra i parametri di configurazione per abilitare le identità gestite per le risorse di Azure sulla risorsa. È possibile aggiungere manualmente queste sezioni seguendo gli esempi in [Configurare identità gestite per le risorse di Azure in una macchina virtuale di Azure usando modelli](qs-configure-template-windows-vm.md).

Quando la funzione di esportazione dello schema sarà disponibile per l'estensione della macchina virtuale relativa alle identità gestite per le risorse di Azure (la cui deprecazione è prevista a gennaio 2019), sarà elencata in [Esportazione di gruppi di risorse contenenti estensioni della macchina virtuale](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>La macchina virtuale non viene avviata dopo essere stata spostata dal gruppo di risorse o dalla sottoscrizione

Se si sposta una macchina virtuale in esecuzione, questa continuerà a eseguire durante lo spostamento. Tuttavia, dopo lo spostamento, se la macchina virtuale viene arrestata e riavviata, non si avvierà. Questo problema si verifica perché la macchina virtuale non aggiorna il riferimento alle identità gestite per le risorse di Azure e continua a puntare a questa nel gruppo di risorse precedente.

**Soluzione alternativa** 
 
Attivare un aggiornamento nella macchina virtuale così da poter ottenere i valori corretti per le identità gestite per le risorse di Azure. È possibile apportare una modifica alla proprietà della macchina virtuale per aggiornare il riferimento alle identità gestite per le risorse di Azure. Ad esempio, è possibile impostare un nuovo valore di tag nella macchina virtuale con il comando seguente:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Questo comando imposta un nuovo tag "fixVM" con valore 1 nella macchina virtuale. 
 
Impostando questa proprietà, la macchina virtuale si aggiorna inserendo l'URI risorsa corretto delle identità gestite per le risorse di Azure. La macchina virtuale dovrebbe a questo punto avviarsi. 
 
Dopo aver avviato la macchina virtuale, il tag può essere rimosso tramite il seguente comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Trasferimento di una sottoscrizione tra directory di Azure AD

Le identità gestite non vengono aggiornate quando una sottoscrizione viene spostata/trasferita a un'altra directory. Di conseguenza, tutte le identità gestite assegnate dal sistema o assegnate dall'utente saranno interrotte. 

Soluzione alternativa per le identità gestite in una sottoscrizione che è stata spostata in un'altra directory:

 - Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
 - Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegarle nuovamente alle risorse necessarie, ad esempio macchine virtuali.

Per altre informazioni, vedere [Trasferire una sottoscrizione di Azure in una directory di Azure AD diversa](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Spostamento di un'identità gestita assegnata dall'utente in un'altra sottoscrizione o in un gruppo di risorse diverso

Lo spostamento di un'identità gestita assegnata dall'utente in un gruppo di risorse diverso non è supportato.
