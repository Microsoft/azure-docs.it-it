---
title: Connettere AWS CloudTrail ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di AWS CloudTrail ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588655"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Connettere Sentinel di Azure a AWS CloudTrail

Usare il connettore AWS per eseguire lo streaming di tutti gli eventi di AWS CloudTrail in Sentinel di Azure. Questo processo di connessione delega l'accesso ad Azure Sentinel ai log delle risorse di AWS, creando una relazione di trust tra AWS CloudTrail e Azure Sentinel. Questa operazione viene eseguita in AWS creando un ruolo che concede l'autorizzazione ad Azure Sentinel per accedere ai log di AWS.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre dell'autorizzazione di scrittura per l'area di lavoro di Azure Sentinel.

> [!NOTE]
> Azure Sentinel raccoglie gli eventi CloudTrail da tutte le aree. Si consiglia di non trasmettere gli eventi da un'area a un'altra.

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



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere AWS CloudTrail ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.

