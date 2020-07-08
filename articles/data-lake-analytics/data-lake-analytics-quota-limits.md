---
title: Modificare le quote e i limiti in Azure Data Lake Analytics
description: Informazioni su come modificare e aumentare le quote e i limiti negli account di Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75644716"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Modificare le quote e i limiti in Azure Data Lake Analytics

Informazioni su come modificare e aumentare la quota e i limiti negli account di Azure Data Lake Analytics. Conoscere questi limiti consentirà di comprendere il comportamento del processo U-SQL. Tutti i limiti di quota sono flessibili, quindi è possibile aumentare i limiti massimi contattando il supporto di Azure.

## <a name="azure-subscriptions-limits"></a>Limiti delle sottoscrizioni Azure

**Numero massimo di account di ADLA per sottoscrizione per area:** 5

Se si tenta di creare un sesto account di ADLA, si ottiene un messaggio di errore che indica che è stato raggiunto il numero massimo di account di Data Lake Analytics consentiti (5) nell'area sotto il nome della sottoscrizione.

Per superare questo limite, è possibile provare queste opzioni:
* Scegliere un'altra area, se appropriato
* Contattare il supporto di Azure [aprendo un ticket di supporto](#increase-maximum-quota-limits) per richiedere un aumento della quota.

## <a name="default-adla-account-limits"></a>Limiti predefiniti degli account di ADLA

**Numero massimo di unità di analisi (AUS) per account:** 250, valore predefinito 32

Questo è il numero massimo di unità di analisi che si possono eseguire contemporaneamente nell'account. Se il numero totale di unità di analisi dei processi in esecuzione supera questo limite, i processi più recenti vengono accodati automaticamente. Ad esempio:

* Se si ha un solo processo in esecuzione con 32 unità di analisi, quando si invia un secondo processo, esso rimarrà in attesa nella coda finché il primo non viene completato.
* Se si dispone di quattro processi in esecuzione e ognuno usa 8 unità di analisi, quando si invia un quinto processo che richiede 8 unità di analisi questo attende nella coda dei processi fino a quando non sono presenti 8 unità di analisi disponibili.

    ![Pagina limiti e quota Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Numero massimo di unità di analisi (AUS) per processo:** 250, valore predefinito 32

Questo è il numero massimo di unità di estensione che è possibile assegnare a ogni singolo processo nell'account. I processi assegnati oltre questo limite verranno rifiutati, a meno che l'utente che invia il processo non disponga di un criterio di calcolo impostato (limite di invio processi) che fornisce altre unità di analisi per ciascun processo. Il limite superiore di questo valore è il limite di unità di analisi per l'account.

**Numero massimo di processi simultanei di U-SQL per ogni account:** 20

Questo è il numero massimo di processi che si possono eseguire contemporaneamente nell'account. Al di sopra di questo valore, i processi più recenti vengono accodati automaticamente.

## <a name="adjust-adla-account-limits"></a>Regolare i limiti degli account di ADLA

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere un account ADLA esistente.
3. Scegliere **Proprietà**.
4. Modificare i valori per **Numero massimo di AU**, **Maximum number of running jobs** (Numero massimo di processi in esecuzione) e **Limiti di invio del processo** in base alle esigenze.

## <a name="increase-maximum-quota-limits"></a>Aumentare i limiti massimi di quota

È possibile trovare altre informazioni sui limiti di Azure nella [documentazione sui limiti specifici del servizio di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Aprire una richiesta di supporto nel portale di Azure.

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selezionare il tipo di problema **Quota**.
3. Selezionare la **sottoscrizione** (assicurarsi che non sia una sottoscrizione di prova).
4. Selezionare il tipo di quota **Data Lake Analytics**.

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Nella pagina del problema indicare la richiesta di aumento del limite con i **dettagli** del motivo per cui viene richiesta la capacità aggiuntiva.

    ![Pagina del portale di Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verificare le informazioni di contatto e creare la richiesta di supporto.

Microsoft esamina la richiesta e tenta di eseguire l’operazione richiesta appena possibile.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorare e risolvere i problemi Azure Data Lake Analytics processi utilizzando portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
