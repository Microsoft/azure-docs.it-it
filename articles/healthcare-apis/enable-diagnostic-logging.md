---
title: Abilitare la registrazione diagnostica nell'API di Azure per FHIR
description: Questo articolo illustra come abilitare la registrazione diagnostica nell'API di Azure per FHIR®
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: dseven
ms.author: cavoeg
author: zxue
ms.date: 03/03/2021
ms.openlocfilehash: 8dde5c6206087c8bb1c1f456a0c858abbccf0cc4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096310"
---
# <a name="enable-diagnostic-logging-in-azure-api-for-fhir"></a>Abilitare la registrazione diagnostica nell'API di Azure per FHIR

In questo articolo si apprenderà come abilitare la registrazione diagnostica nell'API di Azure per FHIR ed essere in grado di esaminare alcune query di esempio per questi log. L'accesso ai log di diagnostica è essenziale per qualsiasi servizio sanitario in cui la conformità ai requisiti normativi (ad esempio HIPAA) è una necessità. La funzionalità dell'API di Azure per FHIR che Abilita i log di diagnostica è rappresentata dalle [**impostazioni di diagnostica**](../azure-monitor/essentials/diagnostic-settings.md) del portale di Azure. 

## <a name="view-and-download-fhir-metrics-data"></a>Visualizza e Scarica i dati di metrica FHIR

È possibile visualizzare le metriche in monitoraggio | Metriche del portale. Le metriche includono il numero di richieste, la latenza media, il numero di errori, le dimensioni dei dati, le UR usate, il numero di richieste che hanno superato la capacità e la disponibilità (in%). La schermata seguente mostra le UR usate per un ambiente di esempio con pochissime attività negli ultimi 7 giorni. È possibile scaricare i dati in formato JSON.

   :::image type="content" source="media/diagnostic-logging/fhir-metrics-rus-screen.png" alt-text="Metriche dell'API di Azure per FHIR dal portale" lightbox="media/diagnostic-logging/fhir-metrics-rus-screen.png":::

## <a name="enable-audit-logs"></a>Abilitare i log di controllo
1. Per abilitare la registrazione diagnostica nell'API di Azure per FHIR, selezionare l'API di Azure per il servizio FHIR nel portale di Azure 
2. Passa a **impostazioni di diagnostica** 

   :::image type="content" source="media/diagnostic-logging/diagnostic-settings-screen.png" alt-text="Aggiungere le impostazioni di diagnostica di Azure FHIR." lightbox="media/diagnostic-logging/diagnostic-settings-screen.png":::

3. Selezionare **+ Aggiungi impostazione di diagnostica**

4. Immettere un nome per l'impostazione

5. Selezionare il metodo che si vuole usare per accedere ai log di diagnostica:

    1. **Archiviare in un account di archiviazione per il** controllo o l'ispezione manuale. È necessario che l'account di archiviazione che si vuole usare sia già stato creato.
    2. **Trasmettere a hub eventi** per l'inserimento da parte di un servizio di terze parti o di una soluzione analitica personalizzata. Prima di poter configurare questo passaggio, è necessario creare uno spazio dei nomi dell'hub eventi e un criterio dell'hub eventi.
    3. **Trasmettere all'area di lavoro log Analytics** in monitoraggio di Azure. Prima di poter selezionare questa opzione, è necessario creare l'area di lavoro log Analytics.

6. Selezionare **AuditLogs** e/o **AllMetrics**. Le metriche includono il nome del servizio, la disponibilità, le dimensioni dei dati, la latenza totale, le richieste totali, gli errori totali e il timestamp. È possibile trovare altre informazioni sulle [metriche supportate](https://docs.microsoft.com/azure/azure-monitor/essentials/metrics-supported#microsofthealthcareapisservices). 

   :::image type="content" source="media/diagnostic-logging/fhir-diagnostic-setting.png" alt-text="Impostazioni di diagnostica di Azure FHIR. Selezionare AuditLogs e/o AllMetrics." lightbox="media/diagnostic-logging/fhir-diagnostic-setting.png":::

7. Selezionare **Salva**


> [!Note] 
> Potrebbero essere necessari fino a 15 minuti prima che il primo log venga visualizzato in Log Analytics. Se, inoltre, l'API di Azure per FHIR viene spostata da un gruppo di risorse o una sottoscrizione a un'altra, aggiornare l'impostazione al termine dello spostamento. 
 
Per altre informazioni su come usare i log di diagnostica, vedere la [documentazione relativa al log delle risorse di Azure](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="audit-log-details"></a>Dettagli log di controllo
Al momento, il servizio API di Azure per FHIR restituisce i campi seguenti nel log di controllo: 

|Nome campo  |Type  |Note  |
|---------|---------|---------|
|CallerIdentity|Dynamic|Contenitore di proprietà generico che contiene informazioni sull'identità
|CallerIdentityIssuer|string|Issuer 
|CallerIdentityObjectId|string|Object_Id 
|CallerIPAddress|string|Indirizzo IP del chiamante 
|CorrelationId|string| ID correlazione
|FhirResourceType|string|Il tipo di risorsa per cui è stata eseguita l'operazione
|LogCategory|string|Categoria log (attualmente in fase di restituzione di ' AuditLogs ' LogCategory)
|Location|string|Il percorso del server che ha elaborato la richiesta (ad esempio, Stati Uniti centro-meridionali)
|OperationDuration|Int|Tempo richiesto per completare la richiesta in pochi secondi
|OperationName|string| Descrive il tipo di operazione (ad esempio, Update, Search-Type)
|RequestUri|string|L'URI della richiesta 
|ResultType|string|I valori disponibili attualmente sono **Started**, **succeeded** o **failed**
|StatusCode|Int|Codice di stato HTTP. (ad esempio, 200) 
|TimeGenerated|Datetime|Data e ora dell'evento|
|Proprietà|string| Descrive le proprietà di fhirResourceType
|SourceSystem|string| Sistema di origine (sempre Azure in questo caso)
|TenantId|string|ID tenant
|Type|string|Tipo di log (sempre MicrosoftHealthcareApisAuditLog in questo caso)
|_ResourceId|string|Dettagli sulla risorsa

## <a name="sample-queries"></a>Query di esempio

Di seguito sono riportate alcune query Application Insights di base che è possibile usare per esplorare i dati di log.

Eseguire la query per visualizzare i **100 log più recenti** :

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

Eseguire questa query per raggruppare le operazioni per **tipo di risorsa FHIR**:

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by FhirResourceType
```

Eseguire questa query per ottenere tutti i **risultati non riusciti**

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>Conclusione 
L'accesso ai log di diagnostica è essenziale per il monitoraggio di un servizio e per la fornitura di report di conformità. API di Azure per FHIR consente di eseguire queste azioni tramite i log di diagnostica. 
 
FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come abilitare i log di controllo per l'API di Azure per FHIR. Successivamente, informazioni sulle altre impostazioni che è possibile configurare nell'API di Azure per FHIR
 
>[!div class="nextstepaction"]
>[Impostazioni aggiuntive](azure-api-for-fhir-additional-settings.md)
