---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67180034"
---
La tabella seguente elenca i requisiti per i gateway VPN PolicyBased e RouteBased. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse. Per il modello classico, i gateway VPN PolicyBased corrispondono ai gateway statici, mentre i gateway RouteBased corrispondono ai gateway dinamici.

|  | **Gateway VPN Basic PolicyBased** | **Gateway VPN Basic RouteBased** | **Gateway VPN Standard RouteBased** | **Gateway VPN High Performance RouteBased** |
| --- | --- | --- | --- | --- |
| **Connettività da sito a sito (S2S)** |Configurazione VPN PolicyBased |Configurazione VPN RouteBased |Configurazione VPN RouteBased |Configurazione VPN RouteBased |
| **Connettività da punto a sito (P2S**) |Non supportato |Supportato (può coesistere con Site-to-Site) |Supportato (può coesistere con Site-to-Site) |Supportato (può coesistere con Site-to-Site) |
| **Metodo di autenticazione** |Chiave condivisa |Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito |Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito |Chiave precondivisa per la connettività da sito a sito, Certificati per la connettività da punto a sito |
| **Numero massimo di connessioni Site-to-Site** |1 |10 |10 |30 |
| **Numero massimo di connessioni Point-to-Site** |Non supportato |128 |128 |128 |
| **Supporto routing attivo (BGP)** (*) |Non supportato |Non supportate |Supportato |Supportato |

  (*) BGP non è supportato per il modello di distribuzione classica.
