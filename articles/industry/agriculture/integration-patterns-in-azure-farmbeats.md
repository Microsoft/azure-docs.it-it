---
title: Architettura di Azure FarmBeats
description: Descrive l'architettura di Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: ebc1cdc7c762117851072037624f11e9fa98b6f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182605"
---
# <a name="integration-patterns"></a>Modelli di integrazione

Azure FarmBeats è un'offerta business-to-business, disponibile in Azure Marketplace. FarmBeats consente l'aggregazione dei set di dati di agricoltura tra i provider e la generazione di informazioni dettagliate di utilità pratica mediante la creazione di modelli di intelligenza artificiale (AI) o di Machine Learning (ML) fondendo i set di dati.

![Progetto FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Le sezioni seguenti descrivono il modello di integrazione per Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Perché eseguire l'integrazione con Azure FarmBeats?

Questa sezione è incentrata sui partner che vogliono integrare i propri sistemi di dati (ad esempio sensori, droni, stazioni meteo) in Azure FarmBeats.

Azure FarmBeats è un'offerta estendibile, che consente alle aziende agricole di aggiungere i set di set di impostazioni cronologici e in tempo reale diversi in un'unica piattaforma. Azure FarmBeats consente a un'azienda agricola di normalizzare, contestualizzare e aggregare i dati nel contesto di una farm.

Diventando un partner dati con Azure FarmBeats, è possibile aprire i sistemi a un'adozione più ampia e rivolgersi a più clienti con le offerte di dati. Azure FarmBeats offre un livello API estensibile denominato datahub, che consente di inserire i dati dai dispositivi in modo sistematico e in uno schema standardizzato.

Quando i dati sono disponibili nell'istanza di Azure FarmBeats dei clienti, i clienti possono creare strumenti e analisi più completi sui dati.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione dei dati dei sensori, vedere [integrazione dei dati dei sensori](sensor-partner-integration-in-azure-farmbeats.md) e per l'integrazione con i partner di immagini, vedere [integrazione del partner immagini](imagery-partner-integration-in-azure-farmbeats.md).
