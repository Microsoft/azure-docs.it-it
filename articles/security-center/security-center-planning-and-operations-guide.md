---
title: Guida alla pianificazione e alla gestione del Centro sicurezza di Azure
description: Questo documento contiene indicazioni utili per l'adozione del Centro sicurezza di Azure e considerazioni relative alle operazioni quotidiane.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: de6a416666866a4089d22f2fa047dc860c922d3c
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341244"
---
# <a name="planning-and-operations-guide"></a>Guida alla pianificazione e alle operazioni
Questa guida è destinata a professionisti IT, architetti IT, analisti della sicurezza delle informazioni e amministratori cloud che intendono usare il Centro sicurezza di Azure.


## <a name="planning-guide"></a>Guida alla pianificazione
Questa guida illustra le attività che è possibile eseguire per ottimizzare l'uso del Centro sicurezza in base ai requisiti di sicurezza e al modello di gestione cloud dell'organizzazione. Per sfruttare tutti i vantaggi del Centro sicurezza, è importante sapere come i vari utenti o team dell'organizzazione usano il servizio per soddisfare le esigenze di gestione e sviluppo, monitoraggio, governance e risposta agli eventi imprevisti in modo sicuro. Le aree principali da considerare quando si intende usare il Centro sicurezza sono:

* Ruoli di sicurezza e controlli di accesso
* Criteri di sicurezza e raccomandazioni
* Raccolta dati e archiviazione
* Onboarding delle risorse non Azure
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

Il Centro sicurezza usa il [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md), che fornisce [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. All'utente viene infatti assegnato il ruolo Proprietario, Collaboratore o Lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- **Ruolo con autorizzazioni di lettura per la sicurezza**: un utente che appartiene a questo ruolo può visualizzare solo le configurazioni del Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non potrà apportare modifiche.
- **Amministratore della protezione**: come il ruolo con autorizzazioni di lettura per la sicurezza, ma è anche in grado di aggiornare i criteri di sicurezza e di ignorare raccomandazioni e avvisi.

I ruoli del Centro sicurezza descritti in precedenza non hanno accesso ad altre aree del servizio di Azure come Archiviazione, Web e dispositivi mobili o Internet delle cose.

Con gli utenti tipo descritti nel diagramma precedente sarebbe necessario il controllo degli accessi in base al ruolo seguente:

**Jeff (proprietario del carico di lavoro)**

* Proprietario del gruppo di risorse/Collaboratore

**Ellen (CISO/CIO)**

* Proprietario della sottoscrizione/Collaboratore o Amministratore della sicurezza

**David (sicurezza IT)**

* Proprietario della sottoscrizione/Collaboratore o Amministratore della sicurezza

**Judy (attività di sicurezza)**

* Ruolo con autorizzazioni di lettura per la sottoscrizione o per la sicurezza per visualizzare gli avvisi
* Proprietario della sottoscrizione/Collaboratore o Amministratore della sicurezza per ignorare gli avvisi

**Sam (analista della sicurezza)**

* Ruolo con autorizzazioni di lettura per la sottoscrizione per visualizzare gli avvisi
* Proprietario della sottoscrizione/Collaboratore per ignorare gli avvisi
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
- [Raccolta di dati](security-center-enable-data-collection.md): provisioning dell'agente e impostazioni della raccolta di dati.
- [Criteri di sicurezza](tutorial-security-policy.md): un [criterio di Azure](../governance/policy/overview.md) che determina quali controlli vengono monitorati e consigliati dal Centro sicurezza. In alternativa, è possibile usare i criteri di Azure per creare nuove definizioni, definire criteri aggiuntivi e assegnare criteri nei gruppi di gestione.
- [Notifiche tramite posta elettronica](security-center-provide-security-contact-details.md): contatti e impostazioni di notifica relativi alla sicurezza.
- [Piano tariffario](security-center-pricing.md): con o senza Azure Defender, che determina le funzionalità del Centro sicurezza disponibili per le risorse nell'ambito. Questo criterio può essere specificato per sottoscrizioni e aree di lavoro oppure per gruppi di risorse tramite l'API.

> [!NOTE]
> Specificando un contatto di sicurezza, Azure potrà raggiungere la persona corretta dell'organizzazione in caso di evento imprevisto relativo alla sicurezza. Per altre informazioni su come abilitare questa raccomandazione, vedere [Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure](security-center-provide-security-contact-details.md) .

### <a name="security-policies-definitions-and-recommendations"></a>Definizioni dei criteri di sicurezza e raccomandazioni
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. È possibile modificare il criterio nel Centro sicurezza o usare i criteri di Azure per creare nuove definizioni, definire criteri aggiuntivi e assegnare criteri a gruppi di gestione, che possono rappresentare l'intera organizzazione, una business unit specifica e così via, e infine monitorare la conformità a tali criteri negli ambiti.

Prima di configurare i criteri di sicurezza, è consigliabile esaminare tutte le [raccomandazioni sulla sicurezza](security-center-recommendations.md)e determinare se sono appropriate per le varie sottoscrizioni e i vari gruppi di risorse usati. È importante sapere anche quali azioni devono essere eseguite in relazione alle raccomandazioni sulla sicurezza e quali utenti dell'organizzazione sono responsabili del monitoraggio per le nuove raccomandazioni e dell'adozione delle misure necessarie.

## <a name="data-collection-and-storage"></a>Raccolta dati e archiviazione
Il Centro sicurezza di Azure usa l'agente di Log Analytics, lo stesso agente usato dal servizio Monitoraggio di Azure, per raccogliere dati sulla sicurezza dalle macchine virtuali. I [dati raccolti](security-center-enable-data-collection.md) dall'agente verranno archiviati nelle aree di lavoro Log Analytics.

### <a name="agent"></a>Agente

Dopo l'abilitazione del provisioning automatico nei criteri di sicurezza, l'agente di Log Analytics (per [Windows](../azure-monitor/platform/agent-windows.md) o [Linux](../azure-monitor/learn/quick-collect-linux-computer.md)) viene installato in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create. Se l'agente di Log Analytics è già installato nella macchina virtuale o nel computer, il Centro sicurezza di Azure userà l'agente installato corrente. Il processo dell'agente è progettato per non essere invasivo e ridurre al minimo l'impatto sulle prestazioni delle VM.

L'agente di Log Analytics per Windows richiede l'uso della porta TCP 443. Vedere l'[articolo sulla risoluzione dei problemi](security-center-troubleshooting-guide.md) per altri dettagli.

Per disabilitare successivamente la raccolta dati, è possibile usare i criteri di sicurezza. Tuttavia, poiché l'agente di Log Analytics può essere usato da altri servizi di gestione e monitoraggio di Azure, l'agente non viene disinstallato automaticamente quando si disattiva la raccolta dati nel Centro sicurezza. È possibile disinstallare manualmente l'agente se necessario.

> [!NOTE]
> Per un elenco delle macchine virtuali supportate, vedere [Domande frequenti sul Centro sicurezza di Azure](faq-vms.md).

### <a name="workspace"></a>Area di lavoro

Un'area di lavoro di Azure è una risorsa usata come contenitore per i dati. Nell'organizzazione è possibile usare più aree di lavoro per gestire diversi set di dati raccolti dall'intera infrastruttura IT o da una parte di essa.

I dati raccolti dall'agente di Log Analytics (per conto del Centro sicurezza di Azure) verranno archiviati in una o più aree di lavoro Log Analytics esistenti associate alla sottoscrizione di Azure o in una o più aree di lavoro nuove, a seconda dell'area geografica della macchina virtuale.

Nel portale di Azure è possibile visualizzare un elenco delle aree di lavoro di Log Analytics, incluse quelle create dal Centro sicurezza di Azure. Per le nuove aree di lavoro verrà creato un gruppo di risorse correlato. Entrambi seguiranno questa convenzione di denominazione:

* Area di lavoro: *DefaultWorkspace-[subscription-ID]-[geo]*
* Gruppo di risorse: *DefaultResourceGroup-[geo]*

Per le aree di lavoro create dal Centro sicurezza di Azure, i dati vengono conservati per 30 giorni. Per le aree di lavoro esistenti, il periodo di memorizzazione dipende dal piano tariffario dell'area di lavoro. Se si preferisce, è anche possibile usare un'area di lavoro esistente.

> [!NOTE]
> Microsoft è fortemente impegnata nella protezione della privacy e della sicurezza dei dati. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio. Per altre informazioni sulla privacy e sulla gestione dei dati, leggere [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md).
>

## <a name="onboard-non-azure-resources"></a>Onboarding di risorse non di Azure

Il Centro di sicurezza di Azure consente di monitorare le condizioni di sicurezza dei computer non Azure, ma è necessario prima caricare queste risorse. Per altre informazioni su come eseguire l'onboarding di risorse non di Azure, vedere [Onboarding di computer non di Azure](quickstart-onboard-machines.md).

## <a name="ongoing-security-monitoring"></a>Monitoraggio continuo della sicurezza
Dopo la configurazione iniziale e l'applicazione delle raccomandazioni del Centro sicurezza, il passaggio successivo consiste nel valutare i processi operativi del Centro sicurezza.

La panoramica del Centro sicurezza fornisce una visualizzazione unificata della sicurezza per tutte le risorse di Azure ed eventuali risorse non Azure connesse. L'esempio seguente mostra un ambiente con molti problemi da risolvere:

![Dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Il Centro sicurezza non interferisce con le normali procedure operative, esegue un monitoraggio passivo delle distribuzioni e fornisce raccomandazioni in base ai criteri di sicurezza abilitati.

Quando si acconsente esplicitamente per la prima volta all'uso del Centro sicurezza per l'ambiente Azure corrente, assicurarsi di esaminare tutte le raccomandazioni nella pagina **Raccomandazioni**.

Pianificare una verifica dell'opzione Intelligence per le minacce come parte delle operazioni di sicurezza giornaliere. Questa opzione consente di identificare le minacce alla sicurezza per l'ambiente specifico, ad esempio determinare se un computer specifico fa parre di un botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Monitoraggio di risorse nuove o modificate

Gli ambienti di Azure sono per la maggior parte dinamici, con risorse che vengono regolarmente create, attivate o disattivate, riconfigurate e modificate. Il Centro sicurezza contribuisce a garantire la visibilità dello stato di sicurezza delle nuove risorse.

Quando si aggiungono nuove risorse all'ambiente Azure, come VM o database SQL, il Centro sicurezza le individua automaticamente e iniziare a monitorarne la sicurezza. Ciò include anche i ruoli di lavoro e i ruoli Web PaaS. Se la raccolta dati è abilitata nei [criteri di sicurezza](tutorial-security-policy.md), vengono abilitate automaticamente funzionalità di monitoraggio aggiuntive per le macchine virtuali.

Occorre anche monitorare regolarmente le risorse esistenti per identificare le modifiche alla configurazione che potrebbero aver creato rischi per la sicurezza, deviazioni dalle baseline consigliate e avvisi di sicurezza. 

### <a name="hardening-access-and-applications"></a>Applicare la protezione avanzata all'accesso e alle applicazioni

Come parte delle operazioni di sicurezza è consigliabile adottare misure preventive per limitare l'accesso alle VM e controllare le applicazioni in esecuzioni sulle VM. Bloccando il traffico in ingresso nelle VM di Azure si riduce l'esposizione agli attacchi e si offre al tempo stesso un accesso facilitato per la connessione alle VM quando necessario. Usare la funzionalità [Accesso Just-In-Time alla VM](security-center-just-in-time.md) per applicare la protezione avanzata all'accesso alle macchine virtuali.

È possibile usare i [controlli applicazioni adattivi](security-center-adaptive-application.md) per limitare le applicazioni che possono essere eseguite nelle VM in Azure. Tra gli altri vantaggi, questi controlli semplificano la protezione delle VM dal malware. Usando le funzionalità di Machine Learning, il Centro sicurezza analizza i processi in esecuzione nella VM per creare regole di inserimento nell'elenco elementi consentiti.


## <a name="incident-response"></a>Risposta agli eventi imprevisti
Il Centro sicurezza rileva le minacce appena si verificano e avvisa l'utente. Le organizzazioni devono monitorare i nuovi avvisi di sicurezza e intraprendere le azioni necessarie per indagare più a fondo o correggere l'attacco. Per altre informazioni sul funzionamento della protezione dalle minacce nel Centro sicurezza, vedere [Modalità di rilevamento e risposta alle minacce del Centro sicurezza di Azure](security-center-alerts-overview.md#detect-threats).

Per quanto offrire una guida alla creazione di un piano di risposta agli eventi imprevisti non rientri nelle finalità di questo articolo, il ciclo di vita della risposta del Centro sicurezza di Microsoft Azure nel cloud verrà usato come base per le fasi della risposta agli eventi imprevisti. Le fasi sono illustrate nel diagramma seguente:

![Fasi del ciclo di vita della risposta agli eventi imprevisti nel cloud](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

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

Questa pagina include dettagli relativi al momento in cui si è verificato l'attacco, al nome host di origine, alla macchina virtuale di destinazione e ai passaggi necessari per applicare la raccomandazione. In alcune circostanze le informazioni sull'origine dell'attacco potrebbero non essere disponibili. Per altre informazioni su questo tipo di comportamento, vedere [Missing Source Information in Azure Security Center Alerts](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts) (Informazioni sull'origine mancanti negli avvisi del Centro sicurezza di Azure).

Dopo avere identificato il sistema danneggiato, è possibile eseguire un'[automazione del flusso di lavoro](workflow-automation.md) creata in precedenza. Si tratta di una raccolta di procedure che possono essere eseguite dal Centro sicurezza dopo essere state attivate da un avviso.

Nel video [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) (Come sfruttare il Centro sicurezza di Azure e Microsoft Operations Management Suite per la risposta a un evento imprevisto) sono incluse alcune demo che spiegano come usare il Centro sicurezza per ognuna di queste fasi.

> [!NOTE]
> Vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) per altre informazioni sull'uso delle funzionalità del Centro sicurezza di Azure per rispondere agli eventi imprevisti.
>
>

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come pianificare l'adozione del Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](./security-center-partner-integration.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](faq-general.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](/archive/blogs/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.