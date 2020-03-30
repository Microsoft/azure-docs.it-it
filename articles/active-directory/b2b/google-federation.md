---
title: Aggiungere Google come provider di identità per B2B - Azure ADAdd Google as an identity provider for B2B - Azure AD
description: Configurare una federazione con Google per consentire agli utenti guest di accedere alle app di Azure AD con il proprio account Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126677"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Aggiungere Google come provider di identità per utenti guest B2B

Configurando la federazione con Google, puoi consentire agli utenti invitati di accedere alle app e alle risorse condivise con i propri account Gmail, senza dover creare account Microsoft (MSA). 

> [!NOTE]
> La federazione di Google è progettata specificamente per gli utenti Gmail. Per eseguire la federazione con i domini di G Suite, utilizzare la [funzionalità di federazione diretta.](direct-federation.md)

## <a name="what-is-the-experience-for-the-google-user"></a>Qual è l'esperienza per l'utente di Google?
Quando si invia un invito a un utente di Google Gmail, l'utente guest deve accedere alle app o alle risorse condivise tramite un collegamento che include il contesto tenant. L'esperienza varia a seconda che sia stato già eseguito o meno l'accesso a Google:
  - Se l'utente guest non ha ancora eseguito l'accesso a Google, gli verrà chiesto di farlo.
  - Se l'utente guest ha già eseguito l'accesso a Google, gli verrà chiesto di scegliere un account da usare. L'utente deve scegliere l'account usato nell'invito.

Se viene visualizzato un errore a causa dell'intestazione troppo lunga, l'utente guest può provare a cancellare i cookie oppure aprire una finestra privata o in incognito e tentare nuovamente l'accesso.

![Screenshot della pagina di accesso di Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Limitazioni

Teams supporta completamente gli utenti guest di Google su tutti i dispositivi. Gli utenti Google possono accedere a `https://teams.microsoft.com`Teams da un endpoint comune, ad esempio .

Gli endpoint comuni di altre applicazioni potrebbero non supportare gli utenti Google. Gli utenti guest di Google devono accedere utilizzando un collegamento che include le informazioni sul tenant. Di seguito vengono riportati alcuni esempi:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Se gli utenti guest di Google `https://myapps.microsoft.com` `https://portal.azure.com`tentano di utilizzare un link come o , verrà visualizzato un errore.

Puoi anche fornire agli utenti guest Di Google un collegamento diretto a un'applicazione `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`o a una risorsa, purché questo link includa le informazioni sul tenant, ad esempio . 

## <a name="step-1-configure-a-google-developer-project"></a>Passaggio 1: Configurare un progetto di Google Developers
Prima di tutto creare un nuovo progetto nella console di Google Developers per ottenere un ID client e un segreto client che sarà possibile aggiungere ad Azure AD in un secondo momento. 
1. Passare alle API di Google all'indirizzo https://console.developers.google.com e accedere con l'account Google. È consigliabile usare l'account condiviso di un team Google.
2. Creare un nuovo progetto: nel dashboard selezionare **Create Project** (Crea progetto) e quindi **Create** (Crea). Nella pagina New Project (Nuovo progetto) immettere un nome in **Project Name** (Nome progetto) e quindi selezionare **Create** (Crea).
   
   ![Screenshot che mostra una pagina Nuovo progetto per Google](media/google-federation/google-new-project.png)

3. Assicurarsi che il nuovo progetto sia selezionato nel menu dei progetti. Quindi, in **API & Services (Servizi &)** selezionare **OAuth consent screen (Schermata di consenso OAuth).**

4. Selezionare **Esterno**, quindi **Crea**. 
5. Nella **schermata**di consenso OAuth immettere un **nome applicazione**. Lasciare invariate le altre impostazioni.

   ![Screenshot che mostra l'opzione della schermata di consenso ossibule Google](media/google-federation/google-oauth-consent-screen.png)

6. Scorrere fino alla sezione **Authorized domains** (Domini autorizzati) e immettere microsoftonline.com.

   ![Screenshot della sezione Domini autorizzati](media/google-federation/google-oauth-authorized-domains.png)

7. Selezionare **Salva**.

8. Scegliere **Credenziali**. Dal menu **Create credentials** (Crea credenziali) scegliere **OAuth client ID** (ID client OAuth).

   ![Screenshot che mostra l'opzione di creazione delle credenziali delle API Google](media/google-federation/google-api-credentials.png)

9. In **Application type** (Tipo di applicazione) scegliere **Web application** (Applicazione Web) e quindi in **Authorized redirect URIs** (URI di reindirizzamento autorizzati) immettere gli URI seguenti:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(dove `<directory id>` è l'ID directory)
   
     > [!NOTE]
     > Per trovare l'ID directory, passare a https://portal.azure.com e quindi in **Azure Active Directory** scegliere **Proprietà** e copiare il valore di **ID directory**.

   ![Screenshot della sezione URI di reindirizzamento autorizzati](media/google-federation/google-create-oauth-client-id.png)

10. Selezionare **Crea**. Copiare l'ID client e il segreto client, che verranno usati quando si aggiungerà il provider di identità nel portale di Azure AD.

   ![Screenshot che mostra l'ID client OAuth e il segreto client](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Passaggio 2: Configurare la federazione con Google in Azure AD 
Si imposteranno ora l'ID client e il segreto client di Google immettendoli nel portale di Azure AD o tramite PowerShell. Assicurarsi di testare la configurazione della federazione con Google inviando un invito al proprio indirizzo Gmail e quindi provando a riscattare l'invito con l'account Google invitato. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Per configurare la federazione con Google nel portale di Azure AD 
1. Passare al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **Provider di identità** e quindi fare clic sul pulsante **Google**.
4. Immettere un nome. Immettere quindi l'ID client e il segreto client ottenuti in precedenza. Selezionare **Salva**. 

   ![Screenshot della pagina Aggiungi provider di identità Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Per configurare la federazione con Google tramite PowerShell
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire questo comando: `Connect-AzureAD`.
3. Al prompt di accesso, accedere con l'account di amministratore globale gestito.  
4. Eseguire il comando seguente: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Usare l'ID client e il segreto client dall'app creati nel "Passaggio 1: Configurare un progetto di Google Developers". Per altre informazioni, vedere l'articolo relativo al cmdlet [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Come si rimuove la federazione con Google?
È possibile rimuovere la configurazione della federazione con Google. In questo caso, gli utenti guest di Google che hanno già riscattato l'invito non potranno accedere, ma è possibile concedere loro nuovamente l'accesso alle risorse eliminandoli dalla directory e invitandoli di nuovo. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Per eliminare la federazione con Google nel portale di Azure AD: 
1. Passare al [portale di Azure](https://portal.azure.com). Nel riquadro sinistro selezionare **Azure Active Directory**. 
2. Selezionare **Relazioni aziendali**.
3. Selezionare **Provider di identità**.
4. Nella riga **Google,** seleziona il menu di scelta rapida (**...**) e quindi seleziona **Elimina**. 
   
   ![Screenshot dell'opzione Elimina per il provider di identità di social networking](media/google-federation/google-social-identity-providers.png)

1. Selezionare **Sì** per confermare l'eliminazione. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Per eliminare la federazione con Google tramite PowerShell: 
1. Installare la versione più recente del modulo di Azure AD PowerShell per Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Eseguire `Connect-AzureAD`.  
4. Al prompt di accesso, accedere con l'account amministratore globale gestito.  
5. Immettere il comando seguente:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Per altre informazioni, vedere l'articolo relativo al cmdlet [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
