---
title: Domande frequenti sul marketplace commerciale Microsoft
description: Risposte alle domande comuni su Azure Marketplace e Microsoft AppSource
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/4/2019
ms.author: dsindona
ms.openlocfilehash: 6a142ce3c082f6f9a53287885ba347fc78f77718
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262027"
---
# <a name="microsoft-commercial-marketplace-faqs"></a>Domande frequenti sul marketplace commerciale di Microsoft

Risposte alle domande più comuni sul mercato commerciale.

## <a name="faq-for-customers"></a>Domande frequenti per i clienti

### <a name="what-you-need-to-know-about-the-commercial-marketplace"></a>Cosa c'è da sapere sul mercato commerciale

**Che cos'è Azure Marketplace?**

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) fornisce accesso e informazioni per le soluzioni e i servizi messi a disposizione da Microsoft e dai partner. I clienti possono individuare, provare o acquistare soluzioni software cloud basate su o per Azure.Customers can discover, try, or buy cloud software solutions built on or for Azure. Il catalogo, che contiene oltre 8.000 inserzioni, fornisce i blocchi predefiniti per Azure, ad esempio macchine virtuali (VM), API, app di Azure, modelli di soluzione e applicazioni gestite, app SaaS, contenitori e servizi di consulenza.

**Chi sono i clienti di Azure Marketplace?**

Azure Marketplace è progettato per i professionisti IT e gli sviluppatori cloud interessati a software e servizi IT commerciali.

**Quali tipi di prodotti sono attualmente disponibili in Azure Marketplace?**

Azure Marketplace offre soluzioni tecniche e servizi di Microsoft e dei partner creati per estendere i prodotti e i servizi di Azure. Il catalogo delle soluzioni si estende su diverse categorie, tra cui:The solution catalog spans several categories, including:

* sistemi operativi di base
* database
* security
* identity
* networking
* blockchain
* strumenti per sviluppatori

### <a name="azure-marketplace-for-customers"></a>Azure Marketplace per i clienti

**Come si inizia a usare Azure Marketplace?**

Vistando [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps), è possibile trovare un'ampia gamma di soluzioni e applicazioni aziendali certificate e ottimizzate per l'esecuzione in Azure. È possibile accedere ad Azure Marketplace anche tramite il portale di [Azure] quando si [**crea una risorsa.**](https://portal.azure.com/#create/hub)

**Quali sono i vantaggi principali di Azure Marketplace?**

In Azure Marketplace i clienti possono trovare applicazioni tecniche create per Azure o basate su Azure. Questa piattaforma riunisce l'ecosistema di soluzioni e servizi di Microsoft Azure in un'unica posizione dove trovare, provare, acquistare o distribuire soluzioni con pochi clic.

**Come è possibile acquistare i prodotti da Azure Marketplace?**

Le offerte di Azure Marketplace possono essere acquistate tramite:

* [Lo storefront basato sul web](https://azuremarketplace.microsoft.com/marketplace/apps)
* [Portale di Azure][portale di Azure]
* [Interfaccia della riga di comando di AzureThe Azure Command Line Interface (CLI)](/cli/azure/?view=azure-cli-latest)

>[!Note]
>non è possibile usare crediti prepagati e altre forme di impegno monetario per pagare le tariffe per le licenze software, ma è possibile usarli per pagare gli addebiti per l'utilizzo di Azure associati. Le eccezioni sono indicate nella pagina relativa all'[impegno monetario di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

**È possibile scegliere quali aree di Azure distribuire l'acquisto di Azure Marketplace?**

Il cliente ha la possibilità di distribuire in qualsiasi area del data center di Azure abilitata. È consigliabile selezionare le località dei data center più vicine ai propri servizi in modo da ottimizzare le prestazioni e tenere sotto controllo il budget.

**Se si elimina accidentalmente un acquisto di Azure Marketplace, è possibile *annullare* l'azione?**

No, non è possibile annullare le operazioni di eliminazione. Se una sottoscrizione viene eliminata accidentalmente, è possibile effettuare di nuovo l'acquisto. Eventuali funzionalità inutilizzate o servizi prepagati andranno persi.

**Si riceve un avviso se si cerca di eliminare un acquisto di Azure Marketplace in uso dalle applicazioni?**

No, Azure non invia avvisi quando si elimina un acquisto, anche se è attualmente in uso o se un'applicazione dipende da esso.

**Se sono presenti relazioni di dipendenza tra l'acquisto di Azure Marketplace e altri asset, come un sito Web di Azure, è necessario gestirle?**

Le dipendenze per le offerte di Azure Marketplace non vengono gestite automaticamente. Esaminare con attenzione la descrizione di ogni acquisto di Azure Marketplace prima dell'uso, in modo da determinare se sono presenti dipendenze di cui tenere conto prima della distribuzione della soluzione.

**È possibile acquistare soluzioni di Azure Marketplace da un Azure Cloud Solution Provider?**

Se l'editore ha configurato l'offerta in modo che sia disponibile tramite il canale Cloud Solution Provider (CSP), i partner Cloud Solution Provider hanno la possibilità di rivendere la soluzione.

**Quali paesi/aree geografiche sono supportati per l'acquisto di applicazioni e servizi venduti/di cui è stato eseguito il provisioning tramite Azure Marketplace?**

Azure Marketplace è disponibile per i clienti di Azure nei paesi/aree geografiche elencati nei [criteri di partecipazione.](/legal/marketplace/participation-policy)

**Quali valute sono supportate da Azure Marketplace?**

Le transazioni possono essere effettuate nelle seguenti 17 valute: AUD, BRL, CAD, CHF, DKK, EUR, GBP, INR, JPY, KRW, NOK, N -D, RUB, SEK, TWD, USD, RMB.

### <a name="deploying-a-solution-from-azure-marketplace"></a>Distribuzione di una soluzione da Azure Marketplace

**Ho distribuito una macchina virtuale (VM) di Azure Marketplace in una sottoscrizione e ora voglio eseguire la migrazione della sottoscrizione da un account Azure a un altro. Questo è attualmente supportato?**

Per eseguire la migrazione di una sottoscrizione di Azure, inclusi i servizi e le macchine virtuali di Azure Marketplace, è necessario eliminare o annullare ogni sottoscrizione di Azure precedente prima di eseguire l'associazione al nuovo account Azure. Una volta completata la migrazione, le tariffe di utilizzo risultanti vengono fatturate utilizzando il metodo di pagamento del nuovo account registrato.

**Si vuole eseguire la migrazione di una sottoscrizione di macchina virtuale (VM) di Azure Marketplace al contratto Enterprise Agreement. Questo è attualmente supportato?**

Per eseguire la migrazione di una sottoscrizione di macchina virtuale di Azure Marketplace a un contratto Enterprise Agreement, arrestare o annullare ogni sottoscrizione precedente prima di procedere. Al termine della migrazione dell'account azure e delle sottoscrizioni associate, è possibile riacquistare la macchina virtuale o il servizio di Azure Marketplace.Once the migration of your Azure account and associated subscriptions is complete, you can repurchase the Azure Marketplace VM or service.  Le tariffe di utilizzo risultanti vengono fatturate su base trimestrale nel contratto Enterprise Agreement.

### <a name="pricing-and-payment"></a>Prezzi e pagamento

**Come vengono stabiliti i prezzi delle sottoscrizioni di Azure Marketplace?**

I prezzi variano in base ai tipi di prodotto e alle specifiche dell'editore. Le tariffe per le licenze software e i costi di utilizzo di Azure vengono addebitati separatamente tramite la sottoscrizione di Azure.

*Non in bundle:*

+ *Modello BYOL (Bring-Your Own-license):* quando si ottiene una licenza software direttamente dall'editore o dal rivenditore, non sono previsti costi o costi aggiuntivi relativi al software.

*In bundle:*

La sottoscrizione di Azure è inclusa nei prezzi delle soluzioni ISV (Independent Software Vendors) dell'editore.

*Addebitato:*

+ *Gratis:* SKU gratuito. Non vengono addebitati costi per le licenze software o l'utilizzo dell'offerta.

+ *Versione di prova del software gratuito:* Un'offerta gratuita per un periodo limitato. Non sono previsti costi di licenza software dell'editore da utilizzare durante il periodo di prova. Alla scadenza, viene automaticamente eseguita la conversione in offerta a pagamento in base alle tariffe standard previste dall'editore.

+ *Basato sull'utilizzo:* Le tariffe vengono addebitate o fatturate in base all'estensione dell'utilizzo dell'offerta. Per le immagini di macchine virtuali, viene addebitata una tariffa oraria. Per i servizi per sviluppatori e le API, viene addebitata una tariffa per unità di misura definita dall'offerta.

+ *Tariffa forfettaria:* Gli abbonamenti SaaS possono essere valutati come una tariffa fissa che viene fatturata mensilmente o annualmente. Questo può anche includere dimensioni di fatturazione aggiuntive che vengono addebitate in base al consumo (ad esempio, larghezza di banda, e-mail o ticket). 

+ *Per utente:* Gli abbonamenti SaaS possono avere un prezzo per utente che viene fatturato mensilmente o annualmente. 

I dettagli sui prezzi specifici dell'offerta [https://azure.microsoft.com/pricing/](https://azure.microsoft.com/pricing/) sono disponibili nella pagina dei dettagli della soluzione nel portale di Azure o all'interno di [office.]

> [!Note]
> Gli addebiti per l'utilizzo di Azure, ad eccezione delle tariffe mensili, sono applicabili a tutti i modelli di determinazione dei prezzi, se non diversamente indicato.

**Come si deve fornire il codice di licenza software per le soluzioni BYOL del marketplace e qual è il ruolo di Azure Marketplace?**

L'acquisizione e l'applicazione delle credenziali di licenza per le soluzioni BYOL sono di responsabilità dell'editore. Per le offerte di macchine virtuali, l'acquisizione del codice di licenza avviene in genere nell'applicazione dell'editore dopo l'avvio dell'applicazione. Quando si usa un'offerta di macchine virtuali distribuita tramite un modello di soluzione dell'applicazione Azure, il modello di Resource Manager può essere configurato per richiedere all'utente un intervallo di input, incluse le credenziali di licenza.

Ecco le opzioni più comuni per ogni tipo di offerta:

*Offerta macchina virtuale:*

+ *Opzione 1:* L'acquisizione del codice di licenza avviene in genere nell'applicazione dell'editore dopo l'avvio dell'applicazione.

+ *Opzione 2:* Il codice di licenza viene immesso dall'utente finale (tramite la riga di comando / interfaccia web fornita dall'offerta) dopo la distribuzione dell'offerta VM nella sottoscrizione selezionata. La licenza può essere costituita da un codice e/o un file, a discrezione dell'editore.

*App di Azure (modello di soluzione e app gestite):*

+ *Opzione 1:* Il modello Resource Manager può essere configurato per richiedere un intervallo di input, incluse le credenziali di licenza. Le credenziali possono essere fornite sotto forma di file di licenza (caricamento di file) o di codice (immissione di testo), prima della distribuzione dell'offerta, nella sottoscrizione dell'utente finale.

+ *Opzione 2:* È possibile immettere la chiave di licenza tramite la riga di comando / interfaccia web fornita dall'offerta.  Questa operazione viene eseguita dopo la distribuzione dell'offerta di app di Azure nella sottoscrizione selezionata. La licenza può essere costituita da un codice e/o un file, a discrezione dell'editore.

**Quali tipi di prove sono supportate?**

Gli editori possono aggiungere un mese gratuito per le offerte SaaS a pagamento e uno o tre mesi di consumo gratuito per le immagini VM. Le offerte di prova gratuite sono elenchi con un invito all'azione per avviare una prova. Questi portano il cliente a un sito web definito dall'editore per impostare l'esperienza di prova. Le prove possono anche essere aggiunte alle offerte a pagamento in cui il primo mese è gratuito. 

**È necessario avere registrato uno strumento di pagamento (ad esempio una carta di credito) per distribuire offerte del livello gratuito o Bring Your Own License (BYOL)?**

No. Non è necessario alcuno strumento di pagamento per distribuire offerte del livello gratuito o Bring Your Own License (BYOL). Al contrario, le offerte in versione di valutazione gratuita richiedono uno strumento di pagamento. Le inserzioni che includono i pulsanti **Scarica adesso** o **Versione di valutazione gratuita del software** vengono distribuite nella sottoscrizione di Azure selezionata.  Queste inserzioni vengono fatturate utilizzando il metodo di pagamento registrato dell'account selezionato. Gli addebiti per l'utilizzo di Azure vengono fatturati separatamente dalle tariffe di licenza software.

**A chi deve rivolgersi un cliente indiretto con contratto Enterprise Agreement per domande sui prezzi per le offerte vendute in Azure Marketplace?**

I clienti indiretti con contratto Enterprise Agreement devono rivolgersi al loro LSP (Licensing Solution Provider) per informazioni sui prezzi di Azure Marketplace.

**È possibile controllare l'accesso dei dipendenti ad Azure Marketplace e i privilegi di acquisto?**

Sì, per i clienti con contratto Enterprise Agreement l'amministratore delle registrazioni può disattivare i privilegi di acquisto per tutti gli account della registrazione e riattivarli per il tempo necessario per effettuare un acquisto.

**Quali metodi di pagamento sono supportati per gli acquisti sul mercato commerciale?**

I clienti possono acquistare offerte dal mercato commerciale utilizzando le carte di credito. Se si dispone di una sottoscrizione di Azure esistente, gli acquisti da Azure Marketplace utilizzeranno il metodo di pagamento configurato nell'account e verranno visualizzati nella stessa fattura di una voce separata. Alcune offerte utilizzano l'impegno monetario di Azure, ma la maggior parte degli acquisti sul marketplace commerciale non comporta l'essenza degli impegni del contratto Enterprise Agreement anche se lo sarà il consumo dell'infrastruttura di Azure.Some offers consume Azure monetary commitment, but most commercial marketplace purchases do not draw down Enterprise Agreement commitments although Azure infrastructure consumption will.

**È possibile applicare i crediti della sottoscrizione di Azure o i fondi dell'impegno monetario dell'account alle offerte di Azure Marketplace?**

Alcune offerte specifiche di Azure Marketplace possono usare i crediti delle sottoscrizioni di Azure o i fondi dell'impegno monetario. Per un elenco completo dei prodotti che partecipano al programma, vedere la pagina relativa all'[impegno monetario di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/). Queste offerte non includono le opzioni BYOL e BYOS. Tutte le altre offerte di Azure Marketplace non possono usare crediti di sottoscrizione di Azure o impegno monetario, ad esempio il credito di prova gratuito di un mese, i crediti MSDN mensili, i crediti delle promozioni di Azure, i saldi dell'impegno monetario e qualsiasi altro credito gratuito fornito da Azure.All other Azure offers cannot use Azure subscription credits or monetary commitment: such as the free one-month trial credit, monthly MSDN credits, credits from Azure promotions, monetary commitment balances, and any other free credits provided from Azure.

**Gli sconti per i contratti multilicenza si applicano agli acquisti in Azure Marketplace?**

No. I prezzi vengono stabiliti dall'editore proprietario delle soluzioni in Azure Marketplace.  Gli sconti standard per contratti multilicenza Microsoft non sono applicabili agli acquisti in Azure Marketplace.

**Dove è possibile visualizzare i dettagli della sottoscrizione di Azure Marketplace e le informazioni di fatturazione?**

MOSP - I clienti del programma di [abbonamento online Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/?country=us&language=en) (web direct) possono visualizzare i dettagli di acquisto di Marketplace nella scheda Fatture della sezione Gestione costi e fatturazione del portale di [Azure.]

I clienti con contratto Enterprise Agreement (EA) possono visualizzare i dettagli di acquisto del Marketplace nella scheda Fatture della sezione Gestione costi e fatturazione del portale di [Azure.]

I partner Cloud Solution Provider (CSP) possono visualizzare i dettagli di acquisto di Marketplace per ogni cliente nel Centro per i partner all'interno della scheda Cronologia ordini dopo aver selezionato una società nella visualizzazione Clienti.

**Come si annulla un componente aggiuntivo di Azure Marketplace per una macchina virtuale di Azure?**

Poiché il componente aggiuntivo è associato alla macchina virtuale di Azure, per annullare l'acquisto di Azure Marketplace è prima necessario arrestare la macchina virtuale eliminandola. In questo modo vengono interrotti l'utilizzo e gli addebiti di tutte le sottoscrizioni per l'acquisto di Azure Marketplace.

**Qual è la frequenza di emissione delle fatture per gli acquisti di Azure Marketplace?**

Per tutte le offerte di Azure Marketplace non deducibili dall'impegno monetario, la fatturazione viene effettuata ogni mese in modo posticipato. Gli abbonamenti Annual SaaS vengono fatturati una volta per i servizi di un anno intero.

MOSP: i clienti del programma di abbonamento online Microsoft (web direct) vengono addebitati mensilmente sulla stessa carta di credito archiviata per il profilo di sottoscrizione di Azure.MOSP - [Microsoft Online Subscription Program](https://azure.microsoft.com/support/legal/subscription-agreement/) (web direct) customers are charged monthly against the same credit card that is on file for their Azure subscription profile. Gli abbonamenti Annual SaaS vengono fatturati una volta per i servizi di un anno intero.

**Come è possibile spostare gli acquisti di Azure Marketplace dalla sottoscrizione MOSP alla sottoscrizione diretta con contratto Enterprise Agreement?**

Benché la maggior parte delle sottoscrizioni Microsoft possa essere facilmente convertita nel contratto Enterprise Agreement, per gli acquisti di Azure Marketplace all'interno di tali sottoscrizioni la conversione non è possibile.

Per eseguire la migrazione di altri servizi acquistati da Azure Marketplace a una sottoscrizione con contratto Enterprise Agreement, è prima necessario annullare le applicazioni all'interno della sottoscrizione MOSP esistente e quindi acquistarle di nuovo all'interno della sottoscrizione con contratto Enterprise Agreement. Se si esegue questa operazione, è possibile inviare una richiesta di credito per ottenere un rimborso per il potenziale mese di sovrapposizione della copertura tra le due sottoscrizioni del servizio Marketplace. A questo scopo, creare un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Qual è la differenza tra *price*, *software price*e *total price* nella struttura dei costi per le offerte di Macchine virtuali in Azure Marketplace?**

*Il prezzo* si riferisce al costo della macchina virtuale di Azure per eseguire il software. *Il prezzo del software* si riferisce al costo del software dell'editore del Marketplace in esecuzione su una macchina virtuale di Azure.Software price refers to the cost of the Marketplace publisher's software running on an Azure Virtual Machine. *Il prezzo totale* si riferisce al costo totale combinato della macchina virtuale di Azure e del software dell'editore del Marketplace in esecuzione in una macchina virtuale di Azure.Total price refers to the combined total cost of the Azure Virtual Machine and the Marketplace publisher's software running on an Azure Virtual Machine.

**Come è possibile verificare la quantità di acquisti effettuati in Azure Marketplace?**

Informazioni sull'utilizzo stimato sono disponibili nel portale di [Azure.] Queste informazioni possono non includere le attività recenti e possono essere basate sulle proiezioni derivate da dati sull'uso relativi a periodi precedenti. Durante l'anteprima pubblica questa funzionalità potrebbe non essere disponibile per tutti gli acquisti e variare in base al tipo di prodotto.

### <a name="customer-support"></a>Assistenza clienti

**Chi è possibile contattare per problemi di supporto generali relativi ad Azure Marketplace?**

Per il supporto generale per le applicazioni in merito all'utilizzo o alla risoluzione dei problemi, contattare direttamente l'editore dell'applicazione.

Per problemi di fatturazione e sottoscrizione relativi all'acquisto di Azure Marketplace, contattare il [supporto tecnico di Azure](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953).

**Chi posso contattare per ottenere supporto tecnico per una soluzione acquistata in Azure Marketplace?**

Per il supporto tecnico per tutti i prodotti, contattare il provider dell'editore. Le informazioni di contatto dell'editore e/o il collegamento al sito Web di supporto sono disponibili nella pagina dei dettagli della soluzione in Azure Marketplace.

**Chi è possibile contattare per ottenere supporto per la fatturazione o per domande su una soluzione di terze parti acquistata in Marketplace?**

Contattare il supporto tecnico Microsoft nella pagina del [supporto tecnico di Azure](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953).

**Chi è possibile contattare in caso di domande sui prezzi o sulle condizioni per le soluzioni dei partner vendute in Azure Marketplace?**

Per il supporto tecnico per tutti i prodotti, contattare il provider dell'editore. Le informazioni di contatto dell'editore e/o il collegamento al sito Web di supporto sono disponibili nella pagina dei dettagli di ogni soluzione in Azure Marketplace.

**È possibile restituire un prodotto se non si è soddisfatti dell'acquisto?**

Gli acquisti effettuati da [Azure Marketplace](https://azuremarketplace.microsoft.com/) non possono essere restituiti, ma possono essere annullati o eliminati. Le offerte basate sul consumo vengono fatturate in base all'utilizzo, quindi quando si arresta, anche le spese si interrompono. Gli abbonamenti vengono annullati e non verranno fatturati oltre il periodo di fatturazione corrente. Se un abbonamento viene annullato poco dopo l'acquisto (24 ore per mensile e 14 giorni all'anno), viene fornito un rimborso completo.

Per qualsiasi problema tecnico relativo all'acquisto o al servizio di Marketplace, i clienti devono contattare direttamente l'editore. Le informazioni di contatto dell'editore e/o il collegamento al sito Web di supporto sono disponibili nella pagina dei dettagli della soluzione in Azure Marketplace.

**Come vengono gestite le aggiunte alle licenze quando aumenta il medio termine?**

Le licenze aggiunte a un abbonamento esistente vengono ripartite proporzionalmente per il resto della durata dell'abbonamento.

**Come vengono gestite le rimozioni delle licenze a metà termine?**

Le licenze annullate saranno soggette alla politica di rimborso disponibile in questa FAQ.  Tutte le licenze annullate vengono immediatamente rimosse dal tuo account e non saranno più disponibili per l'uso.

**Sono supportati i rimborsi per le offerte basate sul consumo?**

Eventuali addebiti basati sul consumo, che si tratti di macchine virtuali orarie o contatori personalizzati, non sono rimborsabili tramite annullamento. Una volta che il consumo è avvenuto, gli addebiti vengono elaborati dalla piattaforma di commercio di Microsoft e qualsiasi controversia sulla qualità del servizio in cui è richiesto un rimborso verrebbe gestita al di fuori dei sistemi Microsoft tra l'editore e il cliente direttamente. Le offerte che supportano una tariffa forfettaria più la fatturazione a consumo seguono la politica di rimborso standard per le tariffe forfettarie.

**È possibile cambiare i piani a medio termine?**

No, non è possibile passare dal mese all'annuale.

**Un cliente può acquistare due piani dalla stessa offerta?**

Sì, è possibile che un cliente possieda due piani dalla stessa offerta contemporaneamente.

**I criteri di rimborso e di modifica del piano differiscono in base allo storefront?**

No, le politiche aziendali sono coerenti in tutto il mercato commerciale. Se rivenduto da un partner nel programma Cloud Solution Provide, il partner può applicare criteri diversi per i propri clienti.


## <a name="faq-for-publishers"></a>Domande frequenti per gli editori

### <a name="what-you-need-to-know-about-the-commercial-marketplace"></a>Cosa c'è da sapere sul mercato commerciale

**Che cos'è Azure Marketplace?**

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) è un marketplace di servizi e applicazioni online. I clienti (prevalentemente professionisti IT e sviluppatori) possono trovare, provare e acquistare soluzioni software cloud create con o per Azure. Il catalogo contiene oltre 8.000 inserzioni, tra cui i blocchi predefiniti per Azure, ad esempio macchine virtuali (VM), API, modelli di soluzione, applicazioni SaaS e offerte di servizi di consulenza.

Azure Marketplace è il trampolino di lancio per tutte le attività go-to-market in collaborazione con Microsoft.  Lo scopo è quello di aiutare i partner a raggiungere più clienti. È possibile pubblicare nuove inserzioni e usare Azure Marketplace per condurre campagne promozionali e di generazione della domanda, oltre che svolgere attività di vendite/marketing in collaborazione con Microsoft.

**Chi sono i clienti di Azure Marketplace?**

Azure Marketplace si rivolge ai professionisti IT e agli sviluppatori cloud interessati a servizi e software IT commerciali.

### <a name="azure-marketplace-for-publishers"></a>Azure Marketplace per gli editori

**Perché è utile pubblicare un'applicazione in Azure Marketplace e quali sono i vantaggi?**

Azure Marketplace offre ai partner Microsoft un'area commerciale in cui promuovere e vendere i propri prodotti e servizi ai clienti di Azure. Gli editori possono accedere istantaneamente a 140 mercati globali, ai nostri oltre 300.000 partner e alla rete di clienti aziendali di Azure.  Il mercato comprende più del 90% delle aziende Fortune 500 e molti dei principali sviluppatori del mondo. Ai nuovi partner di Azure Marketplace viene offerta automaticamente una serie di [vantaggi Go-To-Market a costo zero](gtm-your-marketplace-benefits.md#list-trial-and-consulting-benefits) per aumentare la consapevolezza delle offerte nel marketplace di Azure.

**Qual è il fattore discriminante che differenzia Azure Marketplace da AppSource?**

I partner Microsoft possono scegliere dove pubblicare in base al loro pubblico di destinazione.

Microsoft provides two distinct cloud marketplace storefronts - Azure Marketplace and AppSource. Queste vetrine permettono ai clienti di trovare, provare e acquistare applicazioni e servizi cloud. Ogni vetrina si rivolge a clienti con esigenze specifiche e consente ai partner Microsoft di offrire le soluzioni o i servizi appropriati in base al pubblico di destinazione.

Scegliere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) per rivolgersi a professionisti IT, sviluppatori o utenti tecnici.

Scegliere [AppSource](https://appsource.microsoft.com/) per rivolgersi a decision maker line-of-business e proprietari di aziende.

Esaminare la [Guida alla pubblicazione](marketplace-publishers-guide.md) per informazioni dettagliate sui vantaggi di Azure Marketplace e AppSource.

**È necessario essere membri Microsoft Partner Network (MPN) per pubblicare inserzioni per applicazioni e servizi in Azure Marketplace?**

Sì, per la pubblicazione in Azure Marketplace è necessario essere membri MPN. Visitare la pagina di [Microsoft Partner Network](https://partner.microsoft.com/membership) per iniziare.

**Quali sono i criteri per pubblicare una soluzione in Azure Marketplace?**

Per pubblicare in Azure Marketplace, i partner devono dimostrare che l'applicazione viene eseguita in Azure o ne estende le funzionalità. Gli editori sono tenuti a fornire ai clienti un contratto di [servizio,](https://azure.microsoft.com/support/legal/sla/)un'informativa [sulla privacy](https://privacy.microsoft.com/privacystatement)e un supporto telefonico e online. Diversi carichi di lavoro prevedono requisiti aggiuntivi. Per altre indicazioni, vedere [Azure Marketplace Participation Policies](./marketplace-participation-policy.md) (Criteri di partecipazione ad Azure Marketplace) e la [Guida alla pubblicazione](marketplace-publishers-guide.md).

**Sono previste tariffe per la pubblicazione in Azure Marketplace?**

Non sono previste tariffe di pubblicazione quando si pubblica un'inserzione per un'offerta gratuita, una versione di valutazione o una soluzione BYOL (Bring Your Own License) tramite Azure Marketplace.

**Sono previste tariffe per le transazioni per gli acquisti effettuati tramite Azure Marketplace?**

Quando la licenza di una soluzione viene acquistata tramite Azure Marketplace, i ricavi per la licenza software vengono suddivisi tra l'editore e Microsoft.  Ciò avviene in base ai termini e alle condizioni del [contratto per gli editori di Marketplace](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf). Per le soluzioni Bring Your Own License (BYOL), agli editori non verranno inoltre addebitate tariffe per le transazioni.

**Che cos'è un contratto standard?**

Microsoft offre termini di contratto standard che un editore può selezionare per sfruttare, in modo che i clienti abbiano un approvvigionamento semplificato e un processo di revisione legale.

**Dove è possibile trovare le indicazioni per integrare un'applicazione con Azure Active Directory (AAD)?**

Microsoft usa AAD per autenticare tutti gli utenti di Marketplace.  È possibile ricevere direttamente una versione di valutazione senza effettuare ulteriori passaggi di accesso.  Ad esempio, quando un utente autenticato fa clic su un'inserzione per una versione di valutazione in Marketplace, viene reindirizzato a un ambiente di valutazione.

Per altre informazioni e per iniziare ad abilitare una versione di valutazione con AAD, vedere la [sezione di Azure Active Directory nella Guida alla pubblicazione](enable-appsource-marketplace-using-azure-ad.md).

**Come si avvia la registrazione al Dev Center?**

Per evitare duplicati, gli editori devono prima di tutto verificare che non sia già stato registrato un [account Dev Center](deprecated/register-dev-center.md). Una volta effettuata questa verifica, il passaggio successivo consiste nell'eseguire la registrazione [accedendo](https://account.microsoft.com/account/) con un account Microsoft, che verrà associato all'account per sviluppatori.

Se non si dispone già di un account Microsoft, è contoso_marketplace@live.compossibile creare un [account](https://signup.live.com/) (ad esempio: ).

**Perché è necessario disporre di un account Dev Center?**

L'account Dev Center consente a Microsoft di fatturare ai clienti per conto dell'editore per i tipi di inserzione transazione. La registrazione dell'account Dev Center consente a Microsoft di convalidare le informazioni legali, fiscali e bancarie. Per altre informazioni, vedere [Eseguire la registrazione in Dev Center](./partner-center-portal/create-account.md).

**Che cosa sono i lead e perché sono importanti per chi pubblica in Marketplace?**

I lead sono i clienti che distribuiscono i prodotti dal Marketplace. Che l'inserzione del prodotto sia pubblicata in [Azure Marketplace](https://azuremarketplace.microsoft.com) o [AppSource](https://appsource.microsoft.com), è possibile ricevere lead dei clienti interessati al prodotto.  È possibile definire i lead destinatari per un'offerta. Per altre informazioni, vedere [Diventare un editore del Marketplace cloud](./partner-center-portal/create-account.md).

**Dove è possibile ottenere assistenza per configurare la destinazione dei lead?**

Per ulteriori informazioni, vedere La documentazione relativa ai clienti del [Centro per](./cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads.md) i partner o inviare un ticket di supporto [https://aka.ms/marketplacepublishersupport](https://aka.ms/marketplacepublishersupport) selezionando il tipo di offerta e la gestione dei lead.

**È necessario configurare i lead destinatari per pubblicare un'offerta in Marketplace?**

Sì, se si pubblica un'offerta di tipo **Contattami**, **app SaaS** o **servizi di consulenza**, è necessario configurare i lead destinatari.

**Come è possibile verificare che la configurazione dei lead sia corretta?**

Dopo aver completato l'offerta e aver impostato una destinazione lead, l'inserzione può essere pubblicata correttamente nel Centro per i [partner.](https://cloudpartner.azure.com/) Prima della pubblicazione dell'inserzione, è possibile verificare se la configurazione dei lead è corretta.  Inviare un lead di test per i lead destinatari configurati nell'offerta.

**Da quali paesi/aree geografiche gli editori possono vendere in Azure Marketplace?**

Gli editori con sede nei seguenti paesi/aree geografiche possono attualmente vendere nel Marketplace di Azure: Afghanistan, Albania, Algeria, Angola, Antigua e Barbuda, Argentina, Armenia, Australia, Austria, Azerbaigian, Bahrein, Bangladesh, Bielorussia, Belgio, Benin, Bolivia, Bosnia-Erzegovina, Botswana, Brasile, Bulgaria, Burkina Faso, Burundi, Cambogia, Camerun, Canada, Repubblica Centrafricana, Ciad, Cile, Colombia, Comore, Congo, Costa Rica, Costa D'Ivoire, Croazia, Cipro, Repubblica Ceca, Danimarca, Dominica, Repubblica Dominicana, Ecuador, Egitto, El, Eritrea, Salvador, Etiopia, Isole Fiji, Finlandia, Francia, Georgia, Germania, Ghana, Grecia, Guatemala, Guinea, Haiti, Honduras, Hong Kong SAR, Ungheria, Islanda, India, Indonesia, Iraq, Irlanda, Israele, Italia, Giamaica, Giappone, Giordania, Kaz Khkistan, Kenya, Corea (Sud), Kuwait, Laos, Lettonia, Libano, Liberia, Liechtenstein, Lituania, Lussemburgo, Madagascar, Malawi, Malesia, Mali, Malta, Mauritius, Messico, Monaco, Mongolia, Montenegro , Marocco, Mozambico, Nepal, Olanda, Nuova èelanda, Nicaragua, Niger, Nigeria, Norvegia, Oman, Pakistan, Panama, Paraguay, Perù, Filippine, Polonia, Portogallo, Qatar, Romania, Russia, Ruanda, Arabia Saudita, Senegal, Serbia, Sierra Leone, Singapore, Slovacchia, Slovenia, Somalia, Sud Africa, Spagna, Sri Lanka, Svezia, Svizzera, Tagikistan, Tanzania, Thailandia, Timor-Leste, Togo, Tonga, Trinidad e Tobago, Tunisia, Turchia, Turkmenistan, Uganda, Ucraina, Emirati Arabi Uniti, Regno Unito, Stati Uniti, Uruguay, Uzbekistan, Venezuela, Vietnam, zombia, .

**Come si elimina un'inserzione da Azure Marketplace?**

*Macchina virtuale & le app di Azure:Virtual Machine & Azure Apps:*

1. Accedere al [Centro per i partner](https://cloudpartner.azure.com/).
1. Seleziona l'offerta dalla scheda **Tutte le offerte.**
1. Nel riquadro sul lato sinistro dello schermo selezionare la scheda **SKU.**
1. Selezionare lo SKU per l'eliminazione e fare clic sul pulsante **elimina** per tale SKU.
1. [Ripubblicare](./cloud-partner-portal/manage-offers/cpp-publish-offer.md) l'offerta su Azure Marketplace.

Per altre informazioni, vedere [Eliminare le offerte](./cloud-partner-portal/manage-offers/cpp-delete-offer.md).

*Web Apps (applicazioni SaaS, componenti aggiuntivi) & servizi di consulenza:*

1. Nel Centro per i partner selezionare l'icona del punto interrogativo e quindi fare clic su **Supporto**.
1. Passare a <https://go.microsoft.com/fwlink/?linkid=844975>.
1. Nella pagina del supporto selezionare il tipo di offerta.
1. Seleziona **Rimuovi** un'offerta pubblicata.
1. Creare un ticket di supporto.
1. Inviare.

*Applicazioni Di O365:*

1.    Accedi al <https://sellerdashboard.microsoft.com> tuo account Dev.
1.    Ritirare il componente aggiuntivo.

    > [!NOTE]
    > Le app scompaiono da una presentazione esistente dopo 90 giorni.

**Perché le modifiche apportate non si riflettono nell'offerta?**

Le modifiche apportate all'interno del Centro per i partner vengono aggiornate nel sistema e i fronti dei negozi solo dopo aver ripubblicato l'offerta. Assicurati di aver inviato l'offerta per la pubblicazione dopo eventuali modifiche.

### <a name="benefits-and-go-to-market-gtm-resources"></a>Vantaggi e risorse go-to-market

**Quali sono i vantaggi go-to-market offerti agli editori che pubblicano inserzioni in Azure Marketplace?**

Azure Marketplace è un trampolino di lancio per le attività go-to-market in collaborazione con Microsoft e la porta di accesso a opportunità di partnership per il co-selling. A tutti i nuovi elenchi in Azure Marketplace viene offerta automaticamente una serie di [vantaggi Go-To-Market a costo zero](https://assetsprod.microsoft.com/mpn/marketplace-gtm-benefits.pdf) per aumentare la consapevolezza delle offerte ai clienti Microsoft. Una volta pubblicata un'offerta, il team di Microsoft GTM contatta l'utente e inizia a offrire i vantaggi.

Visitare [Microsoft Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm) per altre informazioni sui vantaggi go-to-market e su come favorire la crescita del business nel marketplace.

**Dove vengono promosse le soluzioni di Azure Marketplace nelle proprietà Web Microsoft?**

Le soluzioni di Azure Marketplace sono disponibili nel portale di Microsoft [Azure]e nel sito Web di [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/) Gli sviluppatori cloud e i professionisti IT che usano Azure visualizzano le soluzioni dei partner a ogni accesso. Un sottoinsieme di soluzioni dei partner viene anche presentato a rotazione nella [home page di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) e nella [pagina delle soluzioni di Azure](https://azure.microsoft.com/solutions/).

### <a name="billing-and-payments"></a>Fatturazione e pagamenti

**Come si ricevono i pagamenti per le vendite realizzate su Azure Marketplace?**

Tutti i pagamenti da parte di Microsoft vengono elaborati tramite PayPal o bonifico mensile. I pagamenti vengono effettuati entro due mesi dalla data in cui il cliente ha usato il servizio, ma le tempistiche esatte dipendono dallo strumento di pagamento del cliente. Ai clienti che pagano tramite carta di credito viene applicato un periodo di garanzia di 45 giorni.

**Per le soluzioni basate su macchina virtuale acquistate tramite la fatturazione in base all'utilizzo, quando un cliente aumenta o riduce le dimensioni della macchina virtuale sottostante, il prezzo della licenza software cambia di conseguenza?**

Sì, il nuovo prezzo viene fatturato immediatamente.  I prezzi cambiano quando un cliente modifica le dimensioni della macchina virtuale e specifica prezzi diversi nella tabella dei prezzi, basati sulle dimensioni della macchina virtuale.

**La fatturazione per nodo è disponibile per Azure Marketplace?**

Azure Marketplace al momento non supporta la fatturazione per nodo per le macchine virtuali. Gli editori possono comunque determinare una tariffa di fatturazione per nodo con le tariffe delle macchine virtuali Microsoft.  A tale scopo, è necessario moltiplicare il numero di macchine virtuali per il numero di ore di utilizzo e la tariffa oraria.

**Chi è possibile contattare per domande sulla fatturazione o sulla gestione delle offerte?**

Inviare un ticket al [supporto tecnico Microsoft](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=15635).

### <a name="publisher-support"></a>Supporto tecnico per gli editori

**Chi è possibile contattare per problemi di supporto generali relativi ad Azure Marketplace?**

Per il supporto generale delle applicazioni in merito all'usabilità o alla risoluzione dei problemi, contattare il supporto del Centro per i [partner.](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636565784998876007)

Per problemi di fatturazione e sottoscrizione relativi all'acquisto di Azure Marketplace, contattare il [supporto tecnico di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Chi è possibile contattare per domande sulla pubblicazione o sulla gestione delle offerte?**

Visita la [guida all'editore](marketplace-publishers-guide.md) del marketplace commerciale per informazioni aggiornate sulle risorse e la documentazione sulle domande frequenti. Inoltre, è possibile registrare un ticket con [il supporto Microsoft nel Centro per](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953)i partner .

### <a name="azure-marketplace-for-publishers"></a>Azure Marketplace per gli editori

**Come faccio a definire la mia disponibilità geografica per consentire la vendita in diversi paesi/aree geografiche?**

1.  Nel Centro per i partner passare allo SKU a cui si desidera aggiungere nuovi paesi.  All'interno dei **dettagli dello SKU**, passare a **Disponibilità paese e** fare clic su Seleziona **aree**.

    ![Selezionare le regioni](media/marketplace-publishers-guide/FAQ-choose-geo.png)

1.  Verrà visualizzato un elenco con tutti i paesi/aree geografiche disponibili a cui vendere.  Fare clic sulla casella di controllo accanto a ogni paese/area geografica che si desidera rendere disponibile questo SKU. Fare clic su **OK**.

    ![Seleziona elenco paesi/aree geografiche](media/marketplace-publishers-guide/FAQ-select-countries.png)

1.  Infine, per le modifiche da applicare all'offerta live, fai clic su **Pubblica**.  

    > [!Note]
    > Ci vogliono 24 ore per rendere effettive le modifiche.

<!---    ![Publish offer](media/marketplace-publishers-guide/FAQ-publish-offer.png) -->

**In che modo un publisher può modificare la disponibilità geografica per un'offerta esistente?**

L'editore può modificare un'offerta esistente, selezionare i nuovi paesi/aree geografiche e utilizzare la funzione di download/caricamento del foglio di calcolo per impostare i prezzi.

**In quali paesi/aree geografiche i clienti possono acquistare offerte di Azure Marketplace?**

Azure Marketplace supporta 141 aree geografiche di buy-from '. Vedere [le politiche](/legal/marketplace/participation-policy) di partecipazione per un elenco di paesi/aree geografiche.

**Quali valute sono supportate da Azure Marketplace?**

Le transazioni possono essere effettuate nelle seguenti 17 valute: AUD, BRL, CAD, CHF, DKK, EUR, GBP, INR, JPY, KRW, NOK, N -D, RUB, SEK, TWD e USD.

### <a name="pricing-and-payment"></a>Prezzi e pagamento

**Qual è la differenza tra livello gratuito e versione di valutazione gratuita del software?**

Un'offerta di sottoscrizione con livello gratuito rimane sempre gratuita.  Un'offerta di versione di valutazione gratuita del software (Prova adesso) è una sottoscrizione a pagamento, gratuita solo per un periodo di tempo limitato.

**Qual è il processo per convalidare il flusso di acquisto e provisioning end-to-end?**

Durante il processo di pubblicazione, ti verrà fornito l'accesso a un'anteprima della tua offerta. L'accesso è limitato agli utenti specificati nella scheda Anteprima e si tratta di un'offerta in tempo reale che non è visibile a nessun altro. È possibile acquistare questo e testare il processo; tuttavia, ti verrà addebitato l'intero importo in base alla configurazione della tua offerta.

Per completare un acquisto a un prezzo molto basso, Microsoft suggerisce di pubblicare un piano privato a te stesso impostato ad un prezzo che puoi accettare come costo del test. Lo zero è supportato, ma non riflette l'esperienza completa come un abbonamento a pagamento.

**Microsoft fornirà un rimborso al di fuori delle norme standard?**

Sì, su richiesta tramite ticket di supporto, Microsoft elaborerà i crediti al cliente se lo ritieni appropriato.

## <a name="next-steps"></a>Passaggi successivi

Visita la pagina [della guida dell'editore](/azure/marketplace/marketplace-publishers-guide) del marketplace commerciale.


[Azure portal]: https://portal.azure.com
