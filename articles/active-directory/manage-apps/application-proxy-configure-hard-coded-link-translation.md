---
title: Convertire collegamenti e URL - Proxy di app di Azure AD| Microsoft Docs
description: Informazioni su come reindirizzare i collegamenti hardcoded per le app pubblicate con Azure AD proxy di applicazione.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 544aa1a4a859a9c328eb1d36e72d0f9f9ddb55b2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327902"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>Reindirizzamento dei collegamenti hardcoded per le app pubblicate con Azure AD proxy di applicazione

Il proxy di applicazione di Azure AD rende disponibili le app locali per gli utenti remoti o che usano i propri dispositivi. Alcune app, tuttavia, sono state sviluppate con collegamenti locali incorporati nel codice HTML. Questi collegamenti non funzionano correttamente quando l'app viene usato in remoto. Quando si dispone di diverse applicazioni locali che si puntano a vicenda, gli utenti si aspettano che i collegamenti continuino a lavorare quando non si trovano in ufficio. 

Il modo migliore per assicurarsi che i collegamenti funzionino all'interno e all'esterno della rete aziendale consiste nel configurare gli URL esterni delle app in modo che corrispondano con gli URL interni. Usare [domini personalizzati](application-proxy-configure-custom-domain.md) per configurare l'URL esterno in modo che abbia il nome di dominio aziendale anziché il proxy di applicazione predefinito.


Se non è possibile usare domini personalizzati nel tenant, esistono molte altre opzioni per fornire questa funzionalità. Tutte queste sono compatibili con i domini personalizzati e tra loro, pertanto è possibile configurare domini personalizzati e altre soluzioni, se necessario.

> [!NOTE]
> La conversione dei collegamenti non è supportata per gli URL interni hardcoded generati tramite JavaScript.

**Opzione 1: usare Microsoft Edge:** questa soluzione è applicabile solo se si prevede di consigliare o richiedere agli utenti di accedere all'applicazione tramite il browser Microsoft Edge. Gestisce tutti gli URL pubblicati. 

**Opzione 2: Usare l'estensione MyApps** - Questa soluzione richiede agli utenti di installare un'estensione del browser lato client, ma gestisce tutti gli URL pubblicati e funziona con i browser più diffusi. 

**Opzione 3: Usare l'impostazione di conversione dei collegamenti** - Questa è un'impostazione sul lato amministratore invisibile agli utenti. Tuttavia, gestirà gli URL solo in HTML e CSS.   

Queste tre caratteristiche mantengono i collegamenti funzionanti indipendentemente dalla posizione degli utenti. Quando si hanno applicazioni che puntano direttamente a endpoint o porte interne, è possibile mappare questi URL interni agli URL del proxy di applicazione esterno pubblicato. 

 
> [!NOTE]
> L'ultima opzione è riservata ai tenant che, per qualsiasi motivo, non possono usare i domini personalizzati per avere gli stessi URL interni ed esterni per le app. Prima di abilitare questa funzionalità, verificare se i [domini personalizzati nel proxy di applicazione di Azure AD](application-proxy-configure-custom-domain.md) possono fare al caso. 
> 
> Oppure, se l'applicazione che deve essere configurata con la traslazione del collegamento è SharePoint, vedere [Configurare i mapping di accesso alternativo per SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) per un altro approccio ai collegamenti di mapping. 

 
### <a name="option-1-microsoft-edge-integration"></a>Opzione 1: integrazione di Microsoft Edge 

È possibile usare Microsoft Edge per proteggere ulteriormente l'applicazione e il contenuto. Per usare questa soluzione, è necessario richiedere o consigliare agli utenti di accedere all'applicazione tramite Microsoft Edge. Tutti gli URL interni pubblicati con il proxy di applicazione verranno riconosciuti da Edge e reindirizzati all'URL esterno corrispondente. Ciò garantisce il funzionamento di tutti gli URL interni a livello di codice e, se un utente va al browser e digita direttamente l'URL interno, funziona anche se l'utente è in remoto.  

Per altre informazioni, ad esempio su come configurare questa opzione, vedere la documentazione relativa alla [gestione dell'accesso Web tramite Edge per iOS e Android con Microsoft Intune](https://docs.microsoft.com/mem/intune/apps/manage-microsoft-edge) .  

### <a name="option-2-myapps-browser-extension"></a>Opzione 2: Estensione MyApps del browser 

Con l'estensione MyApps del browser, tutti gli URL interni pubblicati con Application Proxy sono riconosciuti dall'estensione e reindirizzati all'URL esterno corrispondente. Ciò garantisce il funzionamento di tutti gli URL interni a livello di codice e, se un utente va alla barra degli indirizzi del browser e digita direttamente l'URL interno, funziona anche se l'utente è in remoto.  

Per usare questa funzionalità, l'utente deve scaricare l'estensione e avere effettuato l'accesso. Non è necessaria nessun'altra configurazione per gli amministratori o gli utenti. 

Per altre informazioni, ad esempio su come configurare questa opzione, vedere la documentazione dell' [estensione del browser app](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) .

### <a name="option-3-link-translation-setting"></a>Opzione 3: Impostazione di conversione dei collegamenti 

Quando la conversione dei collegamenti è abilitata, il servizio Application Proxy cerca nel codice HTML e CSS i collegamenti interni pubblicati e li converte in modo da offrire agli utenti un'esperienza senza interruzioni. L'uso dell'estensione del browser app è preferibile all'impostazione della traduzione dei collegamenti, perché offre agli utenti un'esperienza più efficiente.

> [!NOTE]
> Se si usa l'opzione 2 o 3, è necessario abilitare solo uno di questi alla volta.

## <a name="how-link-translation-works"></a>Come funziona la conversione dei collegamenti

Dopo l'autenticazione, quando il server proxy passa i dati dell'applicazione all'utente, il proxy di applicazione esegue l'analisi dell'applicazione per i collegamenti hardcoded e li sostituisce con i rispettivi URL esterni pubblicati.

Il Proxy dell'applicazione presuppone che le applicazioni vengano codificate in UTF-8. In caso contrario, specificare il tipo di codifica in un'intestazione di risposta HTTP, ad esempio `Content-Type:text/html;charset=utf-8` .

### <a name="which-links-are-affected"></a>Quali collegamenti sono interessati?

La funzionalità di conversione dei collegamenti cerca solo i collegamenti che si trovano nel tag di codice nel corpo di un'app. Il proxy di applicazione ha una funzione separata per la conversione dei cookie o URL in intestazioni. 

Esistono due tipi comuni di collegamenti interni nelle applicazioni locali:

- I **collegamenti interni relativi** che puntano a una risorsa condivisa in una struttura di file locale come `/claims/claims.html`. Questi collegamenti sono attivati automaticamente nelle app che vengono pubblicate tramite il proxy di applicazione e continuano a funzionare con o senza la conversione dei collegamenti. 
- **Collegamenti interni** hardcoded ad altre app locali come `http://expenses` o file pubblicati come `http://expenses/logo.jpg` . La funzionalità di conversione dei collegamenti funziona sui collegamenti interni hardcoded e li modifica in modo che puntino agli URL esterni che gli utenti remoti devono usare.

L'elenco completo dei tag di codice HTML che il proxy di applicazione supporta la conversione dei collegamenti per includono:
* a
* Audio
* base
* .
* div
* embed
* Modulo
* frame
* head
* html
* iframe
* immagine
* input
* link
* MenuItem
* meta
* object
* script
* source
* track
* Video

Inoltre, all'interno di CSS viene convertito anche l'attributo URL.

### <a name="how-do-apps-link-to-each-other"></a>In che modo le app si collegano tra loro?

La conversione dei collegamenti è abilitata per ogni applicazione, perciò si ha il controllo dell'esperienza utente a livello di app. Attivare la conversione dei collegamenti per un'app quando si desidera che siano convertiti i collegamenti *da* tale app e non i collegamenti *a* tale app. 

Ad esempio, si supponga di avere tre applicazioni pubblicate mediante il proxy di applicazione e che tutte siano collegate tra loro: Benefits, Expenses e Travel. C'è una quarta app, Feedback, che non viene pubblicata tramite il proxy di applicazione.

Quando si abilita la conversione dei collegamenti per l'app Benefits, i collegamenti a Expenses e Travel sono reindirizzati agli URL esterni per tali app, ma il collegamento a Feedback non viene reindirizzato perché non esiste alcun URL esterno. I collegamenti da Expenses e Travel a Benefits non funzionano perché la conversione dei collegamenti non è stata abilitata per queste due app.

![Collegamenti da Benefits ad altre app quando è abilitata la conversione dei collegamenti](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quali collegamenti non vengono convertiti?

Per migliorare prestazioni e sicurezza, alcuni collegamenti non vengono convertiti:

- Collegamenti non interni ai tag di codice. 
- Collegamenti non inclusi nel codice HTML o CSS. 
- Collegamenti nel formato con codifica URL.
- Collegamenti interni aperti da altri programmi. I collegamenti inviati tramite posta elettronica o messaggistica istantanea o inclusi in altri documenti non vengono convertiti. Gli utenti devono sapere di passare all'URL esterno.

Se è necessario supportare uno di questi due scenari, usare gli stessi URL interni ed esterni, al posto della conversione dei collegamenti.  

## <a name="enable-link-translation"></a>Abilitare la conversione dei collegamenti

Per iniziare con la conversione dei collegamenti, è sufficiente fare clic su un pulsante:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare a **Azure Active Directory**  >  **applicazioni aziendali**  >  **tutte le applicazioni** > selezionare l'app che si vuole gestire > **proxy di applicazione**.
3. Impostare l'opzione di **conversione URL nel corpo dell'applicazione** su **Sì**.

   ![Selezionare Sì per convertire gli URL nel corpo dell'applicazione](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Selezionare **Salva** per applicare le modifiche.

Ora, quando gli utenti accedono a questa applicazione, il proxy esegue automaticamente un'analisi per individuare gli URL interni che sono stati pubblicati tramite il proxy di applicazione nel tenant.

## <a name="send-feedback"></a>Invia commenti

Abbiamo bisogno dell'intervento dell'utente per rendere questa funzionalità operante per tutte le app. La ricerca viene eseguita in oltre 30 tag del codice HTML e CSS. Se si hanno esempi di collegamenti generati che non vengono convertiti, inviare un frammento di codice all'area [commenti sul proxy di applicazione](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Passaggi successivi
[Usare i domini personalizzati con il proxy di applicazione Azure AD](application-proxy-configure-custom-domain.md) per avere gli stessi URL interni ed esterni

[Configurare i mapping di accesso alternativo per for SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
