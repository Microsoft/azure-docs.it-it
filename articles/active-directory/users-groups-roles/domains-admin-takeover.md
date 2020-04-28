---
title: Acquisizione di un amministratore di una directory non gestita-Azure AD | Microsoft Docs
description: Come assumere un nome di dominio DNS in un'organizzazione Azure AD non gestita (tenant Shadow).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834176"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Acquisire la proprietà di una directory non gestita come amministratore in Azure Active Directory

Questo articolo illustra due modi per acquisire la proprietà di un nome di dominio DNS in una directory non gestita in Azure Active Directory (Azure AD). Quando un utente self-service si iscrive a un servizio cloud che usa Azure AD, viene aggiunto a una directory di Azure AD non gestita basata sul dominio di posta elettronica. Per ulteriori informazioni sull'iscrizione self-service o "virale" per un servizio, vedere informazioni sull' [iscrizione self-service per Azure Active Directory](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decidere come acquisire la proprietà di una directory non gestita
Durante il processo di acquisizione della proprietà da parte di un amministratore, è possibile dimostrare la proprietà come illustrato in [Aggiungere un nome di dominio personalizzato ad Azure AD](../fundamentals/add-custom-domain.md). Nelle sezioni successive viene illustrata l'esperienza di amministratore in modo più dettagliato; in ogni caso qui di seguito è riportato un riepilogo:

* Quando si esegue un'[acquisizione "interna" della proprietà da parte dell'amministratore](#internal-admin-takeover) di una directory di Azure non gestita, si viene aggiunti come amministratore globale della directory non gestita. Non viene eseguita la migrazione di utenti, domini o piani di servizio ad altre directory amministrate.

* Quando si esegue un'[acquisizione "esterna" della proprietà da parte dell'amministratore](#external-admin-takeover) di una directory di Azure non gestita, si aggiunge il nome di dominio DNS della directory non gestita alla directory di Azure gestita. Quando si aggiunge il nome di dominio, viene creato un mapping tra utenti e risorse nella directory di Azure gestita in modo che gli utenti possano continuare ad accedere ai servizi senza interruzioni. 

## <a name="internal-admin-takeover"></a>Acquisizione interna della proprietà da parte dell'amministratore

Alcuni prodotti che includono SharePoint e OneDrive, ad esempio Office 365, non supportano l'acquisizione esterna della proprietà. In questo caso oppure se si è un amministratore e si vuole acquisire la proprietà di un tenant non gestito o "shadow" creato da utenti che hanno usato l'iscrizione self-service, è possibile eseguire questa operazione con un'acquisizione interna della proprietà da parte di un amministratore.

1. Creare un contesto utente nel tenant non gestito tramite l'iscrizione a Power BI. Per semplificare l'esempio, questa procedura presuppone tale percorso.

2. Aprire il [sito di Power BI](https://powerbi.com) e selezionare **Inizia gratuitamente**. Immettere un account utente che usa il nome di dominio per l'organizzazione, ad esempio `admin@fourthcoffee.xyz`. Dopo avere immesso il codice di verifica, verificare la disponibilità del codice di conferma nella posta elettronica.

3. Nel messaggio di conferma da Power BI selezionare **Yes, that's me** (Sì, sono io).

4. Accedere al centro di [amministrazione di Microsoft 365](https://portal.office.com/admintakeover) con l'account utente Power bi. Si riceve un messaggio che invita a **diventare l'amministratore** del nome di dominio già verificato nel tenant non gestito. Selezionare **Yes, I want to be the admin** (Sì, voglio essere l'amministratore).
  
   ![Primo screenshot per Become the Admin (Diventa l'amministratore)](./media/domains-admin-takeover/become-admin-first.png)
  
5. Aggiungere il record TXT per dimostrare di essere il proprietario del nome di dominio **fourthcoffee.xyz** nel registrar. In questo esempio si tratta di GoDaddy.com.
  
   ![Aggiungere un record TXT per il nome di dominio](./media/domains-admin-takeover/become-admin-txt-record.png)

Quando i record TXT DNS vengono verificati nel registrar del nome di dominio, è possibile gestire il tenant di Azure AD.

Dopo avere completato i passaggi precedenti, si è l'amministratore globale del tenant Fourth Coffee in Office 365. Per integrare il nome di dominio con gli altri servizi di Azure, è possibile rimuoverlo da Office 365 e aggiungerlo a un tenant gestito diverso in Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Aggiunta del nome di dominio a un tenant gestito in Azure AD

1. Aprire il [centro di amministrazione Microsoft 365](https://admin.microsoft.com).
2. Selezionare la scheda **utenti** e creare un nuovo account utente con un nome come *User\@fourthcoffeexyz.onmicrosoft.com* che non usa il nome di dominio personalizzato. 
3. Assicurarsi che il nuovo account utente abbia privilegi di amministratore globale per il tenant di Azure AD.
4. Aprire la scheda **domini** nell'interfaccia di amministrazione di Microsoft 365, selezionare il nome di dominio e selezionare **Rimuovi**. 
  
   ![Rimuove il nome di dominio da Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Se in Office 365 sono presenti utenti o gruppi che fanno riferimento al nome di dominio rimosso, devono essere ridenominati nel dominio .onmicrosoft.com. Se si forza l'eliminazione del nome di dominio, tutti gli utenti vengono automaticamente rinominati, in questo esempio, in *fourthcoffeexyz.onmicrosoft.com utente\@*.
  
6. Accedere all'[interfaccia di amministrazione di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account amministratore globale per il tenant di Azure AD.
  
7. Selezionare **Nomi di dominio personalizzati** e quindi aggiungere il nome di dominio. Sarà necessario immettere i record TXT DNS per verificare la proprietà del nome di dominio. 
  
   ![dominio verificato come aggiunto al Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Gli utenti di Power BI o del servizio Azure Rights Management con licenze assegnate nel tenant di Office 365 devono salvare i dashboard se il nome di dominio viene rimosso. Devono accedere con un nome utente come *\@User fourthcoffeexyz.onmicrosoft.com* anziché *User\@fourthcoffee. xyz*.

## <a name="external-admin-takeover"></a>Acquisizione esterna della proprietà da parte dell'amministratore

Se si gestisce già un tenant con i servizi di Azure o Office 365, non è possibile aggiungere un nome di dominio personalizzato se è già verificato in un altro tenant di Azure AD. Dal tenant gestito in Azure AD è tuttavia possibile acquisire esternamente la proprietà di un tenant non gestito come amministratore. La procedura generale segue l'articolo [Aggiungere un nome di dominio personalizzato ad Azure AD](../fundamentals/add-custom-domain.md).

Quando si verifica la proprietà del nome di dominio, Azure AD lo rimuove dal tenant non gestito e lo sposta nel tenant esistente. L'acquisizione esterna della proprietà di una directory non gestita da parte di un amministratore richiede lo stesso processo convalida di convalida TXT DNS dell'acquisizione interna della proprietà da parte di un amministratore, con la differenza che anche gli elementi seguenti vengono spostati con il nome di dominio:

- Utenti
- Sottoscrizioni
- Assegnazioni di licenze

### <a name="support-for-external-admin-takeover"></a>Supporto per l'acquisizione esterna della proprietà da parte dell'amministratore
L'acquisizione esterna della proprietà da parte dell'amministratore è supportata dai servizi online seguenti:

- Azure Rights Management
- Exchange Online

I piani di servizio supportati includono:

- PowerApps gratuito
- PowerFlow gratuito
- RMS per utenti singoli
- Microsoft Stream
- Versione di valutazione gratuita di Dynamics 365

L'acquisizione di un amministratore esterno non è supportata per alcun servizio con piani di servizio che includono SharePoint, OneDrive o Skype for business. ad esempio, tramite una sottoscrizione gratuita di Office. 

Facoltativamente è possibile usare l'[opzione **ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) per rimuovere il nome di dominio dal tenant non gestito e verificarlo nel tenant desiderato. 

#### <a name="more-information-about-rms-for-individuals"></a>Altre informazioni su RMS per utenti singoli

Per [RMS per utenti singoli](/azure/information-protection/rms-for-individuals), quando il tenant non gestito si trova nella stessa area del tenant di cui si è proprietari, anche la [chiave del tenant Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) creata automaticamente e i [modelli di protezione predefiniti](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) vengono spostati con il nome di dominio.

La chiave e i modelli non vengono invece spostati quando il tenant si trova in un'area diversa, Ad esempio, se il tenant non gestito si trova in Europa e l'organizzazione di cui si è proprietari è in America del Nord.

Anche se RMS per utenti singoli è progettato per supportare l'autenticazione Azure AD per l'apertura di contenuti protetti, non impedisce agli utenti di proteggere a loro volta il contenuto. Se gli utenti hanno protetto il contenuto con la sottoscrizione RMS per utenti singoli e la chiave e i modelli non sono stati spostati, il contenuto non sarà accessibile dopo l'acquisizione del dominio.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Cmdlet di Azure AD PowerShell per l'opzione ForceTakeover
È possibile visualizzare questi cmdlet usati in [Esempio di PowerShell](#powershell-example).

Cmdlet | Utilizzo
------- | -------
`connect-msolservice` | Quando richiesto, accedere al tenant gestito.
`get-msoldomain` | Mostra i nomi di dominio associati al tenant corrente.
`new-msoldomain –name <domainname>` | Aggiunge il nome di dominio al tenant come non verificato. Non sono ancora state eseguite verifiche DNS.
`get-msoldomain` | Il nome di dominio ora è incluso nell'elenco di nomi di dominio associati al tenant gestito, ma è elencato come **Non verificato**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fornisce le informazioni da inserire nel nuovo record TXT DNS per il dominio (MS=xxxxx). La verifica potrebbe non essere eseguita immediatamente perché la propagazione del record TXT richiede tempo. Attendere quindi alcuni minuti prima di prendere in considerazione l'opzione **-ForceTakeover**. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Se il nome di dominio continua a non essere verificato, è possibile procedere con l'opzione **-ForceTakeover**, che verifica che il record TXT sia stato creato e avvia il processo di acquisizione della proprietà.<li>L'opzione **-ForceTakeover** deve essere aggiunta al cmdlet solo quando si forza l'acquisizione esterna della proprietà da parte dell'amministratore, ad esempio quando il tenant non gestito ha i servizi di Office 365 che bloccano l'acquisizione della proprietà.
`get-msoldomain` | Nell'elenco di domini il nome di dominio ora risulta **Verificato**.

> [!NOTE]
> L'organizzazione Azure AD non gestita viene eliminata 10 giorni dopo l'utilizzo dell'opzione External OPA Force.

### <a name="powershell-example"></a>Esempio di PowerShell

1. Connettersi ad Azure AD usando le credenziali usate per rispondere all'offerta self-service:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Ottenere un elenco di domini:
  
   ```powershell
   Get-MsolDomain
   ```
3. Eseguire il cmdlet Get-MsolDomainVerificationDns per creare una richiesta di verifica:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Ad esempio:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Copiare il valore (la richiesta di verifica) restituita da questo comando. Ad esempio:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Nello spazio dei nomi DNS pubblico creare un record txt DNS che contiene il valore copiato nel passaggio precedente. Il nome per questo record è quello del dominio padre, quindi se si crea questo record di risorse usando il ruolo DNS di Windows Server, lasciare il nome record vuoto e incollare solo il valore nella casella di testo.
6. Eseguire il cmdlet Confirm-MsolDomain per verificare la richiesta di verifica:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Ad esempio:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Una richiesta di verifica riporta al prompt senza errori.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere un nome di dominio personalizzato ad Azure AD](../fundamentals/add-custom-domain.md)
* [Come installare e configurare Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Informazioni di riferimento sui cmdlet di Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
