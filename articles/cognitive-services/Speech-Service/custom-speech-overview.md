---
title: Panoramica di Riconoscimento vocale personalizzato-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato è un set di strumenti online che consentono di valutare e migliorare l'accuratezza del riconoscimento vocale Microsoft per le applicazioni, gli strumenti e i prodotti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: f2dca513b4413f1761ea1b21bf71f90bb27d3053
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388652"
---
# <a name="what-is-custom-speech"></a>Che cos'è il servizio Riconoscimento vocale personalizzato?

[Riconoscimento vocale personalizzato](https://aka.ms/customspeech) è un set di strumenti basati su interfaccia utente che consentono di valutare e migliorare l'accuratezza del riconoscimento vocale Microsoft per le applicazioni e i prodotti. Per iniziare è necessario solo un numero limitato di file audio di test. Per iniziare a creare un'esperienza di riconoscimento vocale personalizzata, seguire i collegamenti riportati in questo articolo.

## <a name="whats-in-custom-speech"></a>Quali sono i Riconoscimento vocale personalizzato?

Prima di poter eseguire qualsiasi operazione con Riconoscimento vocale personalizzato, saranno necessari un account Azure e una sottoscrizione al servizio vocale. Una volta che si dispone di un account, è possibile preparare i dati, eseguire il training e testare i modelli, controllare la qualità del riconoscimento, valutare l'accuratezza e infine distribuire e usare il modello di riconoscimento vocale personalizzato.

In questo diagramma vengono evidenziati i componenti che costituiscono l' [area riconoscimento vocale personalizzato di speech studio](https://aka.ms/customspeech). Usare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

![Diagramma che evidenzia i componenti che costituiscono l'area Riconoscimento vocale personalizzato di speech studio.](./media/custom-speech/custom-speech-overview.png)

1. [Sottoscrivere e creare un progetto](#set-up-your-azure-account). Creare un account Azure e sottoscrivere il servizio di riconoscimento vocale. Questa sottoscrizione unificata consente di accedere a sintesi vocale, sintesi vocale, traduzione vocale e [speech studio](https://speech.microsoft.com/customspeech). Usare quindi la sottoscrizione al servizio vocale per creare il primo progetto Riconoscimento vocale personalizzato.

1. [Caricare i dati di test](./how-to-custom-speech-test-and-train.md). Caricare i dati di test (file audio) per valutare l'offerta di riconoscimento vocale Microsoft per le applicazioni, gli strumenti e i prodotti.

1. [Controllare la qualità del riconoscimento](how-to-custom-speech-inspect-data.md). Usare [speech studio](https://speech.microsoft.com/customspeech) per riprodurre l'audio caricato e controllare la qualità del riconoscimento vocale dei dati di test. Per le misurazioni quantitative, vedere [esaminare i dati](how-to-custom-speech-inspect-data.md).

1. [Valutazione e miglioramento dell'accuratezza](how-to-custom-speech-evaluate-data.md). Valutare e migliorare l'accuratezza del modello di riconoscimento vocale. [Speech studio](https://speech.microsoft.com/customspeech) fornirà una *percentuale di errori di parola*, che è possibile usare per determinare se è necessario un training aggiuntivo. Se si è soddisfatti dell'accuratezza, è possibile usare direttamente le API del servizio di riconoscimento vocale. Per migliorare l'accuratezza in base a una media relativa del 5% al 20%, usare la scheda **Training** nel portale per caricare dati di training aggiuntivi, come le trascrizioni con etichetta umana e il testo correlato.

1. Eseguire [il training e distribuire un modello](how-to-custom-speech-train-model.md). Per migliorare l'accuratezza del modello di riconoscimento vocale, è possibile fornire trascrizioni scritte (da 10 a 1.000 ore) e testo correlato (<200 MB) insieme ai dati di test audio. Questi dati consentono di eseguire il training del modello di riconoscimento vocale. Dopo il training, riprovare. Se si è soddisfatti del risultato, è possibile distribuire il modello in un endpoint personalizzato.

## <a name="set-up-your-azure-account"></a>Configurare l'account Azure

Prima di poter usare [speech studio](https://speech.microsoft.com/customspeech) per creare un modello personalizzato, è necessario avere un account Azure e una sottoscrizione al servizio di riconoscimento vocale. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Se si prevede di eseguire il training di un modello personalizzato con **dati audio**, scegliere una delle aree seguenti per cui è disponibile hardware dedicato per il training. In questo modo si riduce il tempo necessario per il training di un modello.

* Australia orientale
* Canada centrale
* India centrale
* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti centro-settentrionali
* Europa settentrionale
* Stati Uniti centro-meridionali
* Asia sud-orientale
* Regno Unito meridionale
* US Gov Arizona
* US Gov Virginia
* Europa occidentale
* Stati Uniti occidentali 2

> [!NOTE]
> Assicurarsi di creare una sottoscrizione standard (S0). Le sottoscrizioni gratuite (F0) non sono supportate.

Dopo aver creato un account Azure e una sottoscrizione al servizio vocale, è necessario accedere a [speech studio](https://speech.microsoft.com/customspeech) e connettere la sottoscrizione.

1. Accedere a [speech studio](https://aka.ms/custom-speech).
1. Selezionare la sottoscrizione che è necessario usare e creare un progetto di riconoscimento vocale.
1. Se si vuole modificare la sottoscrizione, selezionare il pulsante ingranaggio nel menu in alto.

## <a name="how-to-create-a-project"></a>Come creare un progetto

Contenuto come dati, modelli, test ed endpoint sono organizzati in *progetti* in [speech studio](https://speech.microsoft.com/customspeech). Ogni progetto è specifico per un dominio e un paese/lingua. Ad esempio, è possibile creare un progetto per i Call Center che usano la lingua inglese nella Stati Uniti.

Per creare il primo progetto, selezionare riconoscimento **vocale/vocale personalizzato**, quindi selezionare **nuovo progetto**. Seguire le istruzioni fornite dalla procedura guidata per creare il progetto. Dopo aver creato un progetto, verranno visualizzate quattro schede: **dati**, **testing**, **Training** e **distribuzione**. Usare i collegamenti forniti nei [passaggi successivi](#next-steps) per informazioni su come usare ogni scheda.

> [!IMPORTANT]
> Il [riconoscimento vocale](https://aka.ms/custom-speech) precedentemente noto come "portale riconoscimento vocale personalizzato" è stato aggiornato di recente. Se sono stati creati dati, modelli, test e endpoint pubblicati in precedenza nel portale di CRIS.ai o con le API, è necessario creare un nuovo progetto nel nuovo portale per connettersi a queste entità obsolete.

## <a name="model-lifecycle"></a>Ciclo di vita del modello

Riconoscimento vocale personalizzato utilizza sia *modelli di base* che *modelli personalizzati*. Ogni lingua dispone di uno o più modelli di base. In genere, quando un nuovo modello di riconoscimento vocale viene rilasciato al servizio di riconoscimento vocale normale, viene anche importato nel servizio Riconoscimento vocale personalizzato come nuovo modello di base. Sono aggiornati ogni 3-6 mesi. I modelli meno recenti diventano in genere meno utili nel tempo perché il modello più recente ha in genere una maggiore precisione.

Al contrario, i modelli personalizzati vengono creati adattando un modello di base scelto a uno scenario specifico del cliente. È possibile usare un modello personalizzato specifico per un lungo periodo di tempo dopo averne uno che soddisfi le proprie esigenze. È tuttavia consigliabile eseguire periodicamente l'aggiornamento al modello di base più recente e ripetere il training nel tempo con dati aggiuntivi. 

Altri termini chiave correlati al ciclo di vita del modello includono:

* **Adattamento**: acquisizione di un modello di base e personalizzazione del proprio dominio/scenario tramite dati di testo e/o dati audio.
* **Decodifica**: uso di un modello ed esecuzione del riconoscimento vocale (decodifica dell'audio nel testo).
* **Endpoint**: distribuzione specifica dell'utente di un modello di base o di un modello personalizzato accessibile *solo* a un determinato utente.

### <a name="expiration-timeline"></a>Sequenza temporale scadenza

Poiché nuovi modelli e nuove funzionalità diventano disponibili e meno recenti, i modelli meno accurati vengono ritirati, vedere le sequenze temporali seguenti per la scadenza del modello e dell'endpoint:

**Modelli di base** 

* Adattamento: disponibile per un anno. Una volta importato, il modello è disponibile per un anno per la creazione di modelli personalizzati. Dopo un anno, è necessario creare nuovi modelli personalizzati da una versione più recente del modello di base.  
* Decodifica: disponibile per due anni dopo l'importazione. Quindi, è possibile creare un endpoint e usare la trascrizione batch per due anni con questo modello. 
* Endpoint: disponibile nella stessa sequenza temporale della decodifica.

**Modelli personalizzati**

* Decodifica: disponibile per due anni dopo la creazione del modello. È quindi possibile usare il modello personalizzato per due anni (batch/tempo reale/test) dopo che è stato creato. Dopo due anni, *è necessario* ripetere il training del modello perché il modello di base sarà in genere deprecato per l'adattamento.  
* Endpoint: disponibile nella stessa sequenza temporale della decodifica.

Quando un modello di base o un modello personalizzato scade, viene sempre eseguito il fallback alla *versione più recente del modello di base*. Quindi, l'implementazione non verrà mai interrotta, ma potrebbe diventare meno accurata per *i dati specifici* se i modelli personalizzati raggiungono la scadenza. È possibile visualizzare la scadenza per un modello nelle posizioni seguenti nell'area Riconoscimento vocale personalizzato di speech studio:

* Riepilogo del training del modello
* Dettagli di training del modello
* Riepilogo della distribuzione
* Dettagli distribuzione

È anche possibile controllare le date di scadenza tramite [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) le [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) API di riconoscimento vocale e personalizzate sotto la `deprecationDates` proprietà nella risposta JSON.

Si noti che è possibile aggiornare il modello in un endpoint di riconoscimento vocale personalizzato senza tempi di inattività cambiando il modello usato dall'endpoint nella sezione distribuzione di speech studio o tramite l'API riconoscimento vocale personalizzato.

## <a name="next-steps"></a>Passaggi successivi

* [Preparare e testare i dati](./how-to-custom-speech-test-and-train.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare e migliorare l'accuratezza del modello](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training di un modello e distribuirlo](how-to-custom-speech-train-model.md)
