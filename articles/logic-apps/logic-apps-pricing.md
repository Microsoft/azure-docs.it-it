---
title: Prezzi & modello di fatturazione
description: Panoramica sul funzionamento dei prezzi e della fatturazione per le app per la logica di Azure
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 9243d089b4a000066ec03dbeeccd046db374f558
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673111"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modello di determinazione dei prezzi per le App per la logica di Azure

[App](../logic-apps/logic-apps-overview.md) per la logica di Azure consente di creare ed eseguire flussi di lavoro di integrazione automatizzati che possono essere ridimensionati nel cloud. Questo articolo descrive il funzionamento della fatturazione e dei prezzi per le app per la logica di Azure. Per informazioni sui prezzi, vedere [prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps)per la logica.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modello di prezzi a consumo

Per le nuove app per la logica eseguite nel servizio pubblico "globale" di app per la logica di Azure multi-tenant, si paga solo per le risorse usate. Queste app per la logica usano un piano e un modello di determinazione prezzi in base al consumo. Nell'app per la logica ogni passaggio è un'azione e le app per la logica di Azure misurano tutte le azioni eseguite nell'app per la logica.

Ad esempio, le azioni includono:

* [Trigger](#triggers), che sono azioni speciali. Tutte le app per la logica richiedono un trigger come primo passaggio.

* [Azioni predefinite o native](../connectors/apis-list.md#built-in) come http, chiamate a funzioni di Azure e gestione API e così via

* Chiamate a [connettori gestiti](../connectors/apis-list.md#managed-connectors) come Outlook 365, Dropbox e così via

* [Controllare le azioni del flusso di lavoro](../connectors/apis-list.md#control-workflow) , ad esempio cicli, istruzioni condizionali e così via

Ai [connettori standard](../connectors/apis-list.md#managed-connectors) viene addebitato il [prezzo del connettore standard](https://azure.microsoft.com/pricing/details/logic-apps). I [connettori aziendali](../connectors/apis-list.md#managed-connectors) disponibili a livello generale vengono addebitati al [prezzo di Enterprise Connector](https://azure.microsoft.com/pricing/details/logic-apps), mentre i connettori Enterprise di anteprima pubblica vengono addebitati al [prezzo del connettore standard](https://azure.microsoft.com/pricing/details/logic-apps).

Altre informazioni sul funzionamento della fatturazione a livello di [trigger](#triggers) e [azioni](#actions) . In alternativa, per informazioni sui limiti, vedere [limiti e configurazione per app per la logica di Azure](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modello di determinazione dei prezzi fissi

Un [ *ambiente Integration Services* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) offre una modalità isolata per creare ed eseguire app per la logica che possono accedere alle risorse in una rete virtuale di Azure. Le app per la logica eseguite in ISE non incorrono sui costi di conservazione dei dati. Quando si crea un ISE e solo durante la creazione, è possibile scegliere un [livello ISE o "SKU"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)con [tariffe](https://azure.microsoft.com/pricing/details/logic-apps)di prezzo diverse:

* **Premium** ISE: le unità di base di questo SKU hanno una capacità fissa, ma se è necessaria una velocità effettiva maggiore, è possibile [aggiungere altre unità di scala](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) durante la creazione di ISE o in seguito. Per i limiti di ISE, vedere [limiti e configurazione per app per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

* Per **sviluppatori** ISE: questo SKU non ha funzionalità per la scalabilità verticale, nessun contratto di servizio (SLA) e nessun limite pubblicato. Usare questo SKU solo per la sperimentazione, lo sviluppo e il test, non per la produzione o il test delle prestazioni.

Per le app per la logica create ed eseguite in un ISE, si paga un [prezzo fisso](https://azure.microsoft.com/pricing/details/logic-apps) (rispetto al pagamento in base all'uso) per queste funzionalità:

* Trigger e azioni [predefiniti](../connectors/apis-list.md#built-in)

  All'interno di ISE, i trigger e le azioni predefiniti visualizzano l'etichetta **principale** ed eseguono nello stesso ISE delle app per la logica.

* Connettori [standard](../connectors/apis-list.md#managed-connectors) e connettori [aziendali](../connectors/apis-list.md#enterprise-connectors) , che consentono di avere tutte le connessioni aziendali desiderate

   I connettori standard ed Enterprise che visualizzano l'etichetta **ISE** vengono eseguiti nello stesso ISE delle app per la logica. I connettori che non visualizzano l'etichetta ISE vengono eseguiti nel servizio app per la logica pubblico "globale" e multi-tenant. I prezzi fissi si applicano anche ai connettori eseguiti nel servizio multi-tenant quando vengono usati con app per la logica eseguite in un ISE.

* Utilizzo dell' [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) senza costi aggiuntivi, in base allo [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Premium** SKU ISE: un unico account di integrazione del [livello standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * Per **sviluppatori** SKU ISE: un unico account di integrazione del [livello gratuito](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Per un costo aggiuntivo, è possibile creare altri account di integrazione per ISE [fino al limite totale](logic-apps-limits-and-config.md#integration-account-limits). 

  * **Premium** SKU ISE: fino a un massimo di 19 account standard. Gli account gratuiti o Basic non sono consentiti.

  * Per **sviluppatori** SKU ISE: fino a 19 account standard se si ha già un account gratuito o 20 account standard totali se non si ha un account gratuito. Gli account Basic non sono consentiti.

  Per altre informazioni sui limiti dell'account di integrazione, vedere [limiti e configurazione per app per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Più avanti in questo argomento sono disponibili ulteriori informazioni sui [livelli di account di integrazione e sul relativo modello di determinazione dei prezzi](#integration-accounts) .

<a name="connectors"></a>

## <a name="connectors"></a>Connettori

I connettori delle app per la logica di Azure consentono all'app per la logica di accedere a app, servizi e sistemi nel cloud o in locale fornendo [trigger](#triggers), [azioni](#actions)o entrambi. I connettori sono classificati come standard o Enterprise. Per una panoramica su questi connettori, vedere [connettori per app per la logica di Azure](../connectors/apis-list.md). Se non sono disponibili connettori predefiniti per le API REST che si vuole usare nelle app per la logica, è possibile creare [connettori personalizzati](/connectors/custom-connectors), che sono solo wrapper intorno a tali API REST. I connettori personalizzati vengono fatturati come connettori standard. Nelle sezioni seguenti vengono fornite ulteriori informazioni sul funzionamento della fatturazione per i trigger e le azioni.

<a name="triggers"></a>

## <a name="triggers"></a>Trigger

Un trigger è sempre il primo passaggio di un flusso di lavoro dell'app per la logica ed è un'azione speciale che crea ed esegue un'istanza di app per la logica quando vengono soddisfatti criteri specifici o si verifica un evento specifico. I trigger agiscono in modi diversi, che influiscono sulla misurazione dell'app per la logica. Ecco i diversi tipi di trigger presenti nelle app per la logica di Azure:

* **Trigger di ricorrenza**: è possibile usare questo trigger generico, che non è specifico per alcun servizio o sistema, per avviare qualsiasi flusso di lavoro dell'app per la logica e creare un'istanza dell'app per la logica che viene eseguita in base all'intervallo di ricorrenza configurato nel trigger. Ad esempio, è possibile impostare un trigger di ricorrenza che viene eseguito ogni tre giorni o in base a una pianificazione più complessa.

* **Trigger di polling**: è possibile usare questo trigger di ricorrenza più specializzato, che in genere è associato al connettore gestito per un servizio o un sistema specifico, per verificare la presenza di eventi o messaggi che soddisfino i criteri per la creazione e l'esecuzione di un'istanza dell'app per la logica in base all'intervallo di ricorrenza configurato nel trigger. Anche quando non viene creata alcuna istanza di app per la logica, ad esempio quando i trigger vengono ignorati, il servizio app per la logica misura ogni richiesta di polling come esecuzione. Per specificare l'intervallo di polling, configurare il trigger tramite la finestra Progettazione app per la logica.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger di Webhook**: invece di usare un trigger di polling, è possibile usare un trigger webhook per attendere che il client invii una richiesta all'app per la logica in un URL di endpoint specifico. Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di un'azione. Ad esempio, il trigger di richiesta e webhook HTTP sono entrambi trigger di Webhook generici. Alcuni connettori per i servizi o i sistemi hanno anche trigger di webhook.

<a name="actions"></a>

## <a name="actions"></a>Actions

App per la logica di Azure "azioni predefinite", ad esempio HTTP, come azioni native. Ad esempio, le azioni predefinite includono le chiamate HTTP, le chiamate da funzioni di Azure o gestione API e i passaggi del flusso di controllo come condizioni, cicli e istruzioni switch. Ogni azione ha un proprio tipo di azione. Ad esempio, le azioni che chiamano [connettori](/connectors) hanno il tipo "ApiConnection". Questi connettori sono classificati come connettori standard o Enterprise, che vengono misurati in base ai rispettivi [prezzi](https://azure.microsoft.com/pricing/details/logic-apps). I connettori aziendali in *Anteprima* vengono addebitati come connettori standard.

App per la logica di Azure misura tutte le azioni riuscite e non riuscite come esecuzioni. Tuttavia, le app per la logica non misurano le azioni seguenti:

* Azioni che vengono ignorate a causa di condizioni non soddisfatte
* Azioni che non vengono eseguite perché l'app per la logica le ha arrestate prima del termine

Per le azioni eseguite all'interno di cicli, app per la logica di Azure conta ogni azione per ogni ciclo del ciclo. Supponiamo ad esempio di avere un ciclo "for each" che elabora un elenco. App per la logica misura un'azione in tale ciclo moltiplicando il numero di voci di elenco per il numero di azioni nel ciclo e aggiungendo l'azione che avvia il ciclo. Il calcolo per un elenco di 10 elementi è quindi (10 * 1) + 1, che comporta 11 esecuzioni di azioni.

## <a name="disabled-logic-apps"></a>App per la logica disabilitate

Le app per la logica disabilitate non vengono addebitate perché non è possibile creare nuove istanze mentre sono disabilitate. Dopo che un'app per la logica è stata disabilitata, l'arresto di eventuali istanze in esecuzione può richiedere del tempo.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Account di integrazione

Un [modello di determinazione prezzi fisso](https://azure.microsoft.com/pricing/details/logic-apps) si applica agli [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) in cui è possibile esplorare, sviluppare e testare le funzionalità di elaborazione [B2B ed EDI](logic-apps-enterprise-integration-b2b.md) e [XML](logic-apps-enterprise-integration-xml.md) in app per la logica di Azure senza costi aggiuntivi. Ogni sottoscrizione di Azure può avere fino a un [limite specifico di account di integrazione](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Ogni account di integrazione può archiviare fino a un [limite specifico di elementi](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), tra cui partner commerciali, contratti, mappe, schemi, assembly, certificati, configurazioni batch e così via.

App per la logica di Azure offre account di integrazione gratuito, di base e standard. I livelli Basic e standard sono supportati dal contratto di servizio delle app per la logica, mentre il livello gratuito non è supportato da un contratto di servizio e presenta limiti per la disponibilità, la velocità effettiva e l'utilizzo dell'area. Ad eccezione degli account di integrazione del livello gratuito, è possibile avere più di un account di integrazione in ogni area di Azure. Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/).

Se si dispone di un [ *ambiente Integration Services* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), ISE può usare un unico account di integrazione senza costi aggiuntivi, anche se il tipo di account incluso varia in base allo [SKU di ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). Per un [costo aggiuntivo](#fixed-pricing), è possibile creare altri account di integrazione per ISE fino al [limite totale](logic-apps-limits-and-config.md#integration-account-limits)per gli account di integrazione. Per informazioni sul funzionamento del modello di determinazione prezzi fisso per un ISE, vedere la sezione [modello di determinazione prezzi fisso](#fixed-pricing) precedente in questo argomento. Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

Per scegliere tra un account di integrazione gratuito, Basic o standard, esaminare le descrizioni dei casi d'uso seguenti:

* **Gratuito**: per i casi in cui si vuole provare scenari esplorativi, non scenari di produzione. Questo livello è disponibile solo per le aree pubbliche in Azure, ad esempio Stati Uniti occidentali o Asia sudorientale, ma non per [Azure Cina 21ViaNet](/azure/china/overview-operations) o [Azure per enti](../azure-government/documentation-government-welcome.md)pubblici.

* **Basic**: per i casi in cui si desidera solo la gestione dei messaggi o fungere da partner commerciale di piccole dimensioni con una relazione tra partner commerciali e un'entità di business più ampia

* **Standard**: per le relazioni B2B più complesse e un numero maggiore di entità che è necessario gestire

<a name="data-retention"></a>

## <a name="data-retention"></a>Conservazione dei dati

Fatta eccezione per le app per la logica eseguite in un ambiente Integration Services (ISE), tutti gli input e gli output archiviati nella cronologia di esecuzione dell'app per la logica vengono fatturati in base al [periodo di memorizzazione](logic-apps-limits-and-config.md#run-duration-retention-limits)dell'app per la logica. Le app per la logica eseguite in ISE non incorrono sui costi di conservazione dei dati. Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

Per facilitare il monitoraggio del consumo di spazio di archiviazione dell'app per la logica, è possibile:

* Visualizzare il numero di unità di archiviazione in GB utilizzate dall'app per la logica mensilmente.

* Visualizzare le dimensioni per gli input e gli output di un'azione specifica nella cronologia di esecuzione dell'app per la logica.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Visualizzare il consumo di archiviazione delle app per la logica

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Dal menu dell'app per la logica, in **monitoraggio** selezionare **metriche**.

1. Nel riquadro di destra, in **titolo grafico**, dall'elenco **metrica** Selezionare **utilizzo fatturazione per le esecuzioni del consumo di archiviazione**.

   Questa metrica indica il numero di unità di consumo di archiviazione in GB al mese fatturate.

   > [!NOTE]
   > Le esecuzioni che utilizzano meno di 500 MB di spazio di archiviazione potrebbero non essere visualizzate nella visualizzazione monitoraggio, ma vengono comunque fatturate.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Visualizzare le dimensioni di input e output dell'azione

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Scegliere **Panoramica** dal menu dell'app per la logica.

1. Nel riquadro di destra, in **Cronologia esecuzioni**, selezionare l'esecuzione con gli input e gli output che si desidera controllare.

1. In **esecuzione App** per la logica scegliere **Dettagli esecuzione**.

1. Nella tabella Actions del riquadro **Dettagli esecuzione App** per la logica, che elenca lo stato e la durata di ogni azione, selezionare l'azione che si desidera visualizzare.

1. Nel riquadro **azione dell'app** per la logica trovare le dimensioni per gli input e gli output dell'azione. In **input collegamento** e **output collegamento** trovare i collegamenti a tali input e output.

   > [!NOTE]
   > Per i cicli for, solo le azioni di primo livello mostrano le dimensioni per gli input e gli output. Per le azioni all'interno di cicli annidati, gli input e gli output mostrano dimensioni zero e nessun collegamento.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sulle app per la logica di Azure](logic-apps-overview.md)
* [Creare la prima app per la logica](quickstart-create-first-logic-app-workflow.md)
