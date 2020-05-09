---
title: Importare un'API SOAP con il portale di Azure | Microsoft Docs
description: Informazioni su come importare un'API SOAP con Gestione API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 7b1fd34824bec9641edc94ce278fa21a1b57b2c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202741"
---
# <a name="import-soap-api"></a>Importare un'API SOAP

Questo articolo illustra come importare una rappresentazione XML standard di un'API SOAP Viene inoltre descritto come testare l'API di Gestione API.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Importare un'API SOAP
> * Testare l'API nel portale di Azure
> * Testare l'API nel portale per sviluppatori

## <a name="prerequisites"></a>Prerequisiti

Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importare e pubblicare un'API back-end

1. Passare al servizio Gestione API nel portale di Azure e selezionare **API** dal menu.
2. Selezionare **WSDL** nell'elenco **Add a new API** (Aggiungere una nuova API).

    ![API SOAP](./media/import-soap-api/wsdl-api.png)
3. In **Specifica WSDL** immettere l'URL in cui risiede l'API SOAP.
4. Il pulsante di opzione **Pass-through SOAP** è selezionato per impostazione predefinita. Con questa selezione, l'API verrà esposta come SOAP. Il consumer dovrà usare regole SOAP. Se si vuole convertire l'API in REST, seguire la procedura descritta in [Importare un'API SOAP e convertirla in REST](restify-soap-api.md).

    ![Pass-through](./media/import-soap-api/pass-through.png)
5. Premere TAB.

    Nei campi seguenti verranno inserite le informazioni dell'API SOAP: nome visualizzato, nome e descrizione.
6. Aggiungere un suffisso dell'URL dell'API. Il suffisso è un nome che identifica questa specifica API in questa istanza di Gestione API. Deve essere univoco nell'istanza di Gestione API.
7. Pubblicare l'API associandola a un prodotto. In questo caso viene usato il prodotto "*Unlimited*".  Per fare in modo che l'API venga pubblicata e sia disponibile per gli sviluppatori, aggiungerla a un prodotto. È possibile eseguire questa operazione durante la creazione dell'API o in un secondo momento.

    I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.

    Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

    * **Starter**
    * **Illimitato**   
8. Immettere altre impostazioni API. È possibile impostare i valori durante la creazione o configurarli successivamente passando alla scheda **Impostazioni**. Le impostazioni sono illustrate nell'esercitazione [Importare e pubblicare la prima API](import-and-publish.md#-import-and-publish-a-backend-api).
9. Selezionare **Create** (Crea).

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testare la nuova API nel portale amministrativo

È possibile chiamare le operazioni direttamente dal portale amministrativo, che consente di visualizzare e testare le operazioni di un'API in tutta comodità.  

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