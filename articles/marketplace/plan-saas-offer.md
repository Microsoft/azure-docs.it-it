---
title: Come pianificare un'offerta SaaS per Microsoft Commercial Marketplace
description: Come pianificare una nuova offerta SaaS (Software as a Service) per l'inserzione o la vendita in Microsoft AppSource, Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) usando il programma Commercial Marketplace nel centro per i partner Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 6f08fa0b2126112fa17fd61be6f44bb5cc6d5396
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552156"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Come pianificare un'offerta SaaS per il Marketplace commerciale

Questo articolo illustra le diverse opzioni e i requisiti per la pubblicazione di offerte Software as a Service (SaaS) nel Marketplace commerciale Microsoft. SaaS offre la fornitura di soluzioni software e licenze per i clienti tramite sottoscrizioni online. Il server di pubblicazione SaaS gestisce e paga l'infrastruttura necessaria per supportare l'utilizzo dell'offerta da parte dei clienti. Questo articolo consente di preparare l'offerta per la pubblicazione nel Marketplace commerciale con il centro per i partner.

## <a name="listing-options"></a>Opzioni di presentazione

Quando si prepara la pubblicazione di una nuova offerta SaaS, è necessario decidere quale opzione di _elenco_ scegliere. L'opzione di elenco scelta determina quali informazioni aggiuntive è necessario fornire durante la creazione dell'offerta nel centro per i partner. Si definirà l'opzione di inserzione nella pagina  **installazione offerta** , come illustrato in [come creare un'offerta SaaS nel Marketplace commerciale](create-new-saas-offer.md).

La tabella seguente mostra le opzioni di elenco per le offerte SaaS nel Marketplace commerciale.

| Elenco di opzioni | Processo di transazione |
| ------------ | ------------- |
| Contact me (Contattami) | Il cliente contatta direttamente le informazioni dell'inserzione.``*`` |
| Versione di prova gratuita | Il cliente viene reindirizzato all'URL di destinazione tramite Azure Active Directory (Azure AD).``*`` |
| Ottieni ora (gratuito) | Il cliente viene reindirizzato all'URL di destinazione tramite Azure AD.``*`` |
| Vendita tramite Microsoft  | Le offerte vendute tramite Microsoft sono denominate offerte _transazionali_ . Un'offerta transazionale è una delle quali Microsoft semplifica lo scambio di denaro per una licenza software per conto dell'editore. Le offerte SaaS vengono fatturate usando il modello di determinazione dei prezzi scelto e gestite le transazioni dei clienti per conto dell'utente. Le tariffe per l'utilizzo dell'infrastruttura di Azure vengono addebitate direttamente all'utente, al partner. È necessario tenere conto dei costi dell'infrastruttura nel modello di determinazione dei prezzi. Questa procedura è illustrata più dettagliatamente nella [fatturazione Saas](#saas-billing) riportata di seguito.  |
|||

``*`` Gli editori sono responsabili del supporto di tutti gli aspetti della transazione di licenza software, tra cui l'ordine, l'evasione, la misurazione, la fatturazione, la fatturazione, il pagamento e la raccolta.

Per ulteriori informazioni su queste opzioni di elenco, vedere la pagina relativa alle [funzionalità di transacting del Marketplace commerciale](marketplace-commercial-transaction-capabilities-and-considerations.md).

Dopo la pubblicazione dell'offerta, l'opzione relativa all'elenco scelto per l'offerta viene visualizzata come pulsante nell'angolo in alto a sinistra della pagina di presentazione dell'offerta. Ad esempio, la schermata seguente mostra una pagina di presentazione dell'offerta in Azure Marketplace con il pulsante **Get it Now (Ottieni ora** ).

![Viene illustrato un elenco di offerte nel negozio online.](./media/saas/listing-options-saas.png)

## <a name="technical-requirements"></a>Requisiti tecnici

I requisiti tecnici variano in base all'opzione di inserzione scelta per l'offerta.

L'opzione _Contact me_ Listing non ha requisiti tecnici. È possibile connettere un sistema CRM (Customer Relationship Management) per gestire i lead dei clienti. Questa operazione è descritta nella sezione [Customer Leads](#customer-leads) , più avanti in questo articolo.

Le opzioni per _ottenere ora (gratuita)_, _versione di valutazione gratuita_ e _vendita tramite Microsoft_ elencano i requisiti tecnici seguenti:

- L'applicazione SaaS deve essere una soluzione multi-tenant.
- È possibile abilitare sia gli account Microsoft (MSA) sia [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione degli utenti.
- È necessario creare una pagina di destinazione. Dopo che l'utente ha acquistato l'offerta, viene indirizzata alla pagina di destinazione. In questo modo, è possibile completare il provisioning o la configurazione aggiuntiva necessaria. Per istruzioni sulla creazione della pagina di destinazione, vedere i seguenti articoli:
  - [Creazione della pagina di destinazione per l'offerta SaaS transazionale nel Marketplace commerciale](azure-ad-transactable-saas-landing-page.md)
  - [Crea la pagina di destinazione per l'offerta SaaS gratuita o di valutazione nel Marketplace commerciale](azure-ad-free-or-trial-landing-page.md)

Questi requisiti tecnici aggiuntivi si applicano solo all'opzione di inserzione _sell through Microsoft_ (transazionale):

- Per l'acquisto dell'utente che accede alla pagina di destinazione, è necessario Azure AD con la gestione delle identità di Single Sign-On (SSO) e l'autenticazione. Per istruzioni dettagliate, vedere [Azure ad e le offerte SaaS transazionali nel Marketplace commerciale](azure-ad-saas.md).
- È necessario usare le [API di evasione Saas](./partner-center-portal/pc-saas-fulfillment-api-v2.md) per l'integrazione con Azure Marketplace e Microsoft AppSource. È necessario esporre un servizio che può interagire con la sottoscrizione SaaS per creare, aggiornare ed eliminare un account utente e un piano di servizio. Le modifiche critiche all'API devono essere supportate entro 24 ore. Le modifiche non critiche all'API verranno rilasciate periodicamente. I diagrammi e le spiegazioni dettagliate che descrivono l'utilizzo dei campi raccolti sono disponibili nella documentazione relativa alle [API](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- È necessario creare almeno un piano per l'offerta. Il piano viene valutato in base al modello di determinazione dei prezzi selezionato prima della pubblicazione: _Tariffa_ fissa o _per utente_. Altre informazioni sui [piani](#plans) sono disponibili più avanti in questo articolo.
- Il cliente può annullare l'offerta in qualsiasi momento.

### <a name="technical-information"></a>Informazioni tecniche

Se si sta creando un'offerta transazionale, è necessario raccogliere le informazioni seguenti per la pagina di **configurazione tecnica** . Se si sceglie di elaborare le transazioni in modo indipendente anziché creare un'offerta transazionale, ignorare questa sezione e passare a [test drive](#test-drives).

- **URL della pagina di destinazione**: URL del sito SaaS (ad esempio, `https://contoso.com/signup` ) a cui gli utenti verranno indirizzati dopo aver acquisito l'offerta dal Marketplace commerciale, avviando il processo di configurazione dalla sottoscrizione Saas appena creata. Questo URL riceverà un token che può essere usato per chiamare le API di evasione per ottenere i dettagli del provisioning per la pagina di registrazione interattiva.

  Questo URL viene chiamato con il parametro del token di identificazione dell'acquisto del Marketplace che identifica in modo univoco l'acquisto SaaS del cliente specifico. È necessario scambiare questo token per i dettagli della sottoscrizione SaaS corrispondenti usando l' [API Resolve](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Questi dettagli e altri utenti che si desidera raccogliere come parte di una pagina Web interattiva del cliente possono essere utilizzati per avviare l'esperienza di onboarding dei clienti, che deve terminare con una chiamata di attivazione sull'API per l'avvio del periodo di sottoscrizione. In questa pagina, l'utente deve eseguire l'iscrizione con l'autenticazione con un clic usando Azure Active Directory (Azure AD).

  Questo URL con il parametro del token di identificazione del Marketplace verrà chiamato anche quando il cliente avvierà un'esperienza SaaS gestita dall'interfaccia di amministrazione di portale di Azure o Microsoft 365. È necessario gestire entrambi i flussi: quando il token viene fornito per la prima volta dopo un nuovo acquisto da parte del cliente e quando viene fornito nuovamente per un cliente esistente che gestisce la soluzione SaaS.

    La pagina di destinazione configurata deve essere in esecuzione 24/7. Questo è l'unico modo per ricevere notifiche sui nuovi acquisti delle offerte SaaS effettuate nel Marketplace commerciale o sulle richieste di configurazione per una sottoscrizione attiva di un'offerta.

- **Webhook di connessione**: per tutti gli eventi asincroni che Microsoft deve inviare all'utente (ad esempio, quando una sottoscrizione Saas è stata annullata), è necessario fornire un URL del webhook di connessione. Questo URL verrà chiamato per notificare l'evento.

  Il webhook fornito dovrebbe essere attivo e in esecuzione 24/7. Questo è l'unico modo per ricevere una notifica sugli aggiornamenti delle sottoscrizioni SaaS dei clienti acquistate tramite il Marketplace commerciale.

  > [!NOTE]
  > All'interno del portale di Azure è necessario creare una [registrazione di app Azure Active Directory (Azure ad)](../active-directory/develop/howto-create-service-principal-portal.md)a tenant singolo. Usare i dettagli di registrazione dell'app per autenticare la soluzione quando si chiamano le API del Marketplace. Per trovare l' [ID tenant](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), passare alla Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero ID directory elencato. Ad esempio: `50c464d3-4930-494c-963c-1e951d15360e`.

- **ID tenant Azure Active Directory**: (noto anche come ID directory). All'interno del portale di Azure è necessario [registrare un'app Azure Active Directory (ad)](../active-directory/develop/howto-create-service-principal-portal.md) in modo che sia possibile aggiungerla all'elenco di controllo di accesso (ACL) dell'API per assicurarsi di essere autorizzati a chiamarlo. Per trovare l'ID tenant per l'app Azure Active Directory (AD), passare al pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app. Cercare quindi il numero di **ID della directory (tenant)** elencato (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ).

- **ID applicazione Azure Active Directory**: è necessario anche l' [ID applicazione](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Per ottenere il valore, passare al pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app. Cercare quindi il numero ID dell'applicazione (client) elencato (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ).

  Il Azure AD ID applicazione è associato all'ID editore nell'account del centro per i partner. È necessario utilizzare lo stesso ID applicazione per tutte le offerte in tale account.

  > [!NOTE]
  > Se il server di pubblicazione dispone di due o più account diversi nel centro per i partner, è possibile usare i dettagli di registrazione dell'app Azure AD in un solo account. Con lo stesso ID tenant, la coppia ID app per un'offerta con un account di Publisher diverso non è supportata.

## <a name="test-drives"></a>Test drive
È possibile scegliere di abilitare un test drive per l'app SaaS. Le unità di test consentono ai clienti di accedere a un ambiente preconfigurato per un numero fisso di ore. È possibile abilitare le unità di test per qualsiasi opzione di pubblicazione, tuttavia questa funzionalità presenta requisiti aggiuntivi. Per ulteriori informazioni sulle unità di test, vedere [che cos'è un test drive?](what-is-test-drive.md). Per informazioni sulla configurazione di diversi tipi di unità di test, vedere [configurazione tecnica di test drive](test-drive-technical-configuration.md).

> [!TIP]
> Una test drive è diversa da una [versione di valutazione gratuita](plans-pricing.md#free-trials). È possibile offrire un test drive, una versione di valutazione gratuita o entrambi. Entrambi forniscono ai clienti la soluzione per un periodo di tempo fisso. Tuttavia, un test drive include anche una presentazione pratica e autonoma delle funzionalità chiave del prodotto e dei vantaggi illustrati in uno scenario di implementazione reale.

## <a name="customer-leads"></a>Clienti potenziali

Per raccogliere informazioni sui clienti, è necessario connettere l'offerta al sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e al negozio online in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Il Marketplace commerciale supporta un'ampia gamma di sistemi CRM, oltre alla possibilità di usare una tabella di Azure o di configurare un endpoint HTTPS usando Power automatici.

È possibile aggiungere o modificare una connessione CRM in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Selezione di uno Store online

Quando si pubblica un'offerta SaaS, questa viene elencata in Microsoft AppSource, in Azure Marketplace o in entrambi. Ogni negozio online serve requisiti univoci per i clienti. AppSource è destinata alle soluzioni aziendali e Azure Marketplace è per le soluzioni IT. Il tipo di offerta, le funzionalità di transazione e le categorie determineranno la posizione in cui verrà pubblicata l'offerta. Le categorie e le sottocategorie sono mappate a ogni negozio online in base al tipo di soluzione. 

Se l'offerta SaaS è *sia* una soluzione it (Azure Marketplace) che una soluzione aziendale (AppSource), selezionare una categoria e una sottocategoria applicabile a ogni negozio online. Le offerte pubblicate in entrambi gli archivi online devono avere una proposta di valore come soluzione IT *e* una soluzione aziendale.

> [!IMPORTANT]
> Le offerte SaaS con [fatturazione a consumo](partner-center-portal/saas-metered-billing.md) sono disponibili tramite Azure Marketplace e il portale di Azure. Le offerte SaaS con solo piani privati sono disponibili tramite il portale di Azure.

| Fatturazione a consumo | Piano pubblico | Piano privato | Disponibile in: |
|---|---|---|---|
| Sì             | Sì         | No           | Azure Marketplace e portale di Azure |
| Sì             | Sì         | Sì          | Azure Marketplace e portale di Azure * |
| Sì             | No          | Sì          | Solo portale di Azure |
| No              | No          | Sì          | Solo portale di Azure |
|||||

&#42; il piano privato dell'offerta sarà disponibile solo tramite il portale di Azure

Ad esempio, un'offerta con fatturazione a consumo e solo un piano privato (nessun piano pubblico) viene acquistata dai clienti nel portale di Azure. Scopri di più sulle [offerte private in Microsoft Commercial Marketplace](private-offers.md).

Per informazioni dettagliate sulle opzioni di elenco supportate dagli archivi online, vedere [Listing and pricing Options by Online Store](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Per ulteriori informazioni sulle categorie e le sottocategorie, vedere [categorie e sottocategorie nel Marketplace commerciale](categories.md).

## <a name="legal-contracts"></a>Contratti legali

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard che è possibile usare per le offerte nel Marketplace commerciale. Quando si offre il software nel contratto standard, i clienti devono leggere e accettare una sola volta e non è necessario creare termini e condizioni personalizzati.

Se si sceglie di utilizzare il contratto standard, si ha la possibilità di aggiungere termini di modifica universale e fino a 10 modifiche personalizzate al contratto standard. È anche possibile usare i propri termini e condizioni anziché il contratto standard. Questi dettagli vengono gestiti nella pagina delle **Proprietà** . Per informazioni dettagliate, vedere [contratto standard per Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il Marketplace commerciale, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard o a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.


## <a name="microsoft-365-integration"></a>Integrazione di Microsoft 365

L'integrazione con Microsoft 365 consente all'offerta SaaS di offrire un'esperienza connessa tra più aree di Microsoft 365 app tramite componenti aggiuntivi gratuiti correlati come app team, componenti aggiuntivi per Office e soluzioni di SharePoint Framework. È possibile aiutare i clienti a individuare facilmente tutti i facet della soluzione E2E (servizio Web + componenti aggiuntivi correlati) e distribuirli in un unico processo fornendo le informazioni seguenti. 
  - Se l'offerta SaaS si integra con Microsoft Graph, fornire l'ID app Azure Active Directory (AAD) usato dall'offerta SaaS per l'integrazione. Gli amministratori possono verificare le autorizzazioni di accesso necessarie per il corretto funzionamento dell'offerta SaaS, come impostato nell'ID app di AAD e concedere l'accesso se è necessaria l'autorizzazione di amministratore avanzato in fase di distribuzione. 
    
     Se si sceglie di vendere l'offerta tramite Microsoft, si tratta dello stesso ID app AAD che è stato registrato per l'uso nella pagina di destinazione per ottenere le informazioni di base sull'utente necessarie per completare l'attivazione della sottoscrizione dei clienti. Per istruzioni dettagliate, vedere la [pagina relativa alla creazione della pagina di destinazione per l'offerta SaaS transazionale nel Marketplace commerciale](azure-ad-transactable-saas-landing-page.md). 
    
   -    Fornire un elenco di componenti aggiuntivi correlati che funzionano con l'offerta SaaS che si vuole collegare. I clienti potranno individuare la tua soluzione E2E in AppSource e gli amministratori possono distribuire sia il sistema SaaS che tutti i componenti aggiuntivi correlati collegati nello stesso processo tramite Microsoft 365 interfaccia di amministrazione.
    
        Per collegare i componenti aggiuntivi correlati, è necessario fornire il collegamento AppSource del componente aggiuntivo. questo significa che il componente aggiuntivo deve essere pubblicato per la prima volta in AppSource. I tipi di componenti aggiuntivi supportati che è possibile collegare sono: app teams, componenti aggiuntivi per Office e soluzioni di SharePoint Framework (SPFx). Ogni componente aggiuntivo collegato deve essere univoco per un'offerta SaaS. 

Per i prodotti collegati, la ricerca in AppSource restituirà un risultato che include sia SaaS che tutti i componenti aggiuntivi collegati. Il cliente può spostarsi tra le pagine dei dettagli del prodotto dell'offerta SaaS e i componenti aggiuntivi collegati. Gli amministratori IT possono esaminare e distribuire i componenti aggiuntivi SaaS e collegati all'interno dello stesso processo tramite un'esperienza integrata e connessa all'interno dell'interfaccia di amministrazione di Microsoft 365. Per altre informazioni, vedere [testare e distribuire app Microsoft 365-Microsoft 365 amministratore](/microsoft-365/admin/manage/test-and-deploy-microsoft-365-apps).

### <a name="microsoft-365-integration-support-limitations"></a>Limitazioni del supporto per l'integrazione Microsoft 365
L'individuazione come una singola soluzione E2E è supportata in AppSource per tutti i casi, tuttavia, la distribuzione semplificata della soluzione E2E come descritto in precedenza tramite l'interfaccia di amministrazione di Microsoft 365 non è supportata per gli scenari seguenti:

   - Lo stesso componente aggiuntivo è collegato a più di un'offerta SaaS.
   - L'offerta SaaS è collegata ai componenti aggiuntivi, ma non si integra con Microsoft Graph e non viene fornito alcun ID app AAD.
  - L'offerta SaaS è collegata ai componenti aggiuntivi, ma l'ID app AAD fornito per l'integrazione Microsoft Graph è condiviso tra più offerte SaaS.

 
## <a name="offer-listing-details"></a>Dettagli elenco offerte

Quando si [Crea una nuova offerta SaaS](create-new-saas-offer.md) nel centro per i partner, si immetteranno testo, immagini, video facoltativi e altri dettagli nella pagina di presentazione dell' **offerta** . Si tratta delle informazioni che i clienti visualizzeranno quando scoprono l'inserzione dell'offerta nel Marketplace commerciale, come illustrato nell'esempio seguente.

:::image type="content" source="./media/saas/example-saas-1.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

**Descrizioni di chiamata**

1. Logo
2. Categorie
3. Settori
4. Indirizzo di supporto (collegamento)
5. Condizioni per l'utilizzo
6. Informativa sulla privacy
7. Nome offerta
8. Riepilogo
9. Descrizione
10. Screenshot/video
11. Documenti

Nell'esempio seguente viene illustrato un elenco di offerte nel portale di Azure.

![Viene illustrato un elenco di offerte nel portale di Azure.](./media/example-managed-service-azure-portal.png)

**Descrizioni delle chiamate**

1. Titolo
1. Descrizione
1. Collegamenti utili
1. Schermate

> [!NOTE]
> Se la descrizione dell'offerta inizia con la frase "questa applicazione è disponibile solo in [lingua non inglese]", non è necessario che il contenuto dell'elenco di offerte sia in inglese.

Per semplificare la creazione dell'offerta, è possibile preparare alcuni di questi elementi in anticipo. Se non diversamente specificato, sono necessari gli elementi seguenti.

- **Nome**: questo nome verrà visualizzato come titolo dell'inserzione dell'offerta nel Marketplace commerciale. Il nome può essere un marchio registrato. Non può contenere emoji (ad eccezione dei simboli di marchio e copyright) e deve essere limitato a 50 caratteri.
- **Riepilogo risultati ricerca**: descrivere lo scopo o la funzione dell'offerta come una singola frase senza interruzioni di riga in caratteri di 100 o meno. Questo riepilogo viene usato nei risultati della ricerca nell'elenco dei Marketplace commerciali.
- **Descrizione**: questa descrizione verrà visualizzata nella panoramica degli elenchi di Marketplace commerciali. Si consiglia di includere una proposta di valore, i vantaggi principali, la base utente prevista, le associazioni di categoria o di settore, le opportunità di acquisto in-app, le divulgazioni richieste e un collegamento per ottenere ulteriori informazioni.

    Questa casella di testo contiene controlli avanzati dell'editor di testo che è possibile usare per rendere più accattivante la descrizione. È anche possibile usare i tag HTML per formattare la descrizione. È possibile immettere fino a 3.000 caratteri di testo in questa casella, incluso il markup HTML. Per altri suggerimenti, vedere [Scrivere un'ottima descrizione dell'app](/windows/uwp/publish/write-a-great-app-description).

- **Introduzione istruzioni**: se si sceglie di vendere l'offerta tramite Microsoft (offerta transazionale), questo campo è obbligatorio. Queste istruzioni consentono ai clienti di connettersi all'offerta SaaS. È possibile aggiungere fino a 3.000 caratteri di testo e collegamenti a documentazione online più dettagliata.
- **Parole chiave di ricerca** (facoltativo): fornire fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta negli archivi online. Non è necessario includere il **nome** e la **Descrizione** dell'offerta: il testo viene automaticamente incluso nella ricerca.
- **Collegamento all'informativa sulla privacy**: l'URL per l'informativa sulla privacy dell'azienda. È necessario fornire un'informativa sulla privacy valida e avere la responsabilità di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy.
- **Informazioni di contatto**: è necessario fornire i contatti seguenti dell'organizzazione:
  - **Contatto del supporto tecnico**: fornire il nome, il telefono e il messaggio di posta elettronica per i partner Microsoft da usare quando i clienti aprono i biglietti. È inoltre necessario includere l'URL per il sito Web di supporto.
  - **Contatto tecnico**: fornire il nome, il telefono e il messaggio di posta elettronica che Microsoft deve usare direttamente quando si verificano problemi con l'offerta. Le informazioni di contatto non sono elencate nel Marketplace commerciale.
  - **Contatto del programma CSP** (facoltativo): specificare il nome, il telefono e il messaggio di posta elettronica se si opta per il programma CSP, in modo che i partner possano contattare l'utente per qualsiasi domanda. È anche possibile includere un URL per i materiali di marketing.
- **Collegamenti utili** (facoltativo): è possibile fornire collegamenti a diverse risorse per gli utenti dell'offerta. Ad esempio forum, domande frequenti e note sulla versione.
- **Documenti di supporto**: è possibile fornire fino a tre documenti rivolte ai clienti, ad esempio white paper, brochure, elenchi di controllo o presentazioni di PowerPoint.
- **Supporti – Logos**: fornire un file PNG per il logo di **grandi dimensioni** . Il centro per i partner lo utilizzerà per creare un logo **piccolo** e **medio** . Facoltativamente, è possibile sostituirli con immagini diverse in un secondo momento.

   - Grande (da 216 x 216 a 350 x 350 px, obbligatorio)
   - Media (90 x 90 px, facoltativo)
   - Piccolo (48 x 48 px, facoltativo)

  Questi logo vengono usati in posizioni diverse negli archivi online:

  - Il logo piccolo viene visualizzato nei risultati della ricerca di Azure Marketplace e nella pagina principale Microsoft AppSource e nei risultati della ricerca.
  - Il logo medio viene visualizzato quando si crea una nuova risorsa in Microsoft Azure.
  - Il logo di grandi dimensioni viene visualizzato nella pagina di inserzione dell'offerta in Azure Marketplace e Microsoft AppSource.

- **Supporti-schermate**: è necessario aggiungere almeno uno e un massimo di cinque screenshot con i requisiti seguenti, che mostrano il funzionamento dell'offerta:
  - 1280 x 720 pixel
  - file con estensione png
  - Deve includere una didascalia
- **Media-video** (facoltativo): è possibile aggiungere fino a quattro video con i requisiti seguenti, che dimostrano l'offerta:
  - Nome
  - URL: deve essere ospitato solo su YouTube o Vimeo.
  - Anteprima: file 1280 x 720. png

> [!Note]
> L'offerta deve soddisfare i [criteri di certificazione del Marketplace commerciale](/legal/marketplace/certification-policies#100-general) generale e i [criteri di software as a Service](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) per la pubblicazione nel Marketplace commerciale.

> [!NOTE]
> Un pubblico di anteprima è diverso da un piano privato. Un piano privato è quello reso disponibile solo per un determinato gruppo di destinatari. In questo modo è possibile negoziare un piano personalizzato con clienti specifici. Per ulteriori informazioni, vedere la sezione successiva: piani.

È possibile inviare gli inviti agli indirizzi di posta elettronica dell'account Microsoft (MSA) o Azure Active Directory (Azure AD). Aggiungere fino a 10 indirizzi di posta elettronica manualmente o importare fino a 20 con un file con estensione CSV. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti all'offerta.

## <a name="plans"></a>Plans

Le offerte transazionali richiedono almeno un piano. Un piano definisce l'ambito e i limiti della soluzione e i prezzi associati. È possibile creare più piani per l'offerta per offrire ai clienti diverse opzioni tecniche e di prezzo. Se si sceglie di elaborare le transazioni in modo indipendente anziché creare un'offerta transazionale, la pagina **piani** non è visibile. In tal caso, ignorare questa sezione e passare ad [altre opportunità di vendita](#additional-sales-opportunities).

Vedi [piani e prezzi per le offerte del Marketplace commerciale](plans-pricing.md) per indicazioni generali sui piani, inclusi i modelli di prezzi, le versioni di valutazione gratuite e i piani privati. Le sezioni seguenti illustrano informazioni aggiuntive specifiche per le offerte SaaS.

### <a name="saas-pricing-models"></a>Modelli di determinazione dei prezzi per SaaS

Le offerte SaaS possono usare uno dei due modelli di determinazione prezzi per ogni piano, ovvero _Tariffa_ fissa o _utente_. Tutti i piani nella stessa offerta devono essere associati allo stesso modello di determinazione prezzi. Un'offerta, ad esempio, non può avere un piano di tariffa fissa e un altro piano per ogni utente.

**Tariffa** fissa: consente di accedere all'offerta con un singolo prezzo tariffario mensile o annuale. Questo approccio è a volte noto come prezzo basato sul sito. Con questo modello di determinazione dei prezzi, è possibile definire facoltativamente piani a consumo che usano l'API del servizio di misurazione del Marketplace per addebitare ai clienti l'utilizzo non coperto dalla tariffa fissa. Per altre informazioni sulla fatturazione a consumo, vedere [fatturazione a consumo per Saas usando il servizio di misurazione del Marketplace commerciale](./partner-center-portal/saas-metered-billing.md). Usare questa opzione anche se il comportamento di utilizzo per il servizio SaaS si trova in picchi.

**Per utente** : consente di accedere all'offerta con un prezzo basato sul numero di utenti che possono accedere all'offerta o occupare le postazioni. Con questo modello basato sull'utente è possibile impostare il numero minimo e massimo di utenti supportati dal piano. È possibile creare più piani per configurare punti di prezzo diversi in base al numero di utenti. Questi campi sono facoltativi. Se viene lasciato deselezionato, il numero di utenti verrà interpretato come non avente un limite (minimo 1 e massimo di quanti ne può supportare il servizio). Questi campi possono essere modificati nell'ambito di un aggiornamento del piano.

> [!IMPORTANT]
> Dopo la pubblicazione dell'offerta, non è possibile modificare il modello di determinazione dei prezzi. Inoltre, tutti i piani per la stessa offerta devono condividere lo stesso modello di determinazione prezzi.

### <a name="saas-billing"></a>Fatturazione SaaS

Per le app SaaS eseguite nella sottoscrizione di Azure (editore), l'utilizzo dell'infrastruttura viene addebitato direttamente all'utente; i clienti non visualizzano i costi di utilizzo dell'infrastruttura effettivi. È necessario aggregare i costi di utilizzo dell'infrastruttura di Azure nei prezzi delle licenze software per compensare il costo dell'infrastruttura distribuita per l'esecuzione della soluzione.

Le offerte di app SaaS vendute tramite Microsoft supportano la fatturazione mensile o annuale in base a una tariffa fissa, per utente o a costi di utilizzo usando il [servizio di fatturazione](./partner-center-portal/saas-metered-billing.md)a consumo. Il Marketplace commerciale opera su un modello di agenzia, in base al quale gli editori impostano i prezzi, i clienti di Microsoft fatturazione e i ricavi per gli editori, mantenendo al tempo stesso la tariffa dell'Agenzia.

Nell'esempio seguente viene illustrata una suddivisione di esempio di costi e pagamenti per illustrare il modello di agenzia. In questo esempio Microsoft fattura $ 100,00 al cliente per la licenza software e paga $ 80,00 all'editore.

| Costo della licenza | $ 100 al mese |
| ------------ | ------------- |
| Costo dell'utilizzo di Azure (D1/1-Core) | Costi addebitati direttamente all'editore, non al cliente |
| Importo addebitato da Microsoft al cliente | $ 100,00 al mese. L'editore deve calcolare i costi di infrastruttura sostenuti o trasferirli nei costi di licenza |
| **Microsoft addebita** | **$ 100 al mese** |
| Microsoft paga l'80% del costo della licenza<br>`*` Per le app SaaS qualificate, Microsoft paga il 90% del costo della licenza| $ 80,00 al mese<br>``*`` $ 90,00 al mese |
|||

**`*` Tariffa ridotta del servizio Marketplace** : per alcune offerte SaaS pubblicate sul Marketplace commerciale, Microsoft ridurrà la tariffa del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%. Per le offerte idonee, le offerte devono essere state designate da Microsoft come co-selling IP di Azure incentivate. L'idoneità deve essere soddisfatta almeno cinque (5) giorni lavorativi prima della fine di ogni mese di calendario per ricevere la tariffa ridotta del servizio Marketplace. Una volta raggiunta l'idoneità, la tariffa di servizio ridotta viene assegnata a tutte le transazioni effettive il primo giorno del mese successivo e continuerà a essere applicata fino a quando lo stato di incentivate di co-selling IP di Azure non viene perso. Per informazioni dettagliate sull'idoneità di co-selling IP, vedere [requisiti per lo stato di co-selling](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). La tariffa ridotta per il servizio Marketplace si applica anche alle macchine virtuali incentivate di Azure con co-selling IP di Azure, alle app gestite e a qualsiasi altra offerta IaaS transazionale qualificata resa disponibile tramite il Marketplace commerciale.

## <a name="preview-audience"></a>Destinatari dell'anteprima

Un pubblico di anteprima può accedere all'offerta prima di essere pubblicata Live negli archivi online. Gli utenti possono verificare il modo in cui l'offerta apparirà nel Marketplace commerciale e testare la funzionalità end-to-end prima della pubblicazione in tempo reale. 

Nella pagina **Anteprima destinatari** è possibile definire un numero limitato di destinatari di anteprima. Questa impostazione non è disponibile se si sceglie di elaborare le transazioni in modo indipendente anziché vendere l'offerta tramite Microsoft. In tal caso, è possibile ignorare questa sezione e passare ad [altre opportunità di vendita](#additional-sales-opportunities).

## <a name="test-offer"></a>Offerta di test

Prima di pubblicare l'offerta in tempo reale, è consigliabile usare la funzionalità di anteprima per sviluppare l'implementazione tecnica, testare e sperimentare modelli tariffari diversi.

Per sviluppare e testare l'offerta SaaS con la quantità più bassa di rischi, è consigliabile creare un'offerta di test e sviluppo (DEV) per la sperimentazione e il testing. L'offerta DEV sarà separata dall'offerta di produzione (PROD).

Per evitare acquisti accidentali dell'offerta DEV, non sarà mai possibile premere il pulsante **Go Live** per pubblicare l'offerta dev Live.

![Viene illustrata la pagina di panoramica dell'offerta per un'offerta nel centro per i partner. Vengono visualizzati il pulsante Go Live e i collegamenti di anteprima. Il collegamento Visualizza report di convalida viene visualizzato anche in convalida automatica.](./media/review-publish-offer/publish-status-saas.png)

Di seguito sono riportati alcuni motivi per creare un'offerta di sviluppo separata per il team di sviluppo da usare per lo sviluppo e il test dell'offerta PROD:

- Evitare addebiti accidentali per i clienti
- Valutazione dei modelli di prezzi
- Non aggiungere piani che non sono destinati ai clienti effettivi

### <a name="avoid-accidental-customer-charges"></a>Evitare addebiti accidentali per i clienti

Usando un'offerta DEV anziché l'offerta PROD e considerandoli come ambienti di sviluppo e di produzione, è possibile evitare addebiti accidentali per i clienti.

Si consiglia di registrare due diverse app Azure AD per chiamare le API del Marketplace. Gli sviluppatori useranno un'app Azure AD con le impostazioni dell'offerta DEV e il team operativo userà la registrazione dell'app PROD. In questo modo, è possibile isolare il team di sviluppo evitando errori accidentali, ad esempio chiamando l'API per annullare la sottoscrizione di un cliente che paga $100.000 al mese. È anche possibile evitare di addebitare un cliente per l'utilizzo a consumo non utilizzato.

### <a name="evaluate-pricing-models"></a>Valutazione dei modelli di prezzi

Il test dei modelli di prezzi nell'offerta DEV riduce i rischi quando gli sviluppatori sperimentano modelli tariffari diversi.

Gli editori possono creare i piani necessari nell'offerta DEV per determinare quale modello di determinazione dei prezzi funziona meglio per la propria offerta. Gli sviluppatori potrebbero voler creare più piani nell'offerta DEV per testare diverse combinazioni di prezzo. Ad esempio, è possibile creare piani con diversi set di dimensioni a consumo personalizzate. È possibile creare un piano diverso con una combinazione di dimensioni forfettarie e dimensioni a consumo personalizzate.

Per testare più opzioni di prezzo, è necessario creare un piano per ogni modello di determinazione prezzi univoco. Per ulteriori informazioni, vedere [piani](#plans).

### <a name="not-adding-plans-that-do-not-target-actual-customers"></a>Non aggiungere piani che non sono destinati ai clienti effettivi

Usando un'offerta di sviluppo per lo sviluppo e il test, è possibile ridurre il disordine superfluo nell'offerta PROD. Ad esempio, non è possibile eliminare i piani creati per testare modelli tariffari o configurazioni tecniche diverse (senza inviare un ticket di supporto). Quindi, creando piani per il test nell'offerta di sviluppo, si riduce il disordine nell'offerta di produzione.

Il disordine nell'offerta di produzione frustra i team di prodotto e marketing, perché si aspettano che tutti i piani siano destinati ai clienti effettivi. In particolare, con i team di grandi dimensioni indipendenti che vogliono interagire con diversi sandbox, la creazione di due offerte fornirà due ambienti diversi per DEV e PROD. In alcuni casi, potrebbe essere necessario creare più offerte di sviluppo per supportare un team più ampio con persone diverse che eseguono diversi scenari di test. Consentire a membri del team diversi di lavorare nell'offerta di sviluppo separata dall'offerta di produzione, contribuisce a garantire la massima disponibilità dei piani di produzione.

Il test di un'offerta di sviluppo consente di evitare il limite di 30 dimensioni a consumo personalizzate per ogni offerta. Gli sviluppatori possono provare diverse combinazioni di contatori nell'offerta DEV senza influire sul limite della dimensione a consumo personalizzato nell'offerta PROD.

## <a name="additional-sales-opportunities"></a>Opportunità di vendita aggiuntive

È possibile scegliere di acconsentire ai canali di marketing e vendita supportati da Microsoft. Quando si crea l'offerta nel centro per i partner, si vedranno due schede verso la fine del processo:

- **Rivendere tramite CSP**: usare questa opzione per consentire ai partner di Microsoft Cloud Solution Provider (CSP) di rivendere la soluzione come parte di un'offerta in bundle. Per ulteriori informazioni su questo programma, vedere il [programma Cloud Solution Provider](cloud-solution-providers.md).

- **Co-selling con Microsoft**: questa opzione consente ai team di vendita Microsoft di prendere in considerazione la soluzione di co-selling IP idonea per valutare le esigenze dei clienti. Per informazioni dettagliate sull'idoneità di co-selling, vedere [requisiti per lo stato di co-selling](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Per informazioni dettagliate su come preparare l'offerta per la valutazione, vedere [opzione di co-selling nel centro per i partner](commercial-marketplace-co-sell.md).

## <a name="next-steps"></a>Passaggi successivi

- [Come creare un'offerta SaaS nel Marketplace commerciale](create-new-saas-offer.md)
- [Procedure consigliate per le inserzioni di offerte](gtm-offer-listing-best-practices.md)
