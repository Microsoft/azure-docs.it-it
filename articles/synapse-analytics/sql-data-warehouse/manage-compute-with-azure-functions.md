---
title: 'Esercitazione: gestire le risorse di calcolo con funzioni di Azure'
description: Come usare funzioni di Azure per gestire il calcolo del pool SQL in Azure sinapsi Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: aa2cff552b49bceeaf6fd46510bf78384f0e7bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631955"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Usare funzioni di Azure per gestire le risorse di calcolo nel pool SQL di Azure sinapsi Analytics

Questa esercitazione usa funzioni di Azure per gestire le risorse di calcolo per un pool SQL in Azure sinapsi Analytics.

Per usare app per le funzioni di Azure con il pool SQL, è necessario creare un [account dell'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) con accesso come collaboratore nella stessa sottoscrizione dell'istanza del pool SQL.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Distribuire il ridimensionamento basato su timer con un modello di Azure Resource Manager

Per distribuire il modello sono necessarie le informazioni seguenti:

- Nome del gruppo di risorse in cui si trova l'istanza del pool SQL
- Nome del server logico in cui si trova l'istanza del pool SQL
- Nome dell'istanza del pool SQL
- ID tenant (ID directory) di Azure Active Directory
- ID sottoscrizione
- ID applicazione dell'entità servizio
- Chiave privata dell'entità servizio

Dopo aver recuperato le informazioni riportate sopra, distribuire questo modello:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Al termine della distribuzione del modello, dovrebbero essere presenti tre nuove risorse: un piano di servizio app di Azure gratuito, un piano di app per le funzioni a consumo e un account di archiviazione che gestisce la registrazione e la coda delle operazioni. Per informazioni su come modificare le funzioni distribuite in base alle esigenze, vedere le altre sezioni.

## <a name="change-the-compute-level"></a>Modificare il livello di calcolo

1. Passare al servizio app per le funzioni. Se il modello è stato distribuito con i valori predefiniti, il servizio sarà denominato *DWOperations*. Dopo aver aperto l'app per le funzioni, si noterà che nel servizio app per le funzioni sono distribuite cinque funzioni.

   ![Funzioni distribuite con il modello](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selezionare *DWScaleDownTrigger* o *DWScaleUpTrigger*, a seconda che si vogliano modificare la data e l'ora dell'aumento o della riduzione delle prestazioni. Nel menu a discesa selezionare Integrazione.

   ![Selezionare Integrazione per la funzione](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Il valore attualmente visualizzato dovrebbe essere *%ScaleDownTime%* o *%ScaleUpTime%*. Questi valori indicano che la pianificazione è basata sui valori definiti nelle [impostazioni dell'applicazione](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Per il momento è possibile ignorare questo valore e modificare la pianificazione impostando la data e l'ora preferite in base ai passaggi successivi.

4. Nell'area relativa alla pianificazione aggiungere l'espressione CRON di data e ora desiderata per riflettere la frequenza con cui si vogliono aumentare le prestazioni di SQL Data Warehouse.

   ![Modificare la pianificazione della funzione](./media/manage-compute-with-azure-functions/change-schedule.png)

   Il valore di `schedule` è un'[espressione CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) che include i 6 campi seguenti:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Ad esempio, *"0 30 9 * * 1-5"* corrisponde a un trigger alle 9.30 di ogni giorno feriale. Per altre informazioni, vedere gli [esempi di pianificazione](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) di Funzioni di Azure.

## <a name="change-the-time-of-the-scale-operation"></a>Modificare la data e l'ora dell'operazione di ridimensionamento

1. Passare al servizio app per le funzioni. Se il modello è stato distribuito con i valori predefiniti, il servizio sarà denominato *DWOperations*. Dopo aver aperto l'app per le funzioni, si noterà che nel servizio app per le funzioni sono distribuite cinque funzioni.

2. Selezionare *DWScaleDownTrigger* o *DWScaleUpTrigger*, a seconda che si voglia modificare il valore di calcolo per l'aumento o per la riduzione delle prestazioni. Quando si selezionano le funzioni, nel riquadro dovrebbe essere visualizzato il file *index.js*.

   ![Modificare il livello di calcolo del trigger della funzione](././media/manage-compute-with-azure-functions/index-js.png)

3. Modificare il valore di *ServiceLevelObjective* impostando il livello desiderato e scegliere Salva. Questo valore corrisponde al livello di calcolo a cui passerà l'istanza di Data Warehouse in base alla pianificazione definita nella sezione Integrazione.

## <a name="use-pause-or-resume-instead-of-scale"></a>Usare la sospensione o la ripresa invece del ridimensionamento

Attualmente, le funzioni attivate per impostazione predefinita sono *DWScaleDownTrigger* e *DWScaleUpTrigger*. Se si preferisce invece sospendere e riprendere le funzionalità, è possibile abilitare *DWPauseTrigger* o *DWResumeTrigger*.

1. Passare al riquadro Funzioni.

   ![Riquadro Funzioni](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Fare clic sull'interruttore scorrevole corrispondente ai trigger da abilitare.

3. Passare alle schede *Integrazione* per i rispettivi trigger per modificarne la pianificazione.

   > [!NOTE]
   > La differenza funzionale fra i trigger di ridimensionamento e i trigger di sospensione o ripresa è data dal messaggio inviato alla coda. Per altre informazioni, vedere [Aggiungere una nuova funzione trigger](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Aggiungere una nuova funzione trigger

Attualmente, nel modello sono incluse solo due funzioni di ridimensionamento. Con queste funzioni, nel corso di un giorno è possibile eseguire solo una riduzione e un aumento delle prestazioni. Per un controllo più granulare, ad esempio per ridurre le prestazioni più volte al giorno o impostare un comportamento di ridimensionamento diverso nei fine settimana, è necessario aggiungere un altro trigger.

1. Creare una nuova funzione vuota. Selezionare il *+* pulsante accanto al percorso funzioni per visualizzare il riquadro modello di funzione.

   ![Creare una nuova funzione](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Selezionare *JavaScript* in Linguaggio e quindi *TimerTrigger*.

   ![Creare una nuova funzione](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Assegnare un nome alla funzione e impostare la pianificazione. L'immagine mostra come è possibile attivare la funzione ogni sabato a mezzanotte (ossia nella tarda serata di venerdì).

   ![Ridurre le prestazioni di sabato](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copiare il contenuto di *index.js* da una delle altre funzioni trigger.

   ![Copiare index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Impostare la variabile operation sul comportamento desiderato come illustrato di seguito:

   ```javascript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```

## <a name="complex-scheduling"></a>Pianificazione complessa

Questa sezione illustra brevemente gli elementi necessari per ottenere una pianificazione più complessa delle funzionalità di sospensione, ripresa e ridimensionamento.

### <a name="example-1"></a>Esempio 1

Eseguire ogni giorno l'aumento delle prestazioni a DW600 alle ore 8 e la riduzione delle prestazioni a DW200 alle ore 20.

| Funzione  | Pianificazione     | Operazione                                |
| :-------- | :----------- | :--------------------------------------- |
| Funzione 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Funzione 2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Esempio 2

Eseguire ogni giorno l'aumento delle prestazioni a DW1000 alle ore 8 e la riduzione delle prestazioni a DW600 alle ore 16 e a DW200 alle ore 22.

| Funzione  | Pianificazione     | Operazione                                |
| :-------- | :----------- | :--------------------------------------- |
| Funzione 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Funzione 2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Funzione 3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Esempio 3

Eseguire nei giorni feriali l'aumento delle prestazioni a DW1000 alle ore 8 e la riduzione delle prestazioni a DW600 alle ore 16, nonché sospendere alle ore 23 di venerdì e riprendere alle ore 7 di lunedì.

| Funzione  | Pianificazione       | Operazione                                |
| :-------- | :------------- | :--------------------------------------- |
| Funzione 1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Funzione 2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Funzione 3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Funzione 4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzioni di Azure [attivate da un timer](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Checkout the SQL pool [Samples repository](https://github.com/Microsoft/sql-data-warehouse-samples).
