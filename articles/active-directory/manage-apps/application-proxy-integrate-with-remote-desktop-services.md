---
title: Pubblicare Desktop remoto con il proxy applicazione di Azure AD | Microsoft Docs
description: Tratta i fondamenti dei connettori del proxy applicazione di Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108471"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Pubblicare Desktop remoto con il proxy applicazione di Azure AD

Servizi Desktop remoto e il proxy di applicazione di Azure AD si integrano per migliorare la produttività dei dipendenti che si trovano all'esterno della rete aziendale. 

I destinatari di questo articolo sono:
- Attuali clienti del proxy di applicazione di Azure AD che vogliono offrire un maggior numero di applicazioni ai propri utenti finali tramite la pubblicazione di applicazioni locali attraverso Servizi Desktop remoto.
- I clienti attuali di Servizi Desktop remoto che vogliono ridurre la superficie di attacco della propria distribuzione usando il proxy applicazione di AD Azure. Questo scenario fornisce un set limitato di verifica in due passaggi e controlli di accesso condizionale per RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Ruolo del proxy applicazione nella distribuzione standard di Servizi Desktop remoto

Una distribuzione standard di Servizi Desktop remoto include vari servizi ruolo Desktop remoto in esecuzione su Windows Server. L'[architettura di Servizi Desktop remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) offre più opzioni di distribuzione. Diversamente da altre opzioni di distribuzione di Servizi Desktop remoto, la [distribuzione di Servizi Desktop remoto con il proxy di applicazione di AD Azure](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (mostrata nel diagramma seguente) ha una connessione in uscita permanente dal server che esegue il servizio connettore. Altre distribuzioni lasciano le connessioni in ingresso aperte tramite un servizio di bilanciamento del carico.

![Il proxy applicazione si trova tra la VM di Servizi Desktop remoto e la rete Internet pubblica](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

In una distribuzione di Servizi Desktop remoto, il ruolo di Web Desktop remoto e il ruolo di Gateway Desktop remoto vengono eseguiti su computer con connessione Internet. Questi endpoint vengono esposti per i motivi seguenti:
- Web Desktop remoto fornisce all'utente un endpoint pubblico per accedere e visualizzare le applicazioni e i desktop locali vari a cui può accedere. Dopo che è stata selezionata una risorsa, viene creata una connessione RDP tramite l'app nativa nel sistema operativo.
- Gateway Desktop remoto entra in gioco una volta che l'utente avvia la connessione RDP. Gateway Desktop remoto gestisce il traffico RDP crittografato su Internet e lo trasferisce nel server locale a cui si connette l'utente. In questo scenario il traffico che Gateway Desktop remoto riceve proviene dal proxy applicazione di AD Azure.

>[!TIP]
>Se Servizi Desktop remoto non è stato distribuito prima e se si desiderano altre informazioni prima di iniziare, vedere come [distribuire facilmente Servizi Desktop remoto con Azure Resource Manager e Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requisiti

- Usare un client diverso da client web Desktop remoto, poiché il client del web non supporta il Proxy di applicazione.

- Entrambi gli endpoint Web Desktop remoto e Gateway Desktop remoto devono trovarsi nello stesso computer e avere una radice comune. Web Desktop remoto e Gateway Desktop remoto vengono pubblicati come un'unica applicazione con il proxy di applicazione in modo da offrire un'esperienza di accesso Single Sign-On tra le due applicazioni.

- Si dovrebbe avere già [distribuito Servizi Desktop remoto](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure) e avere già [abilitato il proxy applicazione](application-proxy-add-on-premises-application.md).

- Questo scenario presuppone che gli utenti finali usino Internet Explorer su PC desktop Windows 7 o Windows 10 che si connettono tramite la pagina di Web Desktop remoto. Se è necessario supportare altri sistemi operativi, vedere [Supportare altre configurazione client](#support-for-other-client-configurations).

- Quando si pubblica Web Desktop remoto, è consigliabile usare lo stesso FQDN interno ed esterno. Se gli FQDN interni ed esterni sono diversi è consigliabile disabilitare la richiesta di traduzione dell'intestazione per evitare che il client riceva collegamenti non validi. 

- In Internet Explorer abilitare il componente aggiuntivo ActiveX di Servizi Desktop remoto.

- Per il flusso di preautenticazione di Azure AD, gli utenti possono connettersi solo alle risorse pubblicate nel riquadro RemoteApp e Desktop.For the Azure AD pre-authentication flow, users can only connect to resources published to them in the **RemoteApp and Desktops** pane. Gli utenti non possono connettersi a un desktop utilizzando il riquadro **Connetti a un PC remoto.**

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Distribuire lo scenario di Servizi desktop remoto e proxy applicazione congiunti

Dopo avere configurato Servizi Desktop remoto e il proxy applicazione di Azure AD per l'ambiente in uso, seguire questa procedura per combinare le due soluzioni. Questa procedura descrive come pubblicare i due endpoint di Servizi Desktop remoto per il Web (Web Desktop remoto e Gateway Desktop remoto) come applicazioni e quindi come indirizzare il traffico su Servizi Desktop remoto attraverso il proxy applicazione.

### <a name="publish-the-rd-host-endpoint"></a>Pubblicare l'endpoint host di Desktop remoto

1. [Pubblicare un nuovo proxy applicazione](application-proxy-add-on-premises-application.md) con i valori seguenti:
   - URL interno: `https://\<rdhost\>.com/`, dove `\<rdhost\>` è la radice comune condivisa da Web Desktop Remoto e Gateway Desktop remoto.
   - URL esterno: questo campo viene popolato automaticamente in base al nome dell'applicazione, ma è possibile modificarlo. Gli utenti passeranno a questo URL quando accedono a Servizi Desktop remoto.
   - Metodo di autenticazione preliminare: Azure Active Directory
   - Tradurre le intestazioni degli URL: No
2. Assegnare utenti all'applicazione di Desktop remoto pubblicata. Assicurarsi che tutti gli utenti abbiano accesso anche a Servizi Desktop remoto.
3. Lasciare il metodo Single Sign-On per l'applicazione come **Single Sign-On di Azure AD disabilitato**. Agli utenti viene richiesto di eseguire l'autenticazione una volta in Azure AD e una volta in Web Desktop remoto, ma dispongono dell'accesso Single Sign-On a Gateway Desktop remoto.
4. Selezionare **Azure Active Directory**e quindi **Registrazioni app**. Scegli la tua app dall'elenco.
5. In **Gestisci**selezionare **Personalizzazione**.
6. Aggiornare il campo **URL della home page** `https://\<rdhost\>.com/RDWeb`in modo che punti all'endpoint Web Desktop remoto (ad esempio ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Dirigere il traffico di Servizi Desktop remoto verso il proxy applicazione

Connettersi alla distribuzione di Servizi Desktop remoto come amministratore e modificare il nome del server Gateway Desktop remoto per la distribuzione. Questa configurazione garantisce che le connessioni vengano indirizzate tramite il servizio proxy di applicazione di Azure AD.

1. Connettersi al server di Servizi Desktop remoto che esegue il ruolo Gestore connessione Desktop remoto.
2. Avviare **Server Manager**.
3. Selezionare **Servizi Desktop remoto** dal riquadro a sinistra.
4. Selezionare **Panoramica**.
5. Nella sezione Panoramica della distribuzione selezionare il menu a discesa e scegliere **Modificare proprietà di distribuzione**.
6. Nella scheda Gateway Desktop remoto modificare il campo **Nome server** con l'URL esterno che è stato impostato per l'endpoint host di Desktop remoto nel proxy applicazione.
7. Impostare il campo **Metodo di accesso** su **Autenticazione della password**.

   ![Schermata Proprietà di distribuzione in Servizi Desktop remoto](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Per ogni raccolta, eseguire questo comando. Sostituire * \<yourcollectionname\> * e * \<proxyfrontendurl\> * con le proprie informazioni. Questo comando abilita l'accesso Single Sign-On tra Web Desktop remoto e Gateway Desktop remoto e ottimizza le prestazioni:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Per esempio:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Il comando precedente usa un apice inverso in "`nrequire".

9. Per verificare la modifica delle proprietà RDP personalizzate, nonché visualizzare il contenuto del file RDP che sarà scaricato da Web Desktop remoto per questa raccolta, eseguire il comando seguente:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Dopo avere configurato Desktop remoto, il proxy applicazione di Azure AD diventa il componente con connessione Internet di Servizi Desktop remoto. È possibile rimuovere gli altri endpoint con connessione Internet pubblici nei computer Web Desktop remoto e Gateway Desktop remoto.

## <a name="test-the-scenario"></a>Testare lo scenario

Testare lo scenario con Internet Explorer su un computer Windows 7 o 10.

1. Passare all'URL esterno impostato o individuare l'applicazione nel [pannello MyApps](https://myapps.microsoft.com).
2. Viene chiesto di eseguire l'autenticazione ad Azure Active Directory. Usare un account che è stato precedentemente assegnato all'applicazione.
3. Viene chiesto di eseguire l'autenticazione a Web Desktop remoto.
4. Al termine dell'autenticazione di Servizi Desktop remoto, è possibile selezionare il desktop o l'applicazione che si desidera e iniziare a lavorare.

## <a name="support-for-other-client-configurations"></a>Supportare altre configurazione client

La configurazione descritta in questo articolo è rivolta agli utenti di Windows 7 o 10 che usano Internet Explorer e il componente aggiuntivo ActiveX di Servizi Desktop remoto. Se necessario è possibile comunque supportare altri sistemi operativi o browser. La differenza sta nell'uso del metodo di autenticazione.

| Metodo di autenticazione | Configurazione client supportata |
| --------------------- | ------------------------------ |
| Pre-autenticazione    | Windows 7/10 con Internet Explorer + componente aggiuntivo ActiveX di Servizi Desktop remoto |
| Pass-through | Qualsiasi altro sistema operativo che supporta l'applicazione Desktop remoto Microsoft |

Il flusso di pre-autenticazione offre più vantaggi in termini di protezione rispetto al flusso di pass-through. Con la preautenticazione è possibile usare le funzionalità di autenticazione di Azure AD, ad esempio Single Sign-On, Accesso condizionale e verifica in due passaggi per le risorse locali. È anche possibile garantire che solo il traffico autenticato raggiunga la rete.

Per usare l'autenticazione pass-through, sono necessarie solo due modifiche ai passaggi descritti in questo articolo:
1. Nel passaggio 1 [Pubblicare l'endpoint host di Desktop remoto](#publish-the-rd-host-endpoint) impostare il metodo di preautenticazione su **Pass-through**.
2. In [Dirigere il traffico di Servizi Desktop remoto verso il proxy applicazione](#direct-rds-traffic-to-application-proxy), ignorare interamente il passaggio 8.

## <a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso remoto a SharePoint con il proxy di applicazione di Azure AD](application-proxy-integrate-with-sharepoint-server.md)  
[Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD](application-proxy-security.md)
