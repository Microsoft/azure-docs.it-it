---
title: Assegnare la licenza per la reimpostazione della password self-service - Azure Active Directory
description: Requisiti di licenza per la reimpostazione password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848562"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione password self-service di Azure AD

Azure Active Directory (Azure AD) è disponibile in diverse edizioni: Gratuito, Premium P1 e Premium P2. Sono diverse le funzionalità che costituiscono la reimpostazione della password self-service, tra cui la modifica, la reimpostazione, lo sblocco e il writeback, tutte disponibili nelle diverse edizioni di Azure AD. Questo articolo tenta di spiegarne le differenze. Informazioni dettagliate sulle funzionalità incluse in ogni edizione di Azure AD sono disponibili nella pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Confrontare le edizioni e le funzionalità

La licenza per la reimpostazione della password self-service di Azure AD viene concessa a livello di singolo utente e, per mantenere la conformità, le organizzazioni devono assegnare ai propri utenti le licenze appropriate.

* Modifica delle password self-service per gli utenti cloud
   * Sono un **utente solo cloud** e ricordo la password.
      * Vorrei **modificare** la mia password con una nuova.
   * Questa funzionalità è inclusa in tutte le edizioni di Azure AD.

* Reimpostazione delle password self-service per gli utenti cloud
   * Sono un **utente solo cloud** e non ricordo la password.
      * Vorrei **reimpostare** la mia password con una che ricordo più facilmente.
   * Questa funzionalità è inclusa in Azure AD Premium P1 o P2, Microsoft 365 Business o Office 365.

* Reimpostazione/modifica/sblocco **con writeback in locale** delle password in modalità self-service
   * Sono un **utente ibrido**, il mio account utente di Active Directory locale è sincronizzato con il mio account di Azure AD tramite Azure AD Connect. Vorrei modificare la mia password, ho dimenticato la password o è stata bloccata.
      * Vorrei modificare la mia password o reimpostarla con una che ricordo più facilmente oppure vorrei sbloccare il mio account **e** sincronizzare la modifica con Active Directory locale.
   * Questa funzionalità è inclusa in Azure AD Premium P1 o P2, o Microsoft 365 Business.

> [!WARNING]
> I piani di licenza Office 365 autonomi *non supportano "Reimpostazione/modifica/sblocco con writeback in locale delle password in modalità self-service"* e richiedono un piano che includa Azure AD Premium P1, Premium P2 o Microsoft 365 Business per l'uso della funzionalità.
>

Informazioni aggiuntive sulle licenze, ad esempio i costi, sono disponibili nelle pagine seguenti:

* [Sito sui prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Caratteristiche e funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Descrizione del servizio Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Abilitare le licenze per gruppi o per utente

Azure AD ora supporta licenze basate sui gruppi. Gli amministratori possono assegnare le licenze in blocco a un gruppo di utenti, anziché assegnarle loro singolarmente. Per altre informazioni, vedere [Assegnare, verificare e risolvere i problemi relativi alle licenze](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Prima di poter assegnare una licenza a un utente, l'amministratore deve specificare la proprietà **Posizione di utilizzo** dell'utente. L'assegnazione delle licenze può essere eseguita nella sezione Impostazioni**profilo utente** > nel portale di Azure.Assignment of licenses can be done under the **User** > Profile**Settings** section in the Azure portal. *Quando si usa l'assegnazione di licenze ai gruppi, tutti gli utenti per cui non è specificata un percorso d'uso ereditano il percorso della directory.*

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrati per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Credo che qualcosa sia rotto. Come si risolvono i problemi relativi a SSPR?](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)
