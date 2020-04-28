---
title: Modificare il contenuto della pagina nel portale per sviluppatori in gestione API
titleSuffix: Azure API Management
description: Informazioni su come modificare i contenuti di una pagina nel portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: ebf2cbd430339378a09d10d91ad61327d24842e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430625"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modificare il contenuto e il layout delle pagine nel portale per sviluppatori in Gestione API di Azure
Per personalizzare il portale per sviluppatori in Gestione API di Azure ci sono tre modalità principali:

* [Modificare il contenuto di pagine statiche e gli elementi di layout di pagina][modify-content-layout] (descritto in questa guida)
* [Aggiornare gli stili usati per gli elementi della pagina nel portale per sviluppatori][customize-styles]
* [Modificare i modelli usati per le pagine generati dal portale][portal-templates] (ad esempio documenti API, prodotti, autenticazione utente e così via)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="structure-of-developer-portal-pages"></a><a name="page-structure"> </a>Struttura delle pagine del portale per sviluppatori

Il portale per sviluppatori è basato su un sistema di gestione dei contenuti. Il layout delle pagine viene creato in base al set di elementi di pagine di piccole dimensioni noto come widget:

![Struttura della pagina del portale per sviluppatori][api-management-customization-widget-structure]

Tutti i widget sono modificabili.
* I contenuti principali specifici per ogni singola pagina si trovano nel widget "Contenuto". La modifica di una pagina comporta la modifica del contenuto di questo widget.
* Tutti gli elementi di layout della pagina sono contenuti negli altri widget. Le modifiche apportate a questi widget vengono applicate a tutte le pagine. Questi widget sono detti "widget di layout".

Nella modifica quotidiana di una pagina in genere viene modificato solo il widget Contenuto che avrà un contenuto diverso per ogni pagina.

## <a name="modifying-the-contents-of-a-layout-widget"></a><a name="modify-layout-widget"> </a>Modifica dei contenuti di un widget di layout

È possibile accedere al portale per sviluppatori dal portale di Azure.

1. Fare clic su **Portale per sviluppatori** sulla barra degli strumenti dell'istanza di Gestione API.
2. Per modificare i contenuti dei widget, fare clic sull'icona composta da due pennelli nel menu del **portale per sviluppatori** a sinistra.
3. Per modificare il contenuto dell'intestazione, scorrere fino alla sezione **Intestazione** nell'elenco a sinistra.

    I widget possono essere modificati all'interno dei campi.
4. Quando si è pronti per pubblicare le modifiche, fare clic su **Pubblica** nella parte inferiore della pagina.

A questo punto, la nuova intestazione dovrebbe essere visualizzata in ogni pagina del portale per sviluppatori.

## <a name="next-steps"></a><a name="next-steps"> </a>Passaggi successivi
* [Aggiornare gli stili usati per gli elementi della pagina nel portale per sviluppatori][customize-styles]
* [Modificare i modelli usati per le pagine generati dal portale][portal-templates] (ad esempio documenti API, prodotti, autenticazione utente e così via)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
