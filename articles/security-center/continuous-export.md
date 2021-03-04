---
title: L'esportazione continua può inviare avvisi e raccomandazioni del Centro sicurezza di Azure per Log Analytics aree di lavoro o hub eventi di Azure
description: Informazioni su come configurare l'esportazione continua degli avvisi di sicurezza e le raccomandazioni per Log Analytics aree di lavoro o hub eventi di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/24/2020
ms.author: memildin
ms.openlocfilehash: fc3774a01665b88ccae2e25ae8382497f8010c35
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096973"
---
# <a name="continuously-export-security-center-data"></a>Esportazione continua dei dati del Centro sicurezza

Il Centro sicurezza di Azure genera avvisi e raccomandazioni di sicurezza dettagliati. È possibile visualizzarli nel portale o tramite gli strumenti programmatici. Potrebbe anche essere necessario esportare alcune o tutte queste informazioni per tenere traccia con altri strumenti di monitoraggio nell'ambiente in uso. 

L' **esportazione continua** consente di personalizzare completamente gli *elementi che* verranno esportati e la *posizione in cui* verranno rilasciati. Ad esempio, è possibile configurarlo in modo che:

- Tutti gli avvisi con livello di gravità elevato vengono inviati a un hub eventi di Azure
- Tutti i risultati della gravità media o superiore delle analisi di valutazione della vulnerabilità dei server SQL vengono inviati a un'area di lavoro di Log Analytics specifica
- Le raccomandazioni specifiche vengono recapitate a un hub eventi o Log Analytics area di lavoro ogni volta che vengono generate 
- Il Punteggio sicuro per una sottoscrizione viene inviato a un'area di lavoro di Log Analytics ogni volta che il punteggio per un controllo viene modificato di 0,01 o più 

Anche se la funzionalità è chiamata *continua*, è anche possibile esportare snapshot settimanali del Punteggio sicuro o dei dati di conformità alle normative.

Questo articolo descrive come configurare l'esportazione continua in Log Analytics aree di lavoro o hub eventi di Azure.

> [!NOTE]
> Se è necessario integrare il Centro sicurezza con SIEM, vedere [trasmettere avvisi a una soluzione Siem, Soar o IT Service Management](export-to-siem.md).

> [!TIP]
> Il Centro sicurezza offre inoltre la possibilità di eseguire un'esportazione monouso manuale nel volume condiviso cluster. Ulteriori informazioni sono disponibili nell' [esportazione manuale di avvisi e consigli](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|Livello gratuito|
|Autorizzazioni e ruoli obbligatori:|<ul><li>**Amministratore della sicurezza** o **proprietario** del gruppo di risorse</li><li>Autorizzazioni di scrittura per la risorsa di destinazione</li><li>Se si usano i criteri di Azure ' DeployIfNotExist ' descritti di seguito, sono necessarie anche le autorizzazioni per l'assegnazione di criteri</li></ul>|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov, altri governi<br>![Sì](./media/icons/yes-icon.png) Governo cinese|
|||


## <a name="what-data-types-can-be-exported"></a>Quali tipi di dati possono essere esportati?

L'esportazione continua può esportare i tipi di dati seguenti ogni volta che cambiano:

- Avvisi di sicurezza
- Suggerimenti per la sicurezza 
- Risultati di sicurezza che possono essere considerati come raccomandazioni "Sub", come i risultati degli scanner di valutazione della vulnerabilità o aggiornamenti di sistema specifici. È possibile scegliere di includerli con le raccomandazioni ' Parent ', ad esempio "gli aggiornamenti di sistema devono essere installati nei computer".
- Punteggio sicuro (per sottoscrizione o per controllo)
- Dati di conformità normativi

> [!NOTE]
> L'esportazione di un punteggio sicuro e dei dati sulla conformità alle normative è una funzionalità di anteprima e non è disponibile nei cloud governativi. 

## <a name="set-up-a-continuous-export"></a>Configurare un'esportazione continua 

È possibile configurare l'esportazione continua dalle pagine del Centro sicurezza in portale di Azure, tramite l'API REST del Centro sicurezza o su larga scala usando i modelli di criteri di Azure forniti. Selezionare la scheda appropriata di seguito per informazioni dettagliate.

### <a name="use-the-azure-portal"></a>[**Usare il portale di Azure**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Configurare l'esportazione continua dalle pagine del Centro sicurezza in portale di Azure

I passaggi seguenti sono necessari se si sta configurando un'esportazione continua in Log Analytics area di lavoro o hub eventi di Azure.

1. Dall'intestazione laterale del Centro sicurezza selezionare **prezzi & impostazioni**.
1. Selezionare la sottoscrizione specifica per la quale si desidera configurare l'esportazione dei dati.
1. Dall'intestazione laterale della pagina impostazioni per la sottoscrizione selezionare **esportazione continua**.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Opzioni di esportazione nel centro sicurezza di Azure":::

    Qui vengono visualizzate le opzioni di esportazione. È disponibile una scheda per ogni destinazione di esportazione disponibile. 

1. Selezionare il tipo di dati che si desidera esportare e scegliere tra i filtri in ogni tipo (ad esempio, esportare solo gli avvisi con livello di gravità elevato).
1. Selezionare la frequenza di esportazione appropriata:
    - **Streaming** : le valutazioni verranno inviate in tempo reale quando lo stato di integrità di una risorsa viene aggiornato (se non si verifica alcun aggiornamento, non verrà inviato alcun dato).
    - **Snapshot** : uno snapshot dello stato corrente di tutte le valutazioni della conformità alle normative verrà inviato ogni settimana (si tratta di una funzionalità di anteprima per gli snapshot settimanali dei punteggi sicuri e dei dati di conformità alle normative).

1. Facoltativamente, se la selezione include uno di questi consigli, è possibile includere i risultati della valutazione della vulnerabilità insieme a essi:
    - È necessario correggere i risultati della valutazione della vulnerabilità nei database SQL
    - I risultati della valutazione della vulnerabilità nei computer SQL Server devono essere corretti (anteprima)
    - È consigliabile correggere le vulnerabilità delle immagini del Registro Azure Container (con tecnologia Qualys)
    - È consigliabile correggere le vulnerabilità nelle macchine virtuali
    - Gli aggiornamenti di sistema devono essere installati nelle macchine

    Per includere i risultati con questi consigli, abilitare l'opzione **Includi risultati di sicurezza** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Interruttore Includi i risultati per la sicurezza nella configurazione dell'esportazione continua" :::

1. Dall'area "Esporta destinazione" scegliere il percorso in cui salvare i dati. I dati possono essere salvati in una destinazione in una sottoscrizione diversa, ad esempio in un'istanza centrale dell'hub eventi o in un'area di lavoro centrale Log Analytics.
1. Selezionare **Salva**.

### <a name="use-the-rest-api"></a>[**Usare l'API REST**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Configurare l'esportazione continua con l'API REST

L'esportazione continua può essere configurata e gestita tramite l' [API di automazione](/rest/api/securitycenter/automations)del Centro sicurezza di Azure. Usare questa API per creare o aggiornare le regole per l'esportazione in una delle destinazioni possibili seguenti:

- Hub eventi di Azure
- Area di lavoro Log Analytics
- App per la logica di Azure 

L'API fornisce funzionalità aggiuntive non disponibili dal portale di Azure, ad esempio:

* **Volume maggiore** : l'API consente di creare più configurazioni di esportazione in una singola sottoscrizione. La pagina **esportazione continua** nell'interfaccia utente del portale del Centro sicurezza supporta solo una configurazione di esportazione per sottoscrizione.

* **Funzionalità aggiuntive** : l'API offre parametri aggiuntivi che non vengono visualizzati nell'interfaccia utente. Ad esempio, è possibile aggiungere tag alla risorsa di automazione, nonché definire l'esportazione in base a un set più ampio di proprietà di avviso e raccomandazione rispetto a quelle offerte nella pagina **esportazione continua** nell'interfaccia utente del portale del Centro sicurezza.

* **Ambito più mirato** : l'API fornisce un livello più granulare per l'ambito delle configurazioni di esportazione. Quando si definisce un'esportazione con l'API, è possibile eseguire questa operazione a livello di gruppo di risorse. Se si usa la pagina **esportazione continua** nell'interfaccia utente del portale del Centro sicurezza, è necessario definirla a livello di sottoscrizione.

    > [!TIP]
    > Se sono state configurate più configurazioni di esportazione tramite l'API o se sono stati usati parametri solo API, le funzionalità aggiuntive non verranno visualizzate nell'interfaccia utente del Centro sicurezza. Al contrario, sarà presente un banner che informa che sono presenti altre configurazioni.

Altre informazioni sull'API Automations sono disponibili nella [documentazione dell'API REST](/rest/api/securitycenter/automations).





### <a name="deploy-at-scale-with-azure-policy"></a>[**Distribuisci su larga scala con criteri di Azure**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Configurare l'esportazione continua su larga scala usando i criteri forniti

L'automazione dei processi di monitoraggio e risposta agli eventi imprevisti dell'organizzazione può migliorare significativamente il tempo necessario per indagare e attenuare gli eventi imprevisti relativi alla sicurezza.

Per distribuire le configurazioni di esportazione continua nell'organizzazione, usare i criteri di Azure ' DeployIfNotExist ' indicati di seguito per creare e configurare le procedure di esportazione continua.

**Per implementare questi criteri**

1. Nella tabella seguente selezionare il criterio che si vuole applicare:

    |Obiettivo  |Policy  |ID condizione  |
    |---------|---------|---------|
    |Esportazione continua in Hub eventi|[Distribuisci esportazione in hub eventi per gli avvisi e le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Esportazione continua nell'area di lavoro Log Analytics|[Distribuisci esportazione nell'area di lavoro Log Analytics per gli avvisi e le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > È anche possibile trovarli cercando criteri di Azure:
    > 1. Aprire Criteri di Azure.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Accesso a criteri di Azure":::
    > 2. Dal menu criteri di Azure selezionare **definizioni** e cercarle in base al nome. 

1. Nella pagina Criteri di Azure pertinente selezionare **assegna**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Assegnazione dei criteri di Azure":::

1. Aprire ogni scheda e impostare i parametri nel modo desiderato:
    1. Nella scheda **nozioni di base** impostare l'ambito per il criterio. Per utilizzare la gestione centralizzata, assegnare il criterio al gruppo di gestione contenente le sottoscrizioni che utilizzeranno la configurazione dell'esportazione continua. 
    1. Nella scheda **parametri** impostare i dettagli del gruppo di risorse e del tipo di dati. 
        > [!TIP]
        > Ogni parametro presenta una descrizione comando che illustra le opzioni disponibili.
        >
        > La scheda parametri di criteri di Azure (1) consente di accedere a opzioni di configurazione simili come pagina di esportazione continua del Centro sicurezza (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Confronto dei parametri nell'esportazione continua con criteri di Azure" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Facoltativamente, per applicare questa assegnazione alle sottoscrizioni esistenti, aprire la scheda **monitoraggio e aggiornamento** e selezionare l'opzione per creare un'attività di correzione.
1. Esaminare la pagina Riepilogo e selezionare **Crea**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informazioni sull'esportazione in un'area di lavoro Log Analytics

Per analizzare i dati del Centro sicurezza di Azure all'interno di un'area di lavoro Log Analytics o usare gli avvisi di Azure insieme agli avvisi del Centro sicurezza, configurare l'esportazione continua nell'area di lavoro di Log Analytics.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabelle e schemi

Gli avvisi di sicurezza e le raccomandazioni vengono archiviati rispettivamente nelle tabelle *SecurityAlert* e *SecurityRecommendation* . 

Il nome della soluzione Log Analytics contenente queste tabelle varia a seconda che Azure Defender sia abilitato: Security (' Sicurezza e controllo ') o SecurityCenterFree. 

> [!TIP]
> Per visualizzare i dati nell'area di lavoro di destinazione, è necessario abilitare una di queste soluzioni **sicurezza e controllo** o **SecurityCenterFree**.

![La tabella * SecurityAlert * in Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Per visualizzare gli schemi di eventi dei tipi di dati esportati, visitare gli [schemi della tabella log Analytics](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Visualizzare gli avvisi e le raccomandazioni esportati in monitoraggio di Azure

È anche possibile scegliere di visualizzare gli avvisi di sicurezza e/o le raccomandazioni esportati in [monitoraggio di Azure](../azure-monitor/alerts/alerts-overview.md). 

Monitoraggio di Azure offre un'esperienza unificata per gli avvisi per un'ampia gamma di avvisi di Azure, tra cui log di diagnostica, avvisi sulle metriche e avvisi personalizzati basati sulle query Log Analytics area di lavoro.

Per visualizzare gli avvisi e le raccomandazioni dal centro sicurezza in monitoraggio di Azure, configurare una regola di avviso basata su query Log Analytics (avviso log):

1. Dalla pagina degli **avvisi** di monitoraggio di Azure selezionare **nuova regola di avviso**.

    ![Pagina degli avvisi di monitoraggio di Azure](./media/continuous-export/azure-monitor-alerts.png)

1. Nella pagina Crea regola configurare la nuova regola, nello stesso modo in cui si configura una [regola di avviso del log in monitoraggio di Azure](../azure-monitor/alerts/alerts-unified-log.md):

    * Per **risorsa** selezionare l'area di lavoro log Analytics alla quale sono stati esportati gli avvisi di sicurezza e le raccomandazioni.

    * In **condizione** selezionare **Ricerca log personalizzata**. Nella pagina visualizzata configurare la query, il periodo lookback e il periodo di frequenza. Nella query di ricerca è possibile digitare *SecurityAlert* o *SecurityRecommendation* per eseguire una query sui tipi di dati a cui il Centro sicurezza Esporta continuamente quando si Abilita l'esportazione continua in log Analytics funzionalità. 
    
    * Facoltativamente, configurare il [gruppo di azioni](../azure-monitor/alerts/action-groups.md) che si vuole attivare. I gruppi di azioni possono attivare l'invio di posta elettronica, i ticket ITSM, i webhook e altro ancora.
    ![Regola di avviso di monitoraggio di Azure](./media/continuous-export/azure-monitor-alert-rule.png)

Verranno ora visualizzati nuovi avvisi del Centro sicurezza di Azure o consigli (a seconda delle regole di esportazione continua configurate e della condizione definita nella regola di avviso di monitoraggio di Azure) negli avvisi di monitoraggio di Azure, con attivazione automatica di un gruppo di azioni (se specificato).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Esportazione manuale di avvisi e raccomandazioni

Per scaricare un report CSV per avvisi o raccomandazioni, aprire la pagina avvisi o **raccomandazioni** di **sicurezza** e selezionare il pulsante **Scarica report CSV** .

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="Scarica i dati degli avvisi come file CSV" lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> Questi report contengono avvisi e consigli per le risorse delle sottoscrizioni attualmente selezionate.


## <a name="faq---continuous-export"></a>Domande frequenti-esportazione continua

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Quali sono i costi legati all'esportazione dei dati?

Non è previsto alcun costo per l'abilitazione di un'esportazione continua. I costi possono essere sostenuti per l'inserimento e la conservazione dei dati nell'area di lavoro Log Analytics, a seconda della configurazione in uso. 

Scopri di più su [log Analytics prezzi dell'area di lavoro](https://azure.microsoft.com/pricing/details/monitor/).

Scopri di più sui [prezzi di hub eventi di Azure](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>L'esportazione include i dati sullo stato corrente di tutte le risorse?

No. L'esportazione continua è compilata per lo streaming di **eventi**:

- Gli **avvisi** ricevuti prima dell'abilitazione dell'esportazione non verranno esportati.
- Le **raccomandazioni** vengono inviate ogni volta che viene modificato lo stato di conformità di una risorsa. Ad esempio, quando una risorsa passa da integro a non integro. Pertanto, come per gli avvisi, le raccomandazioni per le risorse che non hanno modificato lo stato poiché l'esportazione abilitata non verranno esportate.
- Il **Punteggio sicuro (anteprima)** per ogni controllo di sicurezza o sottoscrizione viene inviato quando il Punteggio di un controllo di sicurezza viene modificato di 0,01 o superiore. 
- **Lo stato di conformità alle normative (anteprima)** viene inviato quando lo stato della conformità della risorsa viene modificato.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>Perché i consigli vengono inviati a intervalli diversi?

Raccomandazioni diverse hanno intervalli di valutazione della conformità diversi, che possono variare da pochi minuti a pochi giorni. Di conseguenza, le raccomandazioni saranno diverse nella quantità di tempo necessaria per la visualizzazione nelle esportazioni.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>L'esportazione continua supporta gli scenari di continuità aziendale o ripristino di emergenza (BCDR)?

Quando si prepara l'ambiente per scenari BCDR, in cui la risorsa di destinazione sta riscontrando un'interruzione o un'altra situazione di emergenza, è responsabilità dell'organizzazione prevenire la perdita di dati stabilendo i backup in base alle linee guida dell'hub eventi di Azure, Log Analytics area di lavoro e app per la logica.

Altre informazioni sono disponibili in [Hub eventi di Azure-ripristino di emergenza geografico](../event-hubs/event-hubs-geo-dr.md).


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>L'esportazione continua è disponibile con il Centro sicurezza di Azure gratuito?

Sì. Si noti che molti avvisi del Centro sicurezza sono forniti solo quando è stato abilitato Azure Defender. Un modo efficace per visualizzare in anteprima gli avvisi che si otterranno nei dati esportati consiste nel visualizzare gli avvisi visualizzati nelle pagine del Centro sicurezza nel portale di Azure.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare le esportazioni continue dei consigli e degli avvisi. Si è inoltre appreso come scaricare i dati degli avvisi come file CSV. 

Per materiale correlato, vedere la documentazione seguente: 

- Altre informazioni sui [modelli di automazione del flusso di lavoro](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Documentazione di Hub eventi di Azure](../event-hubs/index.yml)
- [Documentazione di Azure Sentinel](../sentinel/index.yml)
- [Documentazione di Monitoraggio di Azure](../azure-monitor/index.yml)
- [Esportare gli schemi dei tipi di dati](https://aka.ms/ASCAutomationSchemas)