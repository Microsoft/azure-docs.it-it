---
title: Configurare l'aggiunta ad Azure Active Directory ibrido per i domini federati | Microsoft Docs
description: Informazioni su come configurare l'aggiunta ad Azure Active Directory ibrido per i domini federati.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221b7bdbb8ab5d0121e9c8032be8f18d8ae60d1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578057"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Esercitazione: configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini federati

Come gli utenti dell'organizzazione, anche i dispositivi rappresentano identità importanti da proteggere. È possibile usare l'identità di un dispositivo per proteggere le risorse in qualsiasi momento e da qualunque posizione. È possibile raggiungere questo obiettivo trasferendo e gestendo le identità dei dispositivi in Azure Active Directory (Azure AD) con uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti grazie all'accesso Single Sign-On (SSO) a tutte le risorse locali e cloud. Contemporaneamente, è possibile proteggere l'accesso alle risorse locali e cloud con l'[accesso condizionale](../conditional-access/howto-conditional-access-policy-compliant-device.md).

Un ambiente federato deve includere un provider di identità che supporta i requisiti riportati di seguito. Se l'ambiente federato usa Active Directory Federation Services (AD FS), i requisiti seguenti sono già supportati.

- **Attestazione WIAORMULTIAUTHN:** questa attestazione è necessaria per eseguire l'aggiunta ad Azure AD ibrido per dispositivi Windows di livello inferiore.
- **Protocollo WS-Trust:** questo protocollo è necessario per l'autenticazione con Azure AD degli attuali dispositivi Windows aggiunti ad Azure AD ibrido.
  Quando si usa AD FS, è necessario abilitare gli endpoint WS-Trust seguenti: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Sia **adfs/services/trust/2005/windowstransport** che **adfs/services/trust/13/windowstransport** devono essere abilitati solo come endpoint per Intranet e NON devono essere esposti come endpoint per Extranet tramite Proxy applicazione Web. Per altre informazioni su come disabilitare gli endpoint Windows WS-Trust, vedere [Disabilitare gli endpoint Windows WS-Trust sul proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). È possibile verificare gli endpoint abilitati nella console di gestione di AD FS, in **Servizio** > **Endpoint**.

Questa esercitazione illustra come configurare l'aggiunta ad Azure AD ibrido per i dispositivi di computer aggiunti a un dominio di Active Directory in un ambiente federato con AD FS.

Si apprenderà come:

> [!div class="checklist"]
> * Configurare l'aggiunta ad Azure AD ibrido
> * Abilitare dispositivi Windows di livello inferiore
> * Verificare la registrazione
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che l'utente abbia familiarità con gli articoli seguenti:

- [Informazioni sulle identità dei dispositivi](overview.md)
- [Pianificare l'implementazione dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-plan.md)
- [Come eseguire la convalida controllata dell'aggiunta ad Azure AD ibrido](hybrid-azuread-join-control.md)

Per configurare lo scenario in questa esercitazione, sono necessari gli elementi seguenti:

- Windows Server 2012 R2 con AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versione 1.1.819.0 o successiva

A partire dalla versione 1.1.819.0, Azure AD Connect include una procedura guidata per configurare l'aggiunta ad Azure AD ibrido. Tale procedura semplifica notevolmente il processo di configurazione. La procedura guidata correlata:

- Configura i punti di connessione del servizio per la registrazione dei dispositivi
- esegue il backup del trust della relying party esistente di Azure AD;
- aggiorna le regole attestazioni nel trust di Azure AD.

I passaggi di configurazione descritti in questo articolo sono basati sull'uso della procedura guidata di Azure AD Connect. Se è installata una versione precedente di Azure AD Connect, è necessario eseguire l'aggiornamento alla versione 1.1.819 o successiva per usare la procedura guidata. Se non è possibile installare l'ultima versione di Azure AD Connect, vedere [Configurare manualmente l'aggiunta ad Azure AD ibrido](hybrid-azuread-join-manual.md).

Per l'aggiunta ad Azure AD ibrido i dispositivi devono avere accesso alle risorse Microsoft seguenti dalla rete dell'organizzazione:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Servizio Token di sicurezza (STS) dell'organizzazione (per domini federati)
- `https://autologon.microsoftazuread-sso.com` (se si usa o si prevede di usare Seamless SSO)

> [!WARNING]
> Se l'organizzazione usa server proxy che intercettano il traffico SSL per scenari come la prevenzione della perdita dei dati o restrizioni del tenant di Azure AD, assicurarsi che il traffico verso https://device.login.microsoftonline.com sia escluso dall'interruzione e dall'ispezione di TLS. La mancata esclusione di https://device.login.microsoftonline.com può determinare interferenze con l'autenticazione dei certificati client, causando problemi con la registrazione dei dispositivi e l'accesso condizionale basato su dispositivo.

A partire da Windows 10 1803, se l'aggiunta istantanea ad Azure AD ibrido per un ambiente federato con AD FS non riesce, è possibile usare Azure AD Connect per sincronizzare l'oggetto computer in Azure AD che verrà successivamente usato per completare la registrazione del dispositivo per l'aggiunta ad Azure AD ibrido. Verificare che Azure AD Connect abbia sincronizzato gli oggetti computer dei dispositivi che devono essere aggiunti ad Azure AD ibrido. Se gli oggetti computer appartengono a unità organizzative specifiche, è necessario configurare anche le unità organizzative per la sincronizzazione in Azure AD Connect. Per altre informazioni su come sincronizzare oggetti computer con Azure AD Connect, vedere [Configurare il filtro con Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Per ottenere un join di sincronizzazione della registrazione del dispositivo, come parte della configurazione di registrazione del dispositivo, non escludere gli attributi di dispositivo predefiniti dalla configurazione di sincronizzazione Azure AD Connect. Per altre informazioni sugli attributi di dispositivo predefiniti sincronizzati con AAD, vedere [attributi sincronizzati da Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Se l'organizzazione deve accedere a Internet tramite un proxy in uscita, è consigliabile [implementare WPAD (Web Proxy Auto-Discovery)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) per consentire ai computer Windows 10 di eseguire la registrazione di dispositivi con Azure AD. In caso di problemi nella configurazione e nella gestione di WPAD, vedere [Risolvere i problemi di rilevamento automatico](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se non si non usa WPAD e si vuole configurare le impostazioni proxy nel computer in uso, è possibile farlo a partire da Windows 10 1709. Per altre informazioni, vedere [Configurare le impostazioni WinHTTP tramite un oggetto Criteri di gruppo (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Se si configurano le impostazioni proxy nel computer usando le impostazioni WinHTTP, i computer che non possono connettersi al proxy configurato non riusciranno a connettersi a Internet.

Se l'organizzazione richiede l'accesso a Internet attraverso un proxy in uscita autenticato, è necessario assicurarsi che i computer Windows 10 possano eseguire l'autenticazione nel proxy in uscita. Poiché i computer Windows 10 eseguono la registrazione dei dispositivi usando il contesto del computer, è necessario configurare l'autenticazione proxy in uscita usando il contesto del computer. Per i requisiti di configurazione, contattare il provider del proxy in uscita.

Per verificare se il dispositivo può accedere alle risorse Microsoft sopra riportate con l'account di sistema, è possibile usare lo script disponibile per [testare la connettività durante la registrazione dei dispositivi](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/).

## <a name="configure-hybrid-azure-ad-join"></a>Configurare l'aggiunta ad Azure AD ibrido

Per configurare un'aggiunta ad Azure AD ibrido con Azure AD Connect, è necessario disporre di quanto segue:

- Credenziali di un amministratore globale per il tenant di Azure AD  
- Credenziali di amministratore dell'organizzazione per ognuna delle foreste
- Credenziali dell'amministratore AD FS

**Per configurare l'aggiunta ad Azure AD ibrido con Azure AD Connect**:

1. Avviare Azure AD Connect e quindi selezionare **Configura**.

   ![Schermata iniziale](./media/hybrid-azuread-join-federated-domains/11.png)

1. Nella pagina **Attività aggiuntive** selezionare **Configura le opzioni del dispositivo** e quindi **Avanti**.

   ![Attività aggiuntive](./media/hybrid-azuread-join-federated-domains/12.png)

1. Nella pagina **Panoramica** selezionare **Avanti**.

   ![Panoramica](./media/hybrid-azuread-join-federated-domains/13.png)

1. Nella pagina **Connessione ad Azure AD** immettere le credenziali di un amministratore globale per il tenant di Azure AD e quindi selezionare **Avanti**.

   ![Connessione ad Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Nella pagina **Opzioni dispositivo** selezionare **Configura l'aggiunta ad Azure AD ibrido** e quindi **Avanti**.

   ![Opzioni del dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

1. Nella pagina **Punto di connessione del servizio** completare la procedura seguente e quindi selezionare **Avanti**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selezionare la foresta.
   1. Selezionare il servizio di autenticazione. È necessario selezionare **Server AD FS**, a meno che all'interno dell'organizzazione non siano presenti esclusivamente client Windows 10 e non sia stata configurata la sincronizzazione computer/dispositivo o non si usi Seamless SSO.
   1. Selezionare **Aggiungi** per immettere le credenziali di amministratore aziendale.

1. Nella pagina **Sistemi operativi del dispositivo** selezionare i sistemi operativi usati dai dispositivi nell'ambiente Active Directory e quindi selezionare **Avanti**.

   ![Sistema operativo del dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

1. Nella pagina **Configurazione della federazione** immettere le credenziali di amministratore AD FS e quindi selezionare **Avanti**.

   ![Configurazione della federazione](./media/hybrid-azuread-join-federated-domains/18.png)

1. Nella pagina **Pronto per la configurazione** selezionare **Configura**.

   ![Pronto per la configurazione](./media/hybrid-azuread-join-federated-domains/19.png)

1. Nella pagina **La configurazione è stata completata** selezionare **Esci**.

   ![Configurazione completata](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Abilitare dispositivi Windows di livello inferiore

Se alcuni dei dispositivi aggiunti a un dominio sono dispositivi Windows di livello inferiore, è necessario:

- Configurare le impostazioni intranet locali per la registrazione dei dispositivi
- Installare Microsoft Workplace Join for Windows per i computer di livello inferiore

> [!NOTE]
> Il supporto per Windows 7 è terminato il 14 gennaio 2020. Per altre informazioni, vedere [Il supporto per Windows 7 è terminato](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurare le impostazioni intranet locali per la registrazione dei dispositivi

Per completare l'aggiunta ad Azure AD ibrido dei dispositivi Windows di livello inferiore e per evitare le richieste di certificati quando i dispositivi vengono autenticati in Azure AD, è possibile eseguire il push di criteri nei dispositivi aggiunti al dominio per aggiungere gli URL seguenti all'area Intranet locale in Internet Explorer:

- `https://device.login.microsoftonline.com`
- Servizio token di sicurezza dell'organizzazione (per domini federati)
- `https://autologon.microsoftazuread-sso.com` (per Seamless SSO)

È anche necessario abilitare **Consenti aggiornamenti barra di stato tramite script** nell'area Intranet locale dell'utente.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installare Microsoft Workplace Join for Windows per i computer di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join for non-Windows 10 computers è disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [Microsoft Endpoint Configuration Manager](/configmgr/). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro `quiet`. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo ad Azure AD usando le credenziali utente dopo l'autenticazione con Azure AD.

## <a name="verify-the-registration"></a>Verificare la registrazione

Ecco tre modi per individuare e verificare lo stato del dispositivo:

### <a name="locally-on-the-device"></a>In locale nel dispositivo

1. Aprire Windows PowerShell.
2. Immettere `dsregcmd /status`.
3. Verificare che **AzureAdJoined** e **DomainJoined** siano impostati su **SÌ**.
4. È possibile usare **DeviceId** e confrontare lo stato nel servizio usando il portale di Azure o PowerShell.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

1. Passare alla pagina di dispositivi usando un [collegamento diretto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Per informazioni su come individuare un dispositivo, vedere [Come gestire le identità dei dispositivi con il portale di Microsoft Azure](./device-management-azure-portal.md).
3. Se la colonna **Registrazione completata** indica **In sospeso**, l'operazione di Aggiunta ad Azure AD ibrido non è stata completata. Negli ambienti federati questo problema può verificarsi solo se non è stato possibile eseguire la registrazione e AAD Connect è configurato per sincronizzare i dispositivi.
4. Se la colonna **Registrazione completata** contiene una **data/ora**, l'operazione di Aggiunta ad Azure AD ibrido è stata completata.

### <a name="using-powershell"></a>Utilizzo di PowerShell

Verificare lo stato di registrazione del dispositivo nel tenant di Azure con **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** . Questo cmdlet si trova nel [modulo Azure Active Directory PowerShell](/powershell/azure/active-directory/install-msonlinev1).

Quando si usa il cmdlet **Get-MSolDevice** per controllare i dettagli del servizio:

- Deve essere presente un oggetto con **ID dispositivo** corrispondente all'ID nel client Windows.
- Il valore di **DeviceTrustType** è **Domain Joined**. Questa impostazione equivale allo stato **Aggiunto ad Azure AD ibrido** nella pagina **Dispositivi** nel portale di Azure AD.
- Per i dispositivi usati nell'accesso condizionale, il valore di **Enabled** è **True** e quello di **DeviceTrustLevel** è **Managed**.

1. Aprire Windows PowerShell come amministratore.
2. Immettere `Connect-MsolService` per stabilire la connessione al tenant di Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Contare tutti i dispositivi aggiunti ad Azure AD ibrido (tranne quelli con lo stato **In sospeso**)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Contare tutti i dispositivi aggiunti ad Azure AD ibrido con lo stato **In sospeso**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Elencare tutti i dispositivi aggiunti ad Azure AD ibrido

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Elencare tutti i dispositivi aggiunti ad Azure AD ibrido con lo stato **In sospeso**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Elencare i dettagli di un singolo dispositivo:

1. Immettere `get-msoldevice -deviceId <deviceId>` (il valore di **DeviceId** ottenuta in locale nel dispositivo).
2. Verificare che **Abilitato** sia impostato su **True**.

## <a name="troubleshoot-your-implementation"></a>Risolvere i problemi di implementazione

Se si verificano problemi durante il completamento dell'aggiunta ad Azure AD ibrido per dispositivi Windows aggiunti al dominio, vedere:

- [Risoluzione dei problemi dei dispositivi con il comando dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Risolvere i problemi dei dispositivi Windows correnti aggiunti ad Azure AD ibrido](troubleshoot-hybrid-join-windows-current.md)
- [Risolvere i problemi dei dispositivi Windows di livello inferiore aggiunti ad Azure AD ibrido](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come [gestire le identità dei dispositivi usando il portale di Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
