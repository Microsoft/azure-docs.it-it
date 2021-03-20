---
title: 'Guida introduttiva: Scaricare il report di controllo con il portale di Azure | Microsoft Docs'
description: Informazioni su come scaricare il report di controllo con il portale di Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
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
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "68989689"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Guida introduttiva: scaricare il report di controllo con il portale di Azure

In questa guida introduttiva si apprenderà come scaricare un file CSV dei log di controllo per il tenant per le ultime 24 ore. Dal portale di Azure è possibile scaricare fino a 250.000 record. Per impostazione predefinita, vengono scaricati gli ultimi 250.000 record, ordinati partendo dai record più recenti. 

## <a name="prerequisites"></a>Prerequisiti

È necessario:

* Un tenant di Azure Active Directory. 
* Un utente con ruolo di **Amministratore della sicurezza**, **Ruolo con autorizzazioni di lettura per la sicurezza** o **Amministratore globale** per il tenant. Inoltre, tutti gli utenti nel tenant possono accedere ai propri log di controllo.

## <a name="quickstart-download-an-audit-report"></a>Guida introduttiva: Scaricare un report di controllo

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** nel riquadro di spostamento sinistro e usare il pulsante **Cambia directory** per selezionare la directory attiva.
3. Nel dashboard selezionare **Azure Active Directory** e quindi selezionare **Log di controllo**. 
4. Scegliere **Ultime 24 ore** nell'elenco a discesa del filtro **Intervallo di date** e selezionare **Applica** per visualizzare i log di controllo delle ultime 24 ore. 
5. Selezionare il pulsante **Download**, selezionare **CSV** come formato file e specificare un nome file per scaricare un file CSV contenente i record filtrati. 

![Report](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Passaggi successivi

* [Report delle attività di accesso nel portale di Azure Active Directory](concept-sign-ins.md)
* [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md)
* [Latenze dei report di Azure Active Directory](reference-reports-latencies.md)
