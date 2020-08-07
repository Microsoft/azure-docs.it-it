---
title: Pianificare un join Azure Active Directory ibrido-Azure Active Directory
description: Informazioni su come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8367ec2ece59ca8794bc1eeb2027eb6c14db12a0
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925346"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procedura: Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido

Analogamente agli utenti, i dispositivi sono un'altra identità di base da proteggere e da usare per proteggere le risorse ovunque e in qualsiasi momento. È possibile raggiungere questo obiettivo trasferendo e gestendo le identità dei dispositivi in Azure AD con uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti tramite il Single Sign-On (SSO) in tutte le risorse locali e nel cloud. Allo stesso tempo, è possibile proteggere l'accesso alle risorse cloud e locali con [l'accesso condizionale](../active-directory-conditional-access-azure-portal.md).

Se si dispone di un ambiente di Active Directory (AD) locale e si desidera aggiungere i computer aggiunti a un dominio ad Azure AD, è possibile eseguire questa operazione eseguendo il join di Azure AD ibrido. Questo articolo fornisce i passaggi correlati per implementare un'aggiunta ad Azure AD ibrido nell'ambiente. 

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con l' [Introduzione alla gestione delle identità dei dispositivi in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> La versione minima richiesta del controller di dominio per Windows 10 Hybrid Azure AD join è Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

Per pianificare l'implementazione di Azure AD ibrido, è necessario acquisire familiarità con:

> [!div class="checklist"]
> - Esaminare i dispositivi supportati
> - Esaminare le informazioni utili
> - Verifica la convalida controllata del join Azure AD ibrido
> - Selezionare lo scenario in base all'infrastruttura di identità
> - Verificare il supporto di AD UPN locale per ibrido Azure AD join

## <a name="review-supported-devices"></a>Esaminare i dispositivi supportati

L'aggiunta ad Azure AD ibrido supporta un'ampia gamma di dispositivi Windows. Poiché la configurazione per i dispositivi che eseguono versioni precedenti di Windows richiede passaggi aggiuntivi o diversi, i dispositivi supportati sono raggruppati in due categorie:

### <a name="windows-current-devices"></a>Dispositivi Windows correnti

- Windows 10
- Windows Server 2016
  - **Nota**: i clienti del cloud nazionale di Azure richiedono la versione 1809
- Windows Server 2019

Per i dispositivi che eseguono il sistema operativo desktop Windows, la versione supportata è elencata in questo articolo [informazioni sulla versione di Windows 10](/windows/release-information/). Come procedura consigliata, Microsoft consiglia di eseguire l'aggiornamento alla versione più recente di Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivi Windows di livello inferiore

- Windows 8.1
- Il supporto per Windows 7 è terminato il 14 gennaio 2020. Per ulteriori informazioni, vedere la pagina relativa al completamento [del supporto per Windows 7](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Per informazioni di supporto su Windows Server 2008 e 2008 R2, vedere la pagina relativa [alla fine del supporto tecnico per Windows server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

Come primo passaggio della pianificazione, è consigliabile esaminare l'ambiente e determinare se è necessario supportare dispositivi Windows di livello inferiore.

## <a name="review-things-you-should-know"></a>Esaminare le informazioni utili

### <a name="unsupported-scenarios"></a>Scenari non supportati
- Azure AD ibrido join non è attualmente supportato se l'ambiente è costituito da una singola foresta di Active Directory che sincronizza i dati di identità a più di un tenant di Azure AD.

- Azure AD ibrido join non è supportato per Windows Server che esegue il ruolo controller di dominio (DC).

- Azure AD ibrido join non è supportato nei dispositivi Windows di livello inferiore quando si usa il roaming delle credenziali o il roaming del profilo utente o profilo obbligatorio.

- Il sistema operativo Server Core non supporta alcun tipo di registrazione del dispositivo.

### <a name="os-imaging-considerations"></a>Considerazioni sull'imaging del sistema operativo
- Se si utilizza l'utilità preparazione sistema (Sysprep) e si utilizza un'immagine **precedente a Windows 10 1809** per l'installazione, assicurarsi che l'immagine non venga da un dispositivo già registrato con Azure AD come Azure ad ibrido join.

- Se si utilizza uno snapshot di macchina virtuale (VM) per creare altre macchine virtuali, assicurarsi che lo snapshot non venga da una macchina virtuale già registrata con Azure AD come Azure AD ibrido join.

- Se si usa un [filtro di scrittura unificato](/windows-hardware/customize/enterprise/unified-write-filter) e tecnologie simili che cancellano le modifiche apportate al disco al riavvio, è necessario applicarle dopo che il dispositivo è stato Azure ad ibrido aggiunto. L'abilitazione di tali tecnologie prima del completamento del Azure AD ibrido join comporterà la disgiunzione del dispositivo a ogni riavvio

### <a name="handling-devices-with-azure-ad-registered-state"></a>Gestione dei dispositivi con Azure AD stato registrato
Se i dispositivi Windows 10 aggiunti a un dominio sono [Azure ad registrati](overview.md#getting-devices-in-azure-ad) nel tenant, è possibile che si verifichi il doppio stato del dispositivo Azure ad ibrido aggiunto e Azure ad registrato. È consigliabile eseguire l'aggiornamento a Windows 10 1803 (con KB4489894 applicato) o versione successiva per risolvere automaticamente questo scenario. Nelle versioni precedenti alla 1803 sarà necessario rimuovere manualmente lo stato Azure AD registrato prima di abilitare Azure AD ibrido join. Nelle versioni 1803 e successive sono state apportate le modifiche seguenti per evitare questo doppio stato:

- Gli eventuali stati Azure AD registrati per un utente vengono rimossi automaticamente <i>dopo che il dispositivo è stato Azure ad ibrido aggiunto e lo stesso utente ha eseguito l'accesso</i>. Se, ad esempio, l'utente A dispone di un Azure AD stato registrato sul dispositivo, lo stato doppio per l'utente A viene pulito solo quando l'utente A accede al dispositivo. Se sono presenti più utenti nello stesso dispositivo, lo stato doppio viene pulito singolarmente quando tali utenti eseguono l'accesso. Oltre a rimuovere lo stato Azure AD registrato, Windows 10 Annulla la registrazione del dispositivo da Intune o da un'altra soluzione MDM, se la registrazione è avvenuta durante la registrazione del Azure AD tramite la registrazione automatica.
- È possibile impedire che il dispositivo aggiunto al dominio venga registrato Azure AD aggiungendo il seguente valore del registro di sistema a HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin" = DWORD: 00000001.
- In Windows 10 1803, se è configurato Windows Hello for business, l'utente deve reinstallare Windows Hello for business dopo la pulizia dello stato doppio. Questo problema è stato risolto con KB4512509

> [!NOTE]
> Anche se Windows 10 rimuove automaticamente lo stato Azure AD registrato localmente, l'oggetto dispositivo in Azure AD non viene eliminato immediatamente se è gestito da Intune. È possibile convalidare la rimozione dello stato Azure AD registrato eseguendo dsregcmd/status e prendere in considerazione la mancata Azure AD registrazione del dispositivo in base a tale stato.

### <a name="additional-considerations"></a>Altre considerazioni
- Se l'ambiente USA Virtual Desktop Infrastructure (VDI), vedere la pagina relativa [a identità del dispositivo e virtualizzazione desktop](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

- Azure AD ibrido join è supportato per il TPM 2,0 conforme a FIPS e non è supportato per TPM 1,2. Se i dispositivi hanno un TPM compatibile con FIPS 1,2, è necessario disabilitarli prima di procedere con Azure AD ibrido join. Microsoft non fornisce strumenti per disabilitare la modalità FIPS per TPMs poiché dipende dal produttore del TPM. Contattare l'OEM hardware per assistenza. 

- A partire dalla versione di Windows 10 1903, TPMs 1,2 non vengono usati con il join ibrido Azure AD e i dispositivi con tali TPMs verranno considerati come se non avessero un TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Verifica la convalida controllata del join Azure AD ibrido

Quando tutti i prerequisiti sono soddisfatti, i dispositivi Windows verranno registrati automaticamente come dispositivi nel tenant del Azure AD. Lo stato di queste identità del dispositivo in Azure AD viene definito ibrido Azure AD join. Altre informazioni sui concetti trattati in questo articolo sono disponibili nell'articolo [Introduzione alla gestione delle identità dei dispositivi in Azure Active Directory](overview.md).

È possibile che le organizzazioni desiderino eseguire una convalida controllata del join ibrido di Azure AD prima di abilitarlo nell'intera organizzazione. Per informazioni su come eseguire questa operazione, vedere l'articolo [convalida controllata del join ibrido Azure ad](hybrid-azuread-join-control.md) .

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selezionare lo scenario in base all'infrastruttura di identità

Azure AD ibrido join funziona sia con gli ambienti gestiti che federati, a seconda che l'UPN sia instradabile o non instradabile. Vedere la parte inferiore della pagina per la tabella negli scenari supportati.  

### <a name="managed-environment"></a>Ambiente gestito

Un ambiente gestito può essere distribuito tramite la [sincronizzazione dell'hash delle password](/azure/active-directory/hybrid/whatis-phs) o l'[autenticazione pass-through](/azure/active-directory/hybrid/how-to-connect-pta) con [Single Sign-On facile](/azure/active-directory/hybrid/how-to-connect-sso).

Questi scenari non richiedono la configurazione di un server federativo per l'autenticazione.

> [!NOTE]
> [L'autenticazione cloud con implementazione temporanea](/azure/active-directory/hybrid/how-to-connect-staged-rollout) è supportata solo l'avvio dell'aggiornamento di Windows 10 1903

### <a name="federated-environment"></a>Ambiente federato

Un ambiente federato deve includere un provider di identità che supporta i requisiti riportati di seguito. Se l'ambiente federato usa Active Directory Federation Services (AD FS), i requisiti seguenti sono già supportati.

- **Attestazione WIAORMULTIAUTHN:** questa attestazione è necessaria per eseguire l'aggiunta ad Azure AD ibrido per dispositivi Windows di livello inferiore.
- **Protocollo WS-Trust:** questo protocollo è necessario per l'autenticazione con Azure AD degli attuali dispositivi Windows aggiunti ad Azure AD ibrido. Quando si usa AD FS, è necessario abilitare gli endpoint WS-Trust seguenti: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Sia **adfs/services/trust/2005/windowstransport** che **adfs/services/trust/13/windowstransport** devono essere abilitati solo come endpoint per Intranet e NON devono essere esposti come endpoint per Extranet tramite Proxy applicazione Web. Per altre informazioni su come disabilitare gli endpoint Windows WS-Trust, vedere [Disabilitare gli endpoint Windows WS-Trust sul proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). È possibile verificare gli endpoint abilitati nella console di gestione di AD FS, in **Servizio** > **Endpoint**.

> [!NOTE]
> Azure AD non supporta certificati o smart card nei domini gestiti.

A partire dalla versione 1.1.819.0, Azure AD Connect offre una procedura guidata per configurare l'aggiunta ad Azure AD ibrido. che semplifica in modo significativo il processo di configurazione. Se non si può prendere in considerazione l'installazione della versione richiesta di Azure AD Connect, vedere [come configurare manualmente la registrazione dei dispositivi](hybrid-azuread-join-manual.md). 

In base allo scenario che corrisponde all'infrastruttura di identità, vedere:

- [Configurare il join di Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
- [Configurare il join di Azure Active Directory ibrido per l'ambiente gestito](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Verifica del supporto UPN per gli utenti AD locali per Azure AD ibrido join

In alcuni casi, gli utenti AD locali UPN potrebbero essere diversi dalla Azure AD UPN. In questi casi l'aggiunta ad Azure AD ibrido di Windows 10 offre supporto limitato per i nomi dell'entità utente di AD locale in base al [metodo di autenticazione](/azure/security/fundamentals/choose-ad-authn), al tipo di dominio e alla versione di Windows 10. Nell'ambiente possono essere presenti due tipi di nomi dell'entità utente di AD locale:

- UPN per utenti instradabili: un UPN instradabile dispone di un dominio verificato valido, registrato con un registrar. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.org è il dominio primario in AD locale di proprietà di Contoso e [verificato in Azure AD](/azure/active-directory/fundamentals/add-custom-domain).
- UPN per utenti non instradabili: un UPN non instradabile non dispone di un dominio verificato. È applicabile solo all'interno della rete privata dell'organizzazione. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.local è il dominio primario in AD locale, ma non è un dominio verificabile in Internet e viene usato solo nella rete di Contoso.

> [!NOTE]
> Le informazioni contenute in questa sezione sono valide solo per gli utenti locali UPN. Non è applicabile a un suffisso di dominio del computer locale (ad esempio: Computer1. contoso. local).

La tabella seguente contiene informazioni sul supporto per questi nomi dell'entità utente di AD locale nell'aggiunta ad Azure AD ibrido di Windows 10

| Tipo di nome dell'entità utente di AD locale | Tipo di dominio | Versione di Windows 10 | Descrizione |
| ----- | ----- | ----- | ----- |
| Instradabile | Federato | Dalla versione 1703 | Disponibile a livello generale |
| Non instradabile | Federato | Dalla versione 1803 | Disponibile a livello generale |
| Instradabile | Gestiti | Dalla versione 1803 | Disponibile a livello generale, Azure AD SSPR su Windows lockscreen non è supportato |
| Non instradabile | Gestiti | Non supportate | |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare il join di Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md) 
>  [Configurare il join di Azure Active Directory ibrido per l'ambiente gestito](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
