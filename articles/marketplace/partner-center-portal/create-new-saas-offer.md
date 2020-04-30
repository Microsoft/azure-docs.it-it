---
title: Crea una nuova offerta SaaS per Microsoft Commercial Marketplace
description: Come creare una nuova offerta SaaS (software as a Service) per l'inserzione o la vendita in Microsoft AppSource, Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) tramite il programma Microsoft Commercial Marketplace nel centro per i partner Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: a39e1e19f65722b4b5ae809ca943da719a3c6e22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869782"
---
# <a name="create-a-new-saas-offer"></a>Creare una nuova offerta SaaS

Per iniziare a creare offerte SaaS (software as a Service), prima di tutto è necessario [creare un account del centro](./create-account.md) per i partner e aprire il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), con la scheda **Panoramica** selezionata.

![Dashboard del Marketplace commerciale nel centro per i partner](./media/new-offer-overview.png)

>[!Note]
> Dopo la pubblicazione di un'offerta, le modifiche apportate all'offerta nel centro per i partner verranno aggiornate solo nel sistema e archiviano i front-end dopo la nuova pubblicazione. Assicurarsi di inviare l'offerta per la pubblicazione dopo avere apportato le modifiche.

Selezionare l' **offerta + nuova...** , quindi selezionare la voce **di menu software come servizio** .

Se si seleziona un altro tipo di offerta, è possibile che venga reindirizzato al [portale cloud partner](https://cloudpartner.azure.com/)precedente. Solo le offerte SaaS e Dynamics 365 sono disponibili nel portale del Marketplace commerciale al momento nel centro per i partner.

![Crea finestra offerta nel centro per i partner](./media/new-offer-click.png)

Verrà visualizzata la finestra di dialogo **nuova offerta** .

![Finestra di dialogo nuova offerta](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>ID offerta e alias

- **ID offerta**: identificatore univoco per ogni offerta nell'account. Questo ID sarà visibile ai clienti nell'indirizzo URL per l'offerta del Marketplace e i modelli Azure Resource Manager (se applicabile). L'ID offerta deve essere minuscolo, alfanumerico (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti). L' **ID offerta** è limitato a 50 caratteri e non può essere modificato dopo aver selezionato *Crea*.  
Esempio: test-offer-1
<br>Risultato dell'URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias offerta**: nome usato per fare riferimento all'offerta nel portale del centro per i partner. Questo nome non verrà usato nel Marketplace ed è diverso dal nome dell' *offerta* e da altri valori che verranno visualizzati ai clienti. Questo valore non può essere modificato dopo aver selezionato *Crea*.

<br>Esempio: offerta di test 1&#8482;

Selezionare **Crea**.  Viene creata una pagina di **Panoramica dell'offerta** per questa offerta.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** include:

- Lo **stato di pubblicazione** consente di visualizzare una rappresentazione visiva dei passaggi necessari per pubblicare l'offerta e il tempo necessario per il completamento di ogni passaggio. Le icone delle fasi di pubblicazione incomplete saranno visualizzate in grigio.

- Il menu **Panoramica dell'offerta** contiene un elenco di collegamenti per l'esecuzione di operazioni su questa offerta. Questo elenco di operazioni verrà modificato in base alla selezione effettuata per l'offerta.  
    - Se l'offerta è una bozza di eliminazione
    - Se l'offerta è un'offerta di vendita Live-stop
    - Se l'offerta è in anteprima-Go-Live
    - Se la disconnessione del server di pubblicazione non è stata completata, Annulla pubblicazione

## <a name="offer-setup"></a>Installazione dell'offerta

La scheda **installazione offerta** richiede le informazioni seguenti. Selezionare **Salva** dopo aver completato questi campi.

- **Si vuole vendere tramite Microsoft?** (Sì/No)
    - **Sì**, vuoi vendere la tua offerta tramite Microsoft, con Microsoft che ospita le transazioni del Marketplace per conto tuo. o 
    - **No**, preferisci semplicemente elencare l'offerta attraverso i Marketplace, elaborando le transazioni monetarie in modo indipendente da Microsoft.

### <a name="sell-through-microsoft"></a>Vendita tramite Microsoft

La vendita tramite Microsoft offre una migliore individuazione e acquisizione dei clienti, consente a Microsoft di ospitare le transazioni del Marketplace per conto dell'utente e sfrutta le funzionalità di Commerce disponibili a livello globale di Microsoft.

#### <a name="saas-offer-requirements"></a>Requisiti dell'offerta SaaS

Per elencare le offerte SaaS (software as a Service) con Marketplace commerciale nel centro per i partner, è necessario soddisfare i criteri seguenti:

- L'offerta deve usare [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) per la gestione delle identità e l'autenticazione.
- L'offerta deve usare [API di evasione Saas](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) per l'integrazione con Azure Marketplace.
- Per requisiti più estesi, vedere la [Guida alla pubblicazione dell'offerta SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Opzioni di fatturazione e prezzi SaaS

Con le soluzioni SaaS in esecuzione nella sottoscrizione di Azure dell'editore, i costi di licenza pagati dai clienti includono il costo dell'infrastruttura su cui il software viene distribuito. L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori devono aggregare i costi di utilizzo dell'infrastruttura di Azure nei prezzi delle licenze software. 

Le offerte SaaS supportano la fatturazione mensile o annuale in base a una tariffa fissa, per utente o a costi di utilizzo usando il servizio di fatturazione a consumo. Il Marketplace commerciale di Microsoft opera su un modello di agenzia, in base al quale gli editori impostano i prezzi, i clienti di Microsoft fattura e i ricavi per l'editore, conservando al tempo stesso la tariffa dell'Agenzia.

La tabella seguente illustra un esempio di suddivisione dei costi e dei pagamenti per illustrare il modello di agenzia.

|**Costo della licenza**|**$100 al mese**|
|:---|:---|
|Costo dell'utilizzo di Azure (D1/1-Core)|Costi addebitati direttamente all'editore, non al cliente|
|Importo addebitato da Microsoft al cliente|$100,00 al mese (l'editore deve tenere conto di eventuali costi dell'infrastruttura sostenuti o pass-through nel costo della licenza)|

|**Microsoft addebita**|**$100 al mese**|
|:---|:---|
|Microsoft paga l'80% del costo della licenza <br>**Per le app SaaS qualificate, Microsoft paga il 90% del costo della licenza*|$ 80,00 al mese <br>*$* 90,00 al mese *|

- In questo esempio Microsoft fattura $100,00 al cliente per la licenza software e paga $80,00 all'editore.
- Ai partner qualificati per la **Tariffa ridotta del servizio Marketplace** verrà visualizzato un importo ridotto delle transazioni sulle offerte SaaS, dal 2019 maggio fino al giugno 2020. In questo scenario Microsoft fattura $100,00 per la licenza software e paga $90,00 all'editore.

> [!NOTE]
> **Costo del servizio Marketplace ridotto**: per alcune offerte SaaS pubblicate sul nostro Marketplace commerciale, Microsoft ridurrà la tariffa del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%. Per qualificare l'offerta, è necessario che almeno una delle offerte sia stata designata da Microsoft come indirizzo di co-selling IP pronto o co-selling IP in ordine di priorità.  L'idoneità deve essere soddisfatta per almeno cinque (5) giorni lavorativi prima della fine di ogni mese di calendario per poter ricevere questa tariffa per il servizio Marketplace ridotta per il mese.  La tariffa ridotta del servizio Marketplace non si applica alle macchine virtuali, alle app gestite o ad altri prodotti resi disponibili tramite il Marketplace commerciale.  Il costo del servizio Marketplace ridotto sarà disponibile solo per le offerte qualificate per i costi di licenza raccolti da Microsoft tra il 1 ° maggio 2019 e il 30 giugno 2020.  Al termine di questo periodo di tempo, la tariffa del servizio Marketplace tornerà al valore normale.

### <a name="list-through-microsoft"></a>Elenca tramite Microsoft

Promuovere la propria azienda con Microsoft creando un elenco di Marketplace. Se si seleziona per elencare solo l'offerta e non la transazione tramite Microsoft, Microsoft non partecipa direttamente alle transazioni di licenza software. Non viene addebitata alcuna tariffa per le transazioni e l'editore mantiene il 100% dei costi di licenza software raccolti dal cliente. Tuttavia, il server di pubblicazione è responsabile del supporto di tutti gli aspetti della transazione di licenza software, inclusi, a titolo esemplificativo, evasione degli ordini, misurazione, fatturazione, fatturazione, pagamento e raccolta.

- **In che modo si desidera che i clienti possano interagire con questa offerta di inserzione?**

#### <a name="get-it-now-free"></a>Ottieni ora (gratuito)

Elenca gratuitamente l'offerta ai clienti fornendo un URL valido (a partire da *http* o *https*) in cui è possibile ottenere una versione di valutazione tramite [l'autenticazione con un clic usando Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  ad esempio `https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Versione di valutazione gratuita (elenco)

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da *http* o *https*), in cui è possibile ottenere una versione di valutazione tramite [l'autenticazione con un clic usando Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Ad esempio: `https://contoso.com/trial/saas-app`. Le versioni di valutazione gratuite dell'elenco di offerte vengono create, gestite e configurate dal servizio e non hanno sottoscrizioni gestite da Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento alla versione di valutazione può essere usato solo per ottenere informazioni sugli utenti tramite Azure AD per automatizzare la creazione di account nell'app. Gli account Microsoft (MSA) non sono supportati per l'autenticazione con questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Raccogliere le informazioni di contatto del cliente connettendosi al sistema CRM (Customer Relationship Management). Al cliente verrà richiesta l'autorizzazione a condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e all'origine del Marketplace in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Per ulteriori informazioni sulla configurazione del CRM, vedere [Connect Lead Management](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Elenco di offerte di esempio del marketplace

![Elenco delle offerte di Marketplace di esempio con note](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Abilitare un test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di provare prima di acquistare, ottenendo una maggiore conversione e la generazione di lead altamente qualificati. Per altre informazioni, vedere [consentire ai clienti di test drive l'offerta](./test-drive.md).

- **Abilita una test drive** (casella di controllo)

Abilitando test drive, verrà richiesto di configurare un ambiente dimostrativo per consentire ai clienti di provare l'offerta per un periodo di tempo fisso. 

### <a name="test-drive-resources"></a>Risorse di test drive

- [Test Drive marketing Best Practices](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- [test drive procedure](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) consigliate tecniche
- [Panoramica di test drive (Download PDF)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Gestione dei lead di connessione

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Risorse aggiuntive per la gestione dei lead
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead un cercapersone](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

## <a name="properties"></a>Proprietà

La scheda **Proprietà** richiede di definire le categorie e i settori usati per raggruppare l'offerta nei Marketplace, i contratti legali che supportano l'offerta e la versione dell'app.

Selezionare **Salva** dopo aver completato questi campi.

### <a name="category"></a>Category

Selezionare almeno uno (1) e un massimo di tre (3) categorie utilizzate per raggruppare l'offerta nelle aree di ricerca del Marketplace appropriate. Come l'offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="industry"></a>Settore

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versione dell'app

Questo campo è facoltativo e viene usato in AppSource Marketplace per identificare il numero di versione dell'offerta.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per Microsoft Commercial Marketplace

Microsoft fornisce un modello di contratto standard.

- **Usare il contratto standard per Microsoft Commercial Marketplace?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard per Microsoft Commercial Marketplace, che consente di semplificare le transazioni nel Marketplace. Anziché creare termini e condizioni personalizzati, gli editori del Marketplace commerciale possono scegliere di offrire il proprio software al di sotto del contratto standard, che i clienti devono solo controllare e accettare una sola volta. Il contratto standard è disponibile qui: https://go.microsoft.com/fwlink/?linkid=2041178.

È possibile scegliere di utilizzare il contratto standard anziché fornire i termini e le condizioni personalizzati selezionando la casella di controllo "utilizza il contratto standard per il Marketplace commerciale".

![Uso della casella di controllo contratto standard](./media/use-standard-contract.png)

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per Microsoft Commercial Marketplace, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard **o** a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.

#### <a name="standard-contract-amendments"></a>Modifiche contrattuali standard

Le modifiche contrattuali standard consentono agli editori di selezionare le condizioni di contratto standard per semplificare e personalizzare le condizioni per il prodotto o l'azienda. I clienti devono solo esaminare le modifiche apportate al contratto, se hanno già esaminato e accettato il contratto standard Microsoft.

Per gli editori di Marketplace commerciali sono disponibili due tipi di modifiche:

- Modifiche universali: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Gli emendamenti universali vengono visualizzati a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e la relativa rettifica prima di poter usare l'offerta.
- Modifiche personalizzate: queste modifiche sono modifiche speciali al contratto standard destinate a specifici clienti solo tramite ID tenant di Azure. Gli editori possono scegliere il tenant di destinazione. Solo i clienti del tenant verranno presentati con le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta.  Prima di poter usare l'offerta, i clienti devono accettare le condizioni del contratto standard e le modifiche.

>[!NOTE]
> Questi due tipi di modifiche si impilano tra loro. I clienti destinati a modifiche personalizzate riceveranno anche la modifica universale del contratto standard durante l'acquisto.

**Termini dell'emendamento universale per il contratto standard per il Marketplace commerciale di Microsoft**: immettere le condizioni per la modifica universale in questa casella. È possibile specificare una singola modifica universale per ogni offerta. In questa casella è possibile immettere un numero illimitato di caratteri. Questi termini vengono visualizzati per i clienti in AppSource, Azure Marketplace e/o portale di Azure durante l'individuazione e il flusso di acquisto.

**Condizioni di modifica personalizzate per il contratto standard per il Marketplace commerciale di Microsoft**: iniziare selezionando **Aggiungi condizioni di modifica personalizzate**. È possibile specificare fino a 10 termini di modifica personalizzati per ogni offerta.

- **Condizioni di modifica personalizzate**: immettere le condizioni di modifica personalizzate nella casella condizioni di modifica personalizzate. In questa casella è possibile immettere un numero illimitato di caratteri. Solo i clienti degli ID tenant specificati per questi termini personalizzati verranno presentati con le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta nel portale di Azure.  
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una rettifica personalizzata, è necessario specificare almeno un ID tenant. L'ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e trovarlo passando a portal.azure.com > Azure Active Directory > proprietà. Il valore di ID directory è l'ID tenant (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione del cliente usando l'URL del nome di dominio in cui [si trova il Microsoft Azure e l'ID tenant di Office 365?](https://www.whatismytenantid.com).
- **Descrizione** (facoltativo): specificare facoltativamente una descrizione per l'ID tenant che consente di identificare il cliente di destinazione con la modifica.

#### <a name="terms-and-conditions"></a>Termini e condizioni

Se si desidera fornire termini e condizioni personalizzati, è possibile scegliere di immetterli nel campo termini e condizioni. In questo campo è possibile immettere fino a 10.000 caratteri di testo. Se i termini e le condizioni richiedono una descrizione più lunga, immettere un solo collegamento URL in questo campo in cui è possibile trovare i termini e le condizioni. Verrà visualizzato come collegamento attivo ai clienti.

I clienti devono accettare queste condizioni per poter provare l'offerta.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

## <a name="offer-listing"></a>Elenco offerte

Nella scheda elenco offerte vengono visualizzate le lingue (e i mercati) in cui è disponibile l'offerta, attualmente in lingua inglese (Stati Uniti) è l'unica località disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e della data/ora in cui è stato aggiunto. Sarà necessario definire i dettagli del Marketplace (nome dell'offerta, descrizione, termini di ricerca e così via) per ogni lingua/mercato.

> [!NOTE]
> Il contenuto dell'elenco di offerte, ad esempio Descrizione dell'offerta, documenti, schermate, condizioni per l'utilizzo e informativa sulla privacy, non deve essere in inglese finché la descrizione dell'offerta inizia con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento utile* per offrire contenuto in una lingua diversa da quella usata nell'offerta di visualizzazione del contenuto.

### <a name="offer-listings"></a>Presentazione delle offerte

Fornire i dettagli da visualizzare nel Marketplace, incluse le descrizioni dell'offerta e le risorse di marketing.

- **Nome** (obbligatorio): il nome definito qui verrà visualizzato come titolo dell'inserzione dell'offerta nei Marketplace scelti. Il nome viene prepopolato in base alla **nuova** voce dell'offerta precedente. Il nome può essere marchiato. Non può contenere Emoji (a meno che non siano il marchio e i simboli di copyright) e deve essere limitato a 50 caratteri.
- **Riepilogo** (obbligatorio): fornire una breve descrizione dell'offerta da usare nei risultati della ricerca nell'elenco dei Marketplace. In questo campo è possibile immettere fino a 100 caratteri di testo.
- **Descrizione** (obbligatoria): fornire una descrizione dell'offerta da visualizzare nella panoramica degli annunci del Marketplace. Si consiglia di includere una proposta di valore, i vantaggi principali, le associazioni di categoria o di settore, le opportunità di acquisto in-app, le divulgazioni richieste e un collegamento per ottenere ulteriori informazioni.
In questo campo è possibile immettere fino a 3.000 caratteri di testo, incluso il markup. Per altri suggerimenti, vedere l'articolo [scrivere un'ottima descrizione dell'app](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Parole chiave di ricerca**: immettere fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta nel Marketplace.
- **Istruzioni introduttive** (obbligatorio): spiegare come configurare e iniziare a usare l'app per i potenziali clienti.  Questa Guida introduttiva può contenere collegamenti a documentazione online più dettagliata. In questo campo è possibile immettere fino a 3.000 caratteri di testo.

#### <a name="description"></a>**Descrizione**

Questo campo è obbligatorio. Elementi da includere nella **Descrizione**:

* Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione.  
* Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
* Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
* Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti.

I componenti fondamentali di una proposta di valore devono includere le informazioni seguenti:

* Descrizione del prodotto.
* Tipo di utente che ottiene vantaggi dal prodotto.
* Esigenze del cliente o dolore che il prodotto indirizzi.

Per rendere più accattivante la **Descrizione** dell'offerta, usare l'editor di testo RTF per formattare la descrizione.

![Uso dell'editor di testo RTF](./media/text-editor2.png)

Utilizzare le istruzioni seguenti per utilizzare l'editor di testo RTF:

- Per modificare il formato del contenuto, evidenziare il testo che si desidera formattare e selezionare uno stile di testo, come illustrato di seguito:

     ![Utilizzo dell'editor di testo RTF per modificare il formato del testo](./media/text-editor3.png)

- Per aggiungere un elenco puntato o numerato al testo, usare le opzioni seguenti:

     ![Uso dell'editor di testo RTF per aggiungere elenchi](./media/text-editor4.png)

- Per aggiungere o rimuovere il rientro nel testo, usare le opzioni seguenti:

     ![Utilizzo dell'editor di testo RTF per rientrare](./media/text-editor5.png)

#### <a name="links"></a>Collegamenti

- **Informativa sulla privacy** (obbligatorio): collegamento all'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida
- **Materiali di marketing del programma CSP** (facoltativo): fornire un collegamento ai materiali di marketing se si sceglie di estendere l'offerta al programma [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . CSP estende l'offerta a una gamma più ampia di clienti qualificati, permettendo ai partner CSP di aggregare, commercializzare e rivendere l'offerta. Questi rivenditori dovranno avere accesso ai materiali per il marketing dell'offerta. Per ulteriori informazioni, vedere la pagina relativa ai [Servizi go-to-Market](https://partner.microsoft.com/reach-customers/gtm).
- **Collegamenti utili** (facoltativo): documenti online supplementari facoltativi sull'app o sui servizi correlati elencati fornendo un **titolo** e un **URL**. Aggiungere altri collegamenti utili facendo clic su **+ Aggiungi URL**.

#### <a name="contact-information"></a>Informazioni contatto

- **Contatti**: per ogni contatto del cliente, fornire un **nome**di dipendente, un **numero di telefono**e un indirizzo di **posta elettronica** .  (Questi *non verranno* visualizzati pubblicamente). Per il gruppo di **contatti di supporto** è necessario anche un **URL di supporto** .  (Queste informazioni *verranno* visualizzate pubblicamente).

**Contatto per il supporto tecnico** (obbligatorio): per domande di supporto generale.

**Contatto** tecnico (obbligatorio): per domande tecniche.

**Contatto di Channel Manager** (obbligatorio): per domande sul rivenditore correlate al programma CSP.

#### <a name="files-and-images"></a>File e immagini

- **Documenti** (obbligatorio): aggiungere i documenti di marketing correlati per l'offerta in formato PDF, specificando almeno uno (1) e un massimo di tre (3) documenti per ogni offerta.
- **Immagini** (facoltativo): sono presenti più posizioni in cui le immagini del logo dell'offerta possono essere visualizzate in tutti i Marketplace, richiedendo le dimensioni seguenti: Small: 48 x 48 pixel _(obbligatorio),_ Medium: 90 x 90 pixel _(obbligatorio)_, large: 216 x 216 pixel _(obbligatorio),_ Wide: 255 x 115 pixel e Hero: 815 x 290 pixel. Tutte le immagini devono essere presenti in. Formato PNG.
- **Screenshot** (obbligatorio): aggiungere screenshot che dimostrano l'offerta. È possibile aggiungere un massimo di cinque (5) schermate e dimensionarle a 1280 x 720 pixel. Tutte le immagini devono essere presenti in. Formato PNG.
- **Video** (facoltativo): aggiungere collegamenti a video che illustrano l'offerta. È possibile usare collegamenti a video di YouTube e/o Vimeo che vengono visualizzati insieme all'offerta ai clienti. Sarà inoltre necessario immettere un'immagine di anteprima del video, ridimensionata a 1280 x 720 pixel in formato PNG. È possibile visualizzare un massimo di quattro video per ogni offerta.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

>[!Note]
>Se si verifica un problema durante il caricamento dei file, assicurarsi che la rete locale non blocchi il https://upload.xboxlive.com servizio usato dal centro per i partner.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

- [Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Anteprima

La scheda **Anteprima** consente di definire un pubblico di **Anteprima** limitato per il rilascio dell'offerta prima di pubblicare l'offerta in tempo reale per i destinatari più ampi del Marketplace.

> [!IMPORTANT]
> Dopo aver controllato l'offerta in anteprima, selezionare **Go Live** in modo che l'offerta possa essere pubblicata in diretta per i destinatari pubblici del Marketplace.

- **Definire un gruppo di destinatari di anteprima: aggiungere un singolo account AAD/MSA e-mail per riga, insieme a una descrizione facoltativa.**

È possibile aggiungere fino a 10 indirizzi di posta elettronica manualmente oppure 20 se si carica un file CSV per l'account Microsoft esistente (MSA) o per Azure Active Directory account per convalidare l'offerta prima della pubblicazione in tempo reale. Aggiungendo questi account, si definisce un gruppo di destinatari a cui verrà consentito l'accesso in anteprima all'offerta prima che venga pubblicata nei Marketplace. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti all'offerta.

> [!NOTE]
> Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima è autorizzato ad accedere all'offerta _prima_ di essere pubblicata Live nei Marketplace. È anche possibile scegliere di creare un piano e renderlo disponibile solo per i destinatari privati. Nella scheda **elenco piani** è possibile definire un pubblico privato con la casella di controllo **questo è un piano privato** . È quindi possibile definire un pubblico privato di un massimo di 20.000 clienti usando gli ID tenant di Azure.

## <a name="technical-configuration"></a>Configurazione tecnica

La scheda **configurazione tecnica** definisce i dettagli tecnici (percorso URL, webhook, ID tenant e ID app) usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquisirla. I diagrammi che descrivono l'utilizzo dei campi raccolti sono disponibili nella documentazione per le [API di evasione Saas](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL della pagina di destinazione** (obbligatorio): definire l'URL del sito su cui i clienti potranno atterrare dopo aver acquisito l'offerta dal Marketplace. Questo URL sarà l'endpoint che riceve un token quando un cliente viene indirizzato alla pagina. Questo token può essere scambiato per i dettagli del provisioning usando Risolvi nelle API di evasione. Questi dettagli e altri utenti raccolti possono essere usati come parte di una pagina Web interattiva del cliente integrata nell'esperienza per completare la registrazione e attivare l'acquisto.

- **Webhook di connessione** (obbligatorio): per tutti gli eventi asincroni che Microsoft deve inviare all'utente per conto del cliente (ad esempio: la sottoscrizione SaaS non è più valida), è necessario fornire un webhook di connessione. Se non si dispone già di un sistema di Webhook, la configurazione più semplice consiste nel disporre di un'app per la logica endpoint HTTP che resterà in ascolto di tutti gli eventi da inviare e quindi di gestirli in modo appropriato\/, ad esempio https:/prod-1westus.Logic.Azure.com:443/work. Per altre informazioni, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **Azure ad ID tenant** (obbligatorio): all'interno portale di Azure, è necessario [creare un'app Azure Active Directory (ad)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) in modo che sia possibile convalidare la connessione tra i due servizi sia dietro una comunicazione autenticata. Per trovare l' [ID tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), passare alla Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e).

- **Azure ad ID app** (obbligatorio): sono necessari anche l' [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e una chiave di autenticazione. Per ottenere tali valori, passare alla Azure Active Directory e selezionare **registrazioni app**, quindi cercare il numero di **ID dell'applicazione** elencato (ad esempio, 50c464d3-4930-494c-963C-1e951d15360e). Per trovare la chiave di autenticazione, passare a **Impostazioni** e selezionare **chiavi**. Sarà necessario fornire una descrizione e una durata, quindi verrà fornito un valore numerico.

>[!Note]
>L'ID applicazione di Azure è associato all'ID editore, quindi assicurarsi che lo stesso ID applicazione venga usato in tutte le offerte.

## <a name="plan-overview"></a>Panoramica del piano

La scheda **panoramica piano** consente di fornire diverse opzioni per i piani all'interno della stessa offerta. Questi piani (detti anche SKU) potrebbero differire in termini di versione, monetizzazione o livelli di servizio. È necessario configurare almeno un piano per vendere l'offerta nel Marketplace.

Al termine della creazione, vengono visualizzati i nomi dei piani, gli ID, i modelli tariffari, la disponibilità (pubblica o privata), lo stato di pubblicazione corrente ed eventuali azioni disponibili.

Le **azioni** disponibili nella **Panoramica del piano** variano a seconda dello stato corrente del piano e possono includere:

- Se lo stato del piano **è bozza-eliminazione bozza**
- Se lo stato del piano è il piano di vendita **Live** -stop o la sincronizzazione di un pubblico privato

**Crea nuovo piano** (almeno un piano per coloro che scelgono di vendere tramite Microsoft)

- **ID piano:** Creare un ID piano univoco per ogni piano di questa offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto e Azure Resource Manager modelli (se applicabile). Usare solo lettere minuscole, caratteri alfanumerici, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. Non è possibile modificare l'ID dopo aver selezionato Crea.
- **Nome del piano:** Il nome verrà visualizzato dai clienti quando si decide quale piano selezionare nell'offerta. Creare un nome di offerta univoco per ogni piano nell'offerta. Il nome del piano viene usato per distinguere i piani software che possono far parte della stessa offerta (ad esempio, il nome dell'offerta: Windows Server; piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Elenco di piani

La scheda **elenco piani** Visualizza le lingue e i mercati in cui è disponibile il piano, attualmente in lingua inglese (Stati Uniti) è l'unica località disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e della data/ora in cui è stato aggiunto. Sarà necessario definire i dettagli del Marketplace (nome dell'offerta, descrizione, termini di ricerca e così via) per ogni lingua/mercato.

#### <a name="plan-listing-details"></a>Dettagli elenco piano

Se si seleziona una delle lingue del piano, vengono visualizzate le informazioni relative all' **elenco dei piani** , inclusi **nome** e **Descrizione.**

- **Nome**: pre-popolato in base alla voce di **nuovo piano** di anteprima e verrà visualizzato come titolo del "piano software" dell'offerta visualizzato nel Marketplace.
- **Descrizione:** Questa descrizione è un'opportunità per spiegare cosa rende univoco questo piano software e qualsiasi differenza rispetto ad altri piani software all'interno dell'offerta. Può contenere un massimo di 500 caratteri.

Selezionare **Salva** dopo aver completato questi campi.

#### <a name="plan-pricing-and-availability"></a>Pianificare prezzi e disponibilità

La scheda **prezzi e disponibilità** consente di configurare i mercati in cui sarà disponibile il piano, il modello di monetizzazione, il prezzo e il termine di fatturazione desiderati. Inoltre, è possibile indicare se rendere il piano visibile a tutti o solo a specifici clienti (destinatari privati).

##### <a name="enabling-free-trials"></a>Abilitazione di versioni di valutazione gratuite

Le offerte SaaS tramite il Marketplace commerciale ti permettono di offrire una versione di valutazione gratuita di un mese per la vendita tramite Microsoft. Per tutti i modelli di fatturazione e i termini eccetto i piani a consumo, sono supportate le versioni di valutazione gratuite. Questa opzione consente ai clienti di avere una barriera limitata all'ingresso per un mese di accesso gratuito.  Se si sceglie di abilitare una versione di valutazione gratuita per i piani all'interno dell'offerta, il cliente non sarà in grado di eseguire la conversione a una sottoscrizione a pagamento prima della fine del periodo iniziale di un mese.  Durante questo periodo, i clienti che acquistano l'offerta possono provare i piani supportati con la versione di valutazione gratuita abilitata e convertirli tra loro.  La conversione a una sottoscrizione a pagamento viene eseguita automaticamente alla fine del periodo di validità.

>[!Note]
>Se il cliente sceglie di eseguire la conversione in un piano senza versioni di valutazione gratuite, la conversione verrà eseguita, ma la versione di valutazione gratuita andrà persa immediatamente.  Inoltre, una volta che un cliente inizia a pagare per un piano, non potrà più ottenere una versione di valutazione gratuita sulla stessa sottoscrizione, anche se si converte in uno SKU che supporta versioni di valutazione gratuite.

La possibilità di configurare una versione di valutazione gratuita è disponibile per ogni piano nell'offerta. Passare a prezzi e disponibilità per ogni offerta e selezionare la casella per consentire una versione di valutazione di un mese.

![Casella di controllo della versione di valutazione gratuita di un mese](./media/free-trial-enable.png)

>[!Note]
>Quando l'offerta transazionale è stata pubblicata con una versione di valutazione gratuita, non può essere disabilitata per tale piano. Verificare che questa impostazione sia corretta per la prima pubblicazione, in modo da evitare di dover ricreare il piano.

Per ottenere informazioni sulle sottoscrizioni dei clienti che attualmente partecipano a una versione di valutazione gratuita `isFreeTrial`, usare la nuova proprietà API, che verrà contrassegnata come true o false. Per altre informazioni, vedere l' [API di sottoscrizione Saas Get](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Le versioni di valutazione gratuite non sono supportate per i piani che sfruttano il servizio di misurazione del Marketplace.

#### <a name="markets"></a>Mercati

- **Modificare i mercati** (facoltativo)

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per qualsiasi località di mercato in cui si desidera rendere disponibile questo piano. Una casella di ricerca e un pulsante per la selezione dei paesi in cui vengono riportate le imposte, in cui Microsoft si impegnano le vendite e le imposte per conto dell'utente, vengono fornite informazioni utili.

Se sono già stati impostati i prezzi per il piano in Stati Uniti dollari (USD) e si aggiunge un altro percorso di mercato, il prezzo del nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Esaminare il prezzo di ogni mercato prima della pubblicazione. I prezzi possono essere esaminati tramite il collegamento "Esporta prezzi (xlsx)" dopo aver salvato le modifiche.

#### <a name="pricing"></a>Prezzi

- **Modello di determinazione prezzi**: tariffa fissa o basata su postazione

**Frequenza flat:** Consente di accedere all'offerta con un prezzo tariffario singolo mensile o annuale. Questa operazione viene a volte definita prezzi basati sul sito. Con questo modello di determinazione dei prezzi, è possibile definire facoltativamente piani a consumo che usano l'API del servizio di misurazione del Marketplace per addebitare i clienti in base alle unità non standard.  Per altre informazioni sulla fatturazione a consumo, vedere [fatturazione a consumo con il servizio di misurazione del Marketplace](./saas-metered-billing.md).

**Per utente:** Consentire l'accesso all'offerta con il prezzo in base al numero di utenti che accedono all'offerta o occupano le postazioni. Questo modello basato sull'utente consente di impostare il numero minimo e massimo di utenti consentiti in base al prezzo. In questo modo, è possibile configurare punti di prezzo diversi in base al numero di utenti configurando più piani.  Questi campi sono facoltativi. Se viene lasciato deselezionato, il numero di utenti verrà interpretato in modo da non avere un limite (minimo 1 e massimo di quanti ne può supportare il sistema). Questi campi possono essere modificati come parte di un aggiornamento del piano.

Dopo la pubblicazione, non è possibile modificare la scelta del modello di determinazione prezzi per la fatturazione. Inoltre, tutti i piani per la stessa offerta devono condividere lo stesso modello di determinazione prezzi.

- **Termine fatturazione**: mensile o annuale

Selezionare la frequenza con cui i clienti devono pagare il prezzo elencato. È necessario fornire almeno un prezzo mensile o annuale oppure entrambe le opzioni possono essere rese disponibili ai clienti.

- **Prezzo**: USD al mese o USD all'anno

I prezzi impostati in USD (USD = Stati Uniti dollaro) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti quando vengono salvati. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e esaminando il prezzo in ogni mercato. Se si desidera impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi. L'utente è responsabile della convalida dei prezzi ed è proprietario di queste impostazioni.
*\*È innanzitutto necessario salvare le modifiche ai prezzi per consentire l'esportazione dei dati dei prezzi.*

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune restrizioni su cosa può cambiare dopo la pubblicazione di un piano:

- Una volta pubblicato un piano, il modello di determinazione dei prezzi non può essere modificato.
- Una volta pubblicato un periodo di fatturazione per un piano, non sarà possibile rimuoverlo in un secondo momento.
- Una volta pubblicato il prezzo per un mercato nel piano, non è possibile modificarlo in un secondo momento.

### <a name="plan-audience"></a>Pianificare i destinatari

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando Azure AD ID tenant.

#### <a name="privacy"></a>Privacy

- Si **tratta di un piano privato** (casella di controllo facoltativo)

Selezionare questa casella per rendere il piano privato e visibile solo ai destinatari di propria scelta. Una volta pubblicato come piano privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Una volta che un piano è stato pubblicato come visibile a tutti, deve rimanere visibile a tutti gli utenti. Il piano non può essere configurato nuovamente come piano privato.

- **Destinatari con restrizioni (ID tenant)**

Assegnare i destinatari che avranno accesso a questo piano privato. L'accesso viene assegnato usando gli ID tenant con l'opzione per includere una descrizione di ogni ID tenant assegnato. È possibile aggiungere un massimo di 10 ID tenant oppure 20.000 ID tenant dei clienti se si importa un file di foglio di calcolo con estensione CSV.

Un tenant è una rappresentazione di un'organizzazione, con un ID rappresentato come GUID (identificatore univoco globale, un numero intero a 128 bit usato per identificare le risorse). Si tratta di un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando l'organizzazione o lo sviluppatore di app crea una relazione con Microsoft, ad esempio, quando si esegue l'iscrizione ad Azure, Microsoft Intune o Microsoft 365. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD. Per controllare il tenant, accedere al portale di Azure con l'account che si vuole usare per gestire l'applicazione. Se si dispone già di un tenant, verrà effettuata automaticamente la connessione e si vedrà il nome del tenant direttamente sotto il nome dell'account. Se si passa il puntatore sul nome dell'account nella parte superiore destra del portale di Azure, verranno visualizzati il nome utente, l'indirizzo di posta elettronica, un ID di directory/tenant (GUID) e il dominio. Se l'account è associato a più tenant, è possibile selezionare il nome dell'account per aprire un menu che consente di spostarsi tra i tenant. Ogni tenant ha un proprio ID. È anche possibile cercare l'ID tenant dell'organizzazione usando un URL del nome di dominio in [https://www.whatismytenantid.com](https://www.whatismytenantid.com):.

Sebbene le offerte SaaS usino gli ID tenant per definire un pubblico privato, altri tipi di offerta possono usare gli ID sottoscrizione di Azure (che sono anche rappresentati come GUID).

> [!NOTE]
> I destinatari privati (o destinatari limitati) differiscono da un pubblico di anteprima. Nella scheda **[Anteprima](#preview)** è possibile definire un gruppo di destinatari di anteprima. Un pubblico di anteprima è autorizzato ad accedere all'offerta *prima* che l'offerta venga pubblicata dal Marketplace. Sebbene la designazione dei destinatari privati venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno), ma solo durante il periodo di anteprima limitato durante il test e la convalida del piano.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Elenco di esempio di piani all'interno di un'offerta del Marketplace

![Elenco dei piani di Marketplace di esempio con note](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Destinatari rivenditore Cloud Solution Provider (CSP)

La scelta di rendere disponibile l'offerta nel programma CSP consente ai provider di soluzioni cloud di vendere il prodotto come parte di una soluzione in bundle ai clienti. Per altre informazioni, vedere [provider di soluzioni cloud](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Pubblica

Una volta completate tutte le sezioni obbligatorie dell'offerta, selezionare **pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina di **revisione e pubblicazione** .

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Se è la prima volta che si pubblica questa offerta, è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. È necessario tornare alla sezione e aggiornarla.
    - *Complete* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Verrà inviato un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. È necessario tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta al pubblico (o se un'offerta privata, per i destinatari privati).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
