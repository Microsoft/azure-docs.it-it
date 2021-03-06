---
title: Introduzione all'autenticazione di Azure AD
description: Informazioni su come accedere all'autenticazione di Azure Active Directory (Azure AD) per usare l'API Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/31/2021
ms.author: inhenkel
ms.openlocfilehash: e45a0eefff3a6a7f7dd37bde3ee3bffaedd93dcd
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166616"
---
# <a name="get-credentials-to-access-media-services-api"></a>Ottenere le credenziali per accedere all'API Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando si usa l'autenticazione di Azure AD per accedere all'API di Servizi multimediali di Azure, sono disponibili due opzioni di autenticazione:

- **Autenticazione basata su entità servizio** (opzione consigliata)

    Consente di autenticare un servizio. Le applicazioni che solitamente usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati, ad esempio App Web, app per le funzioni, app per la logica, API o microservizi.
- **Autenticazione utente**

    Consente di autenticare una persona che usa l'app per interagire con le risorse di Servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 

Questo articolo descrive i passaggi per ottenere le credenziali per accedere all'API di Servizi multimediali. Scegliere una delle seguenti schede.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non si dispone di un account, iniziare con una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](account-create-how-to.md).

## <a name="portal"></a>[Portale](#tab/portal/)

### <a name="api-access"></a>Accesso all'API

La pagina di **accesso all'API** consente di selezionare il metodo di autenticazione che si vuole usare per connettersi all'API. La pagina fornisce anche i valori necessari per la connessione all'API.

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali.
2. Selezionare il pannello **accesso all'API** sulla barra di spostamento a sinistra.
3. In **Connettersi all'API di Servizi multimediali** selezionare la versione dell'API di Servizi multimediali a cui si vuole connettersi (v3 è la versione più recente del servizio).

### <a name="service-principal-authentication--recommended"></a>Autenticazione basata su entità servizio (opzione consigliata)

Autentica un servizio usando un'app Azure Active Directory (Azure AD) e il segreto. Questa opzione è consigliata per tutti i servizi di livello intermedio che chiamano l'API di Servizi multimediali. Esempi sono le app Web, le app per le funzioni, le app per la logica, le API e i microservizi. Questo è il metodo di autenticazione consigliato.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Gestire l'app Azure AD e il segreto

La sezione **Gestisci l'app AAD e il segreto** consente di selezionare o creare una nuova app di Azure AD e di generare un segreto. Per motivi di sicurezza, non è possibile visualizzare il segreto dopo la chiusura del pannello. L'applicazione usa l'ID applicazione e il segreto per l'autenticazione per ottenere un token valido per servizi multimediali.

Assicurarsi di disporre delle autorizzazioni sufficienti per registrare un'applicazione con il tenant Azure AD e per assegnare l'applicazione a un ruolo nella sottoscrizione di Azure. Per ulteriori informazioni, vedere [Autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

#### <a name="connect-to-media-services-api"></a>Connettersi all'API Servizi multimediali

**Connettersi all'API Servizi multimediali** fornisce i valori usati per connettersi l'applicazione dell'entità servizio. È possibile ottenere valori di testo o copiare i blocchi JSON o XML.

### <a name="user-authentication"></a>Autenticazione utente

Questa opzione può essere usata per autenticare un dipendente o un membro di Azure Active Directory che usa un'app per interagire con le risorse di Servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Questo metodo di autenticazione deve essere usato solo per le applicazioni di gestione.

#### <a name="connect-to-media-services-api"></a>Connettersi all'API Servizi multimediali

Copiare le credenziali per connettere l'applicazione utente dalla sezione **Connettersi all'API Servizi multimediali**. È possibile ottenere valori di testo o copiare i blocchi JSON o XML.

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Caricare, codificare ed eseguire lo streaming di video con Servizi multimediali v3](stream-files-tutorial-with-api.md).
