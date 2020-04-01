---
title: Guida alla pianificazione e alla gestione del Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento contiene indicazioni utili per l'adozione del Centro sicurezza di Azure e considerazioni relative alle operazioni quotidiane.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 46994413ba765e18a826eebfe85a38bb65efc749
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435609"
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Guida alla pianificazione e alla gestione del Centro sicurezza di Azure
Questa guida è destinata ai professionisti IT, agli architetti IT, agli analisti della sicurezza delle informazioni e agli amministratori cloud che intendono usare il Centro sicurezza di Azure.This guide is for information technology (IT) professionals, IT architects, information security analysts, and cloud administrators planning to use Azure Security Center.


## <a name="planning-guide"></a>Guida alla pianificazione
Questa guida illustra le attività che è possibile seguire per ottimizzare l'uso del Centro sicurezza in base ai requisiti di sicurezza dell'organizzazione e al modello di gestione cloud. Per sfruttare tutti i vantaggi del Centro sicurezza, è importante sapere come i vari utenti o team dell'organizzazione usano il servizio per soddisfare le esigenze di gestione e sviluppo, monitoraggio, governance e risposta agli eventi imprevisti in modo sicuro. Le aree principali da considerare quando si intende usare il Centro sicurezza sono:

* Ruoli di sicurezza e controlli di accesso
* Criteri di sicurezza e raccomandazioni
* Raccolta dati e archiviazione
* Risorse non Azure in corso
* Monitoraggio continuo della sicurezza
* Risposta agli eventi imprevisti

Nella sezione successiva si apprenderà come pianificare ogni singola area e applicare le raccomandazioni in base alle esigenze.


> [!NOTE]
> Per un elenco delle domande comuni che possono risultare utili anche durante la fase di progettazione e di pianificazione, vedere [Domande frequenti sul Centro sicurezza di Azure](faq-general.md) .

## <a name="security-roles-and-access-controls"></a>Ruoli di sicurezza e controlli di accesso
A seconda delle dimensioni e della struttura dell'organizzazione, il Centro sicurezza di Azure potrebbe essere usato da più utenti e team per l'esecuzione di diverse attività correlate alla sicurezza. Nel diagramma seguente è riportato un esempio di utenti fittizi e dei rispettivi ruoli e responsabilità in materia di sicurezza:

![Ruoli](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Il Centro sicurezza consente a questi utenti di svolgere i relativi ruoli, Ad esempio:

**Jeff (proprietario del carico di lavoro)**

* Gestisce un carico di lavoro cloud e le risorse correlate.
* È responsabile delle attività di implementazione e gestione della protezione secondo i criteri di sicurezza aziendale.

**Ellen (CISO/CIO)**

* È responsabile di tutti gli aspetti della sicurezza aziendale.
* Deve conoscere i comportamenti di sicurezza aziendale per i carichi di lavoro cloud.
* Ha l'esigenza di ricevere informazioni sui rischi e sugli attacchi principali.

**David (sicurezza IT)**

* Imposta i criteri di sicurezza aziendali per garantire l'applicazione della protezione appropriata.
* Monitora la conformità ai criteri.
* Genera report per la direzione o i revisori.

**Judy (attività di sicurezza)**

* Monitora e risponde agli avvisi di sicurezza 24x7.
* Si occupa dell'escalation al proprietario del carico di lavoro cloud o all'analista della sicurezza IT.

**Sam (analista della sicurezza)**

* Analizza gli attacchi
* Collabora con il proprietario del carico di lavoro cloud per applicare la correzione.

Il Centro sicurezza usa il [controllo degli accessi in base al ruolo (RBAC)o](../role-based-access-control/role-assignments-portal.md), che fornisce [ruoli predefiniti](../role-based-access-control/built-in-roles.md) assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. All'utente viene infatti assegnato il ruolo Proprietario, Collaboratore o Lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- **Ruolo con autorizzazioni di lettura per la sicurezza**: un utente che appartiene a questo ruolo può visualizzare solo le configurazioni del Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non potrà apportare modifiche.
- **Amministratore della protezione**: come il ruolo con autorizzazioni di lettura per la sicurezza, ma è anche in grado di aggiornare i criteri di sicurezza e di ignorare raccomandazioni e avvisi.

I ruoli del Centro sicurezza descritti in precedenza non hanno accesso ad altre aree del servizio di Azure come Archiviazione, Web e dispositivi mobili o Internet delle cose.

Con gli utenti tipo descritti nel diagramma precedente sarebbe necessario il controllo degli accessi in base al ruolo seguente:

**Jeff (proprietario del carico di lavoro)**

* Proprietario/collaboratore del gruppo di risorseResource Group Owner/Contributor

**Ellen (CISO/CIO)**

* Proprietario della sottoscrizione/Collaboratore o Amministratore della sicurezza

**David (sicurezza IT)**

* Proprietario della sottoscrizione/Collaboratore o Amministratore della sicurezza

**Judy (attività di sicurezza)**

* Ruolo con autorizzazioni di lettura per la sottoscrizione o per la sicurezza per visualizzare gli avvisi
* Proprietario della sottoscrizione/Collaboratore o Amministratore della sicurezza necessario per ignorare gli avvisiSubscription Owner/Contributor or Security Admin required to dismiss Alerts

**Sam (analista della sicurezza)**

* Ruolo con autorizzazioni di lettura per la sottoscrizione per visualizzare gli avvisi
* Proprietario della sottoscrizione/Collaboratore necessario per ignorare gli avvisiSubscription Owner/Contributor required to dismiss Alerts
* Potrebbe essere richiesto l'accesso all'area di lavoro

Altre informazioni importanti da considerare:

* Solto i ruoli Proprietario/Collaboratore della sottoscrizione e Amministratore della protezione possono modificare i criteri di sicurezza.
* Solo i ruoli Proprietario e Collaboratore della sottoscrizione e del gruppo di risorse possono applicare le raccomandazioni relative alla sicurezza per una risorsa.

Quando si pianifica il controllo di accesso con il Controllo degli accessi in base al ruolo per il Centro sicurezza, è necessario sapere chi userà il Centro sicurezza nell'organizzazione. Si dovrà sapere anche quali tipi di attività eseguiranno e configurare di conseguenza il Controllo degli accessi in base al ruolo.

> [!NOTE]
> È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Ad esempio, agli utenti che hanno solo necessità di visualizzare le informazioni sullo stato di sicurezza delle risorse senza intervenire, per applicare le raccomandazioni o modificare i criteri, deve essere assegnato il ruolo Lettore.
>
>

## <a name="security-policies-and-recommendations"></a>Criteri di sicurezza e raccomandazioni
Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza. Nel Centro sicurezza è possibile definire criteri per le sottoscrizioni di Azure, che possono essere adattati al tipo di carico di lavoro o alla riservatezza dei dati.

I criteri del Centro sicurezza includono i componenti seguenti:
- [Raccolta di dati](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection): provisioning dell'agente e impostazioni della raccolta di dati.
- [Criteri di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policies): un [criterio di Azure](../governance/policy/overview.md) che determina quali controlli vengono monitorati e consigliati dal Centro sicurezza. In alternativa, è possibile usare i criteri di Azure per creare nuove definizioni, definire criteri aggiuntivi e assegnare criteri nei gruppi di gestione.
- [Notifiche tramite posta elettronica](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details): contatti e impostazioni di notifica relativi alla sicurezza.
- [Piano tariffario](https://docs.microsoft.com/azure/security-center/security-center-pricing): sono disponibili le opzioni Gratuito o Standard, che determinano le funzionalità del Centro sicurezza disponibili per le risorse nell'ambito. Questo criterio può essere specificato per sottoscrizioni, gruppi di risorse e aree di lavoro.

> [!NOTE]
> Specificando un contatto di sicurezza, Azure potrà raggiungere la persona corretta dell'organizzazione in caso di evento imprevisto relativo alla sicurezza. Per altre informazioni su come abilitare questa raccomandazione, vedere [Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) .

### <a name="security-policies-definitions-and-recommendations"></a>Definizioni dei criteri di sicurezza e raccomandazioni
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. È possibile modificare il criterio nel Centro sicurezza o usare i criteri di Azure per creare nuove definizioni, definire criteri aggiuntivi e assegnare criteri a gruppi di gestione, che possono rappresentare l'intera organizzazione, una business unit specifica e così via, e infine monitorare la conformità a tali criteri negli ambiti.

Prima di configurare i criteri di sicurezza, è consigliabile esaminare tutte le [raccomandazioni sulla sicurezza](https://docs.microsoft.com/azure/security-center/security-center-recommendations)e determinare se sono appropriate per le varie sottoscrizioni e i vari gruppi di risorse usati. È importante sapere anche quali azioni devono essere eseguite in relazione alle raccomandazioni sulla sicurezza e quali utenti dell'organizzazione sono responsabili del monitoraggio per le nuove raccomandazioni e dell'adozione delle misure necessarie.

## <a name="data-collection-and-storage"></a>Raccolta dati e archiviazione
Il Centro sicurezza di Azure usa l'agente di Log Analytics, ovvero lo stesso agente usato dal servizio Monitoraggio di Azure, per raccogliere dati di sicurezza dalle macchine virtuali. I [dati raccolti](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) dall'agente verranno archiviati nelle aree di lavoro Log Analytics.

### <a name="agent"></a>Agente

Quando il provisioning automatico è abilitato nei criteri di sicurezza, l'agente di Log Analytics (per [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) o [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) viene installato in tutte le macchine virtuali di Azure supportate e in quelle nuove create. Se nella macchina virtuale o nel computer è già installato l'agente Log Analytics, il Centro sicurezza di Azure sfrutterà l'agente installato corrente. Il processo dell'agente è progettato per non essere invasivo e ridurre al minimo l'impatto sulle prestazioni delle VM.

L'agente di Log Analytics per Windows richiede l'utilizzo della porta TCP 443. Vedere l'[articolo sulla risoluzione dei problemi](security-center-troubleshooting-guide.md) per altri dettagli.

Per disabilitare successivamente la raccolta dati, è possibile usare i criteri di sicurezza. Tuttavia, poiché l'agente di Log Analytics può essere usato da altri servizi di gestione e monitoraggio di Azure, l'agente non verrà disinstallato automaticamente quando si disattiva la raccolta dei dati nel Centro sicurezza. È possibile disinstallare manualmente l'agente se necessario.

> [!NOTE]
> Per un elenco delle macchine virtuali supportate, vedere [Domande frequenti sul Centro sicurezza di Azure](faq-vms.md).

### <a name="workspace"></a>Area di lavoro

Un'area di lavoro di Azure è una risorsa usata come contenitore per i dati. Nell'organizzazione è possibile usare più aree di lavoro per gestire diversi set di dati raccolti dall'intera infrastruttura IT o da una parte di essa.

I dati raccolti dall'agente di Log Analytics (per conto del Centro sicurezza di Azure) verranno archiviati in un'area di lavoro di Log Analytics esistente associata alla sottoscrizione di Azure o in una nuova area di lavoro, tenendo conto dell'area geografica della macchina virtuale.

Nel portale di Azure è possibile visualizzare un elenco delle aree di lavoro di Log Analytics, incluse quelle create dal Centro sicurezza di Azure. Per le nuove aree di lavoro verrà creato un gruppo di risorse correlato. Entrambi seguiranno questa convenzione di denominazione:

* Area di lavoro: *DefaultWorkspace-[subscription-ID]-[geo]*
* Gruppo di risorse: *DefaultResourceGroup-[geo]*

Per le aree di lavoro create dal Centro sicurezza di Azure, i dati vengono conservati per 30 giorni. Per le aree di lavoro esistenti, il periodo di memorizzazione dipende dal piano tariffario dell'area di lavoro. Se si preferisce, è anche possibile usare un'area di lavoro esistente.

> [!NOTE]
> Microsoft è fortemente impegnata nella protezione della privacy e della sicurezza dei dati. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio. Per altre informazioni sulla privacy e sulla gestione dei dati, leggere [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md).
>

## <a name="onboarding-non-azure-resources"></a>Onboarding delle risorse non Azure

Il Centro di sicurezza di Azure consente di monitorare le condizioni di sicurezza dei computer non Azure, ma è necessario prima caricare queste risorse. Per altre informazioni su come eseguire l'onboarding di risorse non Azure, vedere [Onboarding nel livello Standard del Centro sicurezza di Azure per una sicurezza avanzata](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers).

## <a name="ongoing-security-monitoring"></a>Monitoraggio continuo della sicurezza
Dopo la configurazione iniziale e l'applicazione delle raccomandazioni del Centro sicurezza, il passaggio successivo consiste nel valutare i processi operativi del Centro sicurezza.

La panoramica del Centro sicurezza fornisce una visualizzazione unificata della sicurezza per tutte le risorse di Azure ed eventuali risorse non Azure connesse. L'esempio seguente mostra un ambiente con molti problemi da risolvere:

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Il Centro sicurezza non interferisce con le normali procedure operative, esegue un monitoraggio passivo delle distribuzioni e fornisce raccomandazioni in base ai criteri di sicurezza abilitati.

Quando si acconsente esplicitamente per la prima volta all'uso del Centro sicurezza per l'ambiente Azure corrente, assicurarsi di esaminare tutte le raccomandazioni nel riquadro **Raccomandazioni** o nelle singole risorse (**Calcolo**, **Rete**, **Archiviazione e dati** e **Applicazione**).

Dopo avere applicato tutte le raccomandazioni, la sezione **Prevenzione** sarà verde per tutte le risorse che sono state corrette. Il monitoraggio continuo a questo punto risulta più semplice perché le azioni da intraprendere dipendono unicamente dalle modifiche apportate nei riquadri Integrità sicurezza delle risorse e Raccomandazioni.

La sezione **Rilevamento** è più reattiva e contiene avvisi relativi a problemi in corso o che si sono verificati in precedenza e sono stati rilevati dai controlli del Centro sicurezza e da sistemi di terze parti. Il riquadro Avvisi di sicurezza mostrerà i grafici a barre che rappresentano il numero di avvisi trovati ogni giorno e la loro distribuzione tra le diverse categorie di gravità (bassa, media, alta). Per altre informazioni sugli avvisi di sicurezza, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).

Pianificare una verifica dell'opzione [Intelligence per le minacce](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) come parte delle operazioni di sicurezza giornaliere. Questa opzione consente di identificare le minacce alla sicurezza per l'ambiente specifico, ad esempio determinare se un computer specifico fa parre di un botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitoraggio di risorse nuove o modificate
La maggior parte degli ambienti Azure sono dinamici, con risorse che vengono regolarmente create, riunite o abbassate, riconfigurate e modificate. Il Centro sicurezza contribuisce a garantire la visibilità dello stato di sicurezza delle nuove risorse.

Quando si aggiungono nuove risorse all'ambiente Azure, come VM o database SQL, il Centro sicurezza le individua automaticamente e iniziare a monitorarne la sicurezza. Ciò include anche i ruoli di lavoro e i ruoli Web PaaS. Se la raccolta dati è abilitata nei [criteri di sicurezza](tutorial-security-policy.md), vengono abilitate automaticamente funzionalità di monitoraggio aggiuntive per le macchine virtuali.

![Aree principali](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Per le macchine virtuali, fare clic su **Calcolo & app**, nella sezione **Igiene della sicurezza delle risorse.** Gli eventuali problemi di abilitazione dei dati e le relative raccomandazioni verranno visualizzati nella scheda **Panoramica**, nella sezione **Raccomandazioni sul monitoraggio**.
2. Per conoscere gli eventuali rischi di sicurezza identificati per la nuova risorsa, vedere **Raccomandazioni** .
3. Accade spesso che nelle nuove macchine virtuali aggiunte all'ambiente inizialmente sia installato solo il sistema operativo. Il proprietario della risorsa potrebbe avere bisogno di tempo per distribuire altre app per l'uso da parte delle macchine virtuali.  È preferibile conoscere la finalità del carico di lavoro, ad esempio se verrà usato come server applicazioni. In base alla finalità del nuovo carico di lavoro, è possibile abilitare i **Criteri di sicurezza** appropriati, che rappresentano il terzo passaggio del flusso di lavoro.
4. Quando vengono aggiunte nuove risorse all'ambiente Azure, è possibile che nel riquadro Avvisi di **sicurezza** vengano visualizzati nuovi avvisi. Cercare nuovi avvisi in questo riquadro e seguire i suggerimenti.

È inoltre consigliabile monitorare regolarmente le risorse esistenti per le modifiche di configurazione che potrebbero aver creato rischi per la sicurezza, deriva dalle linee di base consigliate e avvisi di sicurezza. Avviare il dashboard del Centro sicurezza. Da lì, hai tre aree principali da rivedere su una base coerente.

![Operazioni](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. La sezione **Prevenzione** consente di accedere rapidamente alle risorse chiave. Usare questa opzione per monitorare Calcolo, Rete, Archiviazione e dati e Applicazioni.
2. Il pannello **Raccomandazioni** consente di esaminare le raccomandazioni del Centro sicurezza. Durante il monitoraggio in corso, è possibile che non si disponga di consigli su base giornaliera, il che è normale in quanto è stato affrontato tutti i consigli sulla configurazione iniziale del Centro sicurezza. Per questo motivo, in questa sezione potrebbero non essere presenti nuove informazioni ogni giorno e sarà sufficiente accedervi solo quando necessario.
3. La sezione **Rilevamento** potrebbe cambiare molto di frequente o solo saltuariamente. Esaminare sempre gli avvisi di sicurezza e intraprendere le azioni necessarie in base alle raccomandazioni del Centro sicurezza.

### <a name="hardening-access-and-applications"></a>Applicare la protezione avanzata all'accesso e alle applicazioni

Come parte delle operazioni di sicurezza è consigliabile adottare misure preventive per limitare l'accesso alle VM e controllare le applicazioni in esecuzioni sulle VM. Bloccando il traffico in ingresso nelle VM di Azure si riduce l'esposizione agli attacchi e si offre al tempo stesso un accesso facilitato per la connessione alle VM quando necessario. Usare la funzionalità di accesso [alle macchine virtuali just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) per la protezione avanzata dell'accesso alle macchine virtuali.

È possibile usare i [controlli delle applicazioni adattivi](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) per limitare le applicazioni che possono essere eseguite nelle macchine virtuali che si trovano in Azure.You can use Adaptive Application Controls to limit which applications can run on your VMs located in Azure. Tra gli altri vantaggi, questo aiuta a indurire le macchine virtuali contro il malware. Usando l'apprendimento automatico, il Centro sicurezza analizza i processi in esecuzione nella macchina virtuale per creare regole di whitelisting.


## <a name="incident-response"></a>Risposta agli eventi imprevisti
Il Centro sicurezza rileva le minacce appena si verificano e avvisa l'utente. Le organizzazioni devono monitorare i nuovi avvisi di sicurezza e intraprendere le azioni necessarie per indagare più a fondo o correggere l'attacco. Per altre informazioni sul funzionamento della protezione dalle minacce del Centro sicurezza, vedere Come il Centro sicurezza di [Azure rileva e risponde alle minacce.](security-center-alerts-overview.md#detect-threats)

Per quanto offrire una guida alla creazione di un piano di risposta agli eventi imprevisti non rientri nelle finalità di questo articolo, il ciclo di vita della risposta del Centro sicurezza di Microsoft Azure nel cloud verrà usato come base per le fasi della risposta agli eventi imprevisti. Le fasi sono illustrate nel diagramma seguente:

![Attività sospetta](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Per tale scopo è possibile usare la [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guida alla gestione degli eventi imprevisti della sicurezza nei computer) del National Institute of Standards and Technology (NIST).
>

È possibile usare gli avvisi del Centro sicurezza durante le fasi seguenti.

* **Rilevamento**: identificazione di un'attività sospetta in una o più risorse.
* **Valutazione**: esecuzione della valutazione iniziale per ottenere altre informazioni sull'attività sospetta.
* **Diagnosi**: uso dei passaggi di correzione per completare la procedura tecnica per risolvere il problema.

Ogni avviso di sicurezza fornisce informazioni che possono essere usate per comprendere meglio la natura dell'attacco e trovare soluzioni di mitigazione. Alcuni avvisi forniscono anche collegamenti ad altre informazioni o fonti di informazioni all'interno di Azure. È possibile usare le informazioni fornite per ulteriori ricerche e per avviare la mitigazione dei rischi. È inoltre possibile eseguire ricerche nei dati relativi alla sicurezza archiviati nell'area di lavoro.

L'esempio seguente mostra un'attività RDP sospetta in azione:

![Attività sospetta](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Questa pagina include dettagli relativi al momento in cui si è verificato l'attacco, al nome host di origine, alla macchina virtuale di destinazione e ai passaggi necessari per applicare la raccomandazione. In alcune circostanze, le informazioni di origine dell'attacco possono essere vuote. Per altre informazioni su questo tipo di comportamento, vedere [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Informazioni sull'origine mancanti negli avvisi del Centro sicurezza di Azure).

Da questa pagina è anche possibile avviare un'[indagine](https://docs.microsoft.com/azure/security-center/security-center-investigation) per ottenere una migliore comprensione della sequenza temporale dell'attacco, della modalità in cui è stato effettuato l'attacco, dei sistemi potenzialmente danneggiati e delle credenziali usate e infine per ottenere una rappresentazione grafica dell'intera catena di attacco.

Dopo aver identificato il sistema compromesso, è possibile eseguire [un'automazione del flusso](workflow-automation.md) di lavoro creata in precedenza. Si tratta di una raccolta di procedure che possono essere eseguite dal Centro sicurezza una volta attivato da un avviso.

Nel video Come sfruttare il Centro sicurezza di [Azure & Microsoft Operations Management Suite per una risposta agli eventi imprevisti](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) sono possibili alcune dimostrazioni che consentono di comprendere come è possibile usare il Centro sicurezza in ognuna di queste fasi.

> [!NOTE]
> Per altre informazioni su come usare le funzionalità del Centro sicurezza per facilitare il processo di risposta agli eventi imprevisti, vedere Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure.Read Managing and responding to security [alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) for more information on how to use Security Center capabilities to assist you during your Incident Response process.
>
>

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come pianificare l'adozione del Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di AzureManaging and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Monitoraggio dell'integrità](security-center-monitoring.md) della sicurezza nel Centro sicurezza di Azure: informazioni su come monitorare l'integrità delle risorse di Azure.Security health monitoring in Azure Security Center - Learn how to monitor the health of your Azure resources.
* [Monitoraggio delle soluzioni dei partner con](security-center-partner-solutions.md) il Centro sicurezza di Azure: informazioni su come monitorare lo stato di integrità delle soluzioni partner.
* Domande frequenti sul [Centro sicurezza di Azure:](faq-general.md) domande frequenti sull'uso del servizio.
* Blog sulla [sicurezza di Azure:](https://blogs.msdn.com/b/azuresecurity/) trova post di blog su sicurezza e conformità di Azure.Azure Security blog : Find blog posts about Azure security and compliance.
