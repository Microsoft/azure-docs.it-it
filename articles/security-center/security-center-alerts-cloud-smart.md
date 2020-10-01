---
title: Eventi imprevisti del Centro sicurezza di Azure-correlazioni intelligenti degli avvisi
description: In questo argomento viene illustrato come Fusion utilizza la correlazione intelligente degli avvisi cloud per generare eventi imprevisti di sicurezza nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 742d7122c60b383e8ab7aa73f73b11b47843a9d5
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613824"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlazione tra Smart Alert cloud nel centro sicurezza di Azure (eventi imprevisti)

Il Centro sicurezza di Azure analizza continuamente i carichi di lavoro del cloud ibrido usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.

La maggior parte della copertura delle minacce sta crescendo. La necessità di rilevare anche il minimo compromesso è importante e può essere difficile per gli analisti della sicurezza valutare i diversi avvisi e identificare un attacco effettivo. Il Centro sicurezza consente agli analisti di affrontare la fatica degli avvisi. Consente di diagnosticare gli attacchi non appena si verificano, correlando avvisi diversi e segnali di fedeltà bassa negli eventi imprevisti della sicurezza.

Fusion Analytics è la tecnologia e il back-end analitico che alimenta gli eventi imprevisti del Centro sicurezza, consentendo al reparto IT di correlare diversi avvisi e segnali contestuali insieme. Fusion esamina i diversi segnali segnalati in una sottoscrizione tra le risorse. Fusion trova modelli che rivelano la progressione degli attacchi o segnali con informazioni contestuali condivise, a indicare che è necessario usare una procedura di risposta unificata.

Fusion Analytics combina le informazioni sul dominio di sicurezza con intelligenza artificiale per analizzare gli avvisi, scoprendo nuovi modelli di attacco man mano che si verificano. 

Il Centro sicurezza usa la matrice di attacco MITRE per associare gli avvisi alla finalità percepita, contribuendo a formalizzare la conoscenza del dominio di sicurezza. Inoltre, usando le informazioni raccolte per ogni passaggio di un attacco, il Centro sicurezza può escludere l'attività che sembra essere una procedura di attacco, ma in realtà non lo è.

Poiché gli attacchi si verificano spesso in tenant diversi, il Centro sicurezza può combinare gli algoritmi di intelligenza artificiale per analizzare le sequenze di attacco segnalate in ogni sottoscrizione. Questa tecnica identifica le sequenze di attacco come modelli di avviso prevalenti, anziché essere associato a vicenda.

Durante un'indagine di un evento imprevisto, gli analisti spesso necessitano di un contesto aggiuntivo per raggiungere un verdetto sulla natura della minaccia e su come mitigarlo. Ad esempio, anche quando viene rilevata un'anomalia di rete, senza comprendere quanto altro accade sulla rete o per quanto riguarda la risorsa di destinazione, è difficile capire quali azioni eseguire successivamente. Per semplificare, un evento imprevisto di sicurezza può includere elementi, eventi correlati e informazioni. Le informazioni aggiuntive disponibili per gli eventi imprevisti di sicurezza variano a seconda del tipo di minaccia rilevato e della configurazione dell'ambiente. 

> [!TIP]
> Per un elenco degli avvisi relativi agli eventi imprevisti della sicurezza che possono essere generati da Fusion Analytics, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Screenshot del report rilevato evento imprevisto della sicurezza":::


Per comprendere meglio gli eventi imprevisti della sicurezza, vedere [come gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure](security-center-incident.md).

