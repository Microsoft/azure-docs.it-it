---
title: Aumentare la resilienza delle applicazioni di autenticazione e autorizzazione sviluppate
titleSuffix: Microsoft identity platform
description: Panoramica delle linee guida sulla resilienza per lo sviluppo di applicazioni con Azure Active Directory e la piattaforma di identità Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96317353"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Aumentare la resilienza delle applicazioni di autenticazione e autorizzazione sviluppate

Microsoft Identity usa l'autenticazione e l'autorizzazione moderne basate su token. Ciò significa che un'applicazione acquisisce i token da un provider di identità per autenticare l'utente e autorizzare l'applicazione a chiamare le API protette.

Un token è valido per un determinato periodo di tempo prima che l'app debba acquisirne una nuova. Raramente, una chiamata per recuperare un token potrebbe non riuscire a causa di un problema, ad esempio errori di rete o infrastruttura o interruzione del servizio di autenticazione. In questo documento vengono illustrati i passaggi che uno sviluppatore può adottare per aumentare la resilienza nelle applicazioni se si verifica un errore di acquisizione di token.

Questi articoli forniscono informazioni aggiuntive sull'aumento della resilienza nelle app che usano la piattaforma di identità Microsoft e Azure Active Directory. Sono disponibili linee guida per le applicazioni client che funzionano per conto di un utente connesso e per le applicazioni daemon che funzionano per loro conto. Sono incluse le procedure consigliate per l'uso di token e per la chiamata di risorse.

- [Rafforzare la resilienza nelle applicazioni che gli utenti di accesso](resilience-client-app.md)
- [Creazione di resilienza nelle applicazioni senza utenti](resilience-daemon-app.md)
- [Resilienza della compilazione nell'infrastruttura di gestione delle identità e dell'accesso](resilience-in-infrastructure.md)
- [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
