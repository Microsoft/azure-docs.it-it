---
title: Come installare IoT Edge in Kubernetes | Microsoft Docs
description: Informazioni su come installare IoT Edge in Kubernetes usando un ambiente cluster di sviluppo locale
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fc7b6b480056b56a2776cebd0fa87a5b96f9f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201662"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Come installare IoT Edge in Kubernetes (anteprima)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge può essere integrato con Kubernetes usandolo come livello infrastruttura resiliente e a disponibilità elevata. Di seguito è riportato il supporto di questo supporto in una soluzione IoT Edge di alto livello:

![Introduzione a K8S](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Un modello mentale efficace per questa integrazione consiste nel considerare Kubernetes come un altro ambiente operativo IoT Edge le applicazioni possono essere eseguite in oltre a Linux e Windows.

## <a name="architecture"></a>Architettura 
In Kubernetes IoT Edge fornisce la *definizione di risorsa personalizzata* per le distribuzioni del carico di lavoro perimetrale. IoT Edge agente presuppone il ruolo di un  *controller* di gestione delle richieste che riconcilia lo stato desiderato gestito dal cloud con lo stato del cluster locale.

La durata del modulo viene gestita dall'utilità di pianificazione di Kubernetes, che mantiene la disponibilità dei moduli e sceglie la selezione host. IoT Edge gestisce la piattaforma applicativa perimetrale in esecuzione in primo piano, riconciliando continuamente lo stato desiderato specificato nell'hub Internet con lo stato nel cluster perimetrale. Il modello applicativo è ancora il modello familiare basato su moduli e route IoT Edge. Il controller dell'agente di IoT Edge esegue il modello di applicazione della traduzione *automatica* IOT Edge per i costrutti nativi Kubernetes come Pod, distribuzioni, servizi e così via.

Di seguito è riportato un diagramma dell'architettura di alto livello:

![kubernetes Arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Ogni componente della distribuzione perimetrale ha come ambito uno spazio dei nomi Kubernetes specifico per il dispositivo, rendendo possibile la condivisione delle stesse risorse cluster tra più dispositivi perimetrali e le relative distribuzioni.

>[!NOTE]
>IoT Edge in Kubernetes è in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Esercitazioni e riferimenti 

Per ulteriori informazioni, vedere le esercitazioni e i riferimenti dettagliati per la [IOT Edge in Kubernetes Preview docs](https://aka.ms/edgek8sdoc) .
