---
title: Eseguire l'aggiornamento di Azure Backup Agent
description: Queste informazioni spiegano perché è necessario aggiornare l'agente di backup di Azure e dove scaricare l'aggiornamento.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294131"
---
## <a name="upgrade-the-mars-agent"></a>Aggiornare l'agente MARS

Le versioni dell'agente Servizi di ripristino di Microsoft Azure (MARS) sotto 2.0.9083.0 hanno una dipendenza dal servizio di controllo di accesso di Azure. L'agente MARS è anche noto come agente di backup di Azure.

In 2018, Microsoft ha [deprecato il servizio di controllo di accesso di Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partire dal 19 marzo 2018, per tutte le versioni dell'agente MARS al di sotto di 2.0.9083.0 si verificheranno errori di backup. Per evitare o risolvere gli errori di backup, [aggiornare l'agente Mars alla versione più recente](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Per identificare i server che richiedono un aggiornamento dell'agente MARS, attenersi alla procedura descritta in [aggiornare l'agente di servizi di ripristino di Microsoft Azure (Mars)](../articles/backup/upgrade-mars-agent.md).

L'agente MARS viene usato per eseguire il backup di file e cartelle e dei dati dello stato del sistema in Azure. System Center DPM e server di Backup di Azure usano l'agente MARS per eseguire il backup dei dati in Azure.
