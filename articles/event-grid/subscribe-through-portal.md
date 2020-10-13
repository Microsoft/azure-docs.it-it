---
title: Sottoscrizioni di Griglia di eventi di Azure tramite il portale
description: Questo articolo descrive come creare sottoscrizioni di griglia di eventi per le origini supportate, ad esempio archiviazione BLOB di Azure, usando il portale di Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e80e2243c93ab38187646256f567d6ab73c40100
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308504"
---
# <a name="subscribe-to-events-through-portal"></a>Sottoscrivere eventi tramite il portale

Questo articolo descrive come creare sottoscrizioni di Griglia di eventi tramite il portale.

## <a name="create-event-subscriptions"></a>Creare sottoscrizioni di eventi

Per creare una sottoscrizione di Griglia di eventi per tutte le [origini eventi](overview.md#event-sources) supportate, usare la procedura seguente. Questo articolo illustra come creare una sottoscrizione di Griglia di eventi per una sottoscrizione di Azure.

1. Selezionare **Tutti i servizi**.

   ![Selezionare Tutti i servizi](./media/subscribe-through-portal/select-all-services.png)

1. Cercare e selezionare **Sottoscrizioni di Griglia di eventi** tra le opzioni disponibili.

   ![L'acquisizione schermo mostra la ricerca nella portale di Azure con le sottoscrizioni di griglia di eventi selezionate.](./media/subscribe-through-portal/search.png)

1. Selezionare **+ Sottoscrizione di eventi**.

   ![Aggiungere una sottoscrizione](./media/subscribe-through-portal/add-subscription.png)

1. Selezionare il tipo di sottoscrizione da creare. Ad esempio, per sottoscrivere eventi per la sottoscrizione di Azure, selezionare **Sottoscrizioni di Azure** e la sottoscrizione di destinazione.

   ![Selezionare la sottoscrizione di Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Per sottoscrivere tutti i tipi di evento per questa origine eventi, mantenere selezionata l'opzione **Esegui la sottoscrizione di tutti i tipi di eventi**. In caso contrario, selezionare i tipi di evento per questa sottoscrizione.

   ![Selezionare i tipi di evento](./media/subscribe-through-portal/select-event-types.png)

1. Specificare dettagli aggiuntivi sulla sottoscrizione di eventi, ad esempio l'endpoint per la gestione degli eventi e un nome per la sottoscrizione.

   ![Screenshot che mostra le sezioni "Dettagli endpoint" e "Dettagli sottoscrizione evento" con il valore nome sottoscrizione immesso.](./media/subscribe-through-portal/provide-subscription-details.png)

1. Per abilitare l'inserimento nella coda di messaggi non recapitabili e personalizzare i criteri di ripetizione dei tentativi, selezionare **Funzionalità aggiuntive**.

   ![Selezionare Funzionalità aggiuntive](./media/subscribe-through-portal/select-additional-features.png)

1. Selezionare un contenitore da usare per archiviare gli eventi non recapitati e impostare il numero di tentativi inviati.

   ![Abilitare l'inserimento nella coda di messaggi non recapitabili e i criteri di ripetizione dei tentativi](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Al termine, scegliere **Crea**.

## <a name="create-subscription-on-resource"></a>Creare una sottoscrizione nella risorsa

Alcune origini eventi supportano la creazione di una sottoscrizione di eventi tramite l'interfaccia del portale per la risorsa. Selezionare l'origine eventi e cercare **Eventi** nel riquadro a sinistra.

![Specificare i dettagli sulla sottoscrizione](./media/subscribe-through-portal/resource-events.png)

Il portale include opzioni per la creazione di una sottoscrizione di eventi rilevante per tale origine.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
