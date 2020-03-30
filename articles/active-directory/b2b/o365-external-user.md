---
title: Condivisione esterna di Office 365 e collaborazione B2B - Azure AD
description: Descrive come condividere risorse con partner esterni tramite O365 e Collaborazione B2B di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272277"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Condivisione esterna di Office 365 e Collaborazione B2B di Azure Active Directory

La condivisione esterna in Office 365, con OneDrive, SharePoint Online, gruppi unificati e così via, e quella tramite Collaborazione B2B di Azure Active Directory (Azure AD) tecnicamente si equivalgono. Tutti i tipi di condivisione esterna, ad eccezione di OneDrive/SharePoint Online, inclusi gli utenti guest nei gruppi di Office 365, fanno già uso delle API di invito di Collaborazione B2B di Azure AD per la condivisione.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Qual è la differenza tra Azure AD B2B e la condivisione esterna in SharePoint Online?

La gestione degli inviti di OneDrive/SharePoint Online è separata. Il supporto per la condivisione esterna in OneDrive/SharePoint Online è iniziato prima dello sviluppo del supporto di Azure AD. Nel tempo la condivisione esterna di OneDrive/SharePoint Online ha accumulato diverse funzionalità e milioni di utenti, che fanno uso del modello di condivisione incorporato nel prodotto. Rimangono tuttavia alcune sottili differenze di funzionamento tra la condivisione esterna di OneDrive/SharePoint Online e quella di Collaborazione B2B di Azure AD. Altre informazioni sulla condivisione esterna di OneDrive/SharePoint Online sono disponibili nell'articolo [Panoramica sulla condivisione esterna](https://docs.microsoft.com/sharepoint/external-sharing-overview). In generale, questo processo si differenzia da Azure AD B2B negli aspetti seguenti:

- OneDrive/SharePoint consente di aggiungere utenti alla directory dopo che gli utenti hanno riscattato gli inviti. Prima del riscatto l'utente non è quindi visibile nel portale di Azure AD. Se nel frattempo un altro sito invita un utente, viene generato un nuovo invito. Quando invece si usa Collaborazione B2B di Azure AD, gli utenti vengono aggiunti immediatamente all'invito e sono quindi visibili ovunque.

- L'esperienza di riscatto in OneDrive/SharePoint Online è diversa da quella in Collaborazione B2B di Azure Active Directory. Dopo che un utente ha riscattato un invito, le esperienze sono simili.

- Gli utenti invitati di Collaborazione B2B di Azure AD possono essere selezionati nelle finestre di dialogo di condivisione di OneDrive/SharePoint Online. Dopo avere riscattato gli inviti, gli utenti invitati di OneDrive/SharePoint Online vengono visualizzati anche in Azure AD.

- I requisiti di licenza sono diversi. Per ogni licenza di Azure AD a pagamento, è possibile consentire a un massimo di 5 utenti guest di accedere alle funzionalità di Azure AD a pagamento. Per altre informazioni sulle licenze, vedere [Linee guida sulla Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) e ["Che cos'è un utente esterno?" in Panoramica della condivisione esterna di SharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Per gestire la condivisione esterna in OneDrive/SharePoint Online con Collaborazione B2B di Azure AD, configurare l'impostazione relativa alla condivisione esterna in OneDrive/SharePoint Online su **Allow sharing only with the external users that already exist in your organization's directory** (Consenti la condivisione solo con gli utenti esterni già nella directory dell'organizzazione). Gli utenti possono accedere a siti condivisi esternamente e scegliere tra i collaboratori esterni che l'amministratore ha aggiunto. L'amministratore può aggiungere i collaboratori esterni tramite le API di invito di Collaborazione B2B.


![Impostazione relativa alla condivisione esterna di Online OneDrive/SharePoint](media/o365-external-user/odsp-sharing-setting.png)

Dopo aver abilitato la condivisione esterna, la possibilità di cercare gli utenti guest esistenti nella selezione utenti di SharePoint Online è disattivata per impostazione predefinita per corrispondenza con il comportamento legacy.

È possibile abilitare questa funzionalità usando l'impostazione "ShowPeoplePickerSuggestionsForGuestUsers" a livello di tenant e di raccolta siti. Può essere impostata con i cmdlet Set-SPOTenant e Set-SPOSite, che consentono ai membri di cercare tutti gli utenti guest esistenti nella directory. Le modifiche nell'ambito tenant non influiscono sui siti di SPO di cui si è già stato eseguito il provisioning.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è la collaborazione B2B di Azure AD?](what-is-b2b.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](use-dynamic-groups.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](troubleshoot.md)
