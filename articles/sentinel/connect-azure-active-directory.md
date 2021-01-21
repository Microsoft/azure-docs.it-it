---
title: Connettere i dati Azure Active Directory ad Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere dati da Azure Active Directory e trasmettere Azure AD log di accesso e log di controllo in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 9700e5d9179f7c1e33b2371eea89be9bb1c8d08f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621363"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Connettere i dati da Azure Active Directory (Azure AD)

È possibile usare il connettore integrato di Sentinel di Azure per raccogliere i dati da [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e trasmetterli in Azure Sentinel. Il connettore consente di eseguire lo streaming di [log di accesso](../active-directory/reports-monitoring/concept-sign-ins.md) e [log di controllo](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere una sottoscrizione di [Azure ad Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) per inserire i log di accesso in Sentinel di Azure. Potrebbero essere applicati addebiti aggiuntivi per Gigabyte per monitoraggio di Azure (Log Analytics) e Sentinel di Azure.

- All'utente deve essere assegnato il ruolo Collaboratore sentinella di Azure nell'area di lavoro.

- All'utente deve essere assegnato il ruolo di amministratore globale o amministratore della sicurezza nel tenant da cui si desidera trasmettere i log.

- Per poter visualizzare lo stato della connessione, l'utente deve disporre delle autorizzazioni di lettura e scrittura per le impostazioni di diagnostica Azure AD. 

## <a name="connect-to-azure-active-directory"></a>Connessione ad Azure Active Directory

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Dalla raccolta Data Connector selezionare **Azure Active Directory** e quindi fare clic su **Apri connettore pagina**.

1. Selezionare le caselle di controllo accanto ai log che si vuole trasmettere in Azure Sentinel e fare clic su **Connetti**.

1. È possibile scegliere se si desidera che gli avvisi Azure AD generino automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti** selezionare **Abilita** per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

1. Per utilizzare lo schema pertinente in Log Analytics per l'esecuzione di query sugli avvisi Azure AD, digitare `SigninLogs` o `AuditLogs` nella finestra query.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Active Directory ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
