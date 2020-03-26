---
title: 'Guida introduttiva: Scaricare un report relativo agli accessi con il portale di Azure | Microsoft Docs'
description: Informazioni su come scaricare un report relativo agli accessi con il portale di Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "68989663"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Guida introduttiva: Scaricare un report relativo agli accessi con il portale di Azure

In questa guida introduttiva si apprenderà come scaricare i dati di accesso per il tenant relativi alle ultime 24 ore. Dal portale di Azure è possibile scaricare fino a 250.000 record. Per impostazione predefinita, vengono scaricati gli ultimi 250.000 record, ordinati partendo dai record più recenti. 

## <a name="prerequisites"></a>Prerequisites

È necessario:

* Un tenant di Azure Active Directory con una licenza Premium per visualizzare il report delle attività di accesso. vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. Si noti che se i dati sulle attività non fossero disponibili prima dell'aggiornamento, saranno necessari un paio di giorni per visualizzare i dati nei report dopo aver eseguito l'aggiornamento a una licenza Premium.
* Un utente con ruolo di **Amministratore della sicurezza**, **Ruolo con autorizzazioni di lettura per la sicurezza**, **Ruolo con autorizzazioni di lettura per i report** o **Amministratore globale** per il tenant. Qualsiasi utente nel tenant, inoltre, può visualizzare i propri accessi.

## <a name="quickstart-download-a-sign-in-report"></a>Guida introduttiva: Scaricare un report relativo agli accessi

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** nel riquadro di spostamento sinistro e usare il pulsante **Cambia directory** per selezionare la directory attiva.
3. Nel dashboard selezionare **Azure Active Directory** e quindi selezionare **Accessi**. 
4. Scegliere **Ultime 24 ore** nell'elenco a discesa del filtro **Data** e selezionare **Applica** per visualizzare i dati di accesso delle ultime 24 ore. 
5. Selezionare il pulsante **Download**, selezionare **CSV** come formato file e specificare un nome file per scaricare un file CSV contenente i record filtrati. 

![Report](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Passaggi successivi

* [Report delle attività di accesso nel portale di Azure Active Directory](concept-sign-ins.md)
* [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
* [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
