---
title: Configurare Sincronizzazione file di Azure
description: Configurare Sincronizzazione file di Azure. Un blocco di testo comune, condiviso tra documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 64b99976a306c3c8423f5115c95a15158a3ddb51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043144"
---
Questo passaggio associa tutte le risorse e le cartelle configurate nell'istanza di Windows Server durante i passaggi precedenti.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Individuare la risorsa del servizio di sincronizzazione archiviazione.
1. Creare un nuovo *gruppo di sincronizzazione* all'interno della risorsa del servizio di sincronizzazione archiviazione per ogni condivisione file di Azure. In Sincronizzazione file di Azure terminologia, la condivisione file di Azure diventerà un *endpoint cloud* nella topologia di sincronizzazione che si sta descrivendo con la creazione di un gruppo di sincronizzazione. Quando si crea il gruppo di sincronizzazione, assegnargli un nome familiare in modo da riconoscere il set di file sincronizzati qui. Assicurarsi di fare riferimento alla condivisione file di Azure con un nome corrispondente.
1. Dopo la creazione del gruppo di sincronizzazione, viene visualizzata una riga per l'elenco dei gruppi di sincronizzazione. Selezionare il nome (un collegamento) per visualizzare il contenuto del gruppo di sincronizzazione. La condivisione file di Azure verrà visualizzata in **endpoint cloud**.
1. Individuare il pulsante **+ Aggiungi endpoint server** . La cartella nel server locale di cui è stato effettuato il provisioning diventerà il percorso di questo *endpoint server*.
