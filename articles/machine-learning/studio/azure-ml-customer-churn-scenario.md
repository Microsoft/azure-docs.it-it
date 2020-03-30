---
title: Analizzare la varianza dei clienti
titleSuffix: ML Studio (classic) - Azure
description: Case study sullo sviluppo di un modello integrato per l'analisi e il punteggio della varianza dei clienti tramite Azure Machine Learning Studio (classico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204529"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Analizzare la varianza dei clienti usando Azure Machine Learning Studio (classico)Analyze Customer Churn using Azure Machine Learning Studio (classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Panoramica
Questo articolo presenta un'implementazione di riferimento di un progetto di analisi della varianza del cliente compilato tramite Azure Machine Learning Studio (classico). L'articolo illustra i modelli generici associati per la risoluzione olistica dei problemi di varianza del cliente industriale. Viene inoltre misurata l'accuratezza dei modelli compilati usando Machine Learning e vengono poi valutate direzioni di sviluppo ulteriore.  

### <a name="acknowledgements"></a>Riconoscimenti
Questo esperimento è stato sviluppato e testato da Serge Berger, Principal Data Scientist presso Microsoft, e Roger Barga, precedentemente Product Manager per Microsoft Azure Machine Learning Studio (classico). Il team di documentazione di Azure esprime riconoscenza e ringrazia gli esperti per aver condiviso le proprie competenze in questo white paper.

> [!NOTE]
> I dati usati per questo esperimento non sono disponibili pubblicamente. Per un esempio su come compilare un modello di apprendimento automatico per l'analisi della varianza, vedere: [Retail churn model template](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) (Modello di varianza al dettaglio) nella [Raccolta di intelligenza artificiale per Azure](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Il problema della varianza del cliente
Le aziende del mercato consumer, come tutte quelle dei settori commerciali, devono fare i conti con la varianza. In certi casi una varianza eccessiva può arrivare a influenzare i criteri decisionali. La soluzione tradizionale consiste nel prevedere i fattori con elevata propensione alla varianza e soddisfare le relative esigenze tramite un servizio di concierge, campagne di marketing o tramite l'applicazione di dispense speciali. Questi approcci possono variare da settore a settore. Possono variare persino da un determinato gruppo di clienti a un altro all'interno di un settore (ad esempio quello delle telecomunicazioni).

Il fattore comune è che le aziende hanno necessità di ridurre al minimo questi sforzi speciali rivolti alla fidelizzazione. Pertanto, una metodologia consisterebbe nell'assegnazione di un punteggio a ogni cliente con probabilità di varianza per poi gestire i primi N classificati. I clienti principali potrebbero essere anche i più remunerativi. Ad esempio, in scenari più sofisticati viene impiegata una funzione di redditività durante la selezione dei candidati per una dispensa speciale. Tuttavia, queste considerazioni rappresentano solo una parte della strategia completa alla base della gestione della varianza. Le aziende devono inoltre tenere in considerazione il rischio (e la tolleranza al rischio associata), il livello e i costi dell'intervento e una segmentazione dei clienti plausibile.  

## <a name="industry-outlook-and-approaches"></a>Prospettive e approcci di settore
La gestione sofisticata della varianza è un indicatore di maturità di un settore. L'esempio classico è dato dal settore delle telecomunicazioni, nel quale gli abbonati sono soliti passare frequentemente da un provider a un altro. Questa varianza volontaria rappresenta un motivo principale di preoccupazione. I provider, inoltre, hanno accumulato notevoli conoscenze riguardo agli *elementi che inducono varianza*ovvero i fattori che spingono i clienti a cambiare.

La scelta dell'apparecchio o del dispositivo, ad esempio, è un fattore di varianza ben noto nel settore della telefonia mobile. Come risultato, un criterio diffuso è la sovvenzione promozionale di una parte del costo di un telefono per i nuovi abbonati e l'addebito del prezzo intero per i clienti esistenti per un cambio di modello. Storicamente, tale criterio ha portato i clienti a passare da un provider all'altro per ottenere un nuovo sconto. Questo, a sua volta, ha indotto i provider ad affinare le proprie strategie.

L'elevata volatilità nelle offerte legate ai dispositivi è un fattore in grado di invalidare velocemente i modelli di varianza che si basano sui modelli di dispositivo attuali. Inoltre, i telefoni cellulari non sono solo dispositivi di telecomunicazione, sono anche oggetti alla moda (si pensi all'iPhone). Queste variabili predittive sociali esulano dall'ambito dei set di dati delle normali telecomunicazioni.

Il risultato netto per l'uso dei modelli è l'impossibilità di implementare un criterio efficace solamente eliminando i motivi noti per la varianza. Infatti, una strategia di modellazione continua, che include modelli classici che quantificano le variabili di categoria (ad esempio alberi decisionali) è **obbligatoria**.

Usando set di Big Data sui clienti, le organizzazioni eseguono l'analisi di tali Big Data, sfruttandoli in modo particolare per quanto riguarda il rilevamento della varianza e individuando in questo metodo un efficace approccio al problema. Nella sezione Consigli su ETL, sono disponibili ulteriori informazioni sull'approccio dei Big Data al problema della varianza.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia per generare un modello di varianza dei clienti
Un processo comune di risoluzione dei problemi legati alla varianza dei clienti è illustrato nelle figure 1-3:  

1. Un modello di rischio consente di considerare il modo in cui le azioni influiscono su probabilità e rischio.
2. Un modello di intervento permette di valutare il modo in cui il livello di intervento può influire sulle probabilità di varianza e su indicatori di marketing come il Lifetime Value del cliente (CLV).
3. Questa stessa analisi contribuisce a un'analisi qualitativa che viene poi inserita in una campagna di marketing proattiva rivolta ai segmenti di clientela a cui recapitare l'offerta ottimizzata.  

![Diagramma che mostra come la tolleranza al rischio e i modelli decisionali possono produrre informazioni dettagliate interattive](./media/azure-ml-customer-churn-scenario/churn-1.png)

Si tratta di un approccio rivolto al futuro è il modo migliore per trattare la varianza, ma risulta essere molto complesso: è necessario sviluppare un archetipo multi modello e tracciare le dipendenze tra i modelli. L'interazione tra modelli può essere incapsulata come illustrato nel diagramma seguente:  

![Diagramma di interazione tra modelli di varianza](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Figura 4: archetipo multi modello unificato*  

L'interazione tra modelli è fondamentale per lo sviluppo di un approccio olistico alla fidelizzazione del cliente. Ogni modello subisce inevitabilmente un degrado con il passare degli anni, pertanto, l'architettura è un ciclo implicito (simile all'archetipo impostato dallo standard di data mining CRISP-DM [***3***]).  

Il ciclo generale di segmentazione/scomposizione di rischio-decisione-marketing è ancora una struttura generalizzata, applicabile a molti problemi di business. L'analisi della varianza è semplicemente un elemento altamente rappresentativo di questo gruppo di problemi, poiché presenta tutti i tratti di una complessa problematica di business che non consente una soluzione predittiva semplificata. Gli aspetti sociali dell'approccio moderno alla varianza non sono particolarmente evidenziati nell'approccio, ma gli aspetti sociali sono incapsulati nell'archetipo di modellazione, come lo sarebbero in qualsiasi modello.  

Un'aggiunta interessante in questo ambito è l'analisi dei Big Data. Negli attuali settori commerciali delle telecomunicazioni e della rivendita al dettaglio vengono raccolti dati esaurienti sui clienti e risulta immediatamente evidente come la necessità di connettività multi modello diverrà un trend comune, date le tendenze emergenti quali "Internet delle cose" e l'universalità dei dispositivi. Ciò che consentirà alle aziende di impiegare soluzioni intelligenti e organizzate su più livelli.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Implementazione dell'archetipo di modellazione in Machine Learning Studio (classico)Implementing the modeling archetype in Machine Learning Studio (classic)
Dato il problema descritto, qual è il modo migliore per implementare un sistema di modellazione e classificazione integrato? In questa sezione verrà illustrato come è stato eseguito l'operazione usando Azure Machine Learning Studio (classico).  

L'approccio multi modello è indispensabile quando si progetta un archetipo globale per la varianza. Anche la parte (predittiva) dell'approccio correlata al punteggio deve essere multi modello.  

Il diagramma seguente mostra il prototipo creato, che utilizza quattro algoritmi di punteggio in Machine Learning Studio (classico) per prevedere la varianza. Il motivo per usare un approccio multimodello non è solo il poter creare un classificatore basato su insiemi per aumentare l'accuratezza, ma anche la protezione da inserimenti superflui e la possibilità di migliorare la selezione futura.  

![Screenshot che illustra un'area di lavoro di Studio complessa (classica) con molti moduli interconnessi](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Figura 5: Prototipo di un approccio di modellazione della varianza*  

Le sezioni seguenti forniscono ulteriori dettagli sul modello di punteggio prototipo implementato tramite Machine Learning Studio (classico).  

### <a name="data-selection-and-preparation"></a>Selezione e preparazione dei dati
I dati usati per creare i modelli e classificare i clienti sono stati ottenuti da una soluzione verticale CRM, con i dati offuscati per proteggere la privacy dei clienti. I dati contengono informazioni su 8.000 sottoscrizioni negli Stati Uniti e unisce tre origini: provisioning dei dati (metadati di sottoscrizione), dati dell'attività (uso del sistema) e dati del supporto tecnico. I dati non includono informazioni commerciali sui clienti. Ad esempio, non sono inclusi metadati relativi alla fedeltà o punteggi di credito.  

Per semplicità, i processi ETL e di pulizia dei dati sono esclusi dall'ambito, in quanto si presuppone che la preparazione dei dati sia già stata completata altrove.

La selezione di funzionalità per la modellazione si basa su una classificazione preliminare di importanza del set di indicatori, inclusi nel processo che usa il modulo foresta casuale. Per l'implementazione in Machine Learning Studio (classico), abbiamo calcolato la media, la mediana e gli intervalli per le funzionalità rappresentative. Ad esempio, sono stati aggiunti aggregati per i dati qualitativi, ad esempio i valori minimi e massimi per l'attività degli utenti.

Sono state inoltre acquisite informazioni temporali per gli ultimi sei mesi. Sono stati analizzati i dati per un anno e si è stabilito che anche se vi sono le tendenze statisticamente significative, l'effetto sulla varianza si riduce notevolmente dopo sei mesi.  

The most important point is that the entire process, including ETL, feature selection, and modeling was implemented in Machine Learning Studio (classic), using data sources in Microsoft Azure.   

Nei diagrammi che seguono sono illustrati i dati usati.  

![Screenshot che mostra un esempio dei dati usati con valori non elaborati](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Figura 6: Estratto dell'origine dati (offuscati)*  

![Screenshot che mostra funzionalità statistiche estratte dall'origine dati](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Figure 7: Funzionalità estratte dall'origine dati*
 

> Questi dati sono privati e quindi il modello e i dati non possono essere condivisi.
> Tuttavia, per un modello simile che usa dati disponibili pubblicamente, vedere questo esperimento di esempio nella [Raccolta di intelligenza artificiale per Azure](https://gallery.azure.ai/): [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383) (Varianza del cliente in ambito di telecomunicazioni).
> 
> Per altre informazioni su come è possibile implementare un modello di analisi della varianza usando Cortana Intelligence Suite, si consiglia anche [questo video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) del Senior Program Manager Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmi usati nel prototipo
Per la realizzazione del prototipo sono stati impiegati i seguenti quattro algoritmi di Machine Learning (senza personalizzazione):  

1. Regressione logistica (LR)
2. Albero delle decisioni incrementato (BT)
3. Percezione media (AP)
4. Macchina a vettori di supporto (SVM)  

Nel diagramma seguente viene illustrata una porzione dell'area di progettazione dell'esperimento, che indica la sequenza secondo cui sono stati creati i modelli:  

![Screenshot di una piccola sezione dell'area di disegno dell'esperimento di Studio](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Figura 8: Creazione di modelli in Machine Learning Studio (classico)Figura 8: Creazione di modelli in Machine Learning Studio (classico)Figure 8: Creating models in Machine Learning Studio (*  

### <a name="scoring-methods"></a>Metodi di assegnazione delle valutazioni
I quattro modelli sono stati classificati usando un set di dati di training etichettati.  

Inoltre, il set di dati di valutazione è stato inviato a un modello analogo compilato utilizzando la versione desktop di SAS Enterprise Miner 12. Abbiamo misurato l'accuratezza del modello SAS e di tutti e quattro i modelli di Machine Learning Studio (classici).  

## <a name="results"></a>Risultati
In questa sezione vengono presentati i risultati relativi all'accuratezza dei modelli in base al set di dati di punteggio.  

### <a name="accuracy-and-precision-of-scoring"></a>Accuratezza e precisione dei valori
In genere, l'implementazione in Azure Machine Learning Studio (classico) è dietro SAS in precisione di circa 10-15% (Area sotto curva o AUC).  

La metrica più importante in ambito di varianza è tuttavia il tasso di errata classificazione, ovvero, tra i primi X candidati alla varianza, secondo le previsioni del classificatore, quali di essi **non** sono variati, ricevendo tuttavia il trattamento speciale? Nel diagramma seguente vengono confrontati tali tassi di errata classificazione per tutti i modelli:  

![Grafico dell'area sottesa dalla curva che confronta le prestazioni di 4 algoritmi](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Figure 9: Area sottesa dalla curva del prototipo Passau*

### <a name="using-auc-to-compare-results"></a>Uso di AUC per il confronto dei risultati
L'area sottesa dalla curva (AUC, Area Under Curve) è una metrica che rappresenta una misura globale di *separabilità* tra le distribuzioni di punteggi per popolazioni positive e negative. È simile al grafico ROC (Receiver Operator Characteristic) tradizionale, ma un'importante differenza è rappresentata dal fatto che la metrica AUC non richiede un valore soglia. ma fornisce invece un riepilogo dei risultati di **tutte** le scelte possibili. Al contrario, il grafico ROC tradizionale mostra il tasso positivo sull'asse verticale e il tasso di falsi positivi su quello orizzontale, con conseguente variazione della soglia di classificazione.   

La metrica AUC viene usata come misura di valore per diversi algoritmi (o diversi sistemi) perché consente il confronto dei modelli mediante i relativi valori AUC. Si tratta di un approccio comune in settori come la meteorologia e la bioscienza. AUC rappresenta quindi uno strumento popolare per la gestione delle prestazioni dei classificatori.  

### <a name="comparing-misclassification-rates"></a>Confronto dei tassi di classificazione non corretta
I tassi di errata classificazione nel set di dati in questione sono stati confrontati usando i dati CRM di circa 8.000 sottoscrizioni.  

* Il tasso di errata classificazione SAS è risultato essere il 10-15%.
* Il tasso di errata classificazione di Machine Learning Studio (classico) era del 15-20% per i primi 200-300 churner.  

Nel settore delle telecomunicazioni è importante rivolgersi solamente a quei clienti che presentano il maggior rischio di varianza, offrendo loro un servizio concierge o un altro tipo di trattamento speciale. A questo proposito, l'implementazione di Machine Learning Studio (classica) ottiene risultati alla pari con il modello SAS.  

Analogamente, l'accuratezza è più importante della precisione perché ciò che interessa maggiormente è una corretta classificazione dei soggetti inclini alla varianza.  

Il seguente diagramma disponibile su Wikipedia illustra la relazione in un grafico brillante e di facile comprensione:  

![Due bersagli. Un bersaglio mostra i segni raggruppati in modo impreciso ma vicino al mirino contrassegnato con "Scarsa accuratezza: buona esattezza, bassa precisione". L'altro bersaglio mostra i segni raggruppati vicini tra loro ma lontani dal mirino contrassegnato con "Scarsa accuratezza: buona esattezza, buona precisione".](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Figura 10: Compromesso tra accuratezza e precisione*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Risultati di accuratezza e precisione per il modello di albero decisionale incrementato
Il grafico seguente illustra i risultati non elaborati della valutazione usando il prototipo di Machine Learning per il modello di albero decisionale incrementato, che risulta essere il più accurato dei quattro modelli:  

![Frammento di tabella con le colonne Accuracy, Precision, Recall, F-Score, AUC, Average Log Loss e Training Log Loss per quattro algoritmi](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Figure 11: modello di albero delle decisioni con boosting*

## <a name="performance-comparison"></a>Confronto delle prestazioni
Abbiamo confrontato la velocità con cui i dati sono stati valutati utilizzando i modelli di Machine Learning Studio (classici) e un modello comparabile creato utilizzando l'edizione desktop di SAS Enterprise Miner 12.1.  

La tabella seguente riepiloga le prestazioni degli algoritmi:  

*Tabella 1. Prestazioni generali (accuratezza) degli algoritmi*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modello medio |Modello migliore |Prestazioni scarse |Modello medio |

I modelli ospitati in Machine Learning Studio (classico) hanno sovraperformato SAS del 15-25% per la velocità di esecuzione, ma la precisione era in gran parte alla pari.  

## <a name="discussion-and-recommendations"></a>Discussione e raccomandazioni
Nel settore delle telecomunicazioni sono state sviluppate numerose pratiche per l'analisi della varianza, tra cui:  

* Derivare metriche per quattro categorie fondamentali:
  * **Entità (ad esempio, una sottoscrizione)**. Fornire informazioni di base sulle sottoscrizioni e/o sui clienti soggetti a varianza.
  * **Attività**. Ottenere tutte le informazioni di utilizzo possibili correlate all'entità, ad esempio, il numero di account di accesso.
  * **Assistenza clienti**. Raccogliere informazioni dai log del supporto tecnico per indicare se l'abbonato ha avuto problemi o interazioni con il supporto tecnico.
  * **Dati commerciali e competitivi**. Ottenere qualunque informazione possibile in merito al cliente (ad esempio, se non è disponibile o difficile da monitorare).
* Usare l'importanza per alimentare la selezione delle funzionalità. Ciò implica che il modello di albero decisionale incrementato è sempre un approccio promettente.  

L'uso di queste quattro categorie crea l'illusione che un semplice approccio *deterministico* , basato su indici formati su fattori ragionevoli per categoria, sia sufficiente per identificare i clienti a rischio di varianza. Sfortunatamente, per quanto questa nozione appaia plausibile, si tratta di un intendimento errato. Il motivo è dato dal fatto che la varianza è un effetto temporale e i fattori che contribuiscono ad essa si trovano in genere in uno stato temporaneo. Ciò che induce un cliente a valutare un cambio di gestore oggi, potrebbe essere diverso domani e sicuramente sarà ancora diverso tra sei mesi. Pertanto, un modello *probabilistico* è una necessità.  

Questa importante osservazione è spesso sottovalutata dalle aziende, dove in genere si preferisce un approccio di tipo business intelligence, principalmente perché è una soluzione più semplice e ammette un'automazione semplificata.  

Tuttavia, la promessa di analisi self-service utilizzando Machine Learning Studio (classico) è che le quattro categorie di informazioni, classificate per divisione o reparto, diventano una fonte preziosa per l'apprendimento automatico sulla varianza.  

Un'altra funzionalità interessante disponibile in Azure Machine Learning Studio (classico) è la possibilità di aggiungere un modulo personalizzato al repository di moduli predefiniti già disponibili. Essenzialmente, tale capacità consente di creare un'opportunità per selezionare raccolte e creare modelli per mercati verticali. Si tratta di un importante elemento di differenziazione di Azure Machine Learning Studio (classico) sul mercato.  

Si prevede di continuare a trattare questo argomento in futuro, specialmente per quanto riguarda l'analisi dei Big Data.
  

## <a name="conclusion"></a>Conclusioni
In questo documento viene descritto un approccio intelligente alla gestione di un problema comune, vale a dire la varianza dei clienti, usando un framework generico. È stato considerato un prototipo per il punteggio dei modelli e lo abbiamo implementato usando Azure Machine Learning Studio (classico). Infine, sono state valutate l'accuratezza e le prestazioni della soluzione prototipo rispetto ad algoritmi paragonabili in SAS.  

 

## <a name="references"></a>Riferimenti
[1] Analitica predittiva: Beyond the Predictions, W. McKnight, Information Management, luglio/agosto 2011, p.18-20.  

[2] Articolo di Wikipedia: [Precisione e precisione](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Guida dettagliata sul data mining](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: coinvolgere i clienti e valorizzare i prodotti in modo più efficace](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Modello di varianza delle telecomunicazioni](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) nella [Raccolta di intelligenza artificiale per Azure](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Appendice
![Snapshot di una presentazione sul prototipo di varianza](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Figura 12: Snapshot di una presentazione sul prototipo di varianza*
