---
title: Reimpostazione delle password self-service in Azure Active Directory B2C | Microsoft Docs
description: Illustra come configurare la reimpostazione della password self-service per i clienti in Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183109"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configurare la reimpostazione self-service della password per i clienti

La funzione di reimpostazione self-service della password consente ai clienti che si sono registrati per ottenere un account locale di reimpostare le password in modo autonomo. Questo riduce notevolmente il carico di lavoro per lo staff di supporto, soprattutto se l'applicazione viene usata regolarmente da milioni di clienti. L'unico metodo di recupero attualmente supportato è l'uso di un indirizzo di posta elettronica verificato.

> [!NOTE]
> Questo articolo si riferisce alla reimpostazione della password usata nel contesto del flusso di **Accesso** dell'utente a V1, che usa **Accesso all'account locale** come provider di identità. Se è necessario richiamare dall'app flussi dell'utente di reimpostazione della password completamente personalizzabili, vedere [questo articolo](user-flow-overview.md).
>
>

Per impostazione predefinita, per la directory personale la reimpostazione self-service della password non è attivata. Usare i passaggi seguenti per attivarla:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore della sottoscrizione. Si tratta dello stesso account aziendale o dell'istituto d'istruzione o dello stesso account Microsoft usato per la creazione della directory.
2. Aprire **Azure Active Directory** (nella barra di spostamento sul lato sinistro).
3. Scorrere verso il basso nel pannello delle opzioni e selezionare **Reimpostazione password**.
4. Impostare **Reimpostazione password self-service abilitata** su **Tutte**.
5. Fare clic su **Salva** nella parte superiore della pagina. L'operazione è completata.

Per eseguire il test, usare la funzionalità "Esegui adesso" in ogni flusso di accesso dell'utente che include gli account locali come provider di identità. Nella pagina di accesso dell'account locale in cui si immettono l'indirizzo di posta elettronica e la password o il nome utente e la password, fare clic su **Problemi di accesso all'account?** per verificare l'esperienza cliente.

> [!NOTE]
> Le pagine di reimpostazione della password self-service possono essere personalizzate con la [funzionalità di aggiunta di informazioni distintive dell'azienda](../active-directory/fundamentals/customize-branding.md).
>
>

