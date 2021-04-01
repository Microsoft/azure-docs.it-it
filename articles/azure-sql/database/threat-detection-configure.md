---
title: Configurare Advanced Threat Protection
description: Advanced Threat Protection rileva le attività anomale del database che indicano potenziali minacce alla sicurezza per il database nel database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 1425003c718ca52c0bea712e9d25cd3e4c035cf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453958"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Configurare Advanced Threat Protection per il database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Advanced Threat Protection](threat-detection-overview.md) per il database SQL di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Advanced Threat Protection è in grado di identificare **potenziali attacchi SQL injection**, **accesso da posizioni insolite o Data Center**, **accesso da un'applicazione principale o potenzialmente dannosa** e **forza bruta per le credenziali SQL** . per ulteriori informazioni, vedere gli [avvisi di Advanced Threat Protection](threat-detection-overview.md#alerts).

È possibile ricevere notifiche sulle minacce rilevate tramite [notifiche di posta elettronica](threat-detection-overview.md#explore-detection-of-a-suspicious-event) o [portale di Azure](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](threat-detection-overview.md) fa parte dell'offerta [Azure Defender for SQL](azure-defender-for-sql.md) , un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. È possibile accedere a Advanced Threat Protection e gestirlo tramite il portale di Azure Defender centrale per SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurare Advanced Threat Protection nella portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare alla pagina di configurazione del server che si desidera proteggere. In Impostazioni sicurezza selezionare **Centro sicurezza**.
3. Nella pagina di configurazione di **Azure Defender per SQL** :

   - Abilitare **Azure Defender per SQL** nel server.
   - Nelle **impostazioni di Advanced Threat Protection**, fornire l'elenco dei messaggi di posta elettronica per ricevere gli avvisi di sicurezza al rilevamento di attività di database anomale nella casella di testo **Invia avvisi a** .
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="configurare Advanced Threat Protection":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurare Advanced Threat Protection tramite PowerShell

Per un esempio di script, vedere [configurare il controllo e la protezione avanzata dalle minacce tramite PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Advanced Threat Protection](threat-detection-overview.md).
- Altre informazioni sulla [protezione avanzata dalle minacce in SQL istanza gestita](../managed-instance/threat-detection-configure.md).  
- Vedere altre informazioni su [Azure Defender per SQL](azure-defender-for-sql.md).
- Altre informazioni su [Controllo](../../azure-sql/database/auditing-overview.md)
- Scopri di più sul [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)
- Per ulteriori informazioni sui prezzi, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/)