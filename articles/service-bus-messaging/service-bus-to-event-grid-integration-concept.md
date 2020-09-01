---
title: Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi | Microsoft Docs
description: Questo articolo fornisce una descrizione del modo in cui la messaggistica del bus di servizio di Azure si integra con griglia di eventi di Azure.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f0aaa82db61b5f40e42d6dad641bc09d5add9d0f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078334"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi

Il bus di servizio Azure offre ora una nuova integrazione in Griglia di eventi di Azure. Il principale scenario di questa funzionalità è la possibilità, in caso di code e sottoscrizioni del bus di servizio con un volume ridotto di messaggi, di non avere un ricevitore che esegue costantemente il polling dei messaggi. 

Il bus di servizio può ora inviare eventi a Griglia di eventi quando una coda o una sottoscrizione contiene messaggi e non sono presenti ricevitori. È possibile creare sottoscrizioni di Griglia di eventi per gli spazi dei nomi del bus di servizio, restare in ascolto di tali eventi e quindi reagire a essi avviando un ricevitore. Questa funzionalità consente di usare il bus di servizio in modelli di programmazione reattivi.

Per abilitare questa funzionalità, sono necessari gli elementi seguenti:

* Spazio dei nomi Premium del bus di servizio con almeno una coda o un argomento del bus di servizio con almeno una sottoscrizione.
* Accesso di tipo Collaboratore allo spazio dei nomi del bus di servizio.
* Sottoscrizione di Griglia di eventi per lo spazio dei nomi del bus di servizio. Questa sottoscrizione riceve una notifica da Griglia di eventi che segnala la presenza di messaggi da prelevare. I sottoscrittori tipici potrebbero essere la funzionalità App per la logica di Servizio app di Azure, Funzioni di Azure o un webhook che contatta un'app Web. Il sottoscrittore elabora quindi i messaggi. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>Verificare di avere l'accesso di tipo Collaboratore
Passare allo spazio dei nomi del bus di servizio e quindi selezionare **controllo di accesso (IAM)** e selezionare scheda **assegnazioni di ruolo** . Verificare di disporre dell'accesso collaboratore allo spazio dei nomi. 

### <a name="events-and-event-schemas"></a>Eventi e schemi di eventi

Il bus di servizio attualmente invia eventi per due scenari:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* DeadletterMessagesAvailable

Il bus di servizio usa inoltre i [meccanismi di autenticazione](../event-grid/security-authentication.md) e la sicurezza standard di Griglia di eventi.

Per altre informazioni, vedere [Schemi di eventi di Griglia di eventi di Azure](../event-grid/event-schema.md).

#### <a name="active-messages-available-event"></a>Evento per disponibilità di messaggi attivi

Questo evento viene generato se in una coda o una sottoscrizione sono presenti messaggi attivi e nessun ricevitore è in ascolto.

Di seguito è riportato lo schema per questo evento:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-letter-messages-available-event"></a>Evento per disponibilità di messaggi inseriti nella coda di messaggi non recapitabili

Viene generato almeno un evento per ogni coda di messaggi non recapitabili che contiene messaggi e non ha alcun ricevitore attivo.

Di seguito è riportato lo schema per questo evento:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Numero di eventi emessi e frequenza di emissione

Se si hanno più code e argomenti o sottoscrizioni nello spazio dei nomi, vengono generati almeno un evento per coda e un evento per sottoscrizione. Gli eventi vengono emessi immediatamente se non sono presenti messaggi nell'entità del bus di servizio e arriva un nuovo messaggio oppure vengono emessi ogni due minuti a meno che il bus di servizio non rilevi un ricevitore attivo. L'esplorazione di messaggi non interrompe gli eventi.

Per impostazione predefinita, il bus di servizio emette eventi per tutte le entità nello spazio dei nomi. Se si vogliono ottenere eventi solo per entità specifiche, vedere la sezione successiva.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Usare i filtri per limitare le origini da cui è possibile ottenere gli eventi

Se si vogliono ottenere eventi solo da una coda o una sottoscrizione nello spazio dei nomi, ad esempio, è possibile usare i filtri *Inizia con* o *Termina con* offerti da Griglia di eventi, che in alcune interfacce sono denominati filtri per *prefisso* e *suffisso*. Se si vogliono ottenere eventi per più code e sottoscrizioni ma non per tutte, è possibile creare più sottoscrizioni di Griglia di eventi e specificare un filtro per ognuna.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Creare sottoscrizioni di Griglia di eventi per gli spazi dei nomi del bus di servizio

È possibile creare sottoscrizioni di Griglia di eventi per gli spazi dei nomi del bus di servizio in tre diversi modi:

* Nel portale di Azure
* Nell' [interfaccia](#azure-cli-instructions) della riga di comando di Azure
* In [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Istruzioni del portale di Azure

Per creare una nuova sottoscrizione di Griglia di eventi, seguire questa procedura:
1. Nel portale di Azure passare allo spazio dei nomi.
2. Nel riquadro a sinistra selezionare **Griglia eventi**. 
3. Selezionare **sottoscrizione di eventi**.  

   L'immagine seguente mostra uno spazio dei nomi con una sottoscrizione di Griglia di eventi:

   ![Sottoscrizioni di Griglia di eventi](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   L'immagine seguente mostra come sottoscrivere una funzione o un webhook senza filtri specifici:

   ![21][]

## <a name="azure-cli-instructions"></a>Istruzioni per l'interfaccia della riga di comando di Azure

Verificare prima di tutto che sia installata l'interfaccia della riga di comando di Azure versione 2.0 o successiva. [Scaricare il programma di installazione](/cli/azure/install-azure-cli?view=azure-cli-latest). Selezionare **Windows + X**e quindi aprire una nuova console di PowerShell con autorizzazioni di amministratore. In alternativa è possibile usare una shell dei comandi nel portale di Azure.

Eseguire questo codice:

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Se si usa BASH 

## <a name="powershell-instructions"></a>Istruzioni per PowerShell

Verificare che sia installato Azure PowerShell, [Scaricare il programma di installazione](/powershell/azure/install-Az-ps). Premere **Windows + X** e quindi aprire una nuova console di PowerShell con autorizzazioni di amministratore. In alternativa è possibile usare una shell dei comandi nel portale di Azure.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

A questo punto è possibile esplorare le altre opzioni di configurazione oppure testare che gli eventi vengano trasmessi.

## <a name="next-steps"></a>Passaggi successivi

* Ottenere [esempi](service-bus-to-event-grid-integration-example.md) relativi al bus di servizio e Griglia di eventi.
* Altre informazioni su [Griglia di eventi](../event-grid/index.yml).
* Scopri di più sulle [funzioni di Azure](../azure-functions/index.yml).
* Altre informazioni su [App per la logica](../logic-apps/index.yml).
* Altre informazioni sul [bus di servizio](/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
