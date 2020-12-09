---
title: Creare un nuovo pacchetto di accesso nella gestione dei diritti-Azure AD
description: Informazioni su come creare un nuovo pacchetto di risorse di accesso che si vuole condividere in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2ce3b362d02e7acb0a11e6d93b8e94ca8e4d04e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903531"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Creare un nuovo pacchetto di accesso in Azure AD gestione dei diritti

Un pacchetto di accesso consente di eseguire una singola installazione di risorse e criteri che amministra automaticamente l'accesso per la durata del pacchetto di accesso. Questo articolo descrive come creare un nuovo pacchetto di accesso.

## <a name="overview"></a>Panoramica

Tutti i pacchetti di accesso devono essere inseriti in un contenitore denominato catalogo. Un catalogo consente di definire le risorse che è possibile aggiungere al pacchetto di accesso. Se non si specifica un catalogo, il pacchetto di accesso verrà inserito nel catalogo generale. Attualmente non è possibile spostare un pacchetto di accesso esistente in un catalogo diverso.

Se si è una gestione pacchetti di Access, non è possibile aggiungere risorse di cui si è proprietari a un catalogo. Si è limitati all'uso delle risorse disponibili nel catalogo. Se è necessario aggiungere risorse a un catalogo, è possibile richiedere il proprietario del catalogo.

Tutti i pacchetti di accesso devono avere almeno un criterio. I criteri specificano chi può richiedere il pacchetto di accesso e anche le impostazioni di approvazione e ciclo di vita. Quando si crea un nuovo pacchetto di accesso, è possibile creare un criterio iniziale per gli utenti nella directory, per gli utenti non inclusi nella directory, solo per le assegnazioni dirette all'amministratore oppure è possibile scegliere di creare il criterio in un secondo momento.

![Creare un pacchetto di accesso](./media/entitlement-management-access-package-create/access-package-create.png)

Ecco i passaggi di alto livello per creare un nuovo pacchetto di accesso.

1. In governance delle identità avviare il processo per creare un nuovo pacchetto di accesso.

1. Selezionare il catalogo in cui si vuole creare il pacchetto di accesso.

1. Aggiungere le risorse dal catalogo al pacchetto di accesso.

1. Assegnare i ruoli delle risorse per ogni risorsa.

1. Specificare gli utenti che possono richiedere l'accesso.

1. Specificare le impostazioni di approvazione.

1. Specificare le impostazioni del ciclo di vita.

## <a name="start-new-access-package"></a>Avvia nuovo pacchetto di accesso

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.

1. Fare clic su **Nuovo pacchetto di accesso**.
   
    ![Gestione entitlement nel portale di Azure](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Nozioni fondamentali

Nella scheda **nozioni di base** assegnare un nome al pacchetto di accesso e specificare il catalogo in cui creare il pacchetto di accesso.

1. Immettere un nome visualizzato e una descrizione per il pacchetto di accesso. Queste informazioni verranno visualizzate dagli utenti quando inviano una richiesta per il pacchetto di accesso.

1. Nell'elenco a discesa **Catalogo** selezionare il catalogo in cui si vuole creare il pacchetto di accesso. Ad esempio, si potrebbe avere un proprietario del catalogo che gestisce tutte le risorse di marketing che possono essere richieste. In questo caso, è possibile selezionare il catalogo marketing.

    Vengono visualizzati solo i cataloghi per i quali si dispone dell'autorizzazione per creare pacchetti di accesso in. Per creare un pacchetto di accesso in un catalogo esistente, è necessario essere un amministratore globale o un amministratore utente oppure è necessario essere un proprietario del catalogo o accedere a gestione pacchetti in tale catalogo.

    ![Accedere a Package-nozioni di base](./media/entitlement-management-access-package-create/basics.png)

    Se si è un amministratore globale, un amministratore utente o un creatore del catalogo e si desidera creare il pacchetto di accesso in un nuovo catalogo non elencato, fare clic su **Crea nuovo catalogo**. Immettere il nome del catalogo e la descrizione, quindi fare clic su **Crea**.

    Il pacchetto di accesso che si sta creando e le eventuali risorse incluse verranno aggiunte al nuovo catalogo. È anche possibile aggiungere altri proprietari del catalogo in un secondo momento.

1. Fare clic su **Avanti**.

## <a name="resource-roles"></a>Ruoli delle risorse

Nella scheda **ruoli risorsa** selezionare le risorse da includere nel pacchetto di accesso. Gli utenti che richiedono e ricevono il pacchetto di accesso riceveranno tutti i ruoli delle risorse nel pacchetto di accesso.

1. Fare clic sul tipo di risorsa che si desidera aggiungere (**gruppi e team**, **applicazioni** o **siti di SharePoint**).

1. Nel riquadro Seleziona visualizzato selezionare una o più risorse dall'elenco.

    ![Accedere ai ruoli delle risorse del pacchetto](./media/entitlement-management-access-package-create/resource-roles.png)

    Se si sta creando il pacchetto di accesso nel catalogo generale o in un nuovo catalogo, sarà possibile scegliere qualsiasi risorsa dalla directory di cui si è proprietari. È necessario essere almeno un amministratore globale, un amministratore utente o un creatore del catalogo.

    Se si sta creando il pacchetto di accesso in un catalogo esistente, è possibile selezionare qualsiasi risorsa già presente nel catalogo senza possederla.

    Se si è un amministratore globale, un amministratore utente o un proprietario del catalogo, è possibile selezionare le risorse di cui si è proprietari e che non sono ancora presenti nel catalogo. Se si selezionano risorse non presenti nel catalogo selezionato, queste risorse verranno aggiunte anche al catalogo per consentire ad altri amministratori del catalogo di compilare pacchetti di accesso con. Per visualizzare tutte le risorse che è possibile aggiungere al catalogo, selezionare la casella di controllo **Visualizza tutto** nella parte superiore del riquadro Seleziona. Se si desidera solo selezionare le risorse attualmente presenti nel catalogo selezionato, lasciare **la casella di** controllo deselezionata (stato predefinito).

1. Dopo aver selezionato le risorse, nell'elenco **ruolo** selezionare il ruolo che si desidera assegnare agli utenti per la risorsa.

    ![Accedere al pacchetto: selezione del ruolo delle risorse](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Fare clic su **Avanti**.

>[!NOTE]
>È possibile aggiungere gruppi dinamici a un catalogo e a un pacchetto di accesso. Tuttavia, sarà possibile selezionare solo il ruolo proprietario quando si gestisce una risorsa gruppo dinamico in un pacchetto di accesso.

## <a name="requests"></a>Requests

Nella scheda **richieste** creare il primo criterio per specificare gli utenti che possono richiedere il pacchetto di accesso e anche le impostazioni di approvazione. Successivamente, è possibile creare altri criteri di richiesta per consentire a gruppi aggiuntivi di utenti di richiedere il pacchetto di accesso con le proprie impostazioni di approvazione.

![Scheda Access Package-requests](./media/entitlement-management-access-package-create/requests.png)

A seconda di chi desidera essere in grado di richiedere questo pacchetto di accesso, eseguire i passaggi in una delle sezioni seguenti.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Rivedere e creare

Nella scheda **Verifica e crea** è possibile esaminare le impostazioni e verificare la presenza di eventuali errori di convalida.

1. Esaminare le impostazioni del pacchetto di accesso

    ![Accesso al pacchetto-Abilita impostazione dei criteri](./media/entitlement-management-access-package-create/review-create.png)

1. Fare clic su **Crea** per creare il pacchetto di accesso.

    Il nuovo pacchetto di accesso verrà visualizzato nell'elenco dei pacchetti di accesso.

## <a name="creating-an-access-package-programmatically"></a>Creazione di un pacchetto di accesso a livello di codice

È inoltre possibile creare un pacchetto di accesso utilizzando Microsoft Graph.  Un utente in un ruolo appropriato con un'applicazione con l'autorizzazione delegata `EntitlementManagement.ReadWrite.All` può chiamare l'API per

1. [Elencare accessPackageResources nel catalogo](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) e [creare un accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) per tutte le risorse che non sono ancora presenti nel catalogo.
1. [Elencare il accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) di ogni accessPackageResource in un accessPackageCatalog. Questo elenco di ruoli verrà quindi usato per selezionare un ruolo, quando successivamente si crea un accessPackageResourceRoleScope.
1. [Creare un accessPackage](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Creare un accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Creare un accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) per ogni ruolo risorsa necessario nel pacchetto di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Condividi il collegamento per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md)
- [Modificare i ruoli delle risorse per un pacchetto di accesso](entitlement-management-access-package-resources.md)
- [Assegnare direttamente un utente al pacchetto di accesso](entitlement-management-access-package-assignments.md)
