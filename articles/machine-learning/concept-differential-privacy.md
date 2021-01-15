---
title: Implementare la privacy differenziale con il pacchetto SmartNoise (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni sulla privacy differenziale e su come il pacchetto SmartNoise può essere utile per implementare sistemi privati differenziali che proteggono la privacy dei dati.
author: luisquintanilla
ms.author: luquinta
ms.date: 12/21/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: responsible-ml
ms.openlocfilehash: 22ba505a2e13b2f88f212f2fe1b85d07f79f77e5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218961"
---
# <a name="preserve-data-privacy-by-using-differential-privacy-and-the-smartnoise-package-preview"></a>Mantenere la privacy dei dati tramite la privacy differenziale e il pacchetto SmartNoise (anteprima)

Informazioni sulla privacy differenziale e sul modo in cui il pacchetto SmartNoise può essere utile per implementare sistemi privati differenziali.

Con l'aumentare della quantità di dati raccolti e usati dalle organizzazioni per le attività di analisi, crescono anche le preoccupazioni relative alla privacy e alla sicurezza. Le attività di analisi richiedono dati. In genere, quanto maggiore è il numero di dati usati per il training dei modelli, tanto più accurate sono le analisi. Se per le attività di analisi vengono usate informazioni personali, è particolarmente importante che i dati rimangano privati per tutto il periodo di utilizzo.

## <a name="how-differential-privacy-works"></a>Come funziona la privacy differenziale

La privacy differenziale è un set di sistemi e procedure che consentono di preservare la sicurezza e la riservatezza dei dati personali.

> [!div class="mx-imgBorder"]
> ![Processo di privacy differenziale](./media/concept-differential-privacy/differential-privacy-process.jpg)

Negli scenari tradizionali i dati non elaborati vengono archiviati in file e database. Quando gli utenti analizzano i dati, in genere usano dati non elaborati. Questo costituisce un problema, perché può comportare la violazione della privacy personale. La privacy differenziale tenta di risolvere questo problema aggiungendo "rumore" o casualità ai dati in modo che i singoli punti dati non risultino identificabili. Se non altro, questo sistema offre una negazione plausibile.

Nei sistemi di privacy differenziale i dati vengono condivisi tramite richieste denominate **query**. Quando un utente invia una query per i dati, le operazioni note come **meccanismi di privacy** aggiungono rumore ai dati richiesti. I meccanismi di privacy restituiscono un'*approssimazione dei dati* in alternativa ai dati non elaborati. Questo risultato che garantisce la riservatezza viene visualizzato in un **report**. I report sono costituiti da due parti, i dati effettivi elaborati e una descrizione della modalità di creazione dei dati.

## <a name="differential-privacy-metrics"></a>Metriche della privacy differenziale

La privacy differenziale tenta di evitare la possibilità che un utente possa produrre un numero indefinito di report per rivelare i dati sensibili. Un valore noto come **epsilon** misura il grado di rumore o riservatezza di un report. Il valore epsilon presenta una relazione inversa con il rumore o la privacy. Quanto più basso è il valore epsilon, tanto più rumorosi (e privati) sono i dati.

I valori epsilon non sono negativi. I valori inferiori a 1 forniscono una negazione plausibile completa. Qualsiasi valore superiore a 1 presenta un rischio maggiore di esposizione dei dati effettivi. Quando si implementano sistemi di privacy differenziale, si ha come obiettivo di generare report con valori epsilon compresi tra 0 e 1.

Un altro valore direttamente correlato a epsilon è **delta**. Delta è una misura della probabilità che un report non sia completamente privato. Il valore delta è direttamente proporzionale a epsilon. Poiché questi valori sono correlati, epsilon è il valore usato con maggiore frequenza.

## <a name="privacy-budget"></a>Budget per la privacy

Per garantire la privacy nei sistemi in cui sono consentite più query, la privacy differenziale definisce un limite di frequenza. Questo limite è noto come **budget per la privacy**. Ai budget per la privacy viene allocata una quantità epsilon, in genere compresa tra 1 e 3, per limitare il rischio di reidentificazione. Quando vengono generati report, i budget per la privacy tengono traccia del valore epsilon dei singoli report, nonché dell'aggregazione di tutti i report. Quando un budget per la privacy risulta speso o esaurito, gli utenti non possono più accedere ai dati.  

## <a name="reliability-of-data"></a>Affidabilità dei dati

Anche se la tutela della privacy deve essere l'obiettivo prioritario, esiste un compromesso per quanto riguarda l'usabilità e l'affidabilità dei dati. Nell'analisi dei dati, l'accuratezza può essere considerata come una misura dell'incertezza introdotta dagli errori di campionamento. Questa incertezza tende a rientrare in determinati limiti. Dal punto di vista della privacy differenziale, l'**accuratezza** misura invece l'affidabilità dei dati, su cui influisce l'incertezza introdotta dai meccanismi di privacy. In breve, un livello di rumore o privacy più elevato si traduce in dati con un valore più basso di epsilon, accuratezza e affidabilità. Anche se presentano un maggior grado di riservatezza, i dati hanno minore probabilità di essere usati perché non sono affidabili.

## <a name="implementing-differentially-private-systems"></a>Implementazione di sistemi di privacy differenziale

L'implementazione di sistemi di privacy differenziale presenta alcune difficoltà. SmartNoise è un progetto open source che contiene componenti diversi per la creazione di sistemi privati globali differenziali. SmartNoise è costituito dai componenti di primo livello seguenti:

- Core
- SDK

### <a name="core"></a>Core

La libreria Core include i meccanismi di privacy seguenti per l'implementazione di un sistema di privacy differenziale:

|Componente  |Descrizione  |
|---------|---------|
|Analisi     | Illustrazione grafica di calcoli arbitrari. |
|Validator     | Libreria Rust che contiene un set di strumenti per la verifica e la derivazione delle condizioni necessarie affinché un'analisi risponda ai criteri di privacy differenziale.          |
|Runtime     | Mezzo per eseguire l'analisi. Il runtime di riferimento è scritto in Rust, ma i runtime possono essere scritti usando qualsiasi framework di calcolo, ad esempio SQL e Spark, a seconda delle caratteristiche dei dati.        |
|Associazioni     | Associazioni di linguaggio e librerie di supporto per la creazione di analisi. Attualmente SmartNoise fornisce associazioni Python. |

### <a name="sdk"></a>SDK

Nella libreria System sono disponibili gli strumenti e i servizi seguenti per l'utilizzo di dati tabulari e relazionali:

|Componente  |Descrizione  |
|---------|---------|
|Accesso ai dati     | Libreria che intercetta ed elabora le query SQL e genera i report. Questa libreria è implementata in Python e supporta le origini dati ODBC e DBAPI seguenti:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Service     | Servizio di esecuzione che fornisce un endpoint REST per gestire le richieste o le query sulle origini dati condivise. Il servizio è stato progettato per consentire la composizione dei moduli di privacy differenziale che operano su richieste contenenti valori delta ed epsilon diversi, noti anche come richieste eterogenee. Questa implementazione di riferimento rappresenta un ulteriore impatto rispetto alle query sui dati correlati. |
|Evaluator     | Analizzatore stocastico che controlla le violazioni della privacy, l'accuratezza e la distorsione. Sono supportati i test seguenti: <ul><li>Test sulla privacy: determina se un report rispetta le condizioni di privacy differenziale.</li><li>Test sull'accuratezza: misura se l'affidabilità dei report rientra nei limiti minimo e massimo, dato un livello di confidenza del 95%.</li><li>Test sull'utilità: determina se i limiti di confidenza di un report sono sufficientemente vicini ai dati, pur assicurando il massimo livello di privacy.</li><li>Test sulla distorsione: misura la distribuzione dei report per le query ripetute per verificare che non siano sbilanciate.</li></ul> |

## <a name="next-steps"></a>Passaggi successivi

[Mantenere la privacy dei dati](how-to-differential-privacy.md) in Azure Machine Learning.

Per altre informazioni sui componenti di SmartNoise, vedere gli esempi di repository GitHub per [SmartNoise Core Package](https://github.com/opendifferentialprivacy/smartnoise-core), [SmartNoise SDK](https://github.com/opendifferentialprivacy/smartnoise-sdk)e [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-samples).