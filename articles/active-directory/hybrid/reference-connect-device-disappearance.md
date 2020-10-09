---
title: Informazioni Azure AD Connect 1.4. XX. x e la scomparsa del dispositivo | Microsoft Docs
description: Questo documento descrive un problema che si verifica con la versione 1.4. XX. x di Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73176034"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Informazioni Azure AD Connect 1.4. XX. x e la scomparsa del dispositivo
Con la versione 1.4. XX. x di Azure AD Connect, alcuni clienti potrebbero visualizzare alcuni o tutti i dispositivi Windows scomparsi da Azure AD. Questo non è un problema, perché queste identità di dispositivo non vengono usate da Azure AD durante l'autorizzazione dell'accesso condizionale. Questa modifica non eliminerà i dispositivi Windows registrati correttamente con Azure AD per Aggiunta ad Azure AD ibrido.

Se viene visualizzata l'eliminazione degli oggetti dispositivo in Azure AD superando la soglia di eliminazione dell'esportazione, è consigliabile che il cliente consenta il passaggio delle eliminazioni. [Procedura: consentire la propagazione delle eliminazioni quando superano la soglia di eliminazione](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Background
I dispositivi Windows registrati come Azure AD ibrido Uniti sono rappresentati in Azure AD come oggetti dispositivo. Questi oggetti dispositivo possono essere utilizzati per l'accesso condizionale. I dispositivi Windows 10 vengono sincronizzati con il cloud tramite Azure AD Connect, i dispositivi Windows di livello inferiore vengono registrati direttamente usando AD FS o Single Sign-On Seamless.

## <a name="windows-10-devices"></a>Dispositivi Windows 10
Solo i dispositivi Windows 10 con un valore dell'attributo userCertificate specifico configurato da Aggiunta ad Azure AD ibrido devono essere sincronizzati con il Cloud Azure AD Connect. Nelle versioni precedenti di Azure AD Connect questo requisito non è stato applicato rigorosamente, causando la Azure AD di oggetti dispositivo non necessari. Tali dispositivi in Azure AD sempre rimasti nello stato "in sospeso" perché questi dispositivi non erano destinati alla registrazione con Azure AD. 

Con questa versione di Azure AD Connect verranno sincronizzati solo i dispositivi Windows 10 configurati correttamente per essere Azure AD ibrido Uniti in join. Gli oggetti dispositivo Windows 10 senza il Azure AD userCertificate specifico join verranno rimossi da Azure AD.

## <a name="down-level-windows-devices"></a>Dispositivi Down-Level Windows
Azure AD Connect non devono mai sincronizzare i [dispositivi Windows di livello inferiore](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Tutti i dispositivi in Azure AD precedentemente sincronizzati in modo non corretto verranno eliminati da Azure AD. Se Azure AD Connect sta tentando di eliminare i [dispositivi Windows di livello inferiore](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), il dispositivo non è quello creato da [Microsoft workplace join per i computer non Windows 10 MSI](https://www.microsoft.com/download/details.aspx?id=53554) e non può essere utilizzato da altre funzionalità di Azure ad.

È possibile che alcuni clienti debbano rivedere [procedura: pianificare l'implementazione ibrida di Azure Active Directory join](../devices/hybrid-azuread-join-plan.md) per ottenere la registrazione corretta dei dispositivi Windows e assicurarsi che tali dispositivi possano partecipare completamente all'accesso condizionale basato su dispositivo. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Come è possibile verificare I dispositivi eliminati con questo aggiornamento?

Per verificare quali dispositivi vengono eliminati, è possibile usare questo script di PowerShell: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Questo script genera un report sui certificati archiviati in Active Directory oggetti computer, in particolare i certificati emessi dalla funzionalità Azure AD ibrido join.
Verifica i certificati presenti nella proprietà UserCertificate di un oggetto computer in Active Directory e, per ogni certificato non scaduto presente, convalida se il certificato è stato emesso per la funzionalità di join Azure AD ibrido (ad esempio, il nome del soggetto corrisponde a CN = {ObjectGUID}).
Prima di, Azure AD Connect avrebbe sincronizzato con Azure AD qualsiasi computer che contenesse almeno un certificato valido, ma a partire da Azure AD Connect versione 1,4, il motore di sincronizzazione è in grado di identificare Azure AD ibrido certificati di join e cloudfilter l'oggetto computer dalla sincronizzazione a Azure AD a meno che non esista un certificato Azure AD ibrido join valido.
Azure AD i dispositivi già sincronizzati con Active Directory, ma che non dispongono di un certificato Azure AD ibrido join valido verranno eliminati (CloudFiltered = TRUE) dal motore di sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi
- [Cronologia delle versioni di Azure AD Connect](reference-connect-version-history.md)
