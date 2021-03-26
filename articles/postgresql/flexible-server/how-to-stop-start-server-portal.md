---
title: Stop/Start-portale di Azure-server flessibile per database di Azure per PostgreSQL
description: Questo articolo descrive come arrestare/avviare le operazioni nel database di Azure per PostgreSQL tramite il portale di Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e28243f5919c355e6ad0d4998ff8388a56e3d0ca
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607369"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Arrestare/avviare un database di Azure per PostgreSQL-server flessibile (anteprima)

> [!IMPORTANT]
> Database di Azure per PostgreSQL: il server flessibile è attualmente in versione di anteprima.

In questo articolo vengono fornite istruzioni dettagliate per arrestare e avviare un server flessibile.

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server flessibile per database di Azure per PostgreSQL.

## <a name="stop-a-running-server"></a>Arrestare un server in esecuzione

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile che si desidera arrestare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Arresta** sulla barra degli strumenti.

> [!NOTE]
> Una volta arrestato il server, non sono disponibili altre operazioni di gestione per il server flessibile.

Si noti che i server arrestati verranno riavviati automaticamente dopo sette giorni. Eventuali aggiornamenti di manutenzione in sospeso verranno applicati quando il server viene avviato la volta successiva.

## <a name="start-a-stopped-server"></a>Avviare un server arrestato

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server flessibile che si desidera avviare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Avvia** sulla barra degli strumenti.

> [!NOTE]
> Una volta avviato il server, tutte le operazioni di gestione sono ora disponibili per il server flessibile.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [Opzioni di calcolo e archiviazione nel database di Azure per PostgreSQL flexible server](./concepts-compute-storage.md).
