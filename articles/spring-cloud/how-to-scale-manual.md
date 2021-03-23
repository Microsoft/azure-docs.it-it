---
title: Ridimensionare un'applicazione in Azure Spring Cloud | Microsoft Docs
description: Informazioni su come ridimensionare un'applicazione con Azure Spring Cloud nel portale di Azure
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 5632f9a6126615255306cc89425bd08a9ffa9753
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878452"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Ridimensionare un'applicazione in Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java ✔️ C#

Questa documentazione illustra come dimensionare qualsiasi applicazione di microservizi usando il dashboard di Azure Spring Cloud nel portale di Azure.

Aumentare e ridurre le prestazioni dell'applicazione modificando il numero di CPU virtuali (vCPU) e la quantità di memoria. Aumentare e ridurre le prestazioni dell'applicazione modificando il numero di istanze dell'applicazione.

Al termine, si saprà come apportare rapide modifiche manuali a ogni applicazione nel servizio. Il ridimensionamento diventa effettivo entro pochi secondi e non richiede alcuna modifica del codice o ridistribuzione.

## <a name="prerequisites"></a>Prerequisiti

Per seguire queste procedure occorre:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
* Istanza del servizio Azure Spring Cloud distribuita.  Per iniziare, seguire la [guida di avvio rapido sulla distribuzione di un'app tramite l'interfaccia della riga di comando di Azure](spring-cloud-quickstart.md).
* Almeno un'applicazione già creata nell'istanza del servizio.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Passare alla pagina Piano nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare alla pagina **Panoramica** di Azure Spring Cloud.

1. Selezionare il gruppo di risorse contenente il servizio.

1. Selezionare la scheda **App** in **Impostazioni** nel menu a sinistra della pagina.

1. Selezionare l'applicazione da ridimensionare. In questo esempio selezionare l'applicazione denominata **account-service**. Verrà visualizzata la pagina **Panoramica** dell'applicazione.

1. Passare alla scheda **Piano** in **Impostazioni** nel menu a sinistra della pagina. Verranno visualizzate le opzioni per gli attributi di ridimensionamento illustrati nella sezione seguente.

## <a name="scale-your-application"></a>Ridimensionare l'applicazione

Se si modificano gli attributi di ridimensionamento, tenere presente quanto segue:

* **CPU**: il numero massimo consentito è 4 CPU per ogni istanza dell'applicazione. Il numero totale di CPU per un'applicazione corrisponde al valore impostato qui moltiplicato per il numero di istanze dell'applicazione.

* **Memoria/GB**: la quantità massima di memoria consentita è di 8 GB per ogni istanza dell'applicazione. La quantità totale di memoria per un'applicazione corrisponde al valore impostato qui moltiplicato per il numero di istanze dell'applicazione.

* **Numero di istanze**: nel livello Standard è possibile aumentare il numero di istanze fino a un massimo di 20. Questo valore modifica il numero di istanze in esecuzione separate dell'applicazione di microservizi.

Assicurarsi selezionare **Salva** per applicare le impostazioni di ridimensionamento.

![Scheda Piano nel portale di Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Dopo alcuni secondi, le modifiche apportate verranno visualizzate nella pagina **Panoramica**, con ulteriori dettagli disponibili nella scheda **Istanze dell'applicazione**. Il ridimensionamento non richiede alcuna modifica del codice o ridistribuzione.

## <a name="upgrade-to-the-standard-tier"></a>Eseguire l'aggiornamento al livello Standard
Se il piano corrente è il livello Basic ed è vincolato da uno o più di questi [limiti](spring-cloud-quotas.md), è possibile eseguire l'aggiornamento al livello Standard. A questo scopo, passare al menu Piano tariffario selezionando la colonna Livello Standard e facendo clic sul pulsante **Aggiorna**.

## <a name="next-steps"></a>Passaggi successivi

Questo esempio ha illustrato come dimensionare manualmente un'applicazione di Azure Spring Cloud. Per informazioni su come monitorare un'applicazione configurando gli avvisi, vedere [Configurare la scalabilità automatica](spring-cloud-tutorial-setup-autoscale.md).

> [!div class="nextstepaction"]
> [Informazioni su come configurare gli avvisi](spring-cloud-tutorial-alerts-action-groups.md)
