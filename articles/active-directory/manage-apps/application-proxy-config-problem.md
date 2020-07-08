---
title: Problema durante la creazione di un'applicazione Proxy di applicazione | Microsoft Docs
description: Come risolvere i problemi di creazione di applicazioni Proxy di applicazione nel portale di amministrazione di Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2593f80db4c271d6ae4773f324cc9777e5400550
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764962"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problema durante la creazione di un'applicazione Proxy di applicazione 

Di seguito vengono riportati alcuni dei problemi comuni relativi alla creazione di una nuova applicazione Proxy di applicazione.

## <a name="recommended-documents"></a>Documenti consigliati 

Per altre informazioni sulla creazione di un'applicazione Proxy di applicazione tramite il portale di amministrazione, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md).

Se si seguono i passaggi in tale documento e si riceve un errore durante la creazione dell'applicazione, vedere i dettagli dell'errore per informazioni e suggerimenti per la correzione dell'applicazione. La maggior parte dei messaggi di errore include una correzione suggerita. 

## <a name="specific-things-to-check"></a>Elementi specifici da controllare

Per evitare errori comuni, verificare quanto segue:

-   Si dispone del ruolo di amministratore con l'autorizzazione a creare un'applicazione proxy dell'applicazione

-   L'URL interno è univoco

-   L'URL esterno è univoco

-   Gli URL iniziano con http o https e terminano con un carattere "/"

-   L'URL deve essere un nome di dominio e non un indirizzo IP

Quando si crea l'applicazione, il messaggio di errore dovrebbe essere visualizzato in alto a destra. È anche possibile selezionare l'icona di notifica per visualizzare i messaggi di errore.

   ![Prompt di notifica](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Passaggi successivi
[Abilitare il proxy di applicazione nel portale di Azure](application-proxy-add-on-premises-application.md)
