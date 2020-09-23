---
title: Esportare dati in Power BI da Azure Application Insights | Microsoft Docs
description: Le query di Analisi possono essere visualizzate in Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 804da7d3aaa2fd1b7973a686bc9ca5cb5ee973a2
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90979168"
---
# <a name="feed-power-bi-from-application-insights"></a>Feed di Power BI da Application Insights
[Power BI](https://www.powerbi.com/) è un insieme di strumenti aziendali che consente di analizzare i dati e condividere informazioni dettagliate e significative. Dashboard completi sono disponibili in tutti i dispositivi. È possibile combinare dati provenienti da diverse origini, incluse le query di Analytics di [Azure Application Insights](./app-insights-overview.md).

Esistono tre metodi per esportare i dati di Application Insights in Power BI:

* [**Esportare le query di analisi**](#export-analytics-queries). Questo è il metodo preferito. Scrivere le query desiderate ed esportarle in Power BI. La query può essere inserita in un dashboard, insieme a tutti gli altri dati.
* [**Esportazione continua e Analisi di flusso di Azure**](./export-stream-analytics.md). Questo metodo è utile se i dati devono essere archiviati per lunghi periodi. Se non si dispone di un requisito di conservazione dati esteso, usare il metodo di esportazione query di analisi. Esportazione continua e analisi di flusso comportano una configurazione più impegnativa e un overhead di archiviazione aggiuntivo.
* **Adattatore Power BI**. Il set di tabelle è predefinito, ma è possibile aggiungere query da qualsiasi altra origine.

> [!NOTE]
> L'adattatore Power BI è ora **deprecato**. I grafici predefiniti per questa soluzione vengono popolati da query statiche non modificabili. Non è possibile modificare queste query e, a seconda di alcune proprietà dei dati, è possibile che la connessione a Power BI abbia esito positivo, ma nessun dato viene popolato. Ciò è dovuto ai criteri di esclusione impostati all'interno della query hardcoded. Anche se questa soluzione può comunque funzionare per alcuni clienti, a causa della mancanza di flessibilità dell'adattatore la soluzione consigliata consiste nell'usare la funzionalità di [**esportazione query di Analisi**](#export-analytics-queries).

## <a name="export-analytics-queries"></a>Esportare query di Analisi
Questo metodo consente di scrivere tutte le query di Analisi desiderate, o di esportarle dalle visualizzazioni Imbuto, e quindi di inserirle in un dashboard di Power BI. È possibile aggiungerle al dashboard creato dall'adattatore.

### <a name="one-time-install-power-bi-desktop"></a>Operazione da eseguire una sola volta: installazione di Power BI Desktop
Per importare la query di Application Insights, usare la versione desktop di Power BI. Sarà quindi possibile pubblicarla sul Web o nell'area di lavoro cloud di Power BI. 

Installare [Power bi desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Esportare una query di Analisi
1. [Aprire Analisi e scrivere la query](../log-query/get-started-portal.md).
2. Testare e affinare la query fino a quando non si è soddisfatti dei risultati. Verificare che la query venga eseguita correttamente in Analisi prima di esportarla.
3. Nel menu **Esporta** scegliere **Power BI (M)**. Salvare il file di testo.
   
    ![Screenshot di Analisi con il menu Esporta evidenziato](./media/export-power-bi/analytics-export-power-bi.png)
4. In Power bi desktop selezionare **Ottieni dati**  >  **query vuota**. Nella scheda **Visualizza** dell'editor di query selezionare **Editor avanzato**.

    Nell'Editor avanzato incollare lo script in linguaggio M esportato.

    ![Screenshot di Power BI Desktop, con l'Editor avanzato evidenziato](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Per consentire a Power BI di accedere ad Azure può essere necessario specificare le credenziali. Usare l' **account aziendale** per accedere con l'account Microsoft.
   
    ![Screenshot della finestra di dialogo Impostazioni query di Power BI](./media/export-power-bi/power-bi-import-sign-in.png)

    Se è necessario verificare le credenziali, usare il comando di menu **Impostazioni origine dati** nell'editor di query di. Assicurarsi di specificare le credenziali usate per Azure, che potrebbero essere diverse da quelle di Power BI.
6. Scegliere una visualizzazione per la query e selezionare i campi per le assi X e Y e per le dimensioni di segmentazione.
   
    ![Screenshot delle opzioni di visualizzazione di Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Pubblicare il report nell'area di lavoro cloud di Power BI. Da qui è possibile incorporare una versione sincronizzata in altre pagine Web.
   
    ![Screenshot di Power BI Desktop, con il pulsante Pubblica evidenziato](./media/export-power-bi/publish-power-bi.png)
8. Aggiornare manualmente il report a intervalli oppure impostare un aggiornamento pianificato nella pagina Opzioni.

### <a name="export-a-funnel"></a>Esportare una visualizzazione Imbuto
1. [Creare un imbuto](./usage-funnels.md).
2. Selezionare **Power BI**.

   ![Screenshot del pulsante Power BI](./media/export-power-bi/button.png)

3. In Power bi desktop selezionare **Ottieni dati**  >  **query vuota**. Nella scheda **Visualizza** dell'editor di query selezionare **Editor avanzato**.

   ![Screenshot di Power BI Desktop, con il pulsante Query vuota evidenziato](./media/export-power-bi/blankquery.png)

   Nell'Editor avanzato incollare lo script in linguaggio M esportato. 

   ![Screenshot mostra la Power BI Desktop con Editor avanzato evidenziato](./media/export-power-bi/advancedquery.png)

4. Selezionare gli elementi dalla query e scegliere una visualizzazione Imbuto.

   ![Screenshot mostra le opzioni di visualizzazione dell'imbuto Power BI Desktop](./media/export-power-bi/selectsequence.png)

5. Modificare il titolo specificando un testo significativo e pubblicare il report nell'area di lavoro cloud di Power BI. 

   ![Screenshot di Power BI Desktop, con la modifica del titolo evidenziata](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

È possibile che vengano restituiti errori relativi alle credenziali o alla dimensione del set di dati. Ecco alcune informazioni su come gestirli.

### <a name="unauthorized-401-or-403"></a>Non autorizzato (401 o 403)
Questo errore può verificarsi se il token di aggiornamento non è stato aggiornato. Provare a eseguire questi passaggi per verificare di avere ancora i diritti di accesso:

1. Accedere al portale di Azure e verificare che sia possibile accedere alla risorsa.
2. Provare ad aggiornare le credenziali per il dashboard.
3. Provare a cancellare la cache dal Power BI Desktop.


   Se si ha accesso e non è possibile aggiornare le credenziali, aprire un ticket di supporto.

### <a name="bad-gateway-502"></a>Gateway non valido (502)
Questo errore è in genere causato da una query di Analisi che restituisce troppi dati. Provare a usare un intervallo di tempo più breve per la query. 

Se la riduzione del set di dati risultante dalla query di Analisi non è una soluzione adeguata alle proprie esigenze, valutare l'opportunità di usare l'[API](https://dev.applicationinsights.io/documentation/overview) per estrarre un set di dati di dimensioni maggiori. Per convertire l'esportazione della query M per l'uso dell'API, seguire questa procedura.

1. Creare una [chiave API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Aggiornare lo script M di Power BI esportato da Analisi sostituendo l'URL di Azure Resource Manager con l'API di Application Insights.
   * Sostituisci **https: \/ /Management.Azure.com/subscriptions/...**
   * con, **https: \/ /API.applicationinsights.io/beta/Apps/...**
3. Aggiornare infine le credenziali in credenziali di base e usare la chiave API.

**Script esistente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script aggiornato**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Informazioni sul campionamento
A seconda della quantità di dati inviati dall'applicazione, può essere opportuno usare la funzionalità di campionamento adattivo, che invia solo una percentuale dei dati di telemetria. La stessa considerazione vale se il campionamento è stato impostato manualmente nell'SDK o durante l'inserimento. [Altre informazioni sul campionamento](./sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Adattatore Power BI (deprecato)
Con questo metodo si crea un dashboard di dati di telemetria completo per l'utente. Il set di dati iniziale è predefinito, ma è possibile aggiungere altri dati.

### <a name="get-the-adapter"></a>Scaricare l'adattatore
1. Accedere a [Power BI](https://app.powerbi.com/).
2. Aprire **Recupera dati** ![Screenshot dell'icona Recupera dati nell'angolo inferiore sinistro](./media/export-power-bi/001.png), **Servizi**.

    ![Screenshot mostra il pulsante Get nella finestra servizi.](./media/export-power-bi/002.png)

3. Selezionare **Scarica adesso** in Application Insights.

   ![Screenshot per il recupero di dati dall'origine dati di Application Insights](./media/export-power-bi/003.png)
4. Specificare i dettagli della risorsa di Application Insights, quindi effettuare l'**Accesso**.

    ![Screenshot mostra Connetti a Application Insights finestra.](./media/export-power-bi/005.png)

     Queste informazioni sono disponibili nel riquadro di Panoramica di Application Insights:

     ![Screenshot per il recupero di dati dall'origine dati di Application Insights](./media/export-power-bi/004.png)

5. Aprire l'App Application Insights Power BI appena creata.

6. Attendere uno o due minuti per il completamento dell'importazione dei dati.

    ![Screenshot dell'adattatore Power BI](./media/export-power-bi/010.png)

È possibile modificare il dashboard unendo i grafici di Application Insights con i grafici di altre origini e con le query di Analisi. Nella raccolta di visualizzazioni sono disponibili più grafici, ciascuno dei quali include parametri che è possibile impostare.

Dopo l'importazione iniziale, il dashboard e i report continuano a essere aggiornati ogni giorno. È possibile controllare la pianificazione dell'aggiornamento nel set di dati.

## <a name="next-steps"></a>Passaggi successivi
* [Power BI - Informazioni](https://www.powerbi.com/learning/)
* [Esercitazione su Analisi](../log-query/get-started-portal.md)

