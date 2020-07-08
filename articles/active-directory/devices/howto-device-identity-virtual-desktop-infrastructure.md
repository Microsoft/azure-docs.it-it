---
title: Identità del dispositivo e virtualizzazione desktop-Azure Active Directory
description: Informazioni sul modo in cui è possibile usare insieme le identità dei dispositivi VDI e Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74900364"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identità del dispositivo e virtualizzazione desktop

Gli amministratori distribuiscono in genere piattaforme VDI (Virtual Desktop Infrastructure) che ospitano sistemi operativi Windows nelle organizzazioni. Gli amministratori distribuiscono VDI a:

- Gestione semplificata.
- Ridurre i costi tramite il consolidamento e la centralizzazione delle risorse.
- Offri la mobilità degli utenti finali e la libertà di accedere ai desktop virtuali in qualsiasi momento, ovunque si trovino, su qualsiasi dispositivo.

Esistono due tipi principali di desktop virtuali:

- Persistente
- Non persistente

Le versioni permanenti usano un'immagine desktop univoca per ogni utente o un pool di utenti. Questi desktop univoci possono essere personalizzati e salvati per un uso futuro. 

Le versioni non permanenti usano una raccolta di desktop a cui gli utenti possono accedere in base alle esigenze. Questi desktop non persistenti vengono ripristinati allo stato originale dopo che l'utente si è disconnesso.

In questo articolo vengono illustrate le linee guida di Microsoft per gli amministratori sul supporto per l'identità del dispositivo e l'infrastruttura VDI. Per altre informazioni sull'identità del dispositivo, vedere l'articolo [che cos'è un'identità del dispositivo](overview.md).

## <a name="supported-scenarios"></a>Scenari supportati

Prima di configurare le identità del dispositivo in Azure AD per l'ambiente VDI, acquisire familiarità con gli scenari supportati. La tabella seguente illustra gli scenari di provisioning supportati. Il provisioning in questo contesto implica che un amministratore può configurare le identità dei dispositivi su larga scala senza richiedere alcuna interazione con l'utente finale.

| Tipo di identità del dispositivo | Infrastruttura di identità | Dispositivi Windows | Versione della piattaforma VDI | Supportato |
| --- | --- | --- | --- | --- |
| Aggiunta a Azure AD ibrido | Federato | Windows Current * * * e Windows di livello inferiore * * * * | Persistente | Sì |
|   |   | Windows corrente | Non persistente | No |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì |
|   | Gestito * * | Windows Current e Windows legacy-Level | Persistente | Sì |
|   |   | Windows corrente | Non persistente | No |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì |
| Aggiunta ad Azure AD | Federato | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |
|   | Gestito | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |
| Registrato con AAD | Federato | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |
|   | Gestito | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |

\*Un ambiente di infrastruttura di identità **federato** rappresenta un ambiente con un provider di identità, ad esempio ad FS o un altro IDP di terze parti.

\*\*Un ambiente di infrastruttura di identità **gestita** rappresenta un ambiente con Azure ad come provider di identità distribuito con la [sincronizzazione dell'hash delle password (pH)](../hybrid/whatis-phs.md) o con [l'autenticazione pass-through (PTA)](../hybrid/how-to-connect-pta.md) con [Single Sign-on senza](../hybrid/how-to-connect-sso.md)problemi.

\*\*\*I dispositivi **Windows correnti** rappresentano Windows 10, windows server 2016 e windows server 2019.

\*\*\*\*I dispositivi **Windows di livello inferiore** rappresentano Windows 7, Windows 8.1, windows Server 2008 R2, windows Server 2012 e windows Server 2012 R2. Per informazioni di supporto su Windows 7, vedere la pagina relativa al [supporto per Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Per informazioni di supporto su Windows Server 2008 R2, vedere [preparare la fine del supporto tecnico di Windows server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Informazioni aggiuntive su Microsoft

Gli amministratori devono fare riferimento agli articoli seguenti, in base alla relativa infrastruttura di identità, per informazioni su come configurare Hybrid Azure AD join.

- [Configurare il join di Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
- [Configurare il join di Azure Active Directory ibrido per l'ambiente gestito](hybrid-azuread-join-managed-domains.md)

Se si utilizza l'utilità preparazione sistema (sysprep.exe) e si utilizza un'immagine precedente a Windows 10 1809 per l'installazione, assicurarsi che l'immagine non venga da un dispositivo già registrato con Azure AD come ibrido Azure AD aggiunto.

Se si utilizza uno snapshot di macchina virtuale (VM) per creare altre macchine virtuali, assicurarsi che lo snapshot non venga da una macchina virtuale già registrata con Azure AD come Azure AD ibrido join.

Quando si distribuisce un'infrastruttura VDI non persistente, gli amministratori IT devono prestare particolare attenzione alla gestione dei dispositivi non aggiornati in Azure AD. Microsoft consiglia agli amministratori IT di implementare le linee guida riportate di seguito. In caso contrario, la directory contiene molti dispositivi Azure AD ibrido aggiunti non aggiornati che sono stati registrati dalla piattaforma VDI non persistente.

- Creare e utilizzare un prefisso per il nome visualizzato del computer che indica il desktop come basato su VDI.
- Implementare il comando seguente come parte dello script di disconnessione. Questo comando attiverà una chiamata al massimo sforzo per Azure AD eliminare il dispositivo.
   - Per dispositivi Windows di livello inferiore-autoworkplace.exe/Leave
- Definire e implementare il processo per la [gestione dei dispositivi non aggiornati](manage-stale-devices.md).
   - Quando si ha una strategia per identificare i dispositivi non persistenti Azure AD ibrido aggiunti, è possibile essere più aggressivi per la pulizia di questi dispositivi per assicurarsi che la directory non venga usata con molti dispositivi non aggiornati.
 
## <a name="next-steps"></a>Passaggi successivi

[Configurazione del join Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
