---
title: Ridimensionamento di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Ridimensionamento di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d49191abbba9c189672be4cd8bad4346e9689bf6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675425"
---
# <a name="sizing"></a>Ridimensionamento

Il ridimensionamento per le istanze Large di HANA non è diverso dal ridimensionamento per HANA in generale. Per i sistemi esistenti e distribuiti che devono essere spostati da altri RDBMS ad HANA, SAP fornisce una serie di report che vengono eseguiti sui sistemi SAP esistenti. Se il database viene spostato in HANA, questi report controllano i dati e calcolano i requisiti di memoria per l'istanza di HANA. Per altre informazioni su come eseguire i report e su come ottenere le patch o le versioni più recenti, leggere le note SAP seguenti:

- [Note SAP #1793345-dimensionamento per SAP Suite in HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Nota SAP #1872170-suite nei report di dimensionamento Hana e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Note SAP #2121330-domande frequenti: SAP BW sul rapporto di ridimensionamento HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Nota SAP #1736976: report di dimensionamento per BW in HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Nota SAP #2296290-nuovo report di ridimensionamento per BW in HANA](https://launchpad.support.sap.com/#/notes/2296290)

Per le implementazioni "vergini", è disponibile SAP Quick Sizer per calcolare i requisiti di memoria dell'implementazione del software SAP in HANA.

I requisiti di memoria per HANA aumentano in base al volume dei dati. È quindi opportuno conoscere l'utilizzo attuale della memoria per poter prevedere quello futuro. In base ai requisiti di memoria, è possibile associare la propria richiesta a uno degli SKU delle istanze Large di HANA.

**Passaggi successivi**
- Fare riferimento a [Richieste di Onboarding](hana-onboarding-requirements.md)