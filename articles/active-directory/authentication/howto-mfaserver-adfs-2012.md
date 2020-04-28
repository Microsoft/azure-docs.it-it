---
title: Server di autenticazione a più fattori di Azure con AD FS in Windows Server-Azure Active Directory
description: Questo articolo offre una guida introduttiva ad Azure Multi-Factor Authentication e AD FS in Windows Server 2012 R2 e 2016.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2777675d915d99b8c0e3c2a123b24ab60d41f672
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80653361"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Configurare il server Azure Multi-Factor Authentication per l'uso con AD FS in Windows Server

Se l'organizzazione usa Active Directory Federation Services (ADFS), per proteggere le risorse del cloud o locali è possibile configurare il server Azure Multi-Factor Authentication per l'uso con ADFS. Questa configurazione attiva la verifica in due passaggi per gli endpoint di alto valore.

Questo articolo illustra l'uso del server Azure Multi-Factor Authentication con AD FS in Windows Server 2012 R2 o Windows Server 2016. Per altre informazioni, vedere [Proteggere le risorse del cloud e locali mediante il server Azure Multi-Factor Authentication con AD FS 2.0](howto-mfaserver-adfs-2.md).

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non offrirà più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dagli utenti devono usare Azure Multi-Factor Authentication basato sul cloud. I clienti esistenti che hanno attivato il server di autenticazione a più fattori prima del 1 ° luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Proteggere AD FS per Windows Server con il server Azure Multi-Factor Authentication

Quando si installa il server Multi-Factor Authentication sono disponibili le opzioni seguenti:

* Installare il server Azure Multi-Factor Authentication in locale nello stesso server di AD FS
* Installare l'adapter di Azure Multi-Factor Authentication in locale nel server AD FS e quindi installare il server Multi-Factor Authentication in un altro computer

Prima di iniziare, tenere presente le seguenti informazioni:

* Non è necessario installare il server Azure Multi-Factor Authentication nel server AD FS. È tuttavia necessario installare l'adapter Multi-Factor Authentication per AD FS in un computer Windows Server 2012 R2 o Windows Server 2016 che esegue AD FS. È possibile installare il server in un computer diverso se si installa separatamente la scheda AD FS nel server federativo AD FS. Vedere le procedure seguenti per informazioni su come installare l'adapter separatamente.
* Se l'organizzazione usa metodi di autenticazione tramite app per dispositivi mobili o SMS, le stringhe definite in Impostazioni società contengono un segnaposto <$*nome_applicazione*$>. Nel server MFA 7.1 è possibile specificare il nome di un'applicazione per sostituire questo segnaposto. Nella versione 7.0 e in quelle precedenti questo segnaposto non viene sostituito automaticamente quando si usa l'adapter AD FS. In tali versioni è necessario rimuovere il segnaposto dalle stringhe appropriate per la protezione di AD FS.
* L'account usato per accedere deve avere diritti utente per la creazione di gruppi di sicurezza nel servizio Active Directory.
* L'installazione guidata della scheda ADFS di Multi-Factor Authentication crea un gruppo di sicurezza denominato PhoneFactor Admins nell'istanza di Active Directory e aggiunge l'account del servizio ADFS del servizio federativo a questo gruppo. Verificare che il gruppo PhoneFactor Admins sia stato creato nel controller di dominio e che l'account del servizio AD FS sia un membro di questo gruppo. Se necessario, aggiungere l'account del servizio AD FS manualmente al gruppo PhoneFactor Admins nel controller di dominio.
* Per informazioni sull'installazione dell'SDK del servizio Web con il portale utenti, vedere l'articolo relativo alla [distribuzione del portale utenti per il server Azure Multi-Factor Authentication](howto-mfaserver-deploy-userportal.md).

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Installare il server Azure Multi-Factor Authentication in locale nel server AD FS

1. Scaricare e installare il server Azure Multi-Factor Authentication nel server ADFS. Per informazioni sull'installazione, vedere [Introduzione al server Azure Multi-Factor Authentication](howto-mfaserver-deploy.md).
2. Nella console di gestione del server Azure Multi-Factor Authentication fare clic sull'icona **AD FS**. Selezionare le opzioni **Consenti registrazione utente** e **Consenti agli utenti di selezionare il metodo**.
3. Selezionare eventuali opzioni aggiuntive per l'organizzazione.
4. Fare clic su **Installa scheda ADFS**.

   ![Installare la scheda ADFS dalla console del server di autenticazione a più fattori](./media/howto-mfaserver-adfs-2012/server.png)

5. L'eventuale visualizzazione della finestra di Active Directory significa due cose. Il computer è unito a un dominio e la configurazione di Active Directory per proteggere la comunicazione tra la scheda ADFS e il servizio Multi-Factor Authentication è incompleta. Fare clic su **Avanti** per completare automaticamente la configurazione o selezionare la casella di controllo **Ignora la configurazione automatica di Active Directory e configura le impostazioni manualmente**. Fare clic su **Avanti**.
6. L'eventuale visualizzazione delle finestre Gruppo locale significa due cose. Il computer non è unito a un dominio e la configurazione del gruppo locale per proteggere la comunicazione tra la scheda ADFS e il servizio Multi-Factor Authentication è incompleta. Fare clic su **Avanti** per completare automaticamente la configurazione o selezionare la casella di controllo **Ignora la configurazione automatica di Gruppo locale e configura le impostazioni manualmente**. Fare clic su **Avanti**.
7. Nell'installazione guidata fare clic su **Avanti**. Il server Azure Multi-Factor Authentication creerà il gruppo PhoneFactor Admins e aggiungerà l'account del servizio AD FS al gruppo PhoneFactor Admins.
8. Nella pagina **Avvia programma di installazione** fare clic su **Avanti**.
9. Nel programma di installazione dell'adapter AD FS di Multi-Factor Authentication fare clic su **Avanti**.
10. Al termine dell'installazione, fare clic su **Chiudi** .
11. Dopo aver installato l'adapter è necessario registrarlo con AD FS. Aprire Windows PowerShell ed eseguire il comando seguente:

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. Modificare i criteri di autenticazione globali in ADFS per usare la scheda appena registrata. Nella console di gestione di AD FS, passare al nodo **Criteri di autenticazione** . Nella sezione **Multi-factor Authentication** fare clic sul collegamento **Modifica** accanto alla sezione **Impostazioni globali**. Nella finestra **Modifica criteri di autenticazione globali** selezionare **Autenticazione a più fattori** come metodo di autenticazione aggiuntivo e quindi fare clic su **OK**. L'adapter viene registrata come WindowsAzureMultiFactorAuthentication. È necessario riavviare il servizio ADFS per rendere effettiva la registrazione.

![Modificare i criteri di autenticazione globali](./media/howto-mfaserver-adfs-2012/global.png)

A questo punto, il server Multi-Factor Authentication è configurato per essere un provider di autenticazione aggiuntivo per l'uso con AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installare un'istanza autonoma dell'adapter AD FS usando l'SDK del servizio Web

1. Installare l'SDK del servizio Web nel server che esegue il server Multi-Factor Authentication.
2. Copiare i file seguenti dalla directory \Programmi\Multi-Factor Authentication Server al server in cui si prevede di installare l'adapter AD FS:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Eseguire il file di installazione MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Nel programma di installazione della scheda ADFS di Multi-Factor Authentication fare clic su **Avanti** per eseguire l'installazione.
5. Al termine dell'installazione, fare clic su **Chiudi** .

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Modificare il file MultiFactorAuthenticationAdfsAdapter.config

Per modificare il file MultiFactorAuthenticationAdfsAdapter.config, seguire questa procedura:

1. Impostare il nodo **UseWebServiceSdk** su **true**.  
2. Impostare il valore di **WebServiceSdkUrl** sull'URL dell'SDK del servizio Web Multi-Factor Authentication. Ad esempio: *https:\/\/contoso.com/\<certificate>/multifactorauthwebservicesdk/pfwssdk.asmx*, dove * \<certificate>* è il nome del certificato.  
3. Modificare lo script Register-multifactorauthenticationadfsadapter. ps1 aggiungendo `-ConfigurationFilePath &lt;path&gt;` alla fine del `Register-AdfsAuthenticationProvider` comando, dove * &lt;Path&gt; * è il percorso completo del file file multifactorauthenticationadfsadapter. config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurare l'SDK del servizio Web con un nome utente e una password

Per configurare l'SDK del servizio Web è possibile procedere in due modi: usando un nome utente e una password oppure un certificato client. Per la prima opzione, seguire questa procedura. Per la seconda opzione, vedere più avanti.  

1. Impostare il valore di **WebServiceSdkUsername** su un account membro del gruppo di sicurezza PhoneFactor Admins. Usare il formato &lt;dominio&gt;&#92;&lt;nome utente&gt;.  
2. Impostare il valore di **WebServiceSdkPassword** sulla password dell'account appropriato.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurare l'SDK del servizio Web con un certificato client

Se si preferisce non usare un nome utente e una password, seguire questa procedura per configurare l'SDK del servizio Web con un certificato client.

1. Ottenere un certificato client da un'autorità di certificazione per il server che esegue l'SDK del servizio Web. Informazioni su come [ottenere certificati client](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importare il certificato client nell'archivio certificati personali del computer locale nel server che esegue l'SDK del servizio Web. Verificare che il certificato pubblico dell'autorità di certificazione si trovi nell'archivio dei certificati radice attendibili.  
3. Esportare le chiavi pubbliche e private del certificato client in un file PFX.  
4. Esportare la chiave pubblica con codifica Base64 in un file CER.  
5. In Server Manager, verificare che sia installata la funzionalità Autenticazione mapping certificati client IIS in Server Web (IIS)\Server Web\Sicurezza. Se non è installata, selezionare **Aggiungi ruoli e funzionalità** per aggiungerla.  
6. In Gestione IIS fare doppio clic sull' **editor di configurazione** nel sito Web che contiene la directory virtuale dell'SDK del servizio Web. È importante selezionare il sito Web, non la directory virtuale.  
7. Passare alla sezione **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Impostare enabled su **true**.  
9. Impostare oneToOneCertificateMappingsEnabled su **true**.  
10. Fare clic sul pulsante **...** accanto a oneToOneMappings e sul collegamento **Aggiungi**.  
11. Aprire il file CER con codifica Base64 esportato in precedenza. Rimuovere *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* e tutte le interruzioni di riga. Copiare la stringa risultante.  
12. Impostare certificate sulla stringa copiata nel passaggio precedente.  
13. Impostare enabled su **true**.  
14. Impostare userName su un account membro del gruppo di sicurezza PhoneFactor Admins. Usare il formato &lt;dominio&gt;&#92;&lt;nome utente&gt;.  
15. Impostare la password su quella dell'account appropriato e quindi chiudere l'editor di configurazione.  
16. Fare clic sul collegamento **Apply** (Applica).  
17. Nella directory virtuale dell'SDK del servizio Web fare doppio clic su **Autenticazione**.  
18. Verificare che Rappresentazione ASP.NET e Autenticazione di base siano entrambe **abilitate** e che tutti gli altri elementi siano **disabilitati**.  
19. Nella directory virtuale dell'SDK del servizio Web fare doppio clic su **Impostazioni SSL**.  
20. Impostare Certificati client su **Accetta** e fare clic su **Applica**.  
21. Copiare il file PFX esportato in precedenza nel server che esegue la scheda AD FS.  
22. Importare il file PFX nell'archivio certificati personali del computer locale.  
23. Fare clic con il pulsante destro del mouse e selezionare **Gestisci chiavi private**, quindi concedere l'accesso in lettura all'account usato per accedere al servizio AD FS.  
24. Aprire il certificato client e copiare l'identificazione personale dalla scheda **Dettagli** .  
25. Nel file MultiFactorAuthenticationAdfsAdapter.config impostare **WebServiceSdkCertificateThumbprint** sulla stringa copiata nel passaggio precedente.  

Infine, per registrare la scheda, eseguire lo script \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 in PowerShell. L'adapter viene registrata come WindowsAzureMultiFactorAuthentication. È necessario riavviare il servizio ADFS per rendere effettiva la registrazione.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteggere le risorse Azure AD con ADFS

Per proteggere le risorse cloud, configurare una regola attestazioni in modo che Active Directory Federation Services generi l'attestazione multipleauthn quando un utente esegue correttamente la verifica in due passaggi. Questa attestazione viene passata ad Azure AD. Seguire questa procedura per eseguire i passaggi:

1. Aprire il componente di gestione di ADFS.
2. A sinistra selezionare **Attendibilità componente**.
3. Fare clic con il pulsante destro del mouse su **Piattaforma delle identità di Microsoft Office 365** e selezionare **Modifica regole attestazione...**

   ![Modificare le regole attestazione nella console ADFS](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. In regole di trasformazione rilascio fare clic su **Aggiungi regola.**

   ![Modificare le regole di trasformazione nella console ADFS](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. Nell'Aggiunta guidata regole attestazione di trasformazione selezionare **Applicare la funzione di pass-through o di filtro a un'attestazione in ingresso** dall'elenco a discesa e fare clic su **Avanti**.

   ![Aggiunta guidata regola attestazione di trasformazione](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. Assegnare un nome alla regola.
7. Selezionare **Riferimenti dei metodi di autenticazione** come Tipo di attestazione in ingresso.
8. Selezionare **Pass-through di tutti i valori attestazione**.

    ![Aggiunta guidata regole attestazione di trasformazione](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. Fare clic su **Fine**. Chiudere la console di gestione di ADFS.

## <a name="troubleshooting-logs"></a>Log di risoluzione dei problemi

Per contribuire alla risoluzione dei problemi della scheda AD FS del server di Multi-Factor Authentication, seguire questa procedura per abilitare la registrazione aggiuntiva.

1. Nell'interfaccia del server di Multi-Factor Authentication aprire la sezione relativa ad AD FS e selezionare la casella di controllo **Abilita registrazione**.
2. In ogni server di AD FS usare **regedit.exe** per creare la chiave `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` del Registro di sistema per il valore di stringa con il valore `C:\Program Files\Multi-Factor Authentication Server\` o una qualsiasi altra directory di propria scelta.  **Si noti che la barra rovesciata finale è importante.**
3. Creare la directory `C:\Program Files\Multi-Factor Authentication Server\Logs` o qualsiasi altra directory, come indicato nel **Passaggio 2**.
4. Concedere l'accesso di modifica alla directory Logs all'account del servizio AD FS.
5. Riavviare il servizio AD FS.
6. Assicurarsi che il file `MultiFactorAuthAdfsAdapter.log` sia stato creato nella directory Logs.

## <a name="related-topics"></a>Argomenti correlati

Per la risoluzione dei problemi, vedere [Domande frequenti su Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
