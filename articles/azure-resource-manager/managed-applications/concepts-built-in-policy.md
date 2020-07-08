---
title: Distribuire le associazioni per l'applicazione gestita usando i criteri
description: Informazioni sulla distribuzione di associazioni per un'applicazione gestita con il servizio criteri di Azure.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: ec0fe8f66ef2ad2458b4ffad0e848591793e5b05
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75650942"
---
# <a name="deploy-associations-for-a-managed-application-using-azure-policy"></a>Distribuire le associazioni per un'applicazione gestita usando criteri di Azure

I criteri di Azure possono essere usati per distribuire le associazioni per associare le risorse a un'applicazione gestita. In questo articolo viene descritto un criterio incorporato che distribuisce le associazioni e come è possibile usare tale criterio.

## <a name="built-in-policy-to-deploy-associations"></a>Criteri predefiniti per distribuire le associazioni

Distribuisci associazioni per un'applicazione gestita è un criterio predefinito che può essere usato per distribuire un'associazione per associare una risorsa a un'applicazione gestita. Il criterio accetta tre parametri:

- ID applicazione gestita: questo ID è l'ID risorsa dell'applicazione gestita a cui è necessario associare le risorse.
- Tipi di risorse da associare: questi tipi di risorse sono l'elenco dei tipi di risorse da associare all'applicazione gestita. È possibile associare più tipi di risorse a un'applicazione gestita usando gli stessi criteri.
- Prefisso nome associazione: questa stringa è il prefisso da aggiungere al nome della risorsa di associazione da creare. Il valore predefinito è "DeployedByPolicy".

Il criterio usa la valutazione DeployIfNotExists. Viene eseguito dopo che un provider di risorse ha gestito una richiesta di creazione o aggiornamento della risorsa dei tipi di risorse selezionati e la valutazione ha restituito un codice di stato di esito positivo. Successivamente, la risorsa di associazione viene distribuita usando una distribuzione modello.
Per altre informazioni sulle associazioni, vedere Caricamento di [risorse dei provider personalizzati di Azure](../custom-providers/concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Come usare i criteri predefiniti Distribuisci associazioni 

### <a name="prerequisites"></a>Prerequisiti
Se l'applicazione gestita necessita di autorizzazioni per la sottoscrizione per eseguire un'azione, la distribuzione dei criteri della risorsa di associazione non funzionerà senza concedere le autorizzazioni.

### <a name="policy-assignment"></a>Assegnazione di criteri
Per usare i criteri predefiniti, creare un'assegnazione di criteri e assegnare le associazioni Distribuisci per i criteri di un'applicazione gestita. Una volta che il criterio è stato assegnato correttamente, i criteri identificano le risorse non conformi e distribuiscono le associazioni per tali risorse.

![Assegnare i criteri predefiniti](media/concepts-built-in-policy/assign-builtin-policy-managedapp.png)

## <a name="getting-help"></a>Risorse della Guida

In caso di domande sullo sviluppo di provider di risorse personalizzati di Azure, provare a richiederli [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di inserirle. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come usare i criteri predefiniti per distribuire le associazioni. Per ulteriori informazioni, vedere questi articoli:

- [Concetti: onboarding delle risorse del provider personalizzato di Azure](../custom-providers/concepts-resource-onboarding.md)
- [Esercitazione: onboarding delle risorse con provider personalizzati](../custom-providers/tutorial-resource-onboarding.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](../custom-providers/tutorial-get-started-with-custom-providers.md)
- [Guida introduttiva: creare un provider di risorse personalizzato e distribuire risorse personalizzate](../custom-providers/create-custom-provider.md)
- [Procedura: aggiungere azioni personalizzate a un'API REST di Azure](../custom-providers/custom-providers-action-endpoint-how-to.md)
- [Procedura: aggiungere risorse personalizzate a un'API REST di Azure](../custom-providers/custom-providers-resources-endpoint-how-to.md)
