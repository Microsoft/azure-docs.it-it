---
title: Controllo degli accessi in base al ruolo di Azure-Visione personalizzata
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come configurare il controllo degli accessi in base al ruolo di Azure per i progetti Visione personalizzata.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94616893"
---
# <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

Visione personalizzata supporta il controllo degli accessi in base al ruolo di Azure, un sistema di autorizzazione per la gestione dei singoli accessi alle risorse di Azure. Usando il controllo degli accessi in base al ruolo di Azure, è possibile assegnare membri del team diversi livelli di autorizzazioni per i progetti Visione personalizzata Per ulteriori informazioni sul controllo degli accessi in base al ruolo di Azure, vedere la [documentazione relativa a RBAC](../../role-based-access-control/index.yml)

## <a name="add-role-assignment-to-custom-vision-resource"></a>Aggiungere un'assegnazione di ruolo alla risorsa Visione personalizzata

Il controllo degli accessi in base al ruolo può essere assegnato a una risorsa Visione personalizzata. Per concedere l'accesso a una risorsa di Azure, è necessario aggiungere un'assegnazione di ruolo.
1. Nel [portale di Azure](https://ms.portal.azure.com/) selezionare **Tutti i servizi**. 
1. Selezionare quindi **Servizi cognitivi** e passare alla risorsa di training visione personalizzata specifica.
   > [!NOTE]
   > È anche possibile configurare il controllo degli accessi in base al ruolo di Azure per tutti i gruppi di risorse, le sottoscrizioni A tale scopo, selezionare il livello di ambito desiderato, quindi passare all'elemento desiderato, ad esempio selezionando **gruppi di risorse** e quindi facendo clic sul gruppo di risorse desiderato.
1. Selezionare **controllo di accesso (IAM)** nel riquadro di spostamento a sinistra.
1. Selezionare la scheda **assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo per questo ambito.
1. Selezionare **Aggiungi** -> **Aggiungi assegnazione di ruolo**.
1. Nell'elenco a discesa **ruolo** selezionare un ruolo che si desidera aggiungere.
1. Nell'elenco **Seleziona** selezionare un utente, gruppo, entità servizio o identità gestita. Se l'entità di sicurezza non viene visualizzata nell'elenco, è possibile digitare la casella Seleziona per cercare i nomi visualizzati, gli indirizzi di posta elettronica e gli identificatori di oggetto.
1. Selezionare **Salva** per assegnare il ruolo.

Entro pochi minuti, alla destinazione verrà assegnato il ruolo selezionato nell'ambito selezionato.

## <a name="custom-vision-role-types"></a>Tipi di ruolo Visione personalizzata

Usare la tabella seguente per determinare le esigenze di accesso per le risorse di Visione personalizzata.

|Ruolo  |Autorizzazioni  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Accesso completo ai progetti, inclusa la possibilità di creare, modificare o eliminare un progetto.        |
|`Cognitive Services Custom Vision Trainer`     | Accesso completo ad eccezione della possibilità di creare o eliminare un progetto. I trainer possono visualizzare e modificare i progetti, eseguire il training, pubblicare, annullare la pubblicazione o esportare i modelli.        |
|`Cognitive Services Custom Vision Labeler`     | Possibilità di caricare, modificare o eliminare immagini di training e creare, aggiungere, rimuovere o eliminare tag. Gli etichettatori possono visualizzare i progetti ma non possono aggiornare nient'altro che i tag e le immagini di training.         |
|`Cognitive Services Custom Vision Deployment`     | Possibilità di pubblicare, annullare la pubblicazione o esportare i modelli. I deployer possono visualizzare i progetti ma non possono aggiornare un progetto, le immagini di training o i tag.        |
|`Cognitive Services Custom Vision Reader`     | Possibilità di visualizzare i progetti. I lettori non possono apportare modifiche.        |