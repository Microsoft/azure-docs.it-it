---
title: Miglioramento del modello-Servizio visione artificiale personalizzato
titleSuffix: Azure Cognitive Services
description: In questo articolo si apprenderà come la quantità, la qualità e la varietà di dati possono migliorare la qualità del modello nel servizio Visione personalizzata.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ae0112292994fdcf88e80abff8ab52e5971cb0ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101731086"
---
# <a name="how-to-improve-your-custom-vision-model"></a>Come migliorare il modello di Visione personalizzata

In questa guida si apprenderà come migliorare la qualità del modello di Servizio visione artificiale personalizzato. La qualità del [classificatore](./getting-started-build-a-classifier.md) o del [rilevatore di oggetti](./get-started-build-detector.md) dipende dalla quantità, dalla qualità e dalla varietà dei dati con etichetta forniti e dall'equilibrio del set di dati complessivo. Un modello valido dispone di un set di dati di training bilanciato che rappresenta gli elementi che verranno inviati. Il processo di compilazione di un modello di questo tipo è iterativo; per raggiungere i risultati previsti, è comune eseguire alcuni cicli di training.

Di seguito è riportato un modello generale che consente di eseguire il training di un modello più accurato:

1. Primo ciclo di training
1. Aggiungere altre immagini e bilanciare i dati; ripetere il training
1. Aggiungere immagini con sfondi, illuminazione, dimensioni degli oggetti, angolazioni e stili diversi; ripetere il training
1. Usare nuove immagini per testare la stima
1. Modificare i dati di training esistenti in base ai risultati della stima

## <a name="prevent-overfitting"></a>Impedisci overfitting

In alcuni casi, un modello apprenderà a eseguire stime in base alle caratteristiche arbitrarie che le immagini hanno in comune. Ad esempio, se si sta creando un classificatore per mele e agrumi e si sono usate immagini di mele in mano e di agrumi su piatti bianchi, il classificatore potrebbe dare importanza non dovuta a mani e piatti bianchi anziché a mele e agrumi.

![Immagine di classificazione imprevista](./media/getting-started-improving-your-classifier/unexpected.png)

Per risolvere il problema, fornire immagini con diversi angoli, sfondi, dimensioni degli oggetti, gruppi e altre varianti. Le sezioni seguenti si espandono su questi concetti.

## <a name="data-quantity"></a>Quantità di dati

Il numero di immagini di training rappresenta il fattore più importante per il set di dati. È consigliabile usare almeno 50 immagini per etichetta come punto di partenza. Con un numero inferiore di immagini, c'è un rischio maggiore di overfitting e, sebbene i numeri di prestazione possano suggerire una buona qualità, il modello potrebbe avere difficoltà con i dati del mondo reale. 

## <a name="data-balance"></a>Bilanciamento dei dati

È anche importante valutare le quantità relative dei dati di training. Ad esempio, l'uso di 500 immagini per un'etichetta e di 50 immagini per un'altra etichetta rende sbilanciato un set di dati di training. In questo modo il modello sarà più accurato nel predire un'etichetta piuttosto che un'altra. È possibile ottenere risultati migliori se si mantiene un rapporto di almeno 1:2 tra l'etichetta con il minor numero di immagini e l'etichetta con il maggior numero di immagini. Ad esempio, se l'etichetta con più immagini contiene 500 immagini, l'etichetta con il numero inferiore di immagini dovrebbe disporre per il training di almeno 250 immagini.

## <a name="data-variety"></a>Ampia gamma di dati

Assicurarsi di usare immagini rappresentative di ciò che verrà inviato al classificatore durante l'uso normale. In caso contrario, il modello potrebbe imparare a eseguire stime in base alle caratteristiche arbitrarie che le immagini hanno in comune. Ad esempio, se si sta creando un classificatore per mele e agrumi e si sono usate immagini di mele in mano e di agrumi su piatti bianchi, il classificatore potrebbe dare importanza non dovuta a mani e piatti bianchi anziché a mele e agrumi.

![Immagine di classificazione imprevista](./media/getting-started-improving-your-classifier/unexpected.png)

Per risolvere questo problema, includere un'ampia gamma di immagini per garantire che il modello possa generalizzare. Di seguito sono illustrati alcuni modi in cui è possibile diversificare il set di training:

* __Sfondo:__ Fornire immagini dell'oggetto davanti a diversi sfondi. Le foto in contesti naturali sono migliori delle foto con sfondi neutri in quanto forniscono informazioni aggiuntive al classificatore.

    ![Immagine di esempi di sfondo](./media/getting-started-improving-your-classifier/background.png)

* __Illuminazione:__ Fornire immagini con illuminazione variegata (ovvero, con Flash, esposizione elevata e così via), soprattutto se le immagini utilizzate per la stima hanno un'illuminazione diversa. È inoltre utile usare immagini con saturazione, tonalità e luminosità variabili.

    ![Immagine di esempi di illuminazione](./media/getting-started-improving-your-classifier/lighting.png)

* __Dimensioni oggetto:__ Fornire le immagini in cui gli oggetti variano a seconda delle dimensioni e del numero (ad esempio, una foto di grappoli di banane e un primo piano di una singola Banana). Le diverse dimensioni consentono una migliore generalizzazione da parte del classificatore.

    ![Immagine di esempi di dimensioni](./media/getting-started-improving-your-classifier/size.png)

* __Angolazione:__ fornire immagini scattate con diverse angolazioni. In alternativa, se tutte le foto devono essere eseguite con telecamere fisse, ad esempio telecamere di sorveglianza, assicurarsi di assegnare un'etichetta diversa a ciascuna degli oggetti che ricorrono regolarmente per evitare l'overfitting&mdash;nell'interpretazione di oggetti non correlati, ad esempio lampioni, come funzionalità chiave.

    ![Immagine di esempi di angolazione](./media/getting-started-improving-your-classifier/angle.png)

* __Stile:__ Fornire immagini di stili diversi della stessa classe (ad esempio, diverse varietà dello stesso frutto). Tuttavia, se si dispone di oggetti con stili drasticamente diversi (ad esempio, un'immagine di Mickey Mouse rispetto a un topo vero), è consigliabile etichettarle come classi separate in modo da rappresentare meglio le caratteristiche distinte.

    ![Immagine di esempi di stile](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images-classifiers-only"></a>Immagini negative (solo classificatori)

Se si usa un classificatore di immagini, potrebbe essere necessario aggiungere _esempi negativi_ per rendere il classificatore più accurato. Gli esempi negativi sono immagini che non corrispondono ad alcun altro tag. Quando si caricano queste immagini, applicarvi la speciale etichetta **Negative** (Negativa).

I rilevatori di oggetti gestiscono automaticamente campioni negativi, perché tutte le aree di immagine all'esterno dei riquadri delimitatori disegnati sono considerate negative.

> [!NOTE]
> Il servizio Visione personalizzata supporta alcune operazioni di gestione automatica delle immagine negative. Se, ad esempio, si sta creando un classificatore di uva e banane e per la stima si invia l'immagine di una scarpa, il classificatore deve segnare un punteggio per quell'immagine più vicino possibile allo 0%, sia per l'uva che per le banane.
> 
> D'altra parte, nei casi in cui le immagini negative sono soltanto una variazione delle immagini usate nel training, è probabile che il modello classificherà le immagini negative come una classe con etichetta a causa delle molte analogie. Se, ad esempio, si ha un classificatore di arance e di pompelmi e si invia un'immagine di una clementina, è possibile che la clementina sia classificata come un'arancia perché molte sue caratteristiche sono simili a quelle delle arance. Se le immagini negative sono di questo tipo, è consigliabile creare uno o più tag aggiuntivi, ad esempio **Altro**, e assegnare un'etichetta alle immagini negative con questo tag durante il training per consentire al modello di distinguere meglio queste classi.

## <a name="consider-occlusion-and-truncation-object-detectors-only"></a>Considerare l'occlusione e il troncamento (solo per i rilevatori di oggetti)

Se si desidera che il rilevatore di oggetti rilevi oggetti troncati (l'oggetto viene tagliato parzialmente dall'immagine) o oggetti bloccati (l'oggetto è parzialmente bloccato da un altro oggetto nell'immagine), è necessario includere le immagini di training che coprono tali case.

> [!NOTE]
> Il problema degli oggetti bloccati da altri oggetti non deve essere confuso con la **soglia di sovrapposizione**, un parametro per le prestazioni del modello di valutazione. Il dispositivo di scorrimento **soglia di sovrapposizione** nel [sito Web visione personalizzata](https://customvision.ai) gestisce la quantità di un rettangolo di delimitazione stimato che deve sovrapporsi al rettangolo di delimitazione per essere considerato corretto.

## <a name="use-prediction-images-for-further-training"></a>Usare immagini di stima per ulteriori training

Quando si utilizza o si testa il modello inviando immagini all'endpoint di stima, il servizio Visione personalizzata archivia tali immagini. È quindi possibile usarle per migliorare il modello.

1. Per visualizzare le immagini inviate al modello, aprire la [pagina web visione personalizzata](https://customvision.ai), passare al progetto e selezionare la scheda __stime__ . La visualizzazione predefinita mostra le immagini dell'iterazione corrente. È possibile usare il menu a discesa __Iteration__ (Iterazione) per visualizzare le immagini inviate durante le iterazioni precedenti.

    ![Screenshot della scheda delle stime con immagini in vista](./media/getting-started-improving-your-classifier/predictions.png)

2. Passare il mouse su un'immagine per visualizzare i tag stimati dal modello. Le immagini vengono ordinate in modo tale che i miglioramenti apportati al modello siano elencati nella parte superiore. Per usare un metodo di ordinamento diverso, effettuare una selezione all'interno della sezione __Sort__ (Ordinamento). 

    Per aggiungere un'immagine ai dati di training esistenti, scegliere l'immagine, impostare i tag corretti e quindi fare clic su __Salva e chiudi__. L'immagine verrà rimossa da __Predictions__ (Stime) e aggiunta alle immagini di training. È possibile visualizzarla selezionando la scheda __Training Images__ (Immagini di training).

    ![Immagine della pagina dei tag](./media/getting-started-improving-your-classifier/tag.png)

3. Usare quindi il pulsante __Train__ per ripetere il training del modello.

## <a name="visually-inspect-predictions"></a>Controllare visivamente le stime

Per esaminare le stime delle immagini, passare alla scheda __Training Images__ (Immagini di training), selezionare l'iterazione di training precedenti nel menu a discesa **Iteration** (Iterazione) e selezionare uno o più tag nella sezione **Tags** (Tag). La visualizzazione dovrebbe ora mostrare una casella rossa intorno a ciascuna delle immagini per le quali il modello non è riuscito a prevedere correttamente l'etichetta data.

![Immagine della cronologia delle iterazioni](./media/getting-started-improving-your-classifier/iteration.png)

A volte un'ispezione visiva può identificare modelli che è possibile correggere aggiungendo più dati di training o modificando quelli esistenti. Un classificatore di mele rispetto a lime potrebbe ad esempio etichettare erroneamente tutte le mele verdi come lime. È perciò possibile risolvere il problema aggiungendo e fornendo dati di training che contengono immagini con tag di mele verdi.

## <a name="next-steps"></a>Passaggi successivi

In questa guida sono state apprese diverse tecniche per rendere più accurato il modello di classificazione delle immagini o il modello di rilevatore di oggetti personalizzato. Successivamente, altre informazioni su come eseguire il test delle immagini a livello di codice inviandole all'API delle stime.

> [!div class="nextstepaction"]
> [Usare l'API Prediction](use-prediction-api.md)