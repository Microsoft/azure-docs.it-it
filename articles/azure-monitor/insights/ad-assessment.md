---
title: Ottimizzare l'ambiente Active Directory con Monitoraggio di Azure|Microsoft Docs
description: È possibile usare la soluzione Controllo integrità Active Directory per valutare i rischi e l'integrità degli ambienti a intervalli regolari.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: 3c86b21c5e0533ab6a1a3c64dc601eb8bb573547
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318097"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Ottimizzare l'ambiente Active Directory con la soluzione Controllo integrità Active Directory in Monitoraggio di Azure

![Simbolo di Controllo integrità AD](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

È possibile usare la soluzione Controllo integrità Active Directory per valutare i rischi e l'integrità degli ambienti server a intervalli regolari. Questo articolo descrive come installare e usare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in quattro aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione fornisce informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e completato un controllo, nel dashboard di **Controllo integrità AD** per l'infrastruttura nell'ambiente vengono visualizzate le informazioni di riepilogo per le aree di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard di **Controllo integrità AD**, dove è possibile visualizzare, e quindi eseguire, le azioni consigliate per l'infrastruttura server di Active Directory.  

![Immagine del riquadro di Controllo integrità AD](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Immagine del dashboard di Controllo integrità AD](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Prerequisiti

* La soluzione Controllo integrità Active Directory richiede l'installazione di una versione supportata di .NET Framework 4.6.2 o successiva in ogni computer in cui è installato l'Agente di Log Analytics di Microsoft (chiamato anche Microsoft Monitoring Agent (MMA)).  L'agente viene usato da System Center 2016 Operations Manager, Operations Manager 2012 R2, nonché da Monitoraggio di Azure.
* La soluzione supporta controller di dominio che eseguono Windows Server 2008 e 2008 R2, Windows Server 2012 e 2012 R2, Windows Server 2016 e Windows Server 2019.
* area di lavoro Log Analytics per aggiungere la soluzione Controllo integrità Active Directory da Azure Marketplace al portale di Azure. Non è necessaria alcuna configurazione aggiuntiva.

  > [!NOTE]
  > Dopo aver aggiunto la soluzione, il file AdvisorAssessment.exe viene aggiunto al server con agenti. I dati di configurazione vengono letti e quindi inviati a Monitoraggio di Azure nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud.
  >
  >

Per eseguire il controllo integrità dei controller di dominio che sono membri del dominio da valutare, ciascun controller di dominio in quel dominio richiede un agente e la connettività a Monitoraggio di Azure tramite uno dei metodi supportati seguenti:

1. Installare l'[agente di Log Analytics per Windows](../platform/agent-windows.md) se il controller di dominio non è già monitorato da System Center 2016 Operations Manager o Operations Manager 2012 R2.
2. Se si esegue il monitoraggio con System Center 2016 Operations Manager oppure Operations Manager 2012 R2 e il gruppo di gestione non è integrato con Monitoraggio di Azure, è possibile usare una configurazione multihomed del controller di dominio con Monitoraggio di Azure per raccogliere i dati e inoltrarli al servizio, mantenendo il monitoraggio tramite Operations Manager.  
3. In caso contrario, se il gruppo di gestione di Operations Manager è integrato con il servizio, è necessario aggiungere i controller di dominio per la raccolta dati da parte del servizio seguendo i passaggi descritti in [Aggiungere computer gestiti dagli agenti](../platform/om-agents.md#connecting-operations-manager-to-azure-monitor) dopo aver abilitato la soluzione nell'area di lavoro.  

L'agente nel controller di dominio che fa riferimento a un gruppo di gestione di Operations Manager raccoglie i dati e li inoltra al server di gestione assegnato, quindi viene eseguito l'invio direttamente da un server di gestione a Monitoraggio di Azure.  I dati non vengono scritti nei database di Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Informazioni dettagliate sulla raccolta dati di Controllo integrità Active Directory

Controllo integrità Active Directory raccoglie i dati dalle origini seguenti usando l'agente abilitato:

- Registro
- LDAP
- .NET Framework
- Registro eventi
- Active Directory Service Interfaces (ADSI)
- Windows PowerShell
- Dati dei file
- Strumentazione gestione Windows (WMI, Windows Management Instrumentation)
- API dello strumento DCDIAG
- API di File Replication Service (NTFRS)
- Codice personalizzato in C#

I dati vengono raccolti nel controller di dominio e inoltrati a Monitoraggio di Azure ogni sette giorni.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Informazioni sulla classificazione in ordine di priorità delle raccomandazioni

A ogni raccomandazione generata viene assegnato un valore di ponderazione che identifica l'importanza relativa della raccomandazione. Vengono visualizzate solo le 10 raccomandazioni più importanti.

### <a name="how-weights-are-calculated"></a>Come vengono calcolate le ponderazioni

Le ponderazioni sono valori aggregati che si basano su tre fattori chiave:

* La *probabilità* che un problema identificato causi inconvenienti. Una probabilità più elevata equivale a un punteggio complessivamente maggiore per la raccomandazione.
* L' *impatto* del problema per l'organizzazione se causa effettivamente un problema. Un impatto più elevato equivale a un punteggio complessivamente maggiore per la raccomandazione.
* Il *lavoro* richiesto per implementare la raccomandazione. Un lavoro richiesto più elevato equivale a un punteggio complessivamente inferiore per la raccomandazione.

La ponderazione per ogni raccomandazione è espressa come percentuale del punteggio totale disponibile per ogni area di interesse. Ad esempio, se una raccomandazione nell'area di interesse Sicurezza e conformità ha un punteggio pari al 5%, l'implementazione della raccomandazione aumenta del 5% il punteggio complessivo di quell'area.

### <a name="focus-areas"></a>Aree di interesse

**Sicurezza e conformità** : quest'area di interesse descrive raccomandazioni relative alle potenziali minacce e violazioni della sicurezza, ai criteri aziendali e ai requisiti di conformità tecnici, legali e normativi.

**Disponibilità e continuità aziendale** : quest'area di interesse descrive raccomandazioni relative alla disponibilità del servizio, alla resilienza dell'infrastruttura e alla protezione aziendale.

**Prestazioni e scalabilità** : quest'area di interesse descrive raccomandazioni utili per favorire la crescita dell'infrastruttura IT dell'organizzazione, assicurando che l'ambiente IT soddisfi gli attuali requisiti in termini di prestazioni e possa rispondere alle mutevoli esigenze dell'infrastruttura.

**Aggiornamento, migrazione e distribuzione** : quest'area di interesse descrive raccomandazioni utili per aggiornare, eseguire la migrazione e distribuire Active Directory nell'infrastruttura esistente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>È consigliabile mirare a ottenere un punteggio del 100% in tutte le aree di interesse?

Non necessariamente. Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft attraverso migliaia di visite dei clienti. Non esistono tuttavia due infrastrutture di server uguali e raccomandazioni specifiche possono essere più o meno pertinenti per l'utente. Ad esempio, alcune raccomandazioni sulla sicurezza possono risultare meno pertinenti se le macchine virtuali non sono esposte a Internet. Alcune raccomandazioni sulla disponibilità possono risultare meno pertinenti per i servizi che forniscono creazione di report e raccolta dei dati ad hoc a bassa priorità. I problemi che possono essere importanti per un'azienda collaudata possono esserlo meno per una start-up. È consigliabile identificare quali sono le proprie aree di interesse prioritarie e quindi osservare il cambiamento dei punteggi nel tempo.

Ogni raccomandazione include informazioni aggiuntive sui motivi per cui potrebbe essere importante. È consigliabile usare queste informazioni aggiuntive per valutare se l'implementazione della raccomandazione è appropriata, a seconda della natura dei servizi IT e delle esigenze aziendali dell'organizzazione.

## <a name="use-health-check-focus-area-recommendations"></a>Usare le raccomandazioni relative alle aree di interesse del controllo integrità

Dopo l'installazione, è possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro di Controllo integrità nella pagina della soluzione nel portale di Azure.

Visualizzare il riepilogo delle valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Nella pagina **Panoramica** fare clic sul riquadro di **Controllo integrità Active Directory**.

2. Nella pagina **Controllo integrità** esaminare le informazioni di riepilogo in uno dei pannelli delle aree di interesse e quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.

3. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione in **Affected Objects** (Oggetti interessati) per visualizzare i dettagli relativi al motivo per cui è stata generata.

    ![Immagine delle raccomandazioni di Controllo integrità](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. È possibile eseguire le azioni correttive suggerite in **Suggested Actions**(Azioni suggerite). Dopo la risoluzione dell'elemento, le valutazioni successive indicano che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

## <a name="ignore-recommendations"></a>Ignorare le raccomandazioni

Per ignorare alcune raccomandazioni, è possibile creare un file di testo che Monitoraggio di Azure userà per impedire la visualizzazione delle raccomandazioni nei risultati della valutazione.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Per identificare le raccomandazioni che verranno ignorate

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Usare la query seguente per elencare le raccomandazioni non riuscite per i computer nell'ambiente.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Viene riportato di seguito uno screenshot che mostra la query di log:<

![raccomandazioni non riuscite](media/ad-assessment/ad-failed-recommendations.png)

Scegliere le raccomandazioni da ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Per creare e usare un file di testo IgnoreRecommendations.txt

1. Creare un file denominato IgnoreRecommendations.txt.

2. Incollare o digitare ciascun ID raccomandazione per ogni raccomandazione che Monitoraggio di Azure dovrà ignorare in una riga separata e quindi salvare e chiudere il file.

3. Inserire il file nella cartella seguente in ogni computer in cui si vuole che Monitoraggio di Azure ignori le raccomandazioni.

   * Nei computer con Microsoft Monitoring Agent, connesso direttamente o tramite Operations Manager, *SystemDrive*:\Programmi\Microsoft Monitoring Agent\Agent
   * Nel server di gestione di Operations Manager 2012 R2, *UnitàSistema*:\Programmi\Microsoft System Center 2012 R2\Operations Manager\Server
   * Nel server di gestione di Operations Manager 2016, *UnitàSistema*:\Programmi\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Per verificare che le raccomandazioni vengano ignorate

Dopo l'esecuzione del controllo integrità successivo pianificato, per impostazione predefinita ogni sette giorni, le raccomandazioni specificate vengono contrassegnate come *ignorate* e non vengono visualizzate nel dashboard.

1. È possibile usare le query di log seguenti per elencare tutte le raccomandazioni ignorate.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure rimuovere gli ID raccomandazione dagli stessi.

## <a name="ad-health-check-solutions-faq"></a>Domande frequenti sulla soluzione Controllo integrità AD

*Quali controlli vengono eseguiti dalle soluzioni di valutazione AD?*

* La query seguente illustra una descrizione di tutti i controlli attualmente eseguiti:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
I risultati possono quindi essere esportati in Excel per analizzarli più attentamente.

*Con che frequenza viene eseguito un controllo integrità?*

* Il controllo viene eseguito ogni sette giorni.

*È possibile configurare la frequenza di esecuzione del controllo integrità?*

* Attualmente non è possibile.

*Se viene rilevato un altro server dopo l'aggiunta di una soluzione di controllo integrità, il server verrà controllato?*

* Sì, dal momento in cui viene rilevato verrà controllato ogni sette giorni.

*Se un server viene ritirato, quando sarà rimosso dal controllo integrità?*

* Se un server non invia dati per 3 settimane, verrà rimosso.

*Qual è il nome del processo che esegue la raccolta di dati?*

* AdvisorAssessment.exe

*Quanto tempo occorre per la raccolta di dati?*

* La raccolta di dati effettiva sul server richiede circa 1 ora. Potrebbe essere necessario più tempo nei server in cui è presente un numero elevato di server di Active Directory.

*È possibile definire l'orario per la raccolta di dati?*

* Attualmente non è possibile.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?*

* Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, usare una query di log per mostrare tutte le raccomandazioni.

*È possibile ignorare una raccomandazione?*

* Sì, vedere la sezione [Ignorare le raccomandazioni](#ignore-recommendations) sopra.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Query di log di Monitoraggio di Azure](../log-query/log-query-overview.md) per informazioni su come analizzare le raccomandazioni e i dati dettagliati di Controllo integrità AD.

