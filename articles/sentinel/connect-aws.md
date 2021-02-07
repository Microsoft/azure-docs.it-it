---
title: Connettere AWS CloudTrail ad Azure Sentinel | Microsoft Docs
description: Usare AWS Connector per delegare l'accesso di Azure Sentinel ai log delle risorse di AWS, creando una relazione di trust tra AWS CloudTrail e Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 3d24fa1ea046a860feb40d09a6d0a57c79371450
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807598"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Connettere Sentinel di Azure a AWS CloudTrail

Usare il connettore AWS per eseguire lo streaming degli eventi di gestione CloudTrail di AWS in Azure Sentinel. Questo processo di connessione delega l'accesso ad Azure Sentinel ai log delle risorse di AWS, creando una relazione di trust tra AWS CloudTrail e Azure Sentinel. Questa operazione viene eseguita in AWS creando un ruolo che concede l'autorizzazione ad Azure Sentinel per accedere ai log di AWS.

> [!NOTE]
> AWS CloudTrail presenta [limitazioni predefinite](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) per l'API LookupEvents. Non consente più di due transazioni al secondo (TPS) per account e ogni query può restituire un massimo di 50 record. Di conseguenza, se un singolo tenant genera costantemente più di 100 record al secondo in un'area, si verificheranno backlog e ritardi nell'inserimento dati.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre dell'autorizzazione di scrittura per l'area di lavoro di Azure Sentinel.

> [!NOTE]
> Azure Sentinel raccoglie gli eventi di gestione CloudTrail da tutte le aree. Si consiglia di non trasmettere gli eventi da un'area a un'altra.

## <a name="connect-aws"></a>Connettere AWS 


1. In Sentinel di Azure selezionare **connettori dati** , quindi selezionare la riga **Amazon Web Services** nella tabella e nel riquadro AWS a destra fare clic su **Apri pagina connettore**.

1. Seguire le istruzioni in **configurazione** attenendosi alla procedura seguente.
 
1.  Nella console di Amazon Web Services, in **sicurezza, Identity & Compliance**, selezionare **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Scegliere **ruoli** e selezionare **Crea ruolo**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Scegliere **un altro account AWS.** Nel campo **ID account** immettere l'ID dell' **account Microsoft** (**123412341234**) che si trova nella pagina del connettore AWS nel portale di Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Verificare che sia selezionata l'opzione **Richiedi ID esterno** e quindi immettere l'ID esterno (ID area di lavoro) che si trova nella pagina del connettore AWS nel portale di Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  In **Connetti autorizzazioni criteri** selezionare **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Immettere un tag (facoltativo).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Immettere quindi un **nome di ruolo** e selezionare il pulsante **Crea ruolo** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Nell'elenco ruoli scegliere il ruolo creato.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copiare il **ruolo ARN**. Nel portale di Azure Sentinel, nella schermata connettore Amazon Web Services, incollarlo nel campo **ruolo da aggiungere** e fare clic su **Aggiungi**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Per usare lo schema pertinente in Log Analytics per gli eventi AWS, cercare **AWSCloudTrail**.

    > [!IMPORTANT]
    > A partire dal 1 ° dicembre 2020, il campo **AwsRequestId** è stato sostituito dal campo **AwsRequestId_** (si noti il carattere di sottolineatura aggiunto). I dati nel campo **AwsRequestId** precedente verranno conservati fino alla fine del periodo di conservazione dei dati specificato dal cliente.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere AWS CloudTrail ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
