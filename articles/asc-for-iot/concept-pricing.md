---
title: Prezzi e costi associati
description: Informazioni sui costi associati al centro sicurezza di Azure e su come controllarli.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 2c76a79f09629b32767c1dcc78989dfa47c678fd
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2020
ms.locfileid: "89504572"
---
# <a name="pricing-and-associated-costs"></a>Prezzi e costi associati

Questo articolo illustra il Centro sicurezza di Azure per il modello di determinazione dei prezzi di Internet, riepiloga tutti i costi associati e spiega come gestirli.

## <a name="pricing"></a>Prezzi

Il modello di determinazione dei prezzi del Centro sicurezza di Azure per gli Internet è costituito da due parti e viene fatturato una volta che un hub Internet delle cose è [abilitato](quickstart-onboard-iot-hub.md) nel centro sicurezza di Azure per tutto:

- Costo per funzionalità di sicurezza incorporate del dispositivo in base all'analisi dei log dell'hub Internet.

- Costo per funzionalità di sicurezza avanzate per i messaggi in base ai messaggi di sicurezza provenienti da dispositivi IoT Edge o foglia.

Per altre informazioni, vedere [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Costi associati

Il Centro sicurezza di Azure per gli Internet è associato a costi che non fanno parte dei prezzi diretti:

- Costi di archiviazione Log Analytics

È possibile ridurre i costi associati escludendo alcune funzionalità della soluzione. Rifiutare esplicitamente la modifica delle impostazioni.

Per modificare le impostazioni:

1. Aprire l'hub Internet.

1. In **sicurezza**fare clic su **Impostazioni**.

1. Fare clic su **raccolta dati**.

Nella tabella seguente viene fornito un riepilogo dei costi associati e delle implicazioni di ogni opzione.

| Opzione | Uso | Commento |
| --- | --- | --- |
| **Archiviazione Log Analytics** |  |
| Suggerimenti e avvisi del dispositivo| Avvisi e avvisi di sicurezza generati dal servizio | Non facoltativo |
| Dati di sicurezza non elaborati| Dati di sicurezza non elaborati dai dispositivi Internet, raccolti dagli agenti di sicurezza | Disabilitare l' _archiviazione degli eventi di sicurezza dei dispositivi non elaborati_ |
|

>[!Important]
> La disattivazione ha gravi implicazioni nel centro sicurezza di Azure per la disponibilità delle funzionalità di sicurezza.

| Rifiuta esplicitamente | Implicazioni |
| --- | --- |
| _Raccolta di metadati gemelli_ | Disabilitare gli [avvisi personalizzati](quickstart-create-custom-alerts.md) |
| | Disabilitare le raccomandazioni del manifesto IoT Edge |
| | Disabilitare le raccomandazioni e gli avvisi basati sull'identità del dispositivo |
| _Archiviare gli eventi di sicurezza del dispositivo non elaborato_ | Non sono disponibili informazioni dettagliate sulle raccomandazioni di base del sistema operativo del dispositivo |
| | Informazioni dettagliate sulle analisi degli [avvisi](concept-security-alerts.md) e delle [raccomandazioni](concept-recommendations.md) non sono disponibili |
|

## <a name="see-also"></a>Vedi anche

- Accedere ai [dati di sicurezza non elaborati](how-to-security-data-access.md)
- [Analizzare un dispositivo](how-to-investigate-device.md)
- Comprendere ed esplorare le [raccomandazioni sulla sicurezza](concept-recommendations.md)
- Comprendere ed esplorare gli [avvisi di sicurezza](concept-security-alerts.md)
