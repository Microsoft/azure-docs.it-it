---
title: Segmenti 2,5 in messaggi EDIFACT
description: Risolvere i messaggi EDIFACT con i segmenti s 2.5 in app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792531"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Gestire documenti EDIFACT con segmenti UNH2.5 nelle App per la logica di Azure

Se un segmento na 2.5 è presente in un documento EDIFACT, il segmento viene utilizzato per la ricerca dello schema. Ad esempio, in questo messaggio EDIFACT di esempio, il campo na `EAN008`è:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Per gestire questo messaggio, attenersi alla procedura descritta di seguito:

1. Aggiornare lo schema.

1. Controllare le impostazioni dell'accordo.

## <a name="update-the-schema"></a>Aggiornare lo schema

Per elaborare il messaggio, è necessario distribuire uno schema con il nome del nodo radice na 2.5. Ad esempio, il nome radice dello schema per il campo di esempio `EFACT_D03B_ORDERS_EAN008`è. Per ognuno `D03B_ORDERS` dei quali è presente un segmento di un altro intervallo 2.5, è necessario distribuire un singolo schema.

## <a name="add-schema-to-edifact-agreement"></a>Aggiungi schema a contratto EDIFACT

### <a name="edifact-decode"></a>Decodifica EDIFACT

Per decodificare il messaggio in arrivo, configurare lo schema nelle impostazioni di ricezione dell'accordo EDIFACT:

1. Nella [portale di Azure](https://portal.azure.com)aprire l'account di integrazione.

1. Aggiungere lo schema all'account di integrazione.

1. Configurare lo schema nelle impostazioni di ricezione dell'accordo EDIFACT.

1. Selezionare l'accordo EDIFACT e selezionare **modifica come JSON**. Aggiungere il valore per l'oggetto di ricezione 2.5 alla `schemaReferences` sezione dell'accordo di ricezione:

   ![Aggiungere l'accordo per la ricezione di un contratto](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codifica EDIFACT

Per codificare il messaggio in arrivo, configurare lo schema nelle impostazioni di invio del contratto EDIFACT

1. Nella [portale di Azure](https://portal.azure.com)aprire l'account di integrazione.

1. Aggiungere lo schema all'account di integrazione.

1. Configurare lo schema nelle impostazioni di invio dell'accordo EDIFACT.

1. Selezionare il contratto EDIFACT e fare clic su **Modifica come JSON**.  Aggiungere il valore UNH2.5 nel contratto di invio **schemaReferences**

1. Selezionare l'accordo EDIFACT e selezionare **modifica come JSON**. Aggiungere il valore di prepari 2.5 alla sezione dell' `schemaReferences` accordo di invio:

   ![Aggiunta di un contratto per l'invio di un contratto](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [contratti di account di integrazione](../logic-apps/logic-apps-enterprise-integration-agreements.md)