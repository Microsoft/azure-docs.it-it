---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673448"
---
In un progetto Java i binding vengono definiti come annotazioni di binding nel metodo della funzione. Il file *function.json* viene quindi generato automaticamente in base a queste annotazioni.

Passare al percorso del codice della funzione, _src/main/java_, aprire il file di progetto *Function.java* e aggiungere il parametro seguente alla definizione del metodo `run`:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Il parametro `msg` è un tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), che rappresenta una raccolta di stringhe scritte come messaggi in un binding di output al completamento della funzione. In questo caso, l'output è una coda di archiviazione denominata `outqueue`. La stringa di connessione per l'account di archiviazione è impostata dal metodo `connection`. Invece della stringa di connessione stessa, passare l'impostazione applicazione che contiene la stringa di connessione dell'account di archiviazione.

La definizione del metodo `run` dovrà ora essere come indicato nell'esempio seguente:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::