---
title: Usare l'intelligenza artificiale per arricchire il contenuto del BLOB
titleSuffix: Azure Cognitive Search
description: Informazioni sulle funzionalità di analisi delle immagini e del linguaggio naturale in Azure ricerca cognitiva e sul modo in cui tali processi si applicano ai contenuti archiviati nei BLOB di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99222049"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Usare l'intelligenza artificiale per elaborare e analizzare il contenuto di BLOB in Azure ricerca cognitiva

Il contenuto nell'archivio BLOB di Azure composto da immagini o testo non differenziato lungo può essere sottoposto a un'analisi approfondita per rivelare ed estrarre informazioni utili per le applicazioni downstream. Con l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md)è possibile:

+ Estrai il testo dalle immagini usando il riconoscimento ottico dei caratteri (OCR)
+ Produrre una descrizione della scena o i tag da una foto
+ Rilevare la lingua e tradurre il testo in lingue diverse
+ Dedurre la struttura tramite il riconoscimento delle entità mediante la ricerca di riferimenti a persone, date, posizioni o organizzazioni

Sebbene possa essere necessaria solo una di queste funzionalità di intelligenza artificiale, è comune combinare più di essi nella stessa pipeline (ad esempio, estraendo testo da un'immagine digitalizzata e quindi individuando tutte le date e le posizioni a cui viene fatto riferimento). È anche comune includere l'intelligenza artificiale personalizzata o l'elaborazione di Machine Learning sotto forma di pacchetti esterni all'avanguardia o di modelli interni personalizzati per i dati e i requisiti.

Sebbene sia possibile applicare l'arricchimento di intelligenza artificiale a qualsiasi origine dati supportata da un indicizzatore di ricerca, i BLOB sono le strutture usate più di frequente in una pipeline di arricchimento. I risultati vengono inseriti in un indice di ricerca per la ricerca full-text o reindirizzati ad archiviazione di Azure per potenziare le nuove esperienze di applicazioni che includono l'esplorazione dei dati per gli scenari di individuazione o di analisi. 

In questo articolo viene illustrato come arricchire l'intelligenza artificiale tramite un grandangolo, in modo che sia possibile comprendere rapidamente l'intero processo, dalla trasformazione dei dati non elaborati nei BLOB, alle informazioni Queryable in un indice di ricerca o in un archivio informazioni.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Cosa significa "arricchire" i dati BLOB con intelligenza artificiale

L' *arricchimento di intelligenza artificiale* fa parte dell'architettura di indicizzazione di ricerca cognitiva di Azure che integra modelli di apprendimento automatico di Microsoft o di modelli di apprendimento personalizzati forniti dall'utente. Consente di implementare scenari end-to-end in cui è necessario elaborare i BLOB (sia quelli esistenti che quelli nuovi Man mano che vengono aggiornati), crack aprono tutti i formati di file per estrarre immagini e testo, estrarre le informazioni desiderate usando varie funzionalità di intelligenza artificiale e indicizzarle in un indice di ricerca per la ricerca veloce, il recupero e l'esplorazione. 

Gli input sono i BLOB, in un singolo contenitore, nell'archivio BLOB di Azure. I BLOB possono essere quasi qualsiasi tipo di dati di testo o di immagine. 

L'output è sempre un indice di ricerca, usato per la ricerca di testo veloce, il recupero e l'esplorazione nelle applicazioni client. Inoltre, l'output può essere anche un [*Archivio informazioni*](knowledge-store-concept-intro.md) che proietta documenti arricchiti in BLOB di Azure o tabelle di Azure per l'analisi downstream in strumenti come Power bi o in carichi di lavoro Data Science.

In between è l'architettura della pipeline stessa. La pipeline è basata sugli [*indicizzatori*](search-indexer-overview.md), a cui è possibile assegnare un [*Skills*](cognitive-search-working-with-skillsets.md), che è costituita da una o più *competenze* che forniscono l'intelligenza artificiale. Lo scopo della pipeline è quello di produrre *documenti arricchiti* che entrano nella pipeline come contenuto non elaborato, ma acquisiscono struttura, contesto e informazioni aggiuntive durante lo scorrimento della pipeline. I documenti arricchiti vengono utilizzati durante l'indicizzazione per creare indici invertiti e altre strutture utilizzate nella ricerca full-text o nell'esplorazione e nell'analisi.

## <a name="required-resources"></a>Risorse necessarie

Oltre all'archivio BLOB di Azure e ad Azure ricerca cognitiva, è necessario un terzo servizio o meccanismo che fornisce l'intelligenza artificiale:

+ Per l'intelligenza artificiale incorporata, ricerca cognitiva si integra con la visione dei servizi cognitivi di Azure e con le API di elaborazione del linguaggio naturale. È possibile [alleghi una risorsa di servizi cognitivi](cognitive-search-attach-cognitive-services.md) per aggiungere il riconoscimento ottico dei caratteri (OCR), l'analisi delle immagini o l'elaborazione del linguaggio naturale (rilevamento della lingua, traduzione del testo, riconoscimento delle entità, estrazione di frasi chiave). 

+ Per l'intelligenza artificiale personalizzata usando le risorse di Azure, è possibile definire un'abilità personalizzata che esegue il wrapping della funzione o del modello esterno che si vuole usare. Le [competenze personalizzate](cognitive-search-custom-skill-interface.md) possono usare il codice fornito da funzioni di azure, Azure Machine Learning, riconoscimento moduli di Azure o un'altra risorsa raggiungibile tramite HTTPS.

+ Per l'intelligenza artificiale non Azure personalizzata, il modello o il modulo deve essere accessibile a un indicizzatore tramite HTTP.

Se non si dispone di tutti i servizi immediatamente disponibili, iniziare direttamente nella pagina del portale dell'account di archiviazione. Nella pagina di spostamento a sinistra, in **Servizio BLOB** fare clic su **Aggiungi Ricerca cognitiva di Azure** per creare un nuovo servizio o selezionarne uno esistente. 

Una volta aggiunto Azure ricerca cognitiva all'account di archiviazione, è possibile seguire il processo standard per arricchire i dati in qualsiasi origine dati di Azure. È consigliabile usare la procedura guidata **Importa dati** in Azure ricerca cognitiva per una semplice introduzione iniziale all'arricchimento di intelligenza artificiale. È possibile aggiungere una risorsa Servizi cognitivi durante il flusso di lavoro. Questa Guida introduttiva illustra la procedura [di creazione di una pipeline di arricchimento ai nel portale](cognitive-search-quickstart-blob.md). 

Nelle sezioni seguenti vengono esaminati in dettaglio i componenti e il flusso di lavoro.

## <a name="use-a-blob-indexer"></a>Usare un indicizzatore BLOB

L'arricchimento AI è un componente aggiuntivo di una pipeline di indicizzazione e in Azure ricerca cognitiva tali pipeline sono basate su un *indicizzatore*. Un indicizzatore è un sottoservizio in grado di riconoscere le origini dati dotato di logica interna per il campionamento dei dati, la lettura di dati dei metadati, il recupero di dati e la serializzazione dei dati in documenti JSON a partire dal formato nativo per la successiva importazione. Gli indicizzatori vengono spesso usati da soli per l'importazione, separati dall'intelligenza artificiale, ma se si vuole creare una pipeline di arricchimento intelligenza artificiale, è necessario un indicizzatore e un insieme di competenze da usare. In questa sezione viene evidenziato l'indicizzatore; la sezione successiva è incentrata su skillsets.

I BLOB in archiviazione di Azure vengono indicizzati usando l' [indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md). È possibile richiamare questo indicizzatore usando la procedura guidata **Importa dati** , un'API REST o un SDK. Un indicizzatore BLOB viene richiamato quando l'origine dati usata dall'indicizzatore è un contenitore BLOB di Azure. È possibile indicizzare un subset di BLOB creando una directory virtuale, che può quindi essere passata come parametro o filtrando in base a un'estensione del tipo di file.

Un indicizzatore esegue il "cracking di documenti", aprendo un BLOB per esaminare il contenuto. Dopo la connessione all'origine dati, è il primo passaggio della pipeline. Per i dati BLOB, questo è il percorso in cui vengono rilevati PDF, documenti di Office, immagini e altri tipi di contenuto. Il cracking di documenti con estrazione del testo non prevede alcun addebito. Il cracking dei documenti con l'estrazione delle immagini viene addebitato in base alle tariffe disponibili nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/search/).

Sebbene tutti i documenti vengano violati, l'arricchimento si verifica solo se si specificano in modo esplicito le competenze necessarie. Ad esempio, se la pipeline è costituita esclusivamente dall'analisi delle immagini, il testo del contenitore o dei documenti viene ignorato.

L'indicizzatore BLOB viene fornito con i parametri di configurazione e supporta il rilevamento delle modifiche se i dati sottostanti forniscono informazioni sufficienti. Per altre informazioni, vedere [come configurare un indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Aggiungere componenti di intelligenza artificiale

L'arricchimento di intelligenza artificiale si riferisce a moduli che cercano modelli o caratteristiche, quindi esegue un'operazione di conseguenza. I riconoscimenti facciali nelle foto, le descrizioni di testo delle foto, il rilevamento di frasi chiave in un documento e l'OCR (o il riconoscimento di testo stampato o scritto in file binari) sono esempi illustrativi.

In ricerca cognitiva di Azure le *competenze* sono i singoli componenti dell'elaborazione di intelligenza artificiale che è possibile usare autonomamente o in combinazione con altre competenze. 

+ Le competenze predefinite sono supportate da servizi cognitivi, con analisi delle immagini basate su Visione artificiale e elaborazione del linguaggio naturale basata su Analisi del testo. Per l'elenco completo, vedere [competenze predefinite per l'arricchimento del contenuto](cognitive-search-predefined-skills.md).

+ Le competenze personalizzate sono codice personalizzato, racchiuso in una [definizione di interfaccia](cognitive-search-custom-skill-interface.md) che consente l'integrazione nella pipeline. Nelle soluzioni dei clienti, è prassi comune usare entrambi i moduli, con competenze personalizzate che forniscono moduli open source, di terze parti o della prima parte.

Un insieme di *competenze* è la raccolta di competenze usate in una pipeline e viene richiamato dopo che la fase di cracking del documento rende disponibile il contenuto. Un indicizzatore può utilizzare esattamente un solo skillt, ma tale skillt esiste indipendentemente da un indicizzatore, in modo che sia possibile riutilizzarlo in altri scenari.

Le competenze personalizzate possono sembrare complesse ma possono essere semplici e semplici in termini di implementazione. Se sono presenti pacchetti che forniscono criteri di ricerca o modelli di classificazione, il contenuto estratto dai BLOB potrebbe essere passato a questi modelli per l'elaborazione. Poiché l'arricchimento AI è basato su Azure, il modello deve essere anche in Azure. Alcune metodologie di hosting comuni includono l'uso di funzioni o [contenitori](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)di [Azure](cognitive-search-create-custom-skill-example.md) .

Le competenze predefinite supportate dai servizi cognitivi richiedono una chiave di sottoscrizione all-in-One [connessa di servizi cognitivi](cognitive-search-attach-cognitive-services.md) che consente di accedere alla risorsa. Una chiave all-in-One ti offre analisi delle immagini, rilevamento della lingua, traduzione testuale e analisi del testo. Altre competenze predefinite sono le funzionalità di Azure ricerca cognitiva e non richiedono alcun servizio o chiave aggiuntiva. Il formato di testo, la barra di divisione e la fusione sono esempi di competenze di supporto talvolta necessarie per la progettazione della pipeline.

Se si usano solo competenze personalizzate e competenze di utilità predefinite, non esistono dipendenze o costi correlati ai servizi cognitivi.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Utilizzare l'output arricchito con intelligenza artificiale nelle soluzioni downstream

L'output dell'arricchimento AI è costituito da un indice di ricerca in Azure ricerca cognitiva o da un [Archivio informazioni](knowledge-store-concept-intro.md) in archiviazione di Azure.

In Azure ricerca cognitiva un indice di ricerca viene usato per l'esplorazione interattiva usando testo libero e query filtrate in un'app client. I documenti arricchiti creati tramite intelligenza artificiale vengono formattati in JSON e indicizzati nello stesso modo in cui tutti i documenti vengono indicizzati in Azure ricerca cognitiva, sfruttando tutti i vantaggi offerti da un indicizzatore. Ad esempio, durante l'indicizzazione, l'indicizzatore BLOB fa riferimento ai parametri di configurazione e alle impostazioni per utilizzare i mapping dei campi o la logica di rilevamento delle modifiche. Tali impostazioni sono completamente disponibili per l'indicizzazione regolare e i carichi di lavoro arricchiti con intelligenza artificiale. Dopo l'indicizzazione, quando il contenuto viene archiviato in Azure ricerca cognitiva, è possibile compilare query e espressioni di filtro avanzate per comprendere il contenuto.

In archiviazione di Azure, in un archivio informazioni sono presenti due manifesti: un contenitore BLOB o tabelle nell'archivio tabelle. 

+ Un contenitore BLOB acquisisce interamente i documenti arricchiti, che risulta utile se si desidera eseguire il feed in altri processi. 

+ L'archiviazione tabelle può invece contenere proiezioni fisiche di documenti arricchiti. È possibile creare sezioni o livelli di documenti arricchiti che includono o escludono parti specifiche. Per l'analisi in Power BI, le tabelle nell'archiviazione tabelle di Azure diventano l'origine dati per un'ulteriore visualizzazione ed esplorazione.

Un documento arricchito alla fine della pipeline è diverso dalla versione di input originale dalla presenza di campi aggiuntivi contenenti nuove informazioni estratte o generate durante l'arricchimento. Di conseguenza, è possibile utilizzare una combinazione di contenuto originale e creato, indipendentemente dalla struttura di output utilizzata.

## <a name="next-steps"></a>Passaggi successivi

È possibile eseguire molte altre operazioni con l'arricchimento di intelligenza artificiale per sfruttare al meglio i dati in archiviazione di Azure, tra cui combinare servizi cognitivi in modi diversi e creare competenze personalizzate per i casi in cui non esiste un servizio cognitivo esistente per lo scenario. Per altre informazioni, vedere i collegamenti seguenti.

+ [Caricare, scaricare ed elencare BLOB con il portale di Azure (Archiviazione BLOB di Azure)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Configurare un indicizzatore BLOB (Ricerca cognitiva di Azure)](search-howto-indexing-azure-blob-storage.md) 
+ [Panoramica dell'arricchimento AI (ricerca cognitiva di Azure)](cognitive-search-concept-intro.md) 
+ [Creare un valore di competenze (ricerca cognitiva di Azure)](cognitive-search-defining-skillset.md)
+ [Mappare i nodi in un albero delle annotazioni (ricerca cognitiva di Azure)](cognitive-search-output-field-mapping.md)