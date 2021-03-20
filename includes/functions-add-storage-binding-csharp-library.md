---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78190917"
---
In un progetto di libreria di classi C# i binding vengono definiti come attributi di binding nel metodo della funzione. Il *function.jssu* file richiesto dalle funzioni viene quindi generato automaticamente in base a questi attributi.

Aprire il file di progetto *HttpExample. cs* e aggiungere il parametro seguente alla `Run` definizione del metodo:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Il parametro `msg` è un tipo `ICollector<T>`, che rappresenta una raccolta di messaggi scritti in un binding di output al completamento della funzione. In questo caso, l'output è una coda di archiviazione denominata `outqueue`. La stringa di connessione per l'account di archiviazione è impostata da `StorageAccountAttribute`. Questo attributo indica l'impostazione che contiene la stringa di connessione dell'account di archiviazione e può essere applicato a livello di classe, metodo o parametro. In questo caso, è possibile omettere `StorageAccountAttribute`, perché si usa già l'account di archiviazione predefinito.

La definizione del metodo Run dovrà ora essere come indicato di seguito:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
