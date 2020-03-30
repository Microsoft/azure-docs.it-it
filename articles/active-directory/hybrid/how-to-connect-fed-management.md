---
title: Azure AD Connect - Gestione e personalizzazione di AD FS | Microsoft Docs
description: Gestione di AD FS con Azure AD Connect e personalizzazione dell'esperienza utente di accesso ad AD FS con Azure AD Connect e PowerShell.
keywords: AD FS, ADFS, gestione di AD FS, AAD Connect, Connect, accesso, personalizzazione di AD FS, ripristino trust, O365, federazione, relying party
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcbeedddc65a916f869a778616779917a9571181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331971"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gestire e personalizzare Active Directory Federation Services con Azure AD Connect
In questo articolo viene descritto come gestire e personalizzare Active Directory Federation Services (ADFS) tramite Azure Active Directory (Azure AD) Connect. Si includono inoltre altre attività comuni di AD FS che potrebbero essere necessarie per eseguire una configurazione completa di una farm di AD FS.

| Argomento | Contenuto |
|:--- |:--- |
| **Gestire AD FS** | |
| [Ripristinare il trust](#repairthetrust) |Come ripristinare il trust federativo con Office 365. |
| [Eseguire la federazione con Azure AD usando un ID di accesso alternativo](#alternateid) | Configurazione della federazione usando l'ID di accesso alternativo  |
| [Aggiungere un server ADFSAdd an AD FS server](#addadfsserver) |Come espandere una farm AD FS con un server AD FS aggiuntivo. |
| [Aggiungere un server proxy applicazione Web AD FS](#addwapserver) |Come espandere la farm AD FS con un server Proxy applicazione Web (WAP) aggiuntivo. |
| [Aggiunta di un dominio federato](#addfeddomain) |Come aggiungere un dominio federato. |
| [Aggiornare il certificato TLS/SSL](how-to-connect-fed-ssl-update.md)| Come aggiornare il certificato TLS/SSL per una farm ADFS. |
| **Personalizzare AD FS** | |
| [Aggiungere un'illustrazione o il logo personalizzato della società](#customlogo) |Come personalizzare la pagina di accesso ad AD FS con un'illustrazione e il logo della società. |
| [Aggiungere una descrizione di accesso](#addsignindescription) |Come aggiungere una descrizione della pagina di accesso. |
| [Modificare le regole attestazioni per AD FS](#modclaims) |Come modificare le attestazioni di AD FS per vari scenari di federazione. |

## <a name="manage-ad-fs"></a>Gestire AD FS
È possibile eseguire diverse operazioni relative ad Azure AD Connect con la procedura guidata di Azure AD Connect con un intervento minimo dell'utente. Al termine dell'installazione guidata di Azure AD Connect, è possibile eseguire nuovamente la procedura guidata per eseguire attività aggiuntive.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Ripristinare il trust 
È possibile usare Azure AD Connect per controllare lo stato corrente dell'integrità di trust di AD FS e Azure AD e intraprendere le azioni necessarie per ripristinare il trust. Per ripristinare la relazione di trust tra Azure AD e AD FS, seguire questa procedura.

1. Selezionare **Ripristino del trust di AAD e AD FS** nell'elenco delle attività aggiuntive.
   ![Ripristino del trust di AAD e AD FS](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Nella pagina **Connessione ad Azure AD** specificare le credenziali di amministratore globale per Azure AD e fare clic su **Avanti**.
   ![Connessione ad Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Nella pagina **Credenziali di accesso remoto** specificare le credenziali dell'amministratore di dominio.

   ![Credenziali di accesso remoto](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Dopo aver fatto clic su **Avanti**, Azure AD Connect verifica l'integrità del certificato e visualizza gli eventuali problemi.

    ![Stato dei certificati](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    La pagina **Pronto per la configurazione** mostra l'elenco delle azioni che verranno eseguite per ripristinare il trust.

    ![Pronto per la configurazione](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Fare clic su **Installa** per ripristinare il trust.

> [!NOTE]
> Azure AD Connect può solo ripristinare o eseguire azioni sui certificati autofirmati. I certificati di terze parti non possono essere ripristinati da Azure AD Connect.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Eseguire la federazione con Azure AD usando AlternateIDFederate with Azure AD using AlternateID 
Il nome dell’entità utente locale e il nome dell'entità utente cloud devono essere preferibilmente uguali. Se il nome dell’entità utente locale usa un dominio non instradabile (ad esempio Contoso.local) o non può essere modificato a causa di dipendenze dell'applicazione locale, è consigliabile configurare l'ID di accesso alternativo. L’ID di accesso alternativo consente di configurare un'esperienza in cui gli utenti possono accedere con un attributo diverso dal relativo nome dell’entità locale, ad esempio mail. La scelta del nome dell’entità utente in Azure AD Connect ricade per impostazione predefinita sull’attributo userPrincipalName in Active Directory. Se si sceglie qualsiasi altro attributo per il nome dell'entità utente e si sta eseguendo la federazione con AD FS, Azure AD Connect configurerà AD FS per l’ID di accesso alternativo. Di seguito è riportato un esempio della scelta di un attributo diverso per il nome dell'entità utente:

![Selezione dell’attributo ID alternativo](./media/how-to-connect-fed-management/attributeselection.png)

La configurazione dell’ID di accesso alternativo per AD FS consiste in due passaggi principali:
1. **Configurare il set corretto di attestazioni di rilascio**: le regole di attestazione di rilascio nel trust della relying party di Azure AD vengono modificate per usare l’attributo UserPrincipalName selezionato come ID alternativo dell'utente.
2. **Abilitare l'ID di accesso alternativo nella configurazione di AD FS**: la configurazione di AD FS viene aggiornata in modo che AD FS possa cercare gli utenti delle foreste appropriate con un ID alternativo. Questa configurazione è supportata per AD FS in Windows Server 2012 R2 (con KB2919355) o versioni successive. Se i server AD FS sono 2012 R2, Azure AD Connect controlla la presenza della KB richiesta. Se la Knowledge Base non viene rilevata, un avviso verrà visualizzato al termine della configurazione, come illustrato di seguito:

    ![Avviso per KB mancante su 2012 R2](./media/how-to-connect-fed-management/kbwarning.png)

    Per risolvere la configurazione in caso di KB mancante, installare la [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) richiesta e quindi ripristinare il trust usando [Ripristino del trust AAD e AD FS](#repairthetrust).

> [!NOTE]
> Per altre informazioni su alternateID e i passaggi per eseguire la configurazione manuale, leggere [Configurazione di ID di accesso alternativo](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Aggiungere un server ADFSAdd an AD FS server 

> [!NOTE]
> Per aggiungere un server AD FS, Azure AD Connect richiede il file PFX del certificato. È quindi possibile eseguire questa operazione solo se la farm AD FS è stata configurata con Azure AD Connect.

1. Selezionare **Distribuzione di un server federativo aggiuntivo** e fare clic su **Avanti**.

   ![Server federativo aggiuntivo](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Nella pagina **Connessione ad Azure AD** specificare le credenziali di amministratore globale per Azure AD e fare clic su **Avanti**.

   ![Connessione ad Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Specificare le credenziali di amministratore di dominio.

   ![Credenziali dell'amministratore di dominio](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect chiederà la password del file PFX specificato durante la configurazione della nuova farm AD FS con Azure AD Connect. Fare clic su **Immettere la password** per specificare la password del file PFX.

   ![Password certificato](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Specificare il certificato TLS/SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Nella pagina **Server ADFS** immettere il nome del server o l'indirizzo IP da aggiungere alla farm ADFS.

   ![Server AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Fare clic su **Avanti** e procedere fino alla pagina **Configura** finale. Quando Azure AD Connect avrà completato l'aggiunta dei server alla farm AD FS, si potrà verificare la connettività.

   ![Pronto per la configurazione](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Installazione completata](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Aggiungere un server WAP di ADFSAdd an AD FS WAP server 

> [!NOTE]
> Per aggiungere un server WAP, Azure AD Connect richiede il file PFX del certificato. È quindi possibile eseguire questa operazione solo se la farm AD FS è stata configurata con Azure AD Connect.

1. Selezionare **Distribuisci il proxy applicazione Web (nessuno attualmente configurato)** nell'elenco delle attività disponibili.

   ![Distribuire il Proxy applicazione Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Specificare le credenziali di amministratore globale di Azure.

   ![Connessione ad Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Nella pagina **Specificare il certificato SSL** indicare la password per il file PFX specificato durante la configurazione della farm AD FS con Azure AD Connect.
   ![Password certificato](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Specificare il certificato TLS/SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Aggiungere il server da usare come server WAP. Dato che il server WAP potrebbe anche non essere aggiunto al dominio, la procedura guidata richiede le credenziali amministrative da aggiungere per il server.

   ![Credenziali amministrative del server](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Nella pagina **Credenziali attendibilità proxy** specificare le credenziali amministrative per configurare l'attendibilità del proxy e accedere al server primario nella farm AD FS.

   ![Credenziali di attendibilità del proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. La pagina **Pronto per la configurazione** della procedura guidata mostra l'elenco delle azioni che verranno eseguite.

   ![Pronto per la configurazione](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Fare clic su **Installa** per completare la configurazione. Al termine della configurazione, la procedura guidata offre la possibilità di verificare la connettività ai server. Fare clic su **Verifica** per controllare la connettività.

   ![Installazione completata](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Aggiunta di un dominio federato 

Con Azure AD Connect è facile aggiungere un dominio per la federazione con Azure AD. Azure AD Connect aggiunge il dominio per la federazione e modifica le regole attestazioni per riflettere correttamente l'autorità di certificazione quando sono presenti più domini federati con Azure AD.

1. Per aggiungere un dominio federato, selezionare l'attività **Aggiunta di un altro dominio di Azure AD**.

   ![Dominio di Azure AD aggiuntivo](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Nella pagina successiva della procedura guidata specificare le credenziali di amministratore globale per Azure AD.

   ![Connessione ad Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Nella pagina **Credenziali di accesso remoto** specificare le credenziali di amministratore di dominio.

   ![Credenziali di accesso remoto](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Nella pagina successiva la procedura guidata mostra un elenco di domini di Azure AD con cui è possibile attuare la federazione della directory locale. Scegliere il dominio dall'elenco.

   ![Dominio di Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Dopo aver scelto il dominio, la procedura guidata offre informazioni appropriate sulle altre azioni che verranno eseguite e sull'impatto della configurazione. In alcuni casi, se si seleziona un dominio non ancora verificato in Azure AD, la procedura guidata offre informazioni che consentono di verificare il dominio. Per altre informazioni, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](../active-directory-domains-add-azure-portal.md) .

5. Fare clic su **Avanti**. Nella pagina **Pronto per la configurazione** viene visualizzato l'elenco delle azioni che verranno eseguite da Azure AD Connect. Fare clic su **Installa** per completare la configurazione.

   ![Pronto per la configurazione](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Prima che possano accedere ad Azure AD, gli utenti del dominio federato aggiunto devono essere sincronizzati.

## <a name="ad-fs-customization"></a>Personalizzazione di AD FS
Le sezioni seguenti offrono informazioni dettagliate su alcune attività comuni che potrebbe essere necessario eseguire durante la configurazione della pagina di accesso ad AD FS.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Aggiungere un'illustrazione o il logo personalizzato della società 
Per modificare il logo della società visualizzato nella pagina **Accesso** , usare la sintassi e il cmdlet di Windows PowerShell seguenti.

> [!NOTE]
> Le dimensioni consigliate per il logo sono 260 x 35 \@ 96 DPI, con dimensioni del file non maggiori di 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Il parametro *TargetName* è obbligatorio. Il tema predefinito incluso in AD FS è denominato Predefinito.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Aggiungere una descrizione di accesso 
Per aggiungere una descrizione alla **Pagina di accesso**stessa, usare la sintassi e il cmdlet di Windows PowerShell seguenti.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Modificare le regole attestazioni per AD FS 
AD FS supporta un linguaggio di attestazione avanzato che può essere usato per creare regole attestazioni personalizzate. Per altre informazioni, vedere [Ruolo del linguaggio delle regole attestazioni](https://technet.microsoft.com/library/dd807118.aspx).

Le sezioni seguenti descrivono come scrivere regole personalizzate per alcuni scenari relativi alla federazione di AD FS e Azure AD.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID non modificabile in base alla presenza di un valore nell'attributo
Azure AD Connect consente di specificare un attributo da usare come ancoraggio di origine durante la sincronizzazione degli oggetti con Azure AD. Se il valore dell'attributo personalizzato non è vuoto, è consigliabile rilasciare un'attestazione con ID non modificabile.

Ad esempio, è possibile selezionare **ms-ds-consistencyguid** come attributo per l'ancoraggio di origine e rilasciare **ImmutableID** come **ms-ds-consistencyguid** nel caso in cui il valore dell'attributo non sia vuoto. Se non esiste un valore a fronte dell'attributo, rilasciare **objectGuid** come ID non modificabile. È possibile costruire il set di regole attestazioni personalizzate come descritto nella sezione seguente.

**Regola 1: Attributi della query**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

In questa regola si esegue una query da Active Directory sui valori di **ms-ds-consistencyguid** e **objectGuid** per l'utente. Modificare il nome dell'archivio con un nome di archivio appropriato nella distribuzione di AD FS. Sostituire anche il tipo di attestazioni con il tipo appropriato per la federazione, come definito per **objectGuid** e **ms-ds-consistencyguid**.

Inoltre, se si usa **add** invece di **issue**, si evita di aggiungere un rilascio in uscita per l'entità ed è possibile usare i valori come valori intermedi. L'attestazione verrà rilasciata in una regola successiva, dopo aver stabilito il valore usare come ID non modificabile.

**Regola 2: Verificare la presenza di ms-ds-consistencyguid per l'utente**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Questa regola definisce un flag temporaneo denominato **idflag**, che è impostato su **useguid** se non è presente un **ms-ds-concistencyguid** popolato per l'utente. Questo perché secondo la logica di AD FS non sono ammesse attestazioni vuote. Pertanto, quando si aggiungono le attestazioni `http://contoso.com/ws/2016/02/identity/claims/objectguid` e `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` nella regola 1, si ottiene un'attestazione **msdsconsistencyguid** solo se il valore viene popolato per l'utente. Se non è popolato, AD FS rileva che avrà un valore vuoto e lo rimuove immediatamente. Tutti gli oggetti avranno **objectGuid**, quindi l'attestazione sarà sempre presente dopo l'esecuzione della regola 1.

**Regola 3: Rilasciare ms-ds-consistencyguid come ID non modificabile se presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Si tratta di un controllo **Exist** implicito. Se il valore per l'attestazione esiste, viene emesso come ID non modificabile. Nell'esempio precedente si usa l'attestazione **nameidentifier** . Sarà necessario sostituirla con il tipo di attestazione appropriato per l'ID non modificabile nel proprio ambiente.

**Regola 4: Rilasciare objectGuid come ID non modificabile se ms-ds-consistencyGuid non è presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

In questa regola si verifica semplicemente il flag temporaneo **idflag**. Decidere se rilasciare l'attestazione in base al relativo valore.

> [!NOTE]
> La sequenza delle regole è importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO con un UPN di sottodominio

È possibile aggiungere più domini per la federazione usando Azure AD Connect, come descritto in [Aggiungere un nuovo dominio federato](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect versione 1.1.553.0 e successive crea automaticamente la regola di attestazione corretta per issuerID. Se non è possibile usare Azure AD Connect 1.1.553.0 o versione più recente, è consigliabile che venga usato lo strumento delle [regole di attestazioni dell'attendibilità del componente (RPT, Relying Party Trust) di Azure Active Directory](https://aka.ms/aadrptclaimrules) per generare e configurare le regole di attestazioni corrette per il trust della relying party di Azure AD.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle [opzioni di accesso utente](plan-connect-user-signin.md).
