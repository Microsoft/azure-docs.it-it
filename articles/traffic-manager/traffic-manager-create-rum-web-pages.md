---
title: Misurazioni utente reale con le pagine Web-gestione traffico di Azure
description: Questo articolo illustra come configurare le pagine Web per inviare misurazioni utente reale a gestione traffico di Azure.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: f297057549263cd62783778b363665bfb4368c91
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711647"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Come inviare le Misurazioni utente reale a Gestione traffico di Azure usando le pagine Web

È possibile configurare le pagine Web per inviare le Misurazioni utente reale a Gestione traffico ottenendo una chiave delle Misurazioni utente reale (RUM) e incorporando il codice generato nella pagina Web.

## <a name="obtain-a-real-user-measurements-key"></a>Ottenere una chiave delle Misurazioni utente reale

Le misurazioni prese e inviate a Gestione traffico dall'applicazione client vengono identificate dal servizio usando una stringa univoca, chiamata **chiave delle Misurazioni utente reale (RUM)**. È possibile ottenere una chiave RUM attraverso il portale di Azure, un'API REST, oppure tramite PowerShell o l'interfaccia della riga di comando di Azure.

Per ottenere la chiave RUM tramite il portale di Azure:
1. Da un browser accedere al portale di Azure. Se non si dispone già di un account, è possibile iscriversi per ottenere una versione di valutazione gratuita della durata di un mese.
2. Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello del profilo di Gestione traffico fare clic su **Misurazioni utente reale** in **Impostazioni**.
4. Fare clic su **Genera chiave** per creare una nuova chiave RUM.
 
   ![Generare una chiave delle Misurazioni utente reale](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Generazione della chiave per le Misurazioni utente reale**

5. Il pannello visualizzato include ora la chiave RUM generata e un frammento di codice JavaScript che deve essere incorporato nella pagina HTML.
 
    ![Codice JavaScript per la chiave delle Misurazioni utente reale](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Figura 2: Chiave delle Misurazioni utente reale e JavaScript per le misurazioni**
 
6. Fare clic sul pulsante **Copia** per copiare il codice JavaScript. 

>[!IMPORTANT]
> Usare il codice JavaScript generato per eseguire correttamente la funzionalità Misurazioni utente reale. Le modifiche a questo script o agli script usati da Misurazioni utente reale possono causare un comportamento imprevedibile.

## <a name="embed-the-code-to-an-html-web-page"></a>Incorporare il codice in una pagina Web HTML

Dopo aver ottenuto la chiave RUM, il passaggio successivo consiste nell'incorporare il codice JavaScript copiato in una pagina HTML visitata dagli utenti finali. La modifica dell'HTML può essere eseguita in molti modi e con diversi strumenti e flussi di lavoro. In questo esempio viene illustrato come aggiornare una pagina HTML per aggiungere lo script. È possibile usare queste linee guida per adattarlo al flusso di lavoro della gestione origine HTML.

1.  Aprire il file HTML in un editor di testo
2.  Incollare il codice JavaScript copiato nel passaggio precedente nella sezione BODY del codice HTML (il codice copiato è sulle righe 8 e 9, vedere la figura 3).
 
    ![Incorporare il codice JavaScript nella pagina Web per le Misurazioni utente reale](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Figura 3: HTML semplice con codice JavaScript delle Misurazioni utente reale incorporato**

3.  Salvare il file HTML e ospitarlo in un server Web connesso a Internet. 
4. La volta successiva che verrà eseguito il rendering di questa pagina su un Web browser, il codice JavaScript a cui fa riferimento verrà scaricato e lo script eseguirà le operazioni di misurazione e reporting.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Misurazioni utente reale](traffic-manager-rum-overview.md)
- Informazioni [sul funzionamento di gestione traffico](traffic-manager-overview.md)
- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico
- Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md)

