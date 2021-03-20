---
title: App Extensions in Azure Active Directory B2C | Microsoft Docs
description: Ripristino di b2c-extensions-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94560855"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: app Estensioni

Quando viene creata una directory di Azure AD B2C, un'app denominata `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` viene creata automaticamente nella nuova directory. Questa app, definita **b2c-extensions-app**, è visibile in *Registrazioni per l'app*. Viene usata dal servizio Azure AD B2C per archiviare informazioni sugli utenti e sugli attributi personalizzati. Se l'app viene eliminata, Azure AD B2C non funzionerà correttamente e questo problema influirà sull'ambiente di produzione.

> [!IMPORTANT]
> Eliminare l'app b2c-extensions-app solo se si prevede l'eliminazione immediata del tenant. Se l'app rimane eliminata per più di 30 giorni, le informazioni utente andranno perse definitivamente.

## <a name="verifying-that-the-extensions-app-is-present"></a>Verifica della presenza dell'app Estensioni

Per verificare che l'app b2c-extensions-app sia presente:

1. Nel tenant di Azure AD B2C fare clic su **Tutti i servizi** nel menu di spostamento di sinistra.
1. Cercare e aprire **Registrazioni per l'app**.
1. Cercare un'app che inizia con **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Recuperare l'app Estensioni

Se l'app b2c-extensions-app è stata eliminata accidentalmente, sono disponibili 30 giorni per il recupero. È possibile ripristinare l'app usando l'API Graph:

1. Passare a [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Accedere al sito come amministratore globale per la directory di Azure AD B2C per cui si vuole ripristinare l'app eliminata. L'amministratore globale deve avere un indirizzo di posta elettronica simile al seguente: `username@{yourTenant}.onmicrosoft.com`.
1. Emettere una richiesta HTTP GET per l'URL `https://graph.windows.net/myorganization/deletedApplications` con api-version=1.6. Questa operazione consente di elencare tutte le applicazioni eliminate negli ultimi 30 giorni.
1. Trovare l'applicazione nell'elenco in cui il nome inizia con ' B2C-Extensions-app ' e copiarne il `objectid` valore della proprietà.
1. Emettere una richiesta HTTP POST per l'URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Sostituire la parte `{OBJECTID}` dell'URL con il valore `objectid` del passaggio precedente.

Dovrebbe essere possibile [visualizzare l'app ripristinata](#verifying-that-the-extensions-app-is-present) nel portale di Azure.

> [!NOTE]
> Un'applicazione può essere ripristinata solo se è stata eliminata negli ultimi 30 giorni. Se sono trascorsi più di 30 giorni, i dati andranno persi definitivamente. Per ottenere assistenza, inviare un ticket al supporto tecnico.
