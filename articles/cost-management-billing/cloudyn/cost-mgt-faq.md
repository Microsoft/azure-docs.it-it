---
title: Domande frequenti per Cloudyn in Azure
description: Informazioni su come usare il portale di Cloudyn per risolvere i problemi comuni di installazione del canale Enterprise indiretto e rispondere ad altre domande frequenti.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: b9d19923302a40985906fa3c2e0e183045a95860
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460457"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Domande frequenti su Cloudyn

Questo articolo affronta alcune domande comuni su Cloudyn. Eventuali domande su Cloudyn possono essere formulate nel forum [FAQs for Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn) (Domande frequenti su Cloudyn).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Come è possibile risolvere i problemi di configurazione indiretta dell'organizzazione?

Quando si usa per la prima volta il portale di Cloudyn, vengono visualizzati i messaggi seguenti se si ha un contratto Enterprise o Provider di soluzioni cloud (CSP):

- "The specified API key is not a top level enrollment key" (La chiave API specificata non è una chiave di registrazione principale), nella procedura guidata **Set Up Cloudyn** (Configurazione di Cloudyn).
- "Direct Enrollment – No" (Registrazione diretta - No), nel portale per i contratti Enterprise.
- "No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account" (Non sono stati trovati dati di utilizzo per gli ultimi 30 giorni. Contattare il distributore per verificare che per l'account Azure sia stato abilitato il markup), nel portale di Cloudyn.

I messaggi precedenti indicano che è stato acquistato un contratto Enterprise di Azure tramite un rivenditore o un CSP. Per poter visualizzare i dati in Cloudyn, è necessario che il rivenditore o il CSP abiliti il _markup_ per l'account Azure.

Ecco come risolvere i problemi:

1. Il rivenditore deve abilitare il _markup_ per l'account. Per le istruzioni, vedere la [guida introduttiva per i clienti indiretti](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Generare la chiave del contratto Enterprise di Azure da usare con Cloudyn. Per le istruzioni, vedere [Adding Your Azure EA](quick-register-ea.md#register-with-cloudyn) (Aggiunta del contratto Enterprise di Azure) o [How to Find Your EA Enrollment ID and API Key](https://youtu.be/u_phLs_udig) (Come trovare l'ID registrazione e la chiave API del contratto Enterprise).

Cloudyn può essere abilitato solo da un amministratore del servizio Azure. Le autorizzazioni di coamministratore non sono sufficienti.

Per poter generare la chiave API del contratto Enterprise di Azure per la configurazione di Cloudyn, abilitare prima di tutto l'API di fatturazione di Azure seguendo queste istruzioni:

- [Panoramica delle API di creazione di report per i clienti Enterprise](../manage/enterprise-api.md)
- Sezione **Enabling data access to the API** (Abilitazione dell'accesso ai dati nell'API) in [Microsoft Azure Enterprise Portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (API di creazione report di Microsoft Azure Enterprise Portal)


Potrebbe anche essere necessario concedere ad amministratori di reparto, proprietari di account e amministratori dell'organizzazione le autorizzazioni per _visualizzare gli addebiti_ con l'API di fatturazione.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Perché non vengono visualizzate le raccomandazioni dell'utilità di ottimizzazione?

Le informazioni di raccomandazione sono disponibili solo per gli account attivati. Non si vedranno informazioni di raccomandazione nelle categorie di report di **ottimizzazione**per gli account *non attivati*, tra cui:

- Gestione dell'ottimizzazione
- Ottimizzazione delle dimensioni
- Inefficienze

Se non è possibile visualizzare dati di raccomandazione per l'ottimizzazione, è molto probabile che gli account non siano attivati. Per attivare un account, è necessario registrarlo con le credenziali di Azure.

Per attivare un account:

1.    Nel portale di Cloudyn, fare clic su **Impostazioni** in alto a destra e selezionare **Cloud Accounts** (Account cloud).
2.    Nella scheda degli account di Microsoft Azure, cercare gli account con sottoscrizione **non attivata**.
3.    A destra di un account non attivato fare clic sul simbolo di **modifica** simile a una matita.
4.    L'ID del tenant e l'ID della tariffa vengono rilevati automaticamente. Fare clic su **Avanti**.
5.    Si verrà reindirizzati al portale di Azure. Accedi al portale e autorizzare l'agente di raccolta Cloudyn all'accesso ai dati di Azure.
6.    Si verrà quindi reindirizzati alla pagina di gestione degli account Cloudyn e la sottoscrizione verrà aggiornata con lo stato dell'account **attivo**. Viene visualizzato un simbolo di segno di spunta verde.
7.    Se non viene visualizzato un segno di spunta verde per una o più sottoscrizioni, significa che non sono disponibili le autorizzazioni per creare un'app lettore (CloudynCollector) per la sottoscrizione. Un utente con autorizzazioni più elevate per la sottoscrizione deve ripetere i passaggi 3 e 4.  

Dopo aver completato i passaggi precedenti, sarà possibile visualizzare le raccomandazioni di ottimizzazione nel giro di un paio di giorni. Tuttavia, possono essere necessari fino a cinque giorni prima che diventino disponibili i dati di ottimizzazione completi.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Come si abilitano gli utenti sospesi o bloccati?

Di seguito sono elencati gli scenari più comuni in cui gli account utente diventano *initiallySuspended*.

> Admin1 potrebbe essere un utente di Microsoft Cloud Solution Provider o di Enterprise Agreement. L'organizzazione è pronta per iniziare a usare Cloudyn.  Esegue la registrazione tramite il portale di Azure e accede al portale di Cloudyn. Essendo la persona che registra il servizio Cloudyn e accede al portale di Cloudyn, Admin1 diventa l'*amministratore primario*. Admin1 non crea account utente. Tuttavia, usando il portale di Cloudyn, crea account Azure e configura una gerarchia di entità. Admin1 informa Admin2, un amministratore del tenant, che deve eseguire la registrazione con Cloudyn e accedere al portale di Cloudyn.
>
> Admin2 esegue la registrazione tramite il portale di Azure, ma, quando prova ad accedere al portale di Cloudyn, viene visualizzato un errore indicante che il suo account è **sospeso**. All'amministratore principale, Admin1, viene notificata la sospensione dell'account. Admin1 deve attivare l'account di Admin2 e concedere l'*accesso dell'entità amministratore* per le entità appropriate, consente l'accesso per la gestione utenti e attiva l'account utente.


Se si riceve un avviso che richiede di consentire l'accesso a un utente, è necessario attivarne l'account.

Per attivare l'account utente:

1. Accedere a Cloudyn con l'account utente amministratore di Azure utilizzato per configurare Cloudyn. In alternativa accedere con un account utente a cui è stato concesso l'accesso amministrativo.
2. Selezionare il simbolo a forma di ingranaggio in alto a destra e selezionare **User Management** (Gestione utenti).
3. Trovare l'utente e quindi selezionare il simbolo a forma di matita e modificare l'utente.
4. In **User status** (Stato utente) modificare lo stato da **Suspended** (Sospeso) ad **Active** (Attivo).

Gli account utente Cloudyn si connettono tramite Single Sign-On da Azure. Se un utente digita in modo non corretto la propria password, potrebbe essere bloccato da Cloudyn pur potendo ancora accedere ad Azure.

La modifica dell'indirizzo di posta elettronica in Cloudyn rispetto all'indirizzo predefinito in Azure può causare il blocco dell'account e la visualizzazione dell'errore "status initiallySuspended" (stato initiallySuspended). Se l'account utente viene bloccato, contattare un altro amministratore per reimpostarlo.

È consigliabile creare almeno due account amministratore di Cloudyn nel caso in cui uno venga bloccato.

Se non si riesce ad accedere al portale di Cloudyn, verificare di usare l'URL corretto per l'accesso a Cloudyn. Usare [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Evitare di usare l'URL diretto di Cloudyn `https://app.cloudyn.com`.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Come si attivano account non attivati con le credenziali di Azure?

Non appena Cloudyn individua account Azure, i dati sui costi vengono immediatamente resi disponibili in report basati sui costi. Affinché Cloudyn fornisca dati di utilizzo e sulle prestazioni, tuttavia, è necessario registrare le credenziali di Azure per gli account. Per istruzioni, vedere [Aggiungere un account o aggiornare una sottoscrizione](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Per aggiungere le credenziali di Azure per un account, nel portale di Cloudyn selezionare il simbolo di modifica a destra del nome dell'account, non della sottoscrizione.

Finché le credenziali di Azure non vengono aggiunte a Cloudyn, l'account risulta _non attivato_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Come si aggiungono più account ed entità a una sottoscrizione esistente?

Le entità aggiuntive vengono usate per includere altri contratti Enterprise in una sottoscrizione di Cloudyn. Per altre informazioni, vedere [Creare e gestire le entità](tutorial-user-access.md#create-and-manage-entities).

Per CSP:

Per aggiungere altri account CSP a un'entità, selezionare **MSP Access** (Accesso MSP) invece di **Enterprise** durante la creazione della nuova entità. Se l'account è registrato come contratto Enterprise e si vogliono aggiungere credenziali CSP, potrebbe essere necessaria la modifica delle impostazioni dell'account da parte del personale di supporto di Cloudyn. Se si ha una sottoscrizione di Azure a pagamento, è possibile creare una nuova richiesta di supporto nel portale di Azure. Selezionare **Guida e supporto** e quindi **Nuova richiesta di supporto**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Simboli di valuta nei report Cloudyn

Potrebbero essere presenti più account Azure che usano valute diverse. I report dei costi in Cloudyn tuttavia non visualizzano più di un tipo di valuta per report.

Se si hanno più sottoscrizioni che usano valute diverse, un'entità padre e le valute delle entità figlio vengono visualizzate in **$** USD. La procedura consigliata consiste nell'evitare di usare valute diverse nella stessa gerarchia di entità. In altre parole, tutte le sottoscrizioni organizzate in una struttura di entità devono usare la stessa valuta.

Cloudyn rileva automaticamente la valuta della sottoscrizione Enterprise Agreement e la visualizza correttamente nei report.  Cloudyn tuttavia visualizza **$** USD solo per gli account Azure con accesso Web diretto e CSP.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Quali sono le tempistiche di aggiornamento dei dati di Cloudyn?

Cloudyn offre le tempistiche di aggiornamento dei dati riportate di seguito.

- **Iniziale**: la visualizzazione dei dati sui costi in Cloudyn dopo la configurazione può richiedere fino a 24 ore. Possono inoltre trascorrere fino a 10 giorni prima che Cloudyn raccolga dati sufficienti per visualizzare consigli sul ridimensionamento.
- **Giornaliera**: dal giorno 10 fino alla fine di ogni mese, Cloudyn visualizzerà i dati aggiornati del giorno precedente dopo le ore UTC+3 circa del giorno successivo.
- **Mensile**: dal giorno 1 al 10 di ogni mese, Cloudyn potrebbe visualizzare solo i dati fino alla fine del mese precedente.

Cloudyn elabora i dati per il giorno precedente quando sono tutti disponibili. I dati del giorno precedente sono in genere disponibili in Cloudyn entro le ore UTC+3 circa di ogni giorno. L'elaborazione di alcuni dati, come i tag, può richiedere altre 24 ore.

I dati per il mese in corso non sono disponibili per la raccolta all'inizio di ogni mese. Durante tale periodo, i provider di servizi finalizzano la fatturazione per il mese precedente, i cui dati vengono visualizzati in Cloudyn da 5 a 10 giorni dopo l'inizio di ogni mese. In tale intervallo di tempo, potrebbero essere visualizzati solo i costi ammortizzati del mese precedente e non i dati giornalieri di fatturazione o utilizzo. Quando diventano disponibili, i dati vengono elaborati da Cloudyn retroattivamente. Al termine dell'elaborazione tutti i dati mensili vengono visualizzati tra il 5 e il 10 di ogni mese.

Se si verifica un ritardo nell'invio dei dati da Azure a Cloudyn, i dati vengono comunque registrati in Azure e vengono trasferiti a Cloudyn al ripristino della connessione.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Fluttuazioni dei costi nei report di costo Cloudyn

I report di costo mostrano le fluttuazioni dei costi, ogni volta che i provider di servizi cloud inviano i file aggiornati di fatturazione. Le fluttuazioni dei costi si verificano quando i nuovi file vengono ricevuti da un provider di servizi cloud esterno alla consueta pianificazione dei report giornaliera o mensile. Le modifiche dei costi non dipendono dal ricalcolo di Cloudyn.

Nel corso del mese, tutti i file di fatturazione inviati dal provider di servizi cloud sono una stima dei costi giornalieri. In alcuni casi, i dati vengono aggiornati di frequente; occasionalmente, più volte al giorno. Gli aggiornamenti sono più frequenti con AWS rispetto ad Azure. I costi totali dovrebbero rimanere stabili quando il calcolo della fatturazione per il mese precedente è stato completato e si riceve il file di fatturazione finale. In genere, entro il 10 del mese.

Le modifiche si verificano quando si ricevono le regolazioni di costi da provider di servizi cloud. La ricezione di crediti ne è un esempio. Le modifiche possono verificarsi mesi dopo che il mese rilevante è stato chiuso. Le modifiche vengono visualizzate ogni volta che un nuovo calcolo viene effettuato dal provider di servizi cloud. Cloudyn aggiorna i dati cronologici per assicurarsi che tutte le modifiche vengano ricalcolate. Verifica inoltre che i costi vengano visualizzati in modo accurato nei report.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Come può un CSP diretto configurare l'accesso a Cloudyn per partner o clienti CSP indiretti?

Per le istruzioni, vedere [Configurare l'accesso CSP indiretto in Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Perché viene visualizzato l'elemento del menu Optimizer (Ottimizzatore)?

Dopo aver aggiunto l'accesso di Azure Resource Manager e aver raccolto i dati, viene visualizzata l'opzione **Optimizer** (Ottimizzatore). Per attivare l'accesso da Azure Resource Manager, vedere [Come si attivano account non attivati con le credenziali di Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Si basa sull'agente di Cloudyn?

No. Gli agenti non vengono usati. I dati di metrica della macchina virtuale di Azure per le macchine virtuali vengono raccolti dall'API di Microsoft Insights. Se si desidera raccogliere dati di metrica da macchine virtuali di Azure, queste dovranno avere abilitate le impostazioni di diagnostica.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>I report di Cloudyn mostrano più di un tenant di Active Directory per ogni report?

Sì. È possibile [creare un'entità di account cloud corrispondente](tutorial-user-access.md#create-and-manage-entities) per ogni tenant di Active Directory di cui si dispone. È quindi possibile visualizzare tutti i dati del tenant di Azure AD e altri provider di piattaforma cloud inclusi Amazon Web Services e Google Cloud Platform.
