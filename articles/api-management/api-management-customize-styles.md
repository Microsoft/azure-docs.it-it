---
title: Personalizzare lo stile della pagina nel portale per sviluppatori legacy di Gestione API
titleSuffix: Azure API Management
description: Seguire la procedura della guida introduttiva per personalizzare lo stile degli elementi nel portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 0cdad245981ad02d3cdaefba447d131c775e242f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93145770"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Personalizzare lo stile delle pagine del portale per sviluppatori

Per personalizzare il portale per sviluppatori in Gestione API di Azure è possibile procedere in tre modi più comuni:
 
* [Modificare i contenuti di pagine statiche e gli elementi di layout di pagina](api-management-modify-content-layout.md)
* Aggiornare gli stili usati per gli elementi della pagina nel portale per sviluppatori (illustrato in questa guida)
* [Modificare i modelli usati per le pagine generate dal portale](api-management-developer-portal-templates.md) (ad esempio documenti API, prodotti e autenticazione utente)

Questo articolo illustra come personalizzare lo stile degli elementi nelle pagine del portale per **sviluppatori** legacy e visualizzare le modifiche.

![Screenshot che mostra dove è possibile modificare le impostazioni nel portale per sviluppatori legacy.](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prerequisites

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Personalizzare il portale per sviluppatori

1. Selezionare **Panoramica**.
2. Nella parte superiore della finestra **Panoramica** fare clic sul pulsante **Portale per sviluppatori (legacy)** .
3. In alto a sinistra della schermata è disponibile un'icona costituita da due pennelli. Passare il cursore del mouse sull'icona per aprire il menu di personalizzazione del portale.

    ![Screenshot che evidenzia l'icona con due pennelli.](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Selezionare **Stili** dal menu per aprire il riquadro di personalizzazione dello stile.

    Nella pagina vengono visualizzati tutti gli elementi che è possibile personalizzare con gli **stili**
5. Immettere "headings-color" nel campo **Change variable values to customize developer portal appearance:** (Modificare i valori delle variabili per personalizzare l'aspetto del portale per sviluppatori).

    Nella pagina viene visualizzato l'elemento **\@headings-color**. Questa variabile controlla il colore del testo.

    ![personalizzare lo stile](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Fare clic sul campo per la variabile **\@headings-color**. 
    
    Viene aperto il menu a discesa Selezione colori.
7. Nel menu a discesa Selezione colori selezionare un colore nuovo.

    > [!TIP]
    > Per tutte le modifiche è disponibile l'anteprima in tempo reale. Viene visualizzato un indicatore di stato nella parte superiore del riquadro di personalizzazione. Dopo alcuni secondi il testo dell'intestazione cambia e diventa del colore selezionato.

8. Selezionare **Publish** (Pubblica) in basso a sinistra nel menu del riquadro di personalizzazione.
9. Selezionare **Publish customizations** (Pubblica personalizzazioni) per rendere le modifiche disponibili pubblicamente.

## <a name="view-your-change"></a>Visualizzare la modifica

1. Passare al portale per sviluppatori.
2. È possibile visualizzare la modifica effettuata.

## <a name="next-steps"></a>Passaggi successivi

Un altro argomento di interesse è [come personalizzare il portale per sviluppatori di Gestione API di Azure usando i modelli](api-management-developer-portal-templates.md).