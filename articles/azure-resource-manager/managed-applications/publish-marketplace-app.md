---
title: App gestite nel Marketplace
description: Descrive le applicazioni gestite di Azure disponibili tramite il Marketplace.
author: tfitzmac
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: a5e2a3569c70404d64d24ecfc35a8258ea864a4f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87480425"
---
# <a name="tutorial-publish-azure-managed-applications-in-the-marketplace"></a>Esercitazione: Pubblicare un'applicazione gestita di Azure nel Marketplace

I fornitori possono usare le applicazioni gestite per offrire le proprie soluzioni a tutti i clienti di Azure Marketplace. Per fornitori si intendono i provider di servizi gestiti, i fornitori di software indipendente e gli integratori di sistemi. Le applicazioni gestite riducono l'overhead derivante da manutenzione e gestione per i clienti. I fornitori possono vendere l'infrastruttura e il software tramite il marketplace. Possono collegare servizi e supporto operativo alle applicazioni gestite. Per altre informazioni, vedere [Panoramica delle applicazioni gestite di Azure](overview.md).

Questo articolo illustra come sia possibile pubblicare un'applicazione nel marketplace e renderla disponibile per i clienti su larga scala.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Prerequisiti per la pubblicazione di un'applicazione gestita

Per completare questo articolo, è necessario avere già il file ZIP per la definizione di applicazione gestita. Per altre informazioni, vedere [Creare un'applicazione del catalogo di servizi](publish-service-catalog-app.md).

Esistono diversi prerequisiti aziendali. ovvero:

* La società (o la sua affiliata) deve avere sede in un paese/area geografica in cui le vendite sono supportate dal marketplace.
* Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati dal marketplace.
* Rendere disponibile il supporto tecnico per i clienti in una modalità ragionevole dal punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite il supporto della community.
* Concedere in licenza il software e le eventuali dipendenze software di terze parti.
* Offrire contenuti che soddisfino i criteri perché l'offerta sia inserita nel Marketplace e nel portale di Azure.
* Accettare le condizioni delle Politiche di partecipazione a Microsoft Azure Marketplace e del Contratto per gli editori.
* Accettare le Condizioni per l'utilizzo del sito Web di Microsoft Azure, l'Informativa sulla privacy di Microsoft e il Contratto del Programma Microsoft Azure Certified.

È inoltre necessario avere un account di Marketplace. Per creare un account, vedere [Come creare un account di Marketplace commerciale nel Centro per i partner](../../marketplace/partner-center-portal/create-account.md).

## <a name="create-a-new-azure-application-offer"></a>Creare una nuova offerta di applicazione Azure

Una volta creato l'account nel portale dei partner, si è pronti per creare l'offerta di applicazione gestita.

### <a name="set-up-an-offer"></a>Configurare un'offerta

L'offerta per un'applicazione gestita corrisponde a una classe di offerta di prodotti di un editore. Per rendere disponibile nel marketplace un nuovo tipo di applicazione, è possibile configurare una nuova offerta. Un'offerta è una raccolta di SKU. Ogni offerta viene visualizzata come entità in sé nel marketplace.

1. Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).

1. Nella barra di spostamento a sinistra selezionare **+ New Offer**  (+ Nuova offerta)  > **Azure Applications** (Applicazioni Azure).

1. Nella visualizzazione **Editor** sono visibili i moduli necessari. Ogni modulo è descritto più avanti in questo articolo.

## <a name="offer-settings-form"></a>Modulo delle impostazioni dell'offerta

I campi del modulo **Impostazioni dell'offerta** sono:

* **Offer ID** (ID offerta): questo campo è un identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID è visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta.
* **Publisher ID** (ID editore): usare questo elenco a discesa per scegliere il profilo di pubblicazione in cui si vuole pubblicare l'offerta. Questo campo è bloccato dopo la pubblicazione dell'offerta.
* **Name**: nome visualizzato dell'offerta nel Marketplace e nel portale. Può contenere massimo 50 caratteri. Includere un nome di marchio riconoscibile per il prodotto. Non includere il nome della società, a meno che non corrisponda al nome con cui viene commercializzato. Se si sta proponendo questa offerta sul proprio sito Web, assicurarsi che il nome corrisponda esattamente a quello con cui viene visualizzato nel sito Web.

Al termine, selezionare **Salva** per salvare le voci immesse.

## <a name="skus-form"></a>Modulo degli SKU

Il passaggio successivo è quello di aggiungere gli SKU dell'offerta.

Uno SKU è la più piccola unità acquistabile di un'offerta. All'interno della stessa classe di prodotti (offerta), gli SKU consentono di distinguere tra:

* le diverse funzionalità supportate
* il fatto che l'offerta sia gestita o non gestita
* i modelli di fatturazione supportati

Uno SKU viene visualizzato sotto l'offerta padre nel marketplace e come entità acquistabile di per sé nel portale di Azure.

1. Selezionare **SKU** > **New SKU** (Nuovo SKU).

1. Immettere un **ID SKU**. L'ID SKU è l'identificatore univoco dello SKU in un'offerta. Questo ID è visibile negli URL dei prodotti, nei modelli di Resource Manager e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta. All'interno di un'offerta possono essere presenti più SKU. È necessario uno SKU per ogni immagine che si prevede di pubblicare.

1. Compilare la sezione dei **dettagli dello SKU** nel formato seguente:

   Compilare i seguenti campi:

   * **Titolo**: immettere un titolo per lo SKU, che viene visualizzato nella raccolta di questo elemento.
   * **Riepilogo**: immettere un breve riepilogo per questo SKU, che viene visualizzato sotto il titolo.
   * **Descrizione**: immettere una descrizione dettagliata sullo SKU.
   * **Sku Type** (Tipo di SKU): i valori consentiti sono *Managed Application* (Applicazione gestita) e *Solution Templates* (Modelli di soluzioni). In questo caso, selezionare *Managed Application* (Applicazione gestita).
   * **Country/Region availability (Disponibilità paese/area geografica)** : selezionare i paesi/aree geografiche in cui l'applicazione gestita è disponibile.
   * **Pricing** (Prezzi): indica il prezzo della gestione dell'applicazione. Prima di impostare il prezzo, selezionare i paesi/aree geografiche disponibili.

1. Aggiungere un nuovo pacchetto. Compilare la sezione dei **dettagli del pacchetto** nel formato seguente:

   Compilare i seguenti campi:

   * **Versione**: immettere la versione per il pacchetto caricato. Deve essere nel formato `{number}.{number}.{number}{number}`.
   * **Package file (.zip)** (File pacchetto -.zip): questo pacchetto contiene due file necessari compressi in un pacchetto ZIP. Un file è un modello di Resource Manager che definisce le risorse da distribuire per l'applicazione gestita. L'altro file definisce l'[interfaccia utente](create-uidefinition-overview.md) per i consumer che distribuiscono l'applicazione gestita tramite il portale. Nell'interfaccia utente specificare gli elementi che consentono ai consumer di fornire i valori dei parametri.
   * **ID tenant**: ID tenant per l'accesso dell'account.
   * **Enable JIT Access** (Abilita accesso JIT): selezionare **Yes** (Sì) per abilitare il [controllo di accesso JIT](request-just-in-time-access.md) per l'account. Quando è abilitato, si richiede l'accesso all'account del consumer per un periodo di tempo specificato. Affinché i consumer dell'applicazione gestita concedano all'account l'accesso permanente, selezionare **No**.
   * **Customize allowed customer actions?** (Personalizzare le azioni dei clienti consentite?): selezionare **Yes** (Sì) per specificare le azioni che gli utenti possono eseguire con le risorse gestite.
   * **Allowed customer actions** (Azioni dei clienti consentite): se si seleziona **Yes** (Sì) per l'impostazione precedente, è possibile specificare quali azioni sono consentite ai consumer usando le [assegnazioni di rifiuto per le risorse di Azure](../../role-based-access-control/deny-assignments.md).

     Per informazioni sulle azioni disponibili, vedere [Operazioni di provider di risorse con Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md). Ad esempio, per consentire agli utenti di riavviare le macchine virtuali, aggiungere `Microsoft.Compute/virtualMachines/restart/action` alle azioni consentite. L'azione `*/read` viene automaticamente consentita, quindi non è necessario includere tale impostazione.
   * **PrincipalId**: questa proprietà è l'identificatore Azure Active Directory (Azure AD) di un utente, di un gruppo di utenti o di un'applicazione a cui è stato concesso l'accesso alle risorse nella sottoscrizione del cliente. La definizione del ruolo descrive le autorizzazioni.
   * **Role Definition** (Definizione ruolo): questa proprietà è un elenco di tutti i ruoli predefiniti di Azure forniti da Azure AD. È possibile selezionare il ruolo più appropriato da usare per gestire le risorse per conto del cliente.
   * **Policy Settings** (Impostazioni dei criteri): applicare un [criterio di Azure](../../governance/policy/overview.md) all'applicazione gestita per specificare i requisiti di conformità per le soluzioni distribuite. Selezionare i criteri da applicare tra le opzioni disponibili. In **Policy Parameters** (Parametri dei criteri) specificare una stringa JSON con i valori dei parametri. Per le definizioni dei criteri e il formato dei valori dei parametri, vedere [Esempi di criteri di Azure](../../governance/policy/samples/index.md).

È possibile aggiungere varie autorizzazioni. È consigliabile creare un gruppo di utenti di AD e specificare il relativo ID in **PrincipalId**. In questo modo è possibile aggiungere più utenti al gruppo di utenti senza che sia necessario aggiornare lo SKU.

Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Modulo Marketplace

Il form Marketplace include i campi che vengono visualizzati in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Preview Subscription IDs (ID sottoscrizione di anteprima)

Immettere un elenco di ID di sottoscrizione di Azure che possono accedere all'offerta dopo la pubblicazione. È possibile usare queste sottoscrizioni consentite per testare l'offerta in anteprima prima di pubblicarla. È possibile compilare un elenco con un massimo di 100 sottoscrizioni consentite nel portale per i partner.

### <a name="suggested-categories"></a>Suggested Categories (Categorie suggerite)

Nell'elenco selezionare fino a cinque categorie a cui l'offerta può essere associata in modo ottimale. Queste categorie vengono usate per il mapping dell'offerta alle categorie di prodotti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Nel riepilogo dell'applicazione gestita vengono visualizzati i campi seguenti:

![Riepilogo del Marketplace](./media/publish-marketplace-app/publishvm10.png)

Nella scheda **Anteprima** per l'applicazione gestita vengono visualizzati i campi seguenti:

![Panoramica del Marketplace](./media/publish-marketplace-app/publishvm11.png)

Nella scheda **Piani + prezzi** per l'applicazione gestita vengono visualizzati i campi seguenti:

![Piani del Marketplace](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Portale di Azure

Nel riepilogo dell'applicazione gestita vengono visualizzati i campi seguenti:

![Riepilogo del portale](./media/publish-marketplace-app/publishvm12.png)

Nell'anteprima per l'applicazione gestita vengono visualizzati i campi seguenti:

![Panoramica del portale](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Linee guida per il logo

Seguire queste linee guida per qualsiasi logo caricato nel portale per Cloud Partner:

*   La progettazione di Azure ha una tavolozza dei colori semplice. Limitare il numero di colori primari e secondari nel logo.
*   I colori del tema del portale sono il bianco e il nero. Non usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale. Si consiglia di usare colori primari semplici. *Se si usa uno sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.*
*   Non usare uno sfondo sfumato sul logo.
*   Non inserire testo, nemmeno il nome del marchio o della società, sul logo. L'aspetto del logo deve essere semplice e senza sfumature.
*   Verificare che il logo non venga adattato.

#### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo. L'editore può scegliere di non caricare un logo alto. Una volta caricata, l'icona del logo alto non può essere eliminata. A quel punto il partner deve seguire le istruzioni del Marketplace per le icone del logo alto.

Seguire queste linee guida per l'icona del logo alto:

*   Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il colore bianco. Non usare quindi un colore chiaro come sfondo dell'icona del logo alto. Lo sfondo nero, bianco o trasparente non è ammesso per le icone del logo alto.
*   Dopo che l'offerta è stata pubblicata, gli elementi vengono incorporati a livello di codice nel logo banner. Gli elementi incorporati includono il nome visualizzato dell'editore, il titolo del piano, un riepilogo lungo dell'offerta e il pulsante **Crea**. Non inserire quindi testo mentre si progetta il logo alto. Lasciare uno spazio vuoto sulla destra dove il testo viene automaticamente incluso a livello di codice. Lo spazio vuoto per il testo deve essere 415x100 pixel a destra e viene spostato con un offset di 370 pixel da sinistra.

    ![Esempio di logo alto](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Modulo di supporto

Compilare il modulo **Supporto** con i contatti del supporto forniti dalla società, ad esempio le informazioni di contatto del supporto tecnico e dell'assistenza clienti.

## <a name="publish-an-offer"></a>Pubblicare un'offerta

Dopo avere completato tutte le sezioni, selezionare **Publish** (Pubblica) per avviare il processo per rendere disponibile l'offerta per i clienti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su quello che si verifica dopo aver fatto clic su **Pubblica**, vedere [Pubblicare un'offerta di applicazione Azure](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)
* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](overview.md).
* Per informazioni sulla pubblicazione di un'applicazione gestita del catalogo di servizi, vedere [Creare e pubblicare un'applicazione gestita del catalogo di servizi](publish-service-catalog-app.md).
