---
title: Procedure di sicurezza consigliate
description: Quando si usa Azure Lighthouse, è importante prendere in considerazione la sicurezza e il controllo degli accessi.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 3aa50833b547882506bfad125992bb1c2f4e85bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419304"
---
# <a name="recommended-security-practices"></a>Procedure di sicurezza consigliate

Quando si usa [Azure Lighthouse](../overview.md), è importante prendere in considerazione la sicurezza e il controllo degli accessi. Gli utenti del tenant avranno accesso diretto alle sottoscrizioni dei clienti e ai gruppi di risorse, quindi è opportuno eseguire le operazioni necessarie per gestire la sicurezza del tenant. Sarà inoltre necessario assicurarsi di consentire solo l'accesso necessario per gestire efficacemente le risorse dei clienti. Questo argomento fornisce indicazioni utili a tale scopo.

> [!TIP]
> Queste raccomandazioni sono valide anche per le [aziende che gestiscono più tenant](enterprise.md) con Azure Lighthouse.

## <a name="require-azure-ad-multi-factor-authentication"></a>Richiedere Azure AD Multi-Factor Authentication

[Azure AD multi-factor authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (anche noto come verifica in due passaggi) consente agli utenti malintenzionati di accedere a un account richiedendo più passaggi di autenticazione. È necessario richiedere Multi-Factor Authentication per tutti gli utenti del tenant di gestione, inclusi gli utenti che avranno accesso alle risorse dei clienti Delegate.

Si consiglia di richiedere ai clienti di implementare Azure AD Multi-Factor Authentication anche nei propri tenant.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Assegnare le autorizzazioni ai gruppi, usando il principio dei privilegi minimi

Per semplificare la gestione, usare i gruppi Azure Active Directory (Azure AD) per ogni ruolo necessario per gestire le risorse dei clienti. Ciò consente di aggiungere o rimuovere singoli utenti al gruppo in base alle esigenze, anziché assegnare le autorizzazioni direttamente a ogni utente.

> [!IMPORTANT]
> Per aggiungere autorizzazioni per un gruppo di Azure AD, il **tipo di gruppo** deve essere impostato su **sicurezza**. Questa opzione è selezionata quando viene creato il gruppo. Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Quando si crea la struttura di autorizzazioni, assicurarsi di seguire il principio dei privilegi minimi, in modo che gli utenti abbiano solo le autorizzazioni necessarie per completare il proprio lavoro, riducendo la possibilità di errori accidentali.

È ad esempio possibile usare una struttura simile alla seguente:

|Nome gruppo  |Tipo  |principalId  |Definizione di ruolo  |ID di definizione del ruolo  |
|---------|---------|---------|---------|---------|
|Architetti     |Gruppo utenti         |\<principalId\>         |Collaboratore         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Valutazione     |Gruppo utenti         |\<principalId\>         |Lettore         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Specialisti di macchine virtuali     |Gruppo utenti         |\<principalId\>         |Collaboratore macchina virtuale         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automazione     |Nome dell'entità servizio (SPN)         |\<principalId\>         |Autore di contributi         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Una volta creati questi gruppi, è possibile assegnare gli utenti in base alle esigenze. Aggiungere solo gli utenti che devono avere effettivamente accesso. Controllare regolarmente l'appartenenza ai gruppi e rimuovere gli utenti che non è più appropriato o necessario includere.

Tenere presente che, quando si [esegue l'onboarding di clienti tramite un'offerta di servizio gestito pubblica](../how-to/publish-managed-services-offers.md), qualsiasi gruppo (o utente o entità servizio) incluso avrà le stesse autorizzazioni per ogni cliente che acquista il piano. Per assegnare gruppi diversi per lavorare con ogni cliente, è necessario pubblicare un piano privato separato che è esclusivo per ogni cliente o caricare i clienti singolarmente tramite Azure Resource Manager modelli. È ad esempio possibile pubblicare un piano pubblico con accesso molto limitato, quindi collaborare direttamente con il cliente per eseguire l'onboarding delle risorse per l'accesso aggiuntivo usando un modello di risorsa di Azure personalizzato che concede l'accesso aggiuntivo in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare le [informazioni di base sulla sicurezza](../security-baseline.md) per capire in che modo le linee guida del benchmark di sicurezza di Azure si applicano al Faro di Azure.
- [Distribuire Azure AD multi-factor authentication](../../active-directory/authentication/howto-mfa-getstarted.md).
- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
