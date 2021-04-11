---
title: Ottimizzare l'ambiente di SQL Server con monitoraggio di Azure | Microsoft Docs
description: Con monitoraggio di Azure è possibile usare la soluzione controllo integrità SQL per valutare i rischi e l'integrità degli ambienti a intervalli regolari.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 0f2319ea6ba314c08a67651667837f05df5765a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723232"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Ottimizzare l'ambiente SQL con la soluzione controllo integrità SQL Server in monitoraggio di Azure

![Simbolo di Controllo integrità SQL](./media/sql-assessment/sql-assessment-symbol.png)

È possibile usare la soluzione Controllo integrità SQL per valutare i rischi e l'integrità degli ambienti server a intervalli regolari. Questo articolo consente di installare la soluzione in modo che si possano intraprendere azioni correttive per problemi potenziali.

La soluzione offre un elenco con priorità di raccomandazioni specifiche per l'infrastruttura distribuita dei server, classificate in sei aree di interesse che consentono di comprendere rapidamente il rischio e agire in maniera appropriata.

Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft in base a migliaia di visite dei clienti. Ogni raccomandazione fornisce informazioni aggiuntive sui motivi per cui un problema può essere rilevante per l'utente e su come implementare le modifiche suggerite.

Si possono scegliere le aree di interesse più importanti per l'organizzazione e tenere traccia dello stato di avanzamento verso la realizzazione di un ambiente integro ed esente da rischi.

Dopo aver aggiunto la soluzione e completato una valutazione, nel dashboard di **Controllo integrità SQL** per l'infrastruttura nell'ambiente vengono visualizzate le informazioni di riepilogo per le aree di interesse. Le sezioni seguenti descrivono come usare le informazioni visualizzate nel dashboard di **Controllo integrità SQL**, dove è possibile visualizzare, e quindi eseguire, le azioni consigliate per l'infrastruttura di SQL Server.

![Immagine del riquadro di Controllo integrità SQL](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![Immagine del dashboard di Controllo integrità SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Prerequisiti

* La soluzione controllo integrità SQL richiede una versione supportata di .NET Framework 4.6.2 installato in ogni computer in cui è installato il Microsoft Monitoring Agent (MMA).  L'agente MMA viene usato da System Center 2016 Operations Manager e Operations Manager 2012 R2, nonché da Monitoraggio di Azure.  
* La soluzione supporta SQL Server versione 2012, 2014, 2016, 2017 e 2019.
* area di lavoro Log Analytics per aggiungere la soluzione Controllo integrità SQL da Azure Marketplace al portale di Azure. Per installare la soluzione, l'utente deve essere amministratore o collaboratore per la sottoscrizione di Azure.

  > [!NOTE]
  > Dopo aver aggiunto la soluzione, il file AdvisorAssessment.exe viene aggiunto al server con agenti. I dati di configurazione vengono letti e quindi inviati a Monitoraggio di Azure nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud.
  >
  >

Per eseguire il controllo dell'integrità sui server SQL Server, richiedono un agente e la connettività a monitoraggio di Azure usando uno dei metodi supportati seguenti:

1. Installare [Microsoft Monitoring Agent (MMA)](../agents/agent-windows.md) se il server non è già monitorato da System Center 2016 Operations Manager o Operations Manager 2012 R2.
2. Se viene monitorato con System Center 2016-Operations Manager o Operations Manager 2012 R2 e il gruppo di gestione non è integrato con monitoraggio di Azure, il server può essere multihomed con Log Analytics per raccogliere i dati e inviarli al servizio e continuare a monitorarli con Operations Manager.  
3. In caso contrario, se il gruppo di gestione di Operations Manager è integrato con il servizio, è necessario aggiungere i controller di dominio per la raccolta dati da parte del servizio seguendo i passaggi descritti in [Aggiungere computer gestiti dagli agenti](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) dopo aver abilitato la soluzione nell'area di lavoro.  

L'agente sul SQL Server che segnala a un gruppo di gestione di Operations Manager, raccoglie i dati, li inoltra al server di gestione assegnato e quindi viene inviato direttamente da un server di gestione a monitoraggio di Azure.  I dati non vengono scritti nei database di Operations Manager.  

Se l'istanza di SQL Server è monitorata da Operations Manager, è necessario configurare un account RunAs di Operations Manager. Per ulteriori informazioni, vedere [Operations Manager account RunAs per monitoraggio di Azure](#operations-manager-run-as-accounts-for-log-analytics) .

## <a name="sql-health-check-data-collection-details"></a>Informazioni dettagliate sulla raccolta dati di Controllo integrità SQL
Controllo integrità SQL raccoglie i dati dalle origini seguenti usando l'agente abilitato:

* Strumentazione gestione Windows (WMI, Windows Management Instrumentation)
* Registro
* Contatori delle prestazioni
* Risultati della DMV (Dynamic Management View, vista a gestione dinamica) di SQL Server

I dati vengono raccolti in SQL Server e inoltrati a Log Analytics ogni sette giorni.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Account RunAs di Operations Manager per Log Analytics
Log Analytics usa il gruppo di gestione e l'agente Operations Manager per raccogliere e inviare dati al servizio Log Analytics. Log Analytics si basa sui Management Pack per i carichi di lavoro per offrire servizi a valore aggiunto. Ogni carico di lavoro richiede privilegi specifici per eseguire i Management Pack in un contesto di sicurezza diverso, ad esempio un account utente di dominio. È necessario fornire informazioni sulle credenziali configurando un account RunAs di Operations Manager.

Usare le informazioni seguenti per impostare l'account RunAs di Operations Manager per Controllo integrità SQL.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Impostare l'account RunAs per Controllo integrità SQL
 Se si usa già il Management Pack di SQL Server, è consigliabile usare la configurazione RunAs.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Per configurare l'account RunAs di SQL in Operations Console
> [!NOTE]
> Per impostazione predefinita, i flussi di lavoro nel Management Pack vengono sempre eseguiti nel contesto di sicurezza dell'account di sistema locale. Se si usa Microsoft Monitoring Agent connesso direttamente al servizio e non per inviare direttamente i contenuti a un gruppo di gestione di Operations Manager, ignorare i passaggi 1-5 seguenti ed eseguire l'esempio T-SQL o PowerShell, specificando NT AUTHORITY\SYSTEM come nome utente.
>
>

1. In Operations Manager aprire la console operatore e quindi fare clic su **Administration**.
2. In **Run As Configuration** (Configurazione RunAs) fare clic su **Profiles** (Profili) e aprir **SQL Assessment Run As Profile** (Profilo RunAs di Valutazione SQL).
3. Nella pagina **Run As Account** fare clic su **Add**.
4. Selezionare un account RunAs Windows che contiene le credenziali necessarie per SQL Server oppure fare clic su **New** per crearne uno.

   > [!NOTE]
   > Il tipo dell'account RunAs deve essere Windows. L'account RunAs deve appartenere anche al gruppo Local Administrators in tutti i server Windows che ospitano istanze di SQL Server.
   >
   >
5. Fare clic su **Salva**.
6. Modificare ed eseguire l'esempio T-SQL seguente in ogni istanza di SQL Server per concedere le autorizzazioni minime richieste dall'account RunAs per eseguire il controllo integrità. Non è tuttavia necessario farlo se l'account RunAs fa già parte del ruolo del server sysadmin nelle istanze di SQL Server.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Per configurare l'account RunAs di SQL con Windows PowerShell
Aprire una finestra di PowerShell ed eseguire il seguente script dopo averlo aggiornato con le informazioni:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Informazioni sulla classificazione in ordine di priorità delle raccomandazioni
A ogni raccomandazione generata viene assegnato un valore di ponderazione che identifica l'importanza relativa della raccomandazione. Vengono visualizzate solo le dieci raccomandazioni più importanti.

### <a name="how-weights-are-calculated"></a>Come vengono calcolate le ponderazioni
Le ponderazioni sono valori aggregati che si basano su tre fattori chiave:

* *Probabilità* che un problema identificato sia causa di problemi. Una probabilità più elevata equivale a un punteggio complessivamente maggiore per la raccomandazione.
* L' *impatto* del problema per l'organizzazione se causa effettivamente un problema. Un impatto più elevato equivale a un punteggio complessivamente maggiore per la raccomandazione.
* Il *lavoro* richiesto per implementare la raccomandazione. Un lavoro richiesto più elevato equivale a un punteggio complessivamente inferiore per la raccomandazione.

La ponderazione per ogni raccomandazione è espressa come percentuale del punteggio totale disponibile per ogni area di interesse. Ad esempio, se una raccomandazione nell'area di interesse relativa a sicurezza e conformità ha un punteggio pari al 5%, l'implementazione della raccomandazione aumenterà del 5% il punteggio complessivo di quell'area.

### <a name="focus-areas"></a>Aree di interesse
**Sicurezza e conformità** : quest'area di interesse descrive raccomandazioni relative alle potenziali minacce e violazioni della sicurezza, ai criteri aziendali e ai requisiti di conformità tecnici, legali e normativi.

**Disponibilità e continuità aziendale** : quest'area di interesse descrive raccomandazioni relative alla disponibilità del servizio, alla resilienza dell'infrastruttura e alla protezione aziendale.

**Prestazioni e scalabilità** : quest'area di interesse descrive raccomandazioni utili per favorire la crescita dell'infrastruttura IT dell'organizzazione, assicurando che l'ambiente IT soddisfi gli attuali requisiti in termini di prestazioni e possa rispondere alle mutevoli esigenze dell'infrastruttura.

**Aggiornamento, migrazione e distribuzione**: quest'area di interesse mostra le raccomandazioni utili per aggiornare, eseguire la migrazione e distribuire SQL Server nell'infrastruttura esistente.

**Operazioni e monitoraggio**: quest'area di interesse mostra le raccomandazioni utili per semplificare le operazioni IT, implementare la manutenzione preventiva e ottimizzare le prestazioni.

**Gestione modifiche e configurazione**: quest'area di interesse mostra le raccomandazioni utili per proteggere le operazioni quotidiane, assicurare che le modifiche non influiscano negativamente sull'infrastruttura, stabilire procedure di controllo delle modifiche e rilevare e monitorare le configurazioni del sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>È consigliabile mirare a ottenere un punteggio del 100% in tutte le aree di interesse?
Non necessariamente. Le raccomandazioni si basano sulla conoscenza e sull'esperienza acquisite dai tecnici Microsoft attraverso migliaia di visite dei clienti. Non esistono tuttavia due infrastrutture di server uguali e raccomandazioni specifiche possono essere più o meno pertinenti per l'utente. Ad esempio, alcune raccomandazioni sulla sicurezza possono risultare meno pertinenti se le macchine virtuali non sono esposte a Internet. Alcune raccomandazioni sulla disponibilità possono risultare meno pertinenti per i servizi che forniscono creazione di report e raccolta dei dati ad hoc a bassa priorità. I problemi che possono essere importanti per un'azienda collaudata possono esserlo meno per una start-up. È consigliabile identificare quali sono le proprie aree di interesse prioritarie e quindi osservare il cambiamento dei punteggi nel tempo.

Ogni raccomandazione include informazioni aggiuntive sui motivi per cui potrebbe essere importante. È consigliabile usare queste informazioni aggiuntive per valutare se l'implementazione della raccomandazione è appropriata, a seconda della natura dei servizi IT e delle esigenze aziendali dell'organizzazione.

## <a name="use-health-check-focus-area-recommendations"></a>Usare le raccomandazioni relative alle aree di interesse del controllo integrità
Prima di poter usare una soluzione di valutazione in monitoraggio di Azure, è necessario che la soluzione sia installata.  Dopo l'installazione, è possibile visualizzare il riepilogo delle raccomandazioni usando il riquadro di controllo integrità SQL nella pagina **Panoramica** di monitoraggio di Azure nella portale di Azure.

Visualizzare il riepilogo delle valutazioni relative alla conformità per l'infrastruttura, quindi visualizzare le raccomandazioni nel dettaglio.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Per visualizzare le raccomandazioni per un'area di interesse e applicare un'azione correttiva
1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Monitoraggio**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Monitoraggio**.
3. Nella sezione **Insights** del menu selezionare **altro**.  
4. Nella pagina **Panoramica** fare clic sul riquadro di **Controllo integrità SQL**.
5. Nella pagina **Controllo integrità** esaminare le informazioni di riepilogo in uno dei pannelli delle aree di interesse e quindi fare clic su un'area specifica per visualizzare le raccomandazioni corrispondenti.
6. In una delle pagine relative alle aree di interesse è possibile visualizzare le raccomandazioni relative all'ambiente specifico, classificate in ordine di priorità. Fare clic su una raccomandazione in **Affected Objects** (Oggetti interessati) per visualizzare i dettagli relativi al motivo per cui è stata generata.<br><br> ![Immagine delle raccomandazioni di Controllo integrità SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. È possibile eseguire le azioni correttive suggerite in **Suggested Actions**(Azioni suggerite). Dopo la risoluzione dell'elemento, le valutazioni successive indicheranno che le azioni consigliate sono state effettuate e il punteggio relativo alla conformità aumenterà. Gli elementi corretti vengono visualizzati come **Passed Objects**.

## <a name="ignore-recommendations"></a>Ignorare le raccomandazioni
Per ignorare alcune raccomandazioni, è possibile creare un file di testo che Monitoraggio di Azure userà per impedire la visualizzazione delle raccomandazioni nei risultati della valutazione.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Per identificare le raccomandazioni che verranno ignorate
1. Nel menu monitoraggio di Azure fare clic su **log**.
2. Usare la query seguente per elencare le raccomandazioni non riuscite per i computer nell'ambiente.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Ecco una schermata che mostra la query di log:<br><br> ![raccomandazioni non riuscite](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Scegliere le raccomandazioni da ignorare. Nella procedura successiva verranno usati i valori per ID raccomandazione.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Per creare e usare un file di testo IgnoreRecommendations.txt
1. Creare un file denominato IgnoreRecommendations.txt.
2. Incollare o digitare ciascun ID raccomandazione per ogni raccomandazione che Monitoraggio di Azure dovrà ignorare in una riga separata e quindi salvare e chiudere il file.
3. Inserire il file nella cartella seguente in ogni computer in cui si vuole che Monitoraggio di Azure ignori le raccomandazioni.
   * Nei computer con Microsoft Monitoring Agent, connesso direttamente o tramite Operations Manager, *SystemDrive*:\Programmi\Microsoft Monitoring Agent\Agent
   * Nel server di gestione Operations Manager *SystemDrive*: \Programmi\Microsoft System Center 2012 R2\Operations Manager\Server
   * Nel server di gestione di Operations Manager 2016, *UnitàSistema*:\Programmi\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Per verificare che le raccomandazioni vengano ignorate
1. Dopo la successiva esecuzione della valutazione pianificata, per impostazione predefinita ogni 7 giorni, le raccomandazioni specificate sono contrassegnate come ignorate e non verranno visualizzate nel dashboard di valutazione.
2. È possibile usare le query di Ricerca log seguenti per elencare tutte le raccomandazioni ignorate.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Se in seguito si decide che si vogliono vedere le raccomandazioni ignorate, rimuovere eventuali file IgnoreRecommendations.txt oppure rimuovere gli ID raccomandazione dagli stessi.

## <a name="sql-health-check-solution-faq"></a>Domande frequenti sulla soluzione Controllo integrità SQL

*Quali controlli vengono eseguiti dalla soluzione Valutazione SQL?*

* La query seguente illustra una descrizione di tutti i controlli attualmente eseguiti:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
I risultati possono quindi essere esportati in Excel per analizzarli più attentamente.


*Con che frequenza viene eseguito un controllo integrità?*

* Il controllo viene eseguito ogni sette giorni.

*È possibile configurare la frequenza di esecuzione del controllo?*

* Attualmente non è possibile.

*Se viene rilevato un altro server dopo l'aggiunta della soluzione Controllo integrità SQL, il server verrà controllato?*

* Sì, dal momento in cui viene rilevato verrà controllato ogni sette giorni.

*Se un server viene ritirato, quando sarà rimosso dal controllo integrità?*

* Se un server non invia dati per 3 settimane, verrà rimosso.

*Qual è il nome del processo che esegue la raccolta di dati?*

* AdvisorAssessment.exe

*Quanto tempo occorre per la raccolta di dati?*

* La raccolta di dati effettiva sul server richiede circa 1 ora. È possibile che sia necessario più tempo nei server in cui è presente un numero elevato di istanze o database SQL.

*Quali tipi di dati vengono raccolti?*

* Vengono raccolti i tipi di dati seguenti:
  * WMI
  * Registro
  * Contatori delle prestazioni
  * DMV (Dynamic Management View) di SQL.

*È possibile definire l'orario per la raccolta di dati?*

* Attualmente non è possibile.

*Perché è necessario configurare un account RunAs?*

* Per un server SQL vengono eseguite alcune SQL. Per permetterne l'esecuzione, è necessario usare un account RunAs con autorizzazioni di tipo VISUALIZZAZIONE STATO DEL SERVER per SQL.  Per eseguire query relative a WMI, sono inoltre necessarie credenziali di amministratore locale.

*Perché vengono visualizzate solo le prime 10 raccomandazioni?*

* Invece di esaminare un lunghissimo elenco completo di attività, è consigliabile concentrare l'attenzione sulle raccomandazioni con priorità maggiore. Dopo la verifica delle raccomandazioni principali, verranno rese disponibili raccomandazioni aggiuntive. Se si preferisce visualizzare l'elenco dettagliato, è possibile vedere tutte le raccomandazioni usando la ricerca log di Log Analytics.

*È possibile ignorare una raccomandazione?*

* Sì, vedere la sezione [Ignorare le raccomandazioni](#ignore-recommendations) sopra.

## <a name="next-steps"></a>Passaggi successivi
* [Query di log](../logs/log-query-overview.md) per informazioni su come analizzare i dati dettagliati di controllo integrità SQL e le raccomandazioni.

