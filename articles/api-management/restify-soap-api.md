---
title: Importare un'API SOAP e convertirla in REST con il portale di Azure | Microsoft Docs
description: Informazioni su come importare un'API SOAP, convertirla in REST con Gestione API e quindi testare l'API nel portale di Azure e nel portale per sviluppatori.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: a87f4d0748d14ab995f75279b6a192f350165d6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87843828"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>Importare un'API SOAP e convertirla in REST

Questo articolo descrive come importare un'API SOAP e convertirla in REST e come testare l'API di Gestione API.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Importare un'API SOAP e convertirla in REST
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisites

Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importare e pubblicare un'API back-end

1. Selezionare **API** in **GESTIONE API**.
2. Selezionare **WSDL** nell'elenco **Add a new API** (Aggiungere una nuova API).

    ![API SOAP](./media/restify-soap-api/wsdl-api.png)
3. In **Specifica WSDL** immettere l'URL in cui risiede l'API SOAP.
4. Fare clic sul pulsante di opzione **Da SOAP a REST**. Quando si fa clic su questa opzione, Gestione API tenta di eseguire una trasformazione automatica tra XML e JSON. In questo caso i consumer dovranno chiamare l'API come API RESTful, che restituisce JSON. Gestione API converte ogni richiesta in una chiamata SOAP.

    ![Da SOAP a REST](./media/restify-soap-api/soap-to-rest.png)

5. Premere TAB.

    Nei campi seguenti verranno inserite le informazioni dell'API SOAP: nome visualizzato, nome e descrizione.
6. Aggiungere un suffisso dell'URL dell'API. Il suffisso è un nome che identifica questa specifica API in questa istanza di Gestione API. Deve essere univoco nell'istanza di Gestione API.
9. Pubblicare l'API associandola a un prodotto. In questo caso viene usato il prodotto "*Unlimited*".  Per fare in modo che l'API venga pubblicata e sia disponibile per gli sviluppatori, aggiungerla a un prodotto. È possibile eseguire questa operazione durante la creazione dell'API o in un secondo momento.

    I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.

    Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

    * **Starter**
    * **Illimitato**   
10. Selezionare **Create** (Crea).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testare la nuova API nel portale di Azure

È possibile chiamare le operazioni direttamente dal portale di Azure, che consente di visualizzare e testare le operazioni di un'API in tutta comodità.  

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic sulla scheda **Test**.
3. Selezionare un'operazione.

    La pagina visualizza campi per le intestazioni e campi per i parametri di query. Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associato all'API. Se si è creata l'istanza di Gestione API, si è già un amministratore, quindi la chiave viene inserita automaticamente. 
1. Fare clic su **Invia**.

    Il back-end risponde con **200 OK** e alcuni dati.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)