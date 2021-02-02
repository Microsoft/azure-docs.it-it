---
title: Accedere alle app del proxy app di Azure AD in Teams | Microsoft Docs
description: Usare il proxy applicazione di Azure AD per accedere all'applicazione locale tramite Microsoft Teams.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c44716f045340022c871501609cf582015ba20f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256610"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Accedere alle applicazioni locali tramite Microsoft Teams

Il proxy di applicazione di Azure Active Directory offre accesso Single Sign-On ad applicazioni locali ovunque ci si trovi. Microsoft Teams semplifica la collaborazione da un'unica posizione. Integrandoli, gli utenti e possono collaborare in modo produttivo con i colleghi in ogni situazione.

Gli utenti possono aggiungere app cloud ai propri canali di Teams [usando le schede](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ma se un sito di SharePoint o uno strumento di pianificazione è ospitato in locale, il proxy di applicazione è la soluzione ideale. Gli utenti possono aggiungere ai canali le app pubblicate tramite il proxy di applicazione usando gli stessi URL esterni che usano sempre per accedere alle app in remoto e, poiché il proxy di applicazione esegue l'autenticazione tramite Azure Active Directory, gli utenti ottengono un'esperienza di accesso Single Sign-On.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Installare il connettore proxy applicazione e pubblicare l'app

Se necessario, [configurare il proxy di applicazione per il tenant e installare il connettore](application-proxy-add-on-premises-application.md), quindi [pubblicare l'applicazione locale](application-proxy-add-on-premises-application.md) per l'accesso remoto. Quando si pubblica l'app, prendere nota dell'URL esterno, in quanto servirà per aggiungere l'app a Teams.

Se le app sono già state pubblicate, ma non si ricordano gli URL esterni, cercarli nel [portale di Azure](https://portal.azure.com). Accedere, quindi passare a **Azure Active Directory**  >  **applicazioni aziendali**  >  **tutte le applicazioni** > selezionare l'app > **proxy di applicazione**.

## <a name="add-your-app-to-teams"></a>Aggiungere l'app a Teams

Dopo aver pubblicato l'app tramite il proxy di applicazione, informare gli utenti che possono aggiungerla come scheda direttamente nei canali di Teams e che l'app sarà quindi disponibile per l'uso a chiunque nel team. Far eseguire questi tre passaggi:

1. Passare al canale teams in cui si vuole aggiungere l'app e selezionare **+** per aggiungere una scheda.

   ![Selezionare + per aggiungere una scheda in teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Selezionare **Sito Web** dalle opzioni della scheda.

   ![Selezionare sito Web dalla schermata Aggiungi una scheda](./media/application-proxy-integrate-with-teams/website.png)

1. Assegnare un nome alla scheda e impostare l'URL su quello esterno del proxy applicazione.

   ![Assegnare un nome alla scheda e aggiungere l'URL esterno](./media/application-proxy-integrate-with-teams/tab-name-url.png)

La scheda, dopo che è stata aggiunta da un membro di un team, viene visualizzata da tutti nel canale. Tutti gli utenti che hanno accesso all'app ottengono l'accesso Single Sign-On con le credenziali usate per Microsoft Teams. Gli utenti che non hanno accesso all'app visualizzeranno la scheda in Teams, ma saranno bloccati finché non verranno loro concesse le autorizzazioni per l'app locale e la versione pubblicata nel portale di Azure dell'app.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [pubblicare siti di SharePoint locali](application-proxy-integrate-with-sharepoint-server.md) con il proxy di applicazione.
- Configurare le app per usare i [domini personalizzati](application-proxy-configure-custom-domain.md) per l'URL esterno.
