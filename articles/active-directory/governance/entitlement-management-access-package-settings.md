---
title: Condividi il collegamento per richiedere un pacchetto di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come condividere il collegamento per richiedere un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: msaburnley
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
ms.openlocfilehash: 7a29f0a0231cdea5a73b7798088002e63ec93324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078563"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Condividi il collegamento per richiedere un pacchetto di accesso in Azure AD gestione dei diritti

La maggior parte degli utenti nella directory può accedere al portale di accesso personale e visualizzare automaticamente un elenco di pacchetti di accesso che possono richiedere. Tuttavia, per gli utenti di partner commerciali esterni che non si trovano ancora nella directory, è necessario inviare loro un collegamento che possono usare per richiedere un pacchetto di accesso. 

Fino a quando il catalogo per il pacchetto di accesso è [abilitato per gli utenti esterni](entitlement-management-catalog-create.md) e si dispone di un [criterio per la directory dell'utente](entitlement-management-access-package-request-policy.md)esterno, l'utente esterno può usare il collegamento portale di accesso personale per richiedere il pacchetto di accesso.

## <a name="share-link-to-request-an-access-package"></a>Condividi il collegamento per richiedere un pacchetto di accesso

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Nella pagina Overview copiare il **collegamento My Access Portal**.

    ![Panoramica del pacchetto di accesso - Collegamento del portale di Accesso personale](./media/entitlement-management-shared/my-access-portal-link.png)

    È importante copiare l'intero collegamento del portale di accesso personale quando lo si invia a un partner aziendale interno. In questo modo si garantisce che il partner ottenga l'accesso al portale della directory per effettuare la richiesta. Il collegamento inizia con `myaccess` , include un hint di directory e termina con un ID pacchetto di accesso.  Per il governo degli Stati Uniti, il dominio nel collegamento al portale di accesso personale sarà `myaccess.microsoft.us` .

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Inviare un messaggio di posta elettronica o inviare il collegamento al partner aziendale esterno. Possono condividere il collegamento con i loro utenti per richiedere il pacchetto di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md)
- [Approva o rifiuta le richieste di accesso](entitlement-management-request-approve.md)