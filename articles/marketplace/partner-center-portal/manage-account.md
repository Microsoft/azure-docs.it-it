---
title: Come gestire un account Marketplace commerciale nel centro per i partner Microsoft
description: Informazioni su come gestire un account Marketplace commerciale nel centro per i partner Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 54b45825f2322dc127d5db818b7abe52e4a98967
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208398"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Gestisci l'account del Marketplace commerciale nel centro per i partner

Dopo aver [creato un account del centro](./create-account.md)per i partner, è possibile usare il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) per gestire l'account e le offerte.

Questo articolo illustra come gestire l'account del centro per i partner, inclusa la procedura per:

- [Accedere alle impostazioni dell'account del centro per i partner](#access-your-account-settings)
- [Trovare il proprio ID editore, ID Symantec, ID venditore, ID utente, ID MPN e tenant Azure AD](#account-details)
- [Aggiornare le informazioni di contatto](#contact-info)
- [Configurare i GUID di rilevamento per il monitoraggio dell'utilizzo dei clienti](#tracking-guids)
- [Gestisci utenti](#manage-users)
- [Gestire i gruppi](#manage-groups)
- [Gestire applicazioni Azure AD](#manage-azure-ad-applications)
- [Definire ruoli utente e autorizzazioni](#define-user-roles-and-permissions)
- [Gestire i tenant di Azure AD (account di lavoro)](#manage-tenants)
- [Gestisci contratti con il centro per i partner](#agreements)

## <a name="access-your-account-settings"></a>Accedere alle impostazioni dell'account

Se non è ancora stato fatto, l'utente o l'amministratore dell'organizzazione deve accedere alle impostazioni dell' [account](https://partner.microsoft.com/dashboard/account/management) per l'account del centro per i partner:
- Verificare lo stato di verifica dell'account aziendale
- Confermare l'ID Symantec, l'ID venditore, l'ID Microsoft Partner Network (MPN), l'ID editore e le informazioni di contatto, inclusi il responsabile approvazione della società e il contatto del venditore
- Creare account utente per chiunque usi il proprio account aziendale nel centro per i partner

### <a name="open-developer-settings"></a>Apri impostazioni sviluppatore

**Le impostazioni dell'account** si trovano nell'angolo superiore destro del [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace) nel centro per i partner. Selezionare l'icona a forma di ingranaggio nell'angolo in alto a destra del dashboard e quindi selezionare **Impostazioni sviluppatore**.

![Menu delle impostazioni dell'account nel centro per i partner](./media/dashboard-developer-settings.png)

All'interno **delle impostazioni dell'account**, sarà possibile visualizzare:
- **Dettagli account**: tipo di account e stato dell'account
- ID **editore**: ID venditore, ID utente, ID editore, tenant Azure ad e così via.
- **Informazioni di contatto**: nome visualizzato dell'editore, nome contatto del venditore, indirizzo di posta elettronica, telefono e indirizzo
- **GUID di rilevamento**: qualsiasi GUID di rilevamento associato all'account

### <a name="account-details"></a>Dettagli account

Nella sezione dettagli account è possibile visualizzare le informazioni di base, ad esempio il **tipo di account** (società o utente singolo) e lo stato di **Verifica** dell'account. Durante il processo di verifica dell'account, queste impostazioni visualizzeranno ogni passaggio necessario, tra cui verifica della posta elettronica, verifica dell'utilizzo e verifica dell'attività. Se necessario, è anche possibile aggiornare la posta elettronica e inviare nuovamente la verifica.

### <a name="publisher-ids"></a>ID editore

Nella sezione ID editore è possibile visualizzare l' **ID Symantec**, l'ID **venditore**, l'ID **utente**, l' **ID MPN**e i **tenant Azure ad**. Questi valori vengono assegnati da Microsoft per identificare in modo univoco l'account sviluppatore e non possono essere modificati.

### <a name="contact-info"></a>Informazioni di contatto

Nella sezione informazioni di contatto è possibile visualizzare il **nome visualizzato dell'editore**, le **informazioni di contatto del venditore** (il nome del contatto, la posta elettronica, il numero di telefono e l'indirizzo del venditore della società) e il **responsabile approvazione della società** (nome, indirizzo di posta elettronica e numero di telefono dell'utente con autorità per approvare le decisioni per la società).

#### <a name="payout-account"></a>Account per i proventi

Un account di pagamento è il conto bancario a cui vengono inviati i proseguimenti dalle vendite. Questo account bancario deve trovarsi nello stesso paese in cui è stato registrato l'account del centro per i partner.

Per configurare l'account di pagamento:

1. Visitare la [pagina panoramica sul Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nel centro per i partner.
2. Nella sezione profilo, accanto a **profilo di pagamento**, selezionare **Aggiorna**.
3. **Scegliere un metodo di pagamento**: conto bancario o PayPal.
4. **Aggiungere le informazioni di pagamento**: è possibile scegliere un tipo di account (controllo o risparmio), immettere il nome del titolare dell'account, il numero di account, il numero di routing, l'indirizzo di fatturazione, il numero di telefono o l'indirizzo di posta elettronica PayPal. * Per altre informazioni sull'uso di PayPal come metodo di pagamento dell'account e per sapere se è supportato nell'area del mercato, vedere [informazioni su PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, proprio come quando si configura prima l'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.  

#### <a name="tax-profile"></a>Profilo fiscale

Esaminare lo stato corrente del profilo fiscale, verificare che vengano visualizzati il **tipo di entità** e le **informazioni sui certificati fiscali** corretti. Selezionare **modifica** per aggiornare o completare i moduli richiesti.

Per stabilire lo stato di imposta, è necessario specificare il paese di residenza e cittadinanza e completare i moduli fiscali appropriati associati al paese/area geografica.

Indipendentemente dal paese di residenza o cittadinanza, è necessario compilare Stati Uniti moduli fiscali per vendere eventuali offerte tramite Microsoft. I partner che soddisfano determinati requisiti di residenza di Stati Uniti devono compilare un modulo IRS W-9. Altri partner all'esterno del Stati Uniti devono compilare un modulo IRS W-8. Puoi compilare questi moduli online non appena completi il tuo profilo fiscale.

Non è necessario un codice ITIN (Individual Taxpayer Identification Number) degli Stati Uniti per ricevere pagamenti da Microsoft o richiedere agevolazioni fiscali.

È possibile completare e inviare i moduli fiscali elettronicamente nel centro per i partner; nella maggior parte dei casi non è necessario stampare e inviare moduli.

Paesi e regioni diversi presentano requisiti fiscali diversi. La quantità esatta di cui è necessario pagare le tasse dipende dai paesi e dalle aree in cui si vendono le offerte. Microsoft ha fatturato le vendite e usa le imposte per conto dell'utente in alcuni paesi. Questi paesi verranno identificati nel processo di visualizzazione dell'offerta. In altri paesi, a seconda della posizione in cui viene effettuata la registrazione, potrebbe essere necessario ricorrere alle vendite e utilizzare le imposte per le vendite direttamente all'autorità di tassazione locale. Inoltre, le vendite effettuate dall'utente possono essere imposte come reddito. Si consiglia vivamente di contattare l'autorità pertinente per il paese o l'area geografica che può aiutare a determinare le informazioni fiscali corrette per le transazioni di vendita Microsoft.

##### <a name="withholding-rates"></a>Tassi delle trattenute
Le informazioni che fornisci nei moduli fiscali determinano il tasso di trattenuta fiscale appropriato. Il tasso di trattenuta fiscale si applica solo alle vendite effettuate negli Stati Uniti. Le vendite effettuate in altri paesi non sono soggette a trattenuta. I tassi di trattenuta possono variare, ma per la maggior parte degli sviluppatori registrati al di fuori degli Stati Uniti il tasso predefinito è del 30%. Hai la possibilità di ridurre questo tasso se il tuo paese ha stipulato un accordo di agevolazioni fiscali sul reddito con gli Stati Uniti.

##### <a name="tax-treaty-benefits"></a>Agevolazioni fiscali
Se non risiedi negli Stati Uniti, potresti usufruire di agevolazioni fiscali Questi vantaggi variano a seconda del paese e possono consentire di ridurre la quantità di imposte ritenute da Microsoft. Puoi richiedere agevolazioni fiscali compilando la parte II del modulo W-8BEN. Consigliamo di consultare gli organi preposti nel tuo paese o nella tua area geografica per stabilire se hai diritto a tali agevolazioni.

[Altre informazioni sui dettagli delle imposte per gli sviluppatori di app e giochi di Windows e per gli editori di Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Stato dei proventi in attesa

Per impostazione predefinita, Microsoft invia i pagamenti su base mensile. Tuttavia, è possibile mettere in attesa i pagamenti, evitando così di inviare i pagamenti al proprio account. Se si sceglie di mettere in attesa i pagamenti, si continuerà a registrare i ricavi acquisiti e fornire i dettagli nel **Riepilogo dei pagamenti**. Tuttavia, non verrà inviato alcun pagamento all'account fino a quando non viene rimossa l'attesa. 

Per mettere i tuoi pagamenti in attesa, vai a **Impostazioni account**. In **Dettagli finanziari**, nella sezione **Stato dei proventi in attesa** imposta il dispositivo di scorrimento sullo stato **attivato**. Puoi modificare lo stato dei proventi in attesa in qualsiasi momento, ma tieni presente che la tua decisione avrà effetto sui prossimi proventi mensili. Se ad esempio si desidera mantenere il pagamento di aprile, assicurarsi di impostare lo stato di mantenimento dei pagamenti **su on** prima della fine di marzo.

Una volta che hai impostato lo stato dei proventi in attesa come **attivato**, tutti i proventi saranno in attesa finché non imposterai di nuovo il dispositivo di scorrimento sullo stato **disattivato**. Quando si esegue questa operazione, si verrà inclusi durante il successivo ciclo di pagamento mensile (purché siano state soddisfatte le soglie di pagamento applicabili). Se, ad esempio, sono stati **rilevati** i pagamenti in attesa, ma si vuole che venga generato un pagamento in giugno, assicurarsi di impostare lo stato di mantenimento dei pagamenti su OFF prima della fine di maggio.

> [!NOTE]
> La selezione **dello stato di mantenimento dei pagamenti** si applica a **tutte le** origini dei ricavi pagate tramite il centro per i partner Microsoft, tra cui Azure Marketplace, AppSource, Microsoft Store, pubblicità e così via. Non puoi selezionare stati di attesa diversi per ogni fonte di ricavi.

### <a name="devices"></a>Dispositivi

Le impostazioni di gestione del dispositivo si applicano solo alla pubblicazione UWP. [Altre informazioni](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>GUID di rilevamento

Gli identificatori univoci globali (GUID) sono numeri di riferimento univoci (con cifre esadecimali 32) che possono essere usati per tenere traccia dell'utilizzo di Azure. 

Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR3i8TQB_XnRAsV3-7XmQFpFUMVRVVFFLTDFLS0E2QzNYSkFZR1U3WVJCTSQlQCN0PWcu) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si distribuisce un prodotto usando un modello ed è disponibile sia in Azure Marketplace che in GitHub, è possibile creare e registrare due GUID distinti:

*    Prodotto A su Azure Marketplace
*    Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID. È anche possibile tenere traccia dei GUID a un livello più granulare allineando a ogni piano all'interno dell'offerta.

Per altre informazioni, vedere le [domande frequenti su come tenere traccia dell'utilizzo dei clienti di Azure con GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).

## <a name="create-a-billing-profile"></a>Creare un profilo di fatturazione

Se si pubblica un'offerta di [dynamics 365 per Customer Engagement & Power Apps](./create-new-customer-engagement-offer.md) o [Dynamics 365 for Operations](./create-new-operations-offer.md) , è necessario completare il **profilo di fatturazione**.

L'indirizzo di fatturazione è già popolato dall'entità legale ed è possibile aggiornarlo in un secondo momento. I campi TAX e VAT ID sono facoltativi.  Non è possibile modificare il nome del paese e il nome della società.

## <a name="multi-user-account-management"></a>Gestione degli account multiutente

Il centro per i partner utilizza [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure ad) per l'accesso e la gestione di account multiutente. Il Azure AD dell'organizzazione viene automaticamente associato all'account del centro per i partner come parte del processo di registrazione.

## <a name="manage-users"></a>Gestire gli utenti

La sezione **utenti** del centro per i partner (in **Impostazioni account**) consente di usare Azure ad per gestire gli utenti, i gruppi e le applicazioni Azure ad che hanno accesso all'account del centro per i partner. L'account deve disporre delle autorizzazioni a [**livello di gestione**](#define-user-roles-and-permissions) per l' [account aziendale (tenant Azure ad)](./company-work-accounts.md) in cui si desidera aggiungere o modificare gli utenti. Per gestire gli utenti all'interno di un account aziendale o tenant diverso, è necessario disconnettersi e quindi accedere di nuovo come utente con autorizzazioni di **gestione** per tale account di lavoro/tenant.

Dopo aver eseguito l'accesso con l'account aziendale (Azure AD tenant), è possibile:

- [Aggiungere o rimuovere utenti](#add-existing-users)
- [Modifica una password utente](#change-a-user-password)
- [Aggiungi o Rimuovi gruppi](#manage-groups)
- [Aggiunta o rimozione di applicazioni Azure AD](#add-new-azure-ad-applications)
- [Gestire le chiavi per un'applicazione Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definire ruoli utente e autorizzazioni](#define-user-roles-and-permissions)

Tenere presente che tutti gli utenti del centro per i partner (inclusi gruppi e applicazioni Azure AD) devono avere un account di lavoro attivo in un [tenant di Azure ad](#manage-tenants) associato all'account del centro per i partner.

### <a name="add-existing-users"></a>Aggiungi utenti esistenti

Per aggiungere utenti all'account del centro per i partner già esistenti nell'account aziendale dell'azienda [(Azure ad tenant)](./company-work-accounts.md):

1. Passare a **utenti** (in **Impostazioni account**) e selezionare **Aggiungi utenti**.
2. Seleziona uno o più utenti nell'elenco visualizzato. Puoi usare la casella di ricerca per cercare utenti specifici.
* Se si seleziona più di un utente da aggiungere all'account del centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più utenti con ruoli o autorizzazioni diverse, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Al termine della scelta degli utenti, fare clic su **Aggiungi selezionato**.
4. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per gli utenti selezionati.
5. Selezionare **Salva**.

### <a name="create-new-users"></a>Creazione di nuovi utenti

Per creare nuovi account utente, è necessario disporre di un account con autorizzazioni di [**amministratore globale**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . 

1. Passare a **utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **Crea nuovi utenti**.
1. Immettere il nome, il cognome e il nome utente per ogni nuovo utente. 
1. Se si desidera che il nuovo utente disponga di un account amministratore globale nella directory dell'organizzazione, selezionare la casella Assegna a **questo utente un amministratore globale nel Azure ad, con il controllo completo su tutte le risorse della directory**. In questo modo l'utente avrà accesso completo a tutte le funzionalità amministrative nella directory Azure AD della tua azienda. Potranno aggiungere e gestire gli utenti nell'account aziendale (Azure AD tenant) dell'organizzazione, anche se non nel centro per i partner, a meno che non si conceda all'account il ruolo o le autorizzazioni appropriate.
1. Se è stata selezionata la casella per **rendere l'utente un amministratore globale**, è necessario fornire un **messaggio di posta elettronica** per il ripristino della password per consentire all'utente di ripristinare la password, se necessario.
1. Nella sezione **Appartenenza al gruppo** seleziona i gruppi a cui vuoi che appartenga il nuovo utente.
1. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
1. Selezionare **Salva**.

La creazione di un nuovo utente nel centro per i partner creerà anche un account per tale utente nell'account aziendale (tenant Azure AD) a cui è stato effettuato l'accesso. Se si apportano modifiche al nome di un utente nel centro per i partner, le stesse modifiche vengono apportate all'account aziendale dell'organizzazione (Azure AD tenant).

### <a name="invite-new-users-by-email"></a>Invita nuovi utenti tramite posta elettronica

Per invitare gli utenti che non fanno parte dell'account aziendale aziendale (Azure AD tenant) tramite posta elettronica, è necessario disporre di un account con autorizzazioni di [**amministratore globale**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

1. Passare a **utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **invita utenti per posta elettronica**.
2. Immetti uno o più indirizzi e-mail (fino a dieci), separati da virgole o punti e virgola.
3. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
4. Selezionare **Salva**.

Gli utenti invitati riceveranno un invito tramite posta elettronica per aggiungere l'account del centro per i partner. Verrà creato un nuovo account utente guest nell'account aziendale (Azure AD tenant). Ogni utente dovrà accettare l'invito per poter accedere all'account.

Se è necessario inviare di nuovo un invito, visitare la pagina **utenti** , trovare l'invito nell'elenco degli utenti, selezionare l'indirizzo di posta elettronica o il testo che indica l' *invito in sospeso*. Quindi, nella parte inferiore della pagina, selezionare Invia di nuovo l' **invito**.

> [!NOTE]
> Se l'organizzazione usa l' [integrazione della directory](https://go.microsoft.com/fwlink/p/?LinkID=724033) per sincronizzare il servizio directory locale con la Azure ad, non sarà possibile creare nuovi utenti, gruppi o applicazioni Azure ad nel centro per i partner. È necessario che l'utente (o un altro amministratore nella directory locale) la crei direttamente nella directory locale prima di poterlo visualizzare e aggiungere al centro per i partner.

### <a name="remove-a-user"></a>Rimuovere un utente

Per rimuovere un utente dall'account aziendale (Azure AD tenant), passare a **utenti** (in **Impostazioni account**), selezionare l'utente che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui è possibile confermare che si desidera rimuovere gli utenti selezionati.

### <a name="change-a-user-password"></a>Modifica una password utente

Se è necessario modificare la password di uno degli utenti, è possibile farlo autonomamente se è stato fornito un **messaggio di posta elettronica** per il ripristino della password durante la creazione dell'account utente. Puoi anche aggiornare la password di un utente seguendo i passaggi seguenti. Per modificare la password di un utente nell'account aziendale (Azure AD tenant), è necessario eseguire l'accesso con un account con autorizzazioni di [**amministratore globale**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . Si noti che in questo modo verrà modificata la password dell'utente nel tenant di Azure AD, insieme alla password usata per accedere al centro per i partner.

1. Nella pagina **utenti** (in **Impostazioni account**) selezionare il nome dell'account utente che si desidera modificare.
2. Selezionare il pulsante **Reimposta password** nella parte inferiore della pagina.
3. Verrà visualizzata una pagina di conferma che mostra le informazioni di accesso per l'utente, inclusa una password temporanea. Assicurarsi di stampare o copiare queste informazioni e di fornirle all'utente, perché non sarà possibile accedere alla password temporanea dopo aver lasciato questa pagina.

## <a name="manage-groups"></a>Gestire i gruppi

I gruppi consentono di controllare contemporaneamente più ruoli utente e autorizzazioni.

### <a name="add-an-existing-group"></a>Aggiungi un gruppo esistente

Per aggiungere un gruppo già esistente nell'account di lavoro dell'organizzazione (tenant Azure AD) all'account del centro per i partner:

1. Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
2. Seleziona uno o più gruppi nell'elenco visualizzato. Puoi usare la casella di ricerca per cercare gruppi specifici.
Se si seleziona più di un gruppo da aggiungere all'account del centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più gruppi con ruoli/autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Al termine della scelta di gruppi, fare clic su **Aggiungi selezionato**.
4. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per il gruppo o i gruppi selezionati. Tutti i membri del gruppo saranno in grado di accedere all'account del centro per i partner con le autorizzazioni valide per il gruppo, indipendentemente dai ruoli e dalle autorizzazioni associate al proprio account.
5. Selezionare **Salva**.

Quando si aggiunge un gruppo esistente, ogni utente membro di tale gruppo sarà in grado di accedere all'account del centro per i partner, con le autorizzazioni associate al ruolo assegnato del gruppo.

### <a name="add-a-new-group"></a>Aggiungere un nuovo gruppo

Per aggiungere un nuovo gruppo all'account del centro per i partner:

1. Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
2. Nella pagina successiva selezionare **nuovo gruppo**.
3. Immetti il nome visualizzato per il nuovo gruppo.
4. Specificare i ruoli o le autorizzazioni personalizzate per il gruppo. Tutti i membri del gruppo saranno in grado di accedere all'account del centro per i partner con le autorizzazioni applicate, indipendentemente dai ruoli o dalle autorizzazioni associate al rispettivo account.
5. Selezionare gli utenti per il nuovo gruppo nell'elenco visualizzato. Puoi usare la casella di ricerca per cercare utenti specifici.
6. Al termine della selezione degli utenti, fare clic su **Aggiungi selezionati** per aggiungerli al nuovo gruppo.
7. Selezionare **Salva**.

Si noti che questo nuovo gruppo verrà creato anche nell'account di lavoro dell'organizzazione (tenant Azure AD), non solo nell'account del centro per i partner.

### <a name="remove-a-group"></a>Rimuovere un gruppo

Per rimuovere un gruppo dall'account aziendale (Azure AD tenant), passare a **utenti** (in **Impostazioni account**), selezionare il gruppo che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui è possibile confermare che si desidera rimuovere i gruppi selezionati.

## <a name="manage-azure-ad-applications"></a>Gestire applicazioni Azure AD

È possibile consentire alle applicazioni o ai servizi che fanno parte del Azure AD dell'azienda di accedere all'account del centro per i partner.

### <a name="add-existing-azure-ad-applications"></a>Aggiungere applicazioni Azure AD esistenti

Per aggiungere applicazioni già presenti nel Azure Active Directory della società:

1. Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi Azure ad applicazioni**.
2. Seleziona una o più applicazioni Azure AD nell'elenco visualizzato. Puoi usare la casella di ricerca per cercare applicazioni Azure AD specifiche. Se si seleziona più di un'applicazione Azure AD da aggiungere all'account del centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più applicazioni Azure AD con ruoli/autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Dopo aver selezionato le applicazioni Azure AD, fai clic su **Aggiungi selezione**.
4. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per le applicazioni Azure ad selezionate.
5. Selezionare **Salva**.

### <a name="add-new-azure-ad-applications"></a>Aggiungi nuove applicazioni Azure AD

Se si desidera concedere l'accesso al centro per i partner a un nuovo account dell'applicazione Azure AD, è possibile crearne uno nella sezione **utenti** . Si noti che in questo modo verrà creato un nuovo account nell'account aziendale aziendale (tenant Azure AD), non solo nell'account del centro per i partner. Se si usa principalmente questa applicazione Azure AD per l'autenticazione del centro per i partner e non è necessario che gli utenti accedano direttamente al sito, è possibile immettere qualsiasi indirizzo valido per l' **URL di risposta** e l' **URI ID app**, purché tali valori non vengano usati da nessun'altra applicazione Azure ad nella directory.

1. Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi Azure ad applicazioni**.
2. Nella pagina successiva selezionare **nuovo Azure ad applicazione**.
3. Immetti l’indirizzo **URL di risposta** per la nuova applicazione Azure AD. Questo è l'URL da cui gli utenti possono eseguire l'accesso e usare la tua applicazione Azure AD (noto anche come URL dell'app o URL di accesso). L' **URL di risposta** non può contenere più di 256 caratteri e deve essere univoco nella directory.
4. Immetti l’**URI ID app** per la nuova applicazione Azure AD. Si tratta di un identificatore logico per l'applicazione Azure AD presentata quando viene inviata una richiesta di Single Sign-On a Azure AD. Si noti che l' **URI ID app** deve essere univoco per ogni Azure ad applicazione nella directory. Questo ID non può contenere più di 256 caratteri. Per altre informazioni sull'URI ID app, vedere [integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'applicazione Azure ad.
6. Selezionare **Salva**.

Dopo aver aggiunto o creato un'applicazione Azure AD, è possibile tornare alla sezione **utenti** e selezionare il nome dell'applicazione per rivedere le impostazioni per l'applicazione, inclusi ID tenant, ID client, URL di risposta e URI ID app.

### <a name="remove-an-azure-ad-application"></a>Rimuovere un'applicazione Azure AD

Per rimuovere un'applicazione dall'account aziendale (Azure AD tenant), passare a **utenti** (in **Impostazioni account**), selezionare l'applicazione che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui è possibile confermare che si desidera rimuovere le applicazioni selezionate.

### <a name="manage-keys-for-an-azure-ad-application"></a>Gestire le chiavi per un'applicazione Azure AD

Se la tua applicazione Azure AD legge e scrive dati in Microsoft Azure AD, servirà una chiave. È possibile creare chiavi per un'applicazione Azure AD modificando le relative informazioni nel centro per i partner. Puoi anche rimuovere le chiavi non più necessarie.

1. Nella pagina **utenti** (in **Impostazioni account**) selezionare il nome dell'applicazione Azure ad. Verranno visualizzate tutte le chiavi attive per l'applicazione Azure AD, inclusa la data di creazione e di scadenza della chiave. 
2. Per rimuovere una chiave non più necessaria, selezionare **Rimuovi**.
3. Per aggiungere una nuova chiave, selezionare **Aggiungi nuova chiave**.
4. Viene visualizzata una schermata che mostra l' **ID client** e i **valori della chiave**. Assicurarsi di stampare o copiare queste informazioni poiché non sarà possibile accedervi di nuovo dopo aver lasciato questa pagina.
5. Se si desidera creare più chiavi, selezionare **Aggiungi un'altra chiave**.

## <a name="define-user-roles-and-permissions"></a>Definire ruoli utente e autorizzazioni

Agli utenti della società è possibile assegnare i seguenti ruoli e autorizzazioni per il programma Commercial Marketplace nel centro per i partner:

- **Manager**
  - Può accedere a tutte le funzionalità di account Microsoft ad eccezione delle impostazioni fiscali e di pagamento
  - Può gestire utenti, ruoli e account di lavoro (tenant)
- **Sviluppatore**
  - Consente di gestire e pubblicare le offerte
  - Consente di visualizzare alcuni report del server di pubblicazione

> [!NOTE]
> Per il programma Commercial Marketplace, i ruoli amministratore globale, collaboratore aziendale, collaboratore finanziario e marketer non vengono usati. L'assegnazione di questi ruoli agli utenti non ha alcun effetto. Solo i ruoli Manager e Developer concedono le autorizzazioni agli utenti.

Per ulteriori informazioni sulla gestione dei ruoli e delle autorizzazioni in altre aree del centro per i partner, ad esempio Azure Active Directory (AD), Cloud Solution Provider (CSP), il produttore del pannello di controllo (CPV), gli utenti guest o Microsoft Partner Network (MPN), vedere [assegnare utenti ruoli e autorizzazioni nel centro per i partner](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Gestisci tenant

Un tenant di Azure Active Directory (AD), detto anche "account di lavoro" in questa documentazione, è una rappresentazione dell'organizzazione configurata nel portale di Azure e consente di gestire un'istanza specifica dei servizi cloud Microsoft per gli utenti interni ed esterni. Se l'organizzazione ha sottoscritto un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365, è stato stabilito un tenant di Azure AD.

È possibile configurare più tenant da usare con il centro per i partner. Qualsiasi utente con il ruolo di **responsabile** nell'account del centro per i partner avrà la possibilità di aggiungere e rimuovere Azure ad tenant dall'account.  

### <a name="add-an-existing-tenant"></a>Aggiungi un tenant esistente

Per associare un altro tenant Azure AD all'account del centro per i partner:

1. Nella pagina **tenant** (in **Impostazioni account**) selezionare **associa un altro Azure ad tenant**.
2. Immettere le credenziali di Azure AD per il tenant che si vuole associare.
3. Esaminare l'organizzazione e il nome di dominio per il tenant del Azure AD. Per completare l'associazione, selezionare **conferma**.

Se l'associazione ha esito positivo, sarà possibile aggiungere e gestire gli utenti degli account nella sezione **utenti** del centro per i partner.

### <a name="create-a-new-tenant"></a>Creare un nuovo tenant

Per creare un nuovo tenant di Azure AD con l'account del centro per i partner:

1. Nella pagina **tenant** (in **Impostazioni account**) selezionare **Crea un nuovo tenant Azure ad**.
2. Immetti le informazioni sulla directory per la nuova istanza di Azure AD:
    - **Nome di dominio**: nome univoco che verrà usato per il dominio Azure ad, insieme a ". onmicrosoft.com". Ad esempio, se è stato immesso "example", il dominio Azure AD sarà "example.onmicrosoft.com".
    - **Indirizzo di posta elettronica**: indirizzo di posta elettronica a cui possiamo contattarti per questioni relative all'account se necessario.
    - **Informazioni sull'account utente dell'amministratore globale**: il nome, il cognome, il nome utente e la password che si desidera utilizzare per il nuovo account amministratore globale.
3. Selezionare **Crea** per confermare il nuovo dominio e le informazioni sull'account.
4. Accedere con il nuovo nome utente e la password dell'amministratore globale di Azure AD per iniziare ad [aggiungere e gestire gli utenti](#manage-users).

Per ulteriori informazioni sulla creazione di nuovi tenant all'interno del portale di Azure, anziché tramite il portale del centro per i partner, vedere l'articolo [creare un nuovo tenant in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Rimuovere un tenant

Per rimuovere un tenant dall'account del centro per i partner, trovarne il nome nella pagina **tenant** (in **Impostazioni account**), quindi selezionare **Rimuovi**. Verrà richiesto di confermare che si vuole rimuovere il tenant. Una volta eseguita questa operazione, nessun utente in tale tenant potrà accedere all'account del centro per i partner e tutte le autorizzazioni configurate per tali utenti verranno rimosse.

Quando si rimuove un tenant, tutti gli utenti aggiunti all'account del centro per i partner da tale tenant non saranno più in grado di accedere all'account.

> [!TIP]
> Non è possibile rimuovere un tenant se è stato eseguito l'accesso al centro per i partner usando un account nello stesso tenant. Per rimuovere un tenant, è necessario accedere al centro per i partner come **responsabile** di un altro tenant associato all'account. Se è presente un solo tenant associato all'account, il tenant può essere rimosso solo dopo aver eseguito l'accesso con la account Microsoft che ha aperto l'account.

## <a name="agreements"></a>Contratti

La sezione **contratti** di centro per i partner (in **Impostazioni account**) consente di visualizzare un elenco dei contratti di pubblicazione autorizzati. Questi contratti sono elencati in base al nome e al numero di versione, inclusa la data di accettazione e il nome dell'utente che ha accettato l'accordo.

Le **azioni necessarie** possono essere visualizzate nella parte superiore della pagina se sono presenti aggiornamenti del contratto che richiedono l'attenzione dell'utente. Per accettare un contratto aggiornato, leggere prima la versione del contratto collegato, quindi selezionare **accetta accordo**.
