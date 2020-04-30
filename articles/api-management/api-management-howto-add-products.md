---
title: Come creare e pubblicare un prodotto in Gestione API di Azure
description: Informazioni su come creare e pubblicare prodotti in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 686d6e2ff01fd4d8283350ab8097e1f73c914af0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81260997"
---
# <a name="create-and-publish-a-product"></a>Creare e pubblicare un prodotto  

In Gestione API di Azure un prodotto contiene una o più API, oltre a una quota di utilizzo e alle condizioni per l'utilizzo. Dopo la pubblicazione di un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a usare le API del prodotto.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare e pubblicare un prodotto
> * Aggiungere un'API al prodotto

![Esercitazione sull'aggiunta di un prodotto](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

## <a name="create-and-publish-a-product"></a>Creare e pubblicare un prodotto

![Add product](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Fare clic su **Prodotti** nel menu a sinistra per visualizzare la pagina **Prodotti**.
2. Fare clic su **+ Aggiungi**.

    Quando si aggiunge un prodotto, è necessario fornire le informazioni seguenti: 

    | Nome                     | Descrizione                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome visualizzato             | Nome con cui deve essere visualizzato nel **portale per sviluppatori**.                                                                                                                                                                                                                                                        |
    | Nome                     | Nome descrittivo del prodotto.                                                                                                                                                                                                                                                                                      |
    | Descrizione              | Il campo **Descrizione** consente di specificare informazioni dettagliate sul prodotto, ad esempio lo scopo, le API a cui fornisce l'accesso e altre informazioni utili.                                                                                                                                               |
    | State                    | Fare clic su **Pubblicato** se si vuole pubblicare il prodotto. Per poter chiamare le API in un prodotto, il prodotto deve essere pubblicato. Per impostazione predefinita, i nuovi prodotti non sono pubblicati e sono visibili solo agli utenti nel gruppo **Amministratori**.                                                                                      |
    | Richiede la sottoscrizione    | Selezionare **Richiedi sottoscrizione** se un utente deve eseguire la sottoscrizione per usare il prodotto.                                                                                                                                                                                                                                   |
    | Richiede approvazione        | Selezionare **Richiedi approvazione** se si preferisce che i tentativi di sottoscrizione al prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore. Se la casella è deselezionata, i tentativi di sottoscrizione vengono approvati automaticamente.                                                                                                                         |
    | Limite per il numero di sottoscrizioni | Per limitare il numero di più sottoscrizioni simultanee, immettere il limite per le sottoscrizioni.                                                                                                                                                                                                                                |
    | Note legali              | È possibile includere le condizioni per l'utilizzo del prodotto che i sottoscrittori devono accettare per usare il prodotto.                                                                                                                                                                                                             |
    | API                     | I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. <br/> È possibile aggiungere un'API esistente durante la creazione di prodotto. È possibile aggiungere un'API al prodotto in un secondo momento, nella pagina **Impostazioni** del prodotto o durante la creazione di un'API. |

3. Fare clic su **Crea** per creare il nuovo prodotto.

### <a name="add-more-configurations"></a>Aggiungere altre configurazioni

È possibile continuare a configurare il prodotto dopo averlo salvato scegliendo la scheda **Impostazioni**. 

Visualizzare/Aggiungere i sottoscrittori al prodotto dalla scheda **Sottoscrizioni**.

Impostare la visibilità di un prodotto per sviluppatori o utenti guest nella scheda **Controllo dell'accesso**.

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>Aggiungere API a un prodotto

I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. È possibile aggiungere un'API esistente durante la creazione di prodotto. È possibile aggiungere un'API al prodotto in un secondo momento, dalla pagina **Impostazioni** dei prodotti o durante la creazione di un'API.

Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.

### <a name="add-an-api-to-an-existing-product"></a>Aggiungere un'API a un prodotto esistente

![Aggiungere un'API al prodotto](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. Nella scheda **Prodotti** selezionare un prodotto.
2. Passare alla scheda **API**.
3. Fare clic su **+ Aggiungi**.
4. Scegliere un'API e fare clic su **Seleziona**.

> [!TIP]
> È possibile creare o aggiornare la sottoscrizione dell'utente a un *Prodotto* con chiavi di sottoscrizione personalizzate [tramite l'API REST](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate) o un comando PowerShell.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare e pubblicare un prodotto
> * Aggiungere un'API al prodotto

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Creare un'API vuota e simulare le risposte di un'API](mock-api-responses.md)
