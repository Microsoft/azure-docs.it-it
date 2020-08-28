---
title: Fasi della distribuzione di un progetto
description: Informazioni sui passaggi correlati alla sicurezza e agli artefatti durante la creazione di un'assegnazione di progetto.
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 73d5d67cf77d4ccc48517c895630b8299081e2e0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049775"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fasi della distribuzione di un progetto

Quando viene distribuito un progetto, una serie di azioni viene eseguita dal servizio Blueprints di Azure per distribuire le risorse definite nel progetto. Questo articolo fornisce informazioni dettagliate su ogni passaggio.

La distribuzione del progetto viene attivata assegnando un progetto a una sottoscrizione o [aggiornando un'assegnazione esistente](../how-to/update-existing-assignments.md). Durante la distribuzione, i progettisti di Azure adottano i passaggi di alto livello seguenti:

> [!div class="checklist"]
> - Progetti di Azure che hanno concesso diritti di proprietario
> - Viene creato l'oggetto di assegnazione progetto
> - Facoltativo: i progettisti di Azure creano un'identità gestita **assegnata dal sistema**
> - L'identità gestita distribuisce gli artefatti del progetto
> - I diritti di identità gestiti **assegnati al sistema** e al servizio di Azure Blueprints vengono revocati

## <a name="azure-blueprints-granted-owner-rights"></a>Progetti di Azure che hanno concesso diritti di proprietario

All'entità servizio di Azure Blueprints vengono concessi i diritti di proprietario per la sottoscrizione o le sottoscrizioni assegnate quando viene usata un'identità gestita gestita da un' [identità gestita assegnata dal sistema](../../../active-directory/managed-identities-azure-resources/overview.md) . Il ruolo concesso consente ai progetti di Azure di creare e revocare successivamente l'identità gestita **assegnata dal sistema** . Se si usa un'identità gestita **assegnata dall'utente** , l'entità servizio di Azure Blueprints non ottiene e non necessita dei diritti di proprietario per la sottoscrizione.

I diritti vengono concessi automaticamente se l'assegnazione viene eseguita tramite il portale. Tuttavia, se l'assegnazione viene eseguita tramite l'API REST, la concessione dei diritti deve essere eseguita con una chiamata API separata. L'AppId dei progetti di Azure è `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` , ma l'entità servizio varia in base al tenant. Usare [Azure Active Directory API Graph](../../../active-directory/develop/active-directory-graph-api.md) e l'endpoint REST [entità servizio](/graph/api/resources/serviceprincipal) per ottenere l'entità servizio. Quindi, concedere al modello di Azure il ruolo di _proprietario_ tramite il [portale](../../../role-based-access-control/role-assignments-portal.md), l' [interfaccia](../../../role-based-access-control/role-assignments-cli.md)della riga di comando di Azure, [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), l' [API REST](../../../role-based-access-control/role-assignments-rest.md)o un [modello di Azure Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Il servizio Azure Blueprints non distribuisce direttamente le risorse.

## <a name="the-blueprint-assignment-object-is-created"></a>Viene creato l'oggetto di assegnazione progetto

Un utente, un gruppo o un'entità servizio assegna un progetto a una sottoscrizione. L'oggetto di assegnazione esiste a livello di sottoscrizione a cui è stato assegnato il progetto. Le risorse create dalla distribuzione non vengono eseguite nel contesto dell'entità di distribuzione.

Durante la creazione dell'assegnazione del progetto, viene selezionato il tipo di [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md) . Il valore predefinito è un'identità gestita **assegnata dal sistema** . È possibile scegliere un'identità gestita **assegnata dall'utente** . Quando si usa un'identità gestita **assegnata dall'utente** , è necessario definire e concedere le autorizzazioni prima della creazione dell'assegnazione del progetto. Sia i ruoli predefiniti dell'operatore [proprietario](../../../role-based-access-control/built-in-roles.md#owner) e [progetto](../../../role-based-access-control/built-in-roles.md#blueprint-operator) hanno l'autorizzazione necessaria `blueprintAssignment/write` per creare un'assegnazione che usa un'identità gestita **assegnata dall'utente** .

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Facoltativo: i progettisti di Azure creano un'identità gestita assegnata dal sistema

Quando si seleziona [identità gestita assegnata dal sistema](../../../active-directory/managed-identities-azure-resources/overview.md) durante l'assegnazione, i progettisti di Azure creano l'identità e assegnano all'identità gestita il ruolo [proprietario](../../../role-based-access-control/built-in-roles.md#owner) . Se [viene aggiornata un'assegnazione esistente](../how-to/update-existing-assignments.md), i progettisti di Azure usano l'identità gestita creata in precedenza.

L'identità gestita correlata all'assegnazione del progetto viene usata per distribuire o ridistribuire le risorse definite nel progetto. Questa progettazione evita che le assegnazioni interferiscano inavvertitamente tra loro.
Questa progettazione supporta anche la funzionalità di [blocco delle risorse](./resource-locking.md) controllando la sicurezza di ogni risorsa distribuita dal progetto.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>L'identità gestita distribuisce gli artefatti del progetto

L'identità gestita attiva quindi le distribuzioni Gestione risorse degli elementi all'interno del progetto nell' [ordine di sequenziazione](./sequencing-order.md)definito. È possibile modificare l'ordine in modo che gli artefatti dipendenti da altri artefatti vengano distribuiti nell'ordine corretto.

Un errore di accesso da parte di una distribuzione è spesso il risultato del livello di accesso concesso all'identità gestita. Il servizio Blueprints di Azure gestisce il ciclo di vita della sicurezza dell'identità gestita **assegnata dal sistema** . Tuttavia, l'utente è responsabile della gestione dei diritti e del ciclo di vita di un'identità gestita **assegnata dall'utente** .

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Il servizio Blueprint e i diritti di identità gestiti assegnati dal sistema vengono revocati

Una volta completate le distribuzioni, i progettisti di Azure revocano i diritti dell'identità gestita **assegnata dal sistema** dalla sottoscrizione. Quindi, il servizio progetti di Azure revoca i propri diritti dalla sottoscrizione. La rimozione dei diritti impedisce che i progetti di Azure diventino un proprietario permanente in una sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare [parametri statici e dinamici](./parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](./sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](./resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).
