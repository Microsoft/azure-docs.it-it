---
title: 'ML Studio (classico): parametri del servizio Web-Azure'
description: Come usare i parametri del servizio Web di Azure Machine Learning per modificare il comportamento del modello quando si accede al servizio Web.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio-classic
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: 49814d01209b58666c011a6bbd7fe6c328d460f8
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518335"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Usare i parametri del servizio Web Azure Machine Learning Studio (classico)

**SI APPLICA A:**  ![Si applica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica) ![Non si applica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Un servizio Web di Azure Machine Learning viene creato mediante la pubblicazione di un esperimento contenente moduli con parametri configurabili. In alcuni casi può essere utile modificare il comportamento del modulo mentre è in esecuzione il servizio Web. I *parametri del servizio Web* consentono di eseguire questa operazione. 

Un esempio comune è la configurazione del modulo [Import Data][reader] per consentire all’utente del servizio Web pubblicato di specificare un'origine dati diversa quando si accede al servizio Web oppure la configurazione del modulo [Export Data][writer] in modo che sia possibile specificare una destinazione differente. Altri esempi includono la modifica del numero di bit per il modulo [Feature Hashing][feature-hashing] o il numero di funzionalità desiderate per il modulo [Filter-Based Feature Selection][filter-based-feature-selection]. 

È possibile impostare i parametri del servizio Web e associarli a uno o più parametri di modulo nell’esperimento, e specificare se sono obbligatori o facoltativi. L'utente del servizio web può quindi fornire valori per questi parametri quando si chiama il servizio web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Come impostare e usare i parametri del servizio Web
È possibile definire un parametro del servizio Web facendo clic sull'icona accanto al parametro di un modulo e selezionando "Set as web service parameter". Verrà creato un nuovo parametro del servizio Web che verrà connesso al parametro del modulo. Quando si accede al servizio Web, l'utente può quindi specificare un valore per il parametro del servizio Web che viene applicato al parametro del modulo.

Dopo aver definito un parametro del servizio Web, questo parametro sarà disponibile per qualsiasi altro parametro del modulo nell’esperimento. Se si definisce un parametro del servizio Web associato a un parametro per un modulo, è possibile usare lo stesso parametro per qualsiasi altro modulo, purché il parametro preveda lo stesso tipo di valore. Se ad esempio il parametro del servizio Web è un valore numerico, è possibile usarlo solo per i parametri del modulo che prevedono un valore numerico. Quando l'utente imposta un valore per il parametro del servizio Web, tale valore verrà applicato a tutti i parametri del modulo associati.

È possibile decidere se specificare un valore predefinito per il parametro del servizio Web. In questo caso per l'utente del servizio Web il parametro sarà facoltativo. Se si non specifica un valore predefinito, è necessario immettere un valore quando si accede al servizio Web.

La documentazione dell'API per il servizio Web include informazioni per l'utente del servizio Web su come specificare il parametro del servizio Web a livello di codice quando accede al servizio.

> [!NOTE]
> La documentazione dell'API per un servizio Web classico viene fornita tramite il collegamento alla **pagina della Guida dell'API** nel **Dashboard** del servizio Web in Machine Learning Studio (classico). La documentazione dell'API per un nuovo servizio Web è disponibile tramite il portale dei [servizi Web di Azure Machine Learning](https://services.azureml.net/Quickstart) o nelle pagine **Consume** (Uso) e **Swagger API** (API Swagger) per il servizio Web.
> 
> 

## <a name="example"></a>Esempio
Si supponga ad esempio di disporre di un esperimento con un modulo [Export Data][writer] che invia informazioni all'archiviazione BLOB di Azure. Verrà definito un parametro del servizio Web denominato "percorso BLOB" che consente all'utente del servizio Web di modificare il percorso dell'archiviazione BLOB quando si accede al servizio.

1. In Machine Learning Studio (versione classica) fare clic sul modulo [Export Data (Esporta dati][writer] ) per selezionarlo. Le relative proprietà verranno visualizzate nel riquadro delle proprietà a destra dell'area di disegno dell'esperimento.
2. Specificare il tipo di archiviazione:
   
   * In **Please specify data destination**(Specificare la destinazione dei dati) selezionare "Azure Blob Storage" (Archivio BLOB di Azure).
   * In **Please specify authentication type** selezionare "Account".
   * Immettere le informazioni dell'account per l'archiviazione BLOB di Azure. 

3.  Fare clic sull'icona a destra di **Path to blob beginning with container parameter** (Percorso BLOB che inizia con parametro contenitore). Ha questo aspetto:
   
   ![Icona del parametro del servizio Web](./media/web-service-parameters/icon.png)
   
   Selezionare "Set as web service parameter".
   
   Verrà aggiunta una voce in **Web Service Parameters** nella parte inferiore del riquadro Proprietà con il nome "Path to blob beginning with container". Questo sarà il parametro del servizio Web associato al parametro del modulo [Export Data][writer].
4. Per rinominare il parametro del servizio Web, fare clic sul nome, digitare "Blob path" e quindi premere **INVIO** . 
5. Per specificare un valore predefinito per il parametro del servizio Web, fare clic sull'icona a destra del nome, selezionare "Provide default value", immettere un valore (ad esempio, "container1/output1.csv") e quindi premere **INVIO** .
   
   ![Parametro del servizio Web](./media/web-service-parameters/parameter.png)
6. Fare clic su **Esegui**. 
7. Fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]) per distribuire il servizio Web.

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [gestire un servizio Web usando il portale dei servizi web Azure Machine Learning](manage-new-webservice.md). 

È ora possibile specificare una nuova destinazione per il modulo [Export Data][writer] quando si accede al servizio Web.

## <a name="more-information"></a>Ulteriori informazioni
Per un esempio più dettagliato, vedere la voce relativa ai [parametri del servizio Web](/archive/blogs/machinelearning/azureml-web-service-parameters) nel [blog di Machine Learning](/archive/blogs/machinelearning/azureml-web-service-parameters).

Per altre informazioni sull'accesso a un servizio Web di Machine Learning, vedere [Come usare un servizio Web di Azure Machine Learning ](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[reader]: /azure/machine-learning/studio-module-reference/import-data
[writer]: /azure/machine-learning/studio-module-reference/export-data