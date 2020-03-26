---
title: Panoramica di Ancoraggi nello spazio di Azure
description: Informazioni su Ancoraggi nello spazio di Azure, che consente di sviluppare esperienze di realtà mista multipiattaforma.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5ebd29b5fb6fdedcdfbc434209b350512c4cd5dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77657308"
---
# <a name="azure-spatial-anchors-overview"></a>Panoramica di Ancoraggi nello spazio di Azure

Ancoraggi nello spazio di Azure offre agli sviluppatori le funzionalità essenziali per creare applicazioni di realtà mista con riconoscimento dello spazio. Queste applicazioni possono supportare Microsoft HoloLens, i dispositivi basati su iOS compatibili con ARKit e i dispositivi basati su Android compatibili con ARCore. Ancoraggi nello spazio di Azure consente agli sviluppatori di usare piattaforme di realtà mista per percepire lo spazio, designare punti di interesse precisi e richiamarli dai dispositivi supportati.
Questi punti di interesse precisi si definiscono ancoraggi nello spazio.

![Multipiattaforma](./media/cross-platform.png)

## <a name="examples"></a>Esempi

Gli esempi di caso d'uso resi possibili da Ancoraggi nello spazio includono:

- [Esperienze multiutente](tutorials/tutorial-share-anchors-across-devices.md). Ancoraggi nello spazio consente a persone situate nello stesso posto di partecipare ad applicazioni di realtà mista multiutente. Due persone possono ad esempio avviare una partita di scacchi di realtà mista collocando una scacchiera virtuale su un tavolo. Quindi, puntando il loro dispositivo sul tavolo, possono visualizzare e interagire contemporaneamente con la scacchiera virtuale.

- [Orientamento](concepts/anchor-relationships-way-finding.md). Gli sviluppatori possono anche connettere gli ancoraggi nello spazio tra loro per metterli in relazione. Un'app può ad esempio offrire un'esperienza che include due o più punti di interesse con cui gli utenti devono interagire per completare un'attività. Questi punti di interesse possono essere creati in modalità connessa. In seguito, quando l'utente completa l'attività in più passaggi, l'app può richiedere gli ancoraggi vicini a quello corrente per indirizzarlo verso il passaggio successivo dell'attività.

- [Contenuto virtuale persistente nel mondo reale](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Un'app può consentire a un utente di collocare un calendario virtuale sulla parete di una sala riunioni, che le persone possono vedere usando un'app per telefono o un dispositivo HoloLens. In un ambiente industriale, un utente potrebbe ricevere informazioni contestuali su una macchina puntandovi la fotocamera di un dispositivo supportato.

Ancoraggi nello spazio di Azure è costituito da un servizio gestito e da SKD client per le piattaforme di dispositivi supportate. Le sezioni seguenti forniscono informazioni su come iniziare a creare app con Ancoraggi nello spazio di Azure.

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app con Ancoraggi nello spazio.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
