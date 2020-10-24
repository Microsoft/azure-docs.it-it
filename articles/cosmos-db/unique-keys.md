---
title: Usare chiavi univoche in Azure Cosmos DB
description: Informazioni su come definire e usare chiavi univoche per un database di Azure Cosmos. Questo articolo descrive anche il modo in cui le chiavi univoche aggiungono un livello di integrità dei dati.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: dac2ed3888dcf1d38fc5e2c21611edf8008a07f6
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476366"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Vincoli di chiave univoca in Azure Cosmos DB

Le chiavi univoche aggiungono un livello di integrità dei dati a un contenitore di Azure Cosmos. I criteri di chiave univoca si creano al momento della creazione di un contenitore di Azure Cosmos. Con le chiavi univoche si garantisce l'univocità di uno o più valori all'interno di una partizione logica. Si può anche garantire l'univocità per [chiave di partizione](partitioning-overview.md).

Dopo aver creato un contenitore con criteri di chiave univoca, viene impedita la creazione di un nuovo o di un aggiornamento di un elemento esistente risultante in un duplicato all'interno di una partizione logica, come specificato dal vincolo unique key. La chiave di partizione combinata con la chiave univoca garantisce l'univocità di un elemento all'interno dell'ambito del contenitore.

Si consideri ad esempio un contenitore di Azure Cosmos con l'indirizzo di posta elettronica come vincolo di chiave univoca e `CompanyID` come chiave di partizione. Quando si configura l'indirizzo di posta elettronica dell'utente con una chiave univoca, ogni elemento ha un indirizzo di posta elettronica univoco all'interno di un determinato `CompanyID`. Non è possibile creare due elementi con indirizzi di posta elettronica duplicati e con lo stesso valore di chiave di partizione. Nell'API SQL (Core) di Azure Cosmos DB, gli elementi vengono archiviati come valori JSON. Questi valori JSON fanno distinzione tra maiuscole e minuscole. Quando si sceglie una proprietà come chiave univoca, è possibile inserire valori con distinzione tra maiuscole e minuscole per la proprietà. Se, ad esempio, è stata definita una chiave univoca nella proprietà Name, "Gaby" è diverso da "Gaby" ed è possibile inserirli entrambi nel contenitore.

Per creare elementi con lo stesso indirizzo di posta elettronica, ma non con la stessa combinazione di nome, cognome e indirizzo di posta elettronica, aggiungere altri percorsi ai criteri di chiave univoca. Anziché creare una chiave univoca in base all'indirizzo di posta elettronica, è anche possibile creare una chiave univoca con una combinazione del nome, del cognome e dell'indirizzo di posta elettronica. Questa chiave è detta chiave univoca composta. In questo caso è consentita ogni combinazione univoca dei tre valori all'interno di un determinato elemento `CompanyID`. 

Il contenitore può ad esempio contenere elementi con i valori seguenti, in cui ogni elemento rispetta il vincolo di chiave univoca.

|CompanyID|Nome|Cognome|Indirizzo di posta elettronica|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrikam|   |Duperre|gaby@fabraikam.com|
|Fabrikam|   |   |gaby@fabraikam.com|

Se si cerca di inserire un altro elemento con le combinazioni elencate nella tabella riportata sopra, viene visualizzato un messaggio di errore che indica che il vincolo di chiave univoca non è stato rispettato. Si riceve `Resource with specified ID or name already exists` o `Resource with specified ID, name, or unique index already exists` come messaggio restituito. 

## <a name="define-a-unique-key"></a>Definire una chiave univoca

È possibile definire chiavi univoche solo quando si crea un contenitore di Azure Cosmos. Una chiave univoca ha come ambito una partizione logica. Nell'esempio precedente, se il contenitore viene partizionato in base al CAP, risulteranno elementi duplicati in ogni partizione logica. Durante la creazione di chiavi univoche, prendere in considerazione le proprietà seguenti:

* Non è possibile aggiornare un contenitore esistente in modo che usi una chiave univoca diversa. In altre parole, una volta creato un contenitore con criteri di chiave univoca, non è possibile modificarli.

* Per impostare una chiave univoca per un contenitore esistente, creare un nuovo contenitore con il vincolo di chiave univoca. Usare lo strumento di migrazione dei dati appropriato per spostare i dati dal contenitore esistente a quello nuovo. Per i contenitori SQL usare l'[Utilità di migrazione dati](import-data.md) per spostare i dati. Per i contenitori MongoDB usare [mongoimport.exe o mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json) per spostare i dati.

* I criteri di chiave univoca possono contenere un massimo di 16 valori di percorso. Ad esempio, i valori possono essere `/firstName` , `/lastName` e `/address/zipCode` . I singoli criteri di chiave univoca possono avere un massimo di 10 vincoli o combinazioni di vincoli di chiave univoca. I percorsi combinati di ogni vincolo di chiave univoca non devono superare i 60 byte. Nell'esempio precedente, nome, cognome e indirizzo di posta elettronica rappresentano un unico vincolo, che usa 3 dei 16 possibili percorsi.

* Quando un contenitore dispone di un criterio di chiave univoca, gli addebiti delle [unità richiesta](request-units.md) per creare, aggiornare ed eliminare un elemento sono leggermente più elevati.

* Le chiavi univoche di tipo sparse non sono supportate. Se non sono definiti alcuni valori di percorso univoci, questi vengono considerati come valori Null facenti parte del vincolo di univocità. Per questo motivo può essere presente un solo elemento con valore Null per soddisfare il vincolo.

* I nomi delle chiavi univoche distinguono tra maiuscole e minuscole. Si consideri, ad esempio, un contenitore con il vincolo di chiave univoca impostato su `/address/zipcode` . Se i dati hanno un campo denominato `ZipCode` , Azure Cosmos DB inserisce "null" come chiave univoca perché `zipcode` non è uguale a `ZipCode` . A causa di questa distinzione tra maiuscole e minuscole, tutti gli altri record con ZipCode non possono essere inseriti, in quanto il valore "null" duplicato viola il vincolo di chiave univoca.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [partizioni logiche](partitioning-overview.md)
* Scopri [come definire chiavi univoche](how-to-define-unique-keys.md) durante la creazione di un contenitore