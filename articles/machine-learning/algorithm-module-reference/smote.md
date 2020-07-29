---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo PERCOSse in Azure Machine Learning per aumentare il numero di esempi a bassa incidenza in un set di dati usando il sovracampionamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477460"
---
# <a name="smote"></a>SMOTE

Questo articolo descrive come usare il modulo PERCOSse in Azure Machine Learning Designer (anteprima) per aumentare il numero di case sottorappresentati in un set di dati usato per Machine Learning. PERCOSse è un modo migliore per aumentare il numero di casi rari rispetto alla semplice duplicazione dei case esistenti.  

Il modulo PERCOSse viene connesso a un set di dati *sbilanciato*. Esistono molti motivi per cui un set di dati potrebbe essere sbilanciato. Ad esempio, la categoria di destinazione potrebbe essere rara nella popolazione oppure i dati potrebbero essere difficili da raccogliere. In genere, si usa PERCOSse quando la *classe* che si vuole analizzare è sottorappresentata. 
  
Il modulo restituisce un set di dati che contiene gli esempi originali. Restituisce anche un numero di campioni di minoranza sintetica, a seconda della percentuale specificata.  
  
## <a name="more-about-smote"></a>Altre informazioni su PERCOSse

La tecnica di sovracampionamento della minoranza sintetica è una tecnica statistica per aumentare il numero di case nel set di dati in modo bilanciato. Il modulo funziona generando nuove istanze da casi di minoranza esistenti forniti come input. Questa implementazione di COLPÌ non *modifica il* numero di case di maggioranza.

Le nuove istanze non sono solo copie di casi di minoranza esistenti. Al contrario, l'algoritmo accetta esempi dello *spazio delle funzionalità* per ogni classe di destinazione e i relativi adiacenti più vicini. L'algoritmo genera quindi nuovi esempi che combinano le funzionalità del case di destinazione con le funzionalità degli elementi adiacenti. Questo approccio aumenta le caratteristiche disponibili per ciascuna classe e rende i campioni più generali.
  
PERCOSsa accetta l'intero set di dati come input, ma aumenta la percentuale dei soli casi di minoranza. Si supponga, ad esempio, di disporre di un set di dati sbilanciato in cui solo l'1% dei case dispone del valore di destinazione A (classe di minoranza) e il valore B del 99% dei case. Per aumentare la percentuale di casi di minoranza al doppio della percentuale precedente, immettere **200** per la **percentuale di percosse** nelle proprietà del modulo.  
  
## <a name="examples"></a>Esempi  

È consigliabile provare a usare SMOTE con un set di dati di dimensioni inferiori per verificarne il funzionamento. Nell'esempio seguente viene usato il set di dati della donazione di sangue disponibile in Azure Machine Learning Designer.
  
Se si aggiunge il set di dati a una pipeline e si seleziona **Visualizza** nell'output del set di dati, è possibile osservare che le 748 righe o case del set di dati, 570 case (76%) sono della classe 0 e 178 case (24%) sono della classe 1. Sebbene questo risultato non sia terribilmente sbilanciato, la classe 1 rappresenta le persone che hanno donato sangue, quindi queste righe contengono lo *spazio di funzionalità* che si desidera modellare.
 
Per aumentare il numero di case, è possibile impostare il valore di **colpì la percentuale**utilizzando multipli di 100, come indicato di seguito:

||Classe 0|Classe 1|total|  
|-|-------------|-------------|-----------|  
|Set di dati originale<br /><br /> (equivalente alla **percentuale**  =  di percosse **0**)|570<br /><br /> 76%|178<br /><br /> 24|748|  
|Percentuale di percosse **SMOTE percentage**  =  **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|Percentuale di percosse **SMOTE percentage**  =  **200**|570<br /><br /> 52%|534<br /><br /> 48%|1.104|  
|Percentuale di percosse **SMOTE percentage**  =  **300**|570<br /><br /> 44%|712<br /><br /> 56%|1.282|  
  
> [!WARNING]
> L'aumento del numero di case con COLPÌ non garantisce la produzione di modelli più accurati. Provare a eseguire il pipelining con percentuali diverse, set di funzionalità diversi e numeri diversi di adiacenti più vicini per vedere in che modo l'aggiunta dei case influisce sul modello.  
  
## <a name="how-to-configure-smote"></a>Come configurare PERCOSse
  
1.  Aggiungere il modulo PERCOSd alla pipeline. È possibile trovare il modulo in **moduli di trasformazione dei dati**nella categoria **manipolazione** .

2. Connettere il set di dati che si desidera aumentare. Se si desidera specificare lo spazio di funzionalità per la compilazione dei nuovi case, utilizzando solo colonne specifiche o escludendo alcune, utilizzare il modulo [Select Columns in DataSet](select-columns-in-dataset.md) . È quindi possibile isolare le colonne che si desidera utilizzare prima di utilizzare PERCOSse.
  
    In caso contrario, la creazione di nuovi case tramite PERCOSse si basa su *tutte* le colonne fornite come input. Almeno una colonna delle colonne della funzionalità è numerica.
  
3.  Verificare che sia selezionata la colonna contenente l'etichetta o la classe di destinazione. PERCOSse accetta solo etichette binarie.
  
4.  Il modulo PERCOSd identifica automaticamente la classe minoritaria nella colonna Label, quindi ottiene tutti gli esempi per la classe di minoranza. Tutte le colonne non possono avere valori NaN.
  
5.  Nell'opzione **percentuale di percosse** immettere un numero intero che indichi la percentuale di destinazione di casi di minoranza nel set di dati di output. Ad esempio:  
  
    - Immettere **0**. Il modulo PERCOSse restituisce esattamente lo stesso set di dati fornito come input. Non aggiunge nuovi casi di minoranza. In questo set di dati la proporzione della classe non è stata modificata.  
  
    - Immettere **100**. Il modulo PERCOSso genera nuovi casi di minoranza. Aggiunge lo stesso numero di casi di minoranza presenti nel set di dati originale. Poiché COLPÌ non aumenta il numero di case di maggioranza, la percentuale di case di ogni classe è cambiata.  
  
    - Immettere **200**. Il modulo raddoppia la percentuale di casi di minoranza rispetto al set di dati originale. Questa operazione *non* comporta la presenza di un doppio numero di casi di minoranza come prima. La dimensione del set di dati viene invece aumentata in modo tale che il numero di case di maggioranza rimanga invariato. Il numero di casi di minoranza viene aumentato fino a quando non corrisponde al valore percentuale desiderato.  
  
    > [!NOTE]
    > Usare solo multipli di 100 per la percentuale di PERCOSse.

6.  Usare l'opzione **numero di adiacenti più vicini** per determinare la dimensione dello spazio di funzionalità usato dall'algoritmo colpì per la creazione di nuovi case. Un router adiacente più vicino è una riga di dati (caso) simile a un case di destinazione. La distanza tra i due casi viene misurata combinando i vettori di peso di tutte le funzioni.  
  
    + Aumentando il numero di adiacenti più vicini, si ottengono funzionalità da più case.
    + Mantenendo il numero degli adiacenti più vicini, è possibile utilizzare le funzionalità più simili a quelle dell'esempio originale.  
  
7. Immettere un valore nella casella **seed casuale** se si desidera ottenere gli stessi risultati rispetto alle esecuzioni della stessa pipeline con gli stessi dati. In caso contrario, il modulo genera un valore di inizializzazione casuale in base ai valori di clock del processore quando viene distribuita la pipeline. La generazione di un valore di inizializzazione casuale può causare risultati leggermente diversi rispetto alle esecuzioni.

8. Inviare la pipeline.  
  
   L'output del modulo è un set di dati contenente le righe originali più una serie di righe aggiunte con i casi di minoranza.  

## <a name="technical-notes"></a>Note tecniche

+ Quando si pubblica un modello che usa il modulo **percosse** , rimuovere **percosse** dalla pipeline predittiva prima che venga pubblicato come servizio Web. Il motivo è che gli PERCOSsi sono intesi per migliorare un modello durante il training, non per l'assegnazione dei punteggi. Se una pipeline predittiva pubblicata contiene il modulo PERCOSse, potrebbe essere presente un errore.

+ È spesso possibile ottenere risultati migliori se si puliscono i valori mancanti o si applicano altre trasformazioni per correggere i dati prima di applicare PERCOSse. 

+ Alcuni ricercatori hanno verificato se il PERCOSso è efficace sui dati ad alta dimensionalità o di tipo sparse, ad esempio i dati usati nella classificazione di testo o nei set di dati di genomica. Questo documento contiene un riepilogo degli effetti e della validità teorica dell'applicazione di PERCOSse in questi casi: [Blagus e Lusa: percosse per i dati con squilibrio di classe altamente dimensionali](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Se il set di dati non è efficace, altri approcci che è possibile considerare includono:
  + Metodi per sovracampionare i casi di minoranza o sottocampionare la maggior parte dei casi.
  + Tecniche di insieme che consentono agli studenti di usare direttamente il clustering, l'insaccamento o il potenziamento adattivo.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

