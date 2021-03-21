---
title: Impostazioni delle regole di rilevamento intelligente-applicazione Azure Insights
description: Automatizzare la gestione e configurazione delle regole di rilevamento intelligente di Azure Application Insights con modelli di Azure Resource Manager
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726122"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gestire le regole di rilevamento intelligente di Application Insights usando modelli di Azure Resource Manager

Le regole di rilevamento intelligente in Application Insights possono essere gestite e configurate mediante [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
Questo metodo può essere usato durante la distribuzione di nuove risorse di Application Insights con automazione di Azure Resource Manager o per modificare le impostazioni delle risorse esistenti.

## <a name="smart-detection-rule-configuration"></a>Configurazione delle regole di rilevamento intelligente

Per una regola di rilevamento intelligente è possibile configurare le impostazioni seguenti:
- Se la regola è abilitata (il valore predefinito è **true**).
- Se i messaggi di posta elettronica devono essere inviati agli utenti associati ai ruoli di [lettore monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) e [collaboratore monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor) della sottoscrizione quando viene individuato un rilevamento (il valore predefinito è **true**).
- Eventuali destinatari di posta elettronica aggiuntivi che devono ricevere una notifica quando si ha un rilevamento.
    -  La configurazione della posta elettronica non è disponibile per le regole di rilevamento intelligente contrassegnate come _Anteprima_.

Per consentire la configurazione delle impostazioni delle regole tramite Azure Resource Manager, la configurazione delle regole di rilevamento intelligente è ora disponibile come risorsa interna alla risorsa di Application Insights denominata **ProactiveDetectionConfigs**.
Per la massima flessibilità, ogni regola di rilevamento intelligente può essere configurata con impostazioni di notifica univoche.

## <a name="examples"></a>Esempio

Di seguito sono indicati alcuni esempi che illustrano come configurare le impostazioni delle regole di rilevamento intelligente tramite modelli di Azure Resource Manager.
Tutti gli esempi fanno riferimento a una risorsa di Application Insights denominata _"myApplication"_ e alla "regola di rilevamento intelligente dipendenze con durata lunga", che è denominata internamente _"longdependencyduration"_.
Assicurarsi di sostituire il nome della risorsa di Application Insights e di specificare il nome interno della regola di rilevamento intelligente interessata. Consultare la tabella seguente per l'elenco dei nomi interni di Azure Resource Manager corrispondenti per ogni regola di rilevamento intelligente.

### <a name="disable-a-smart-detection-rule"></a>Disabilitare una regola di rilevamento intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Disabilitare l'invio di notifiche tramite posta elettronica per una regola di rilevamento intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Aggiungere altri destinatari di posta elettronica per una regola di rilevamento intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Nomi delle regole di rilevamento intelligente

Di seguito è riportata una tabella dei nomi delle regole di rilevamento intelligente come appaiono nel portale, insieme ai rispettivi nomi interni che devono essere usati nel modello di Azure Resource Manager.

> [!NOTE]
> Le regole di rilevamento intelligente contrassegnate come _Anteprima_ non supportano le notifiche tramite posta elettronica. Pertanto, è possibile impostare solo la proprietà _Enabled_ per queste regole. 

| Nome della regola nel portale di Azure | Nome interno
|:---|:---|
| Rallentamento del tempo di caricamento delle pagine | slowpageloadtime |
| Rallentamento del tempo di risposta del server | slowserverresponsetime |
| Dipendenze con durata lunga | longdependencyduration |
| Riduzione delle prestazioni nel tempo di risposta del server | degradationinserverresponsetime |
| Riduzione delle prestazioni nella durata delle dipendenze | degradationindependencyduration |
| Degradazione del rapporto tra i livelli di gravità delle tracce (anteprima) | extension_traceseveritydetector |
| Aumento anomalo nel volume delle eccezioni (anteprima) | extension_exceptionchangeextension |
| Potenziale perdita di memoria rilevata (anteprima) | extension_memoryleakextension |
| Potenziale problema di sicurezza rilevato (anteprima) | extension_securityextensionspackage |
| Incremento anomalo nel volume giornaliero di dati (anteprima) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Regola di avviso anomalie errori

Questo modello di Azure Resource Manager illustra la configurazione di una regola di avviso di anomalie di errore con gravità 2.

> [!NOTE]
> Le anomalie degli errori sono un servizio globale, quindi la posizione delle regole viene creata nel percorso globale.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Questo modello di Azure Resource Manager è univoco per la regola di avviso di anomalie degli errori ed è diverso dalle altre regole di rilevamento intelligente classiche descritte in questo articolo. Se si vuole gestire manualmente le anomalie degli errori, questa operazione viene eseguita negli avvisi di monitoraggio di Azure, mentre tutte le altre regole di rilevamento intelligente sono gestite nel riquadro rilevamento intelligente dell'interfaccia utente.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul rilevamento automatico:

- [Anomalie degli errori](./proactive-failure-diagnostics.md)
- [Perdite di memoria](./proactive-potential-memory-leak.md)
- [Anomalie delle prestazioni](./proactive-performance-diagnostics.md)

