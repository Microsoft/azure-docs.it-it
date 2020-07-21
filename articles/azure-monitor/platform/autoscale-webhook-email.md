---
title: Usare la scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights
description: Informazioni su come usare le azioni di scalabilità automatica per chiamare URL Web o inviare notifiche tramite posta elettronica in monitoraggio di Azure.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505520"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Monitoraggio di Azure
Questo articolo illustra come configurare i trigger per poter chiamare URL Web specifici o inviare messaggi di posta elettronica in base alle azioni di scalabilità automatica in Azure.  

## <a name="webhooks"></a>Webhook
I webhook consentono di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le notifiche personalizzate. Ad esempio, il routing dell'avviso ai servizi in grado di gestire un SMS in ingresso Request to Send SMS, registrare bug, inviare notifiche a un team tramite servizi di messaggistica o chat e così via. L'URI del webhook deve essere un endpoint HTTP o HTTPS valido.

## <a name="email"></a>Posta elettronica
È possibile inviare un messaggio di posta elettronica a qualsiasi indirizzo di posta elettronica valido. Verrà inviata una notifica anche agli amministratori e ai coamministratori della sottoscrizione in cui viene eseguita la regola.

## <a name="cloud-services-and-app-services"></a>Servizi cloud e servizi app
È possibile acconsentire esplicitamente dal portale di Azure per i servizi cloud e le server farm (servizi app).

* Scegliere la metrica **Ridimensiona di** .

![Ridimensiona di](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Set di scalabilità di macchine virtuali
Per le macchine virtuali più recenti create con Resource Manager (set di scalabilità di macchine virtuali), è possibile configurare questa opzione tramite l'API REST, i modelli di Resource Manager, PowerShell e l'interfaccia della riga di comando. Un'interfaccia del portale non è ancora disponibile.
Quando si usa l'API REST o il modello di Gestione risorse, includere l'elemento Notifications in [autoscalesettings](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) con le opzioni seguenti.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Campo | Obbligatorio? | Descrizione |
| --- | --- | --- |
| operazione |sì |Il valore deve essere "Scale" |
| sendToSubscriptionAdministrator |sì |Il valore deve essere "true" o "false" |
| sendToSubscriptionCoAdministrators |sì |Il valore deve essere "true" o "false" |
| customEmails |sì |Il valore può essere null [] o la matrice di stringhe di messaggi di posta elettronica |
| Webhook |sì |Il valore può essere null o un URI valido |
| serviceUri |sì |Un URI HTTPS valido |
| properties |sì |Il valore deve essere vuoto {} o può contenere coppie chiave-valore |

## <a name="authentication-in-webhooks"></a>Autenticazione nei webhook
È possibile autenticare il webhook usando l'autenticazione basata su token, che prevede il salvataggio dell'URI del webhook con un ID token come parametro di query. Ad esempio, https: \/ /mysamplealert/webcallback? tokenId = sometokenid&someparameter = SomeValue

## <a name="autoscale-notification-webhook-payload-schema"></a>Schema di payload del webhook di notifica di scalabilità automatica
Quando viene generata la notifica di scalabilità automatica, nel payload del webhook vengono inclusi i metadati seguenti:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Campo | Obbligatorio? | Descrizione |
| --- | --- | --- |
| status |sì |Stato che indica che è stata generata un'azione di scalabilità automatica |
| operazione |sì |Per un aumento delle istanze, sarà "Scale Out", mentre per una riduzione delle istanze, sarà "Scale In" |
| contesto |sì |Contesto dell'azione di scalabilità automatica |
| timestamp |sì |Timestamp in cui è stata attivata l'azione di scalabilità automatica |
| id |Sì |ID di Resource Manager dell'impostazione di scalabilità automatica |
| name |Sì |Nome dell'impostazione di scalabilità automatica |
| dettagli |Sì |Spiegazione dell'azione eseguita dal servizio di scalabilità automatica e della modifica al conteggio delle istanze |
| subscriptionId |Sì |ID sottoscrizione della risorsa di destinazione da ridimensionare |
| resourceGroupName |Sì |Nome del gruppo di risorse della risorsa di destinazione da ridimensionare |
| resourceName |Sì |Nome della risorsa di destinazione da ridimensionare |
| resourceType |Sì |I tre valori supportati: "microsoft.classiccompute/domainnames/slots/roles" (ruoli dei servizi cloud), "microsoft.compute/virtualmachinescalesets" (set di scalabilità di macchine virtuali) e "Microsoft.Web/serverfarms" (app Web) |
| resourceId |Sì |ID di Resource Manager della risorsa di destinazione da ridimensionare |
| portalLink |Sì |Collegamento del portale di Azure alla pagina di riepilogo della risorsa di destinazione |
| oldCapacity |Sì |Conteggio delle istanze corrente (precedente) quando la scalabilità automatica ha eseguito un'azione di scalabilità |
| newCapacity |Sì |Nuovo conteggio delle istanze in base al quale la scalabilità automatica ha ridimensionato la risorsa |
| properties |No |Facoltativa. Set di coppie <chiave, valore> (ad esempio Dizionario <Stringa, Stringa>). Il campo properties è facoltativo. In un flusso di lavoro basato su un'interfaccia utente personalizzata o un'app per la logica, è possibile immettere chiavi e valori che possono essere passati usando il payload. Un metodo alternativo per passare le proprietà personalizzate alla chiamata al webhook in uscita è di usare l'URI del webhook stesso (sotto forma di parametri di query) |
