---
title: Usare i segreti di Azure Key Vault nelle attività della pipeline
description: Informazioni su come recuperare le credenziali archiviate da Azure Key Vault e usarle durante data factory esecuzioni di pipeline.
author: ChrisLound
ms.author: chlound
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: d7e1133b8f3880c4c1616ef5ca955ed014348935
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383960"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Usare i segreti di Azure Key Vault nelle attività della pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È possibile archiviare le credenziali o i valori dei segreti in un Azure Key Vault e usarli durante l'esecuzione della pipeline per passare alle attività.

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità si basa sull'identità gestita data factory.  Scopri come funziona da [identità gestita per data factory](./data-factory-service-identity.md) e assicurati che i data factory ne abbiano uno associato.

## <a name="steps"></a>Passaggi

1. Aprire le proprietà del data factory e copiare il valore di ID applicazione identità gestita.

    ![Valore identità gestita](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Aprire i criteri di accesso di Key Vault e aggiungere le autorizzazioni di identità gestite per ottenere ed elencare i segreti.

    ![Screenshot che mostra la pagina "criteri di accesso" con l'azione "Aggiungi criteri di accesso" evidenziata.](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Criteri di accesso di Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Fare clic su **Aggiungi** e quindi su **Salva**.

3. Passare al segreto Key Vault e copiare l'identificatore del segreto.

    ![Identificatore del segreto](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Prendere nota dell'URI del segreto che si desidera ottenere durante l'esecuzione del data factory pipeline.

4. Nella pipeline Data Factory aggiungere una nuova attività Web e configurarla come indicato di seguito.  

    |Proprietà  |Valore  |
    |---------|---------|
    |Output sicuro     |Vero         |
    |URL     |[Valore dell'URI del segreto]? API-Version = 7.0         |
    |Metodo     |GET         |
    |Authentication     |Identità del servizio gestita         |
    |Risorsa        |https://vault.azure.net       |

    ![Attività Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > È necessario aggiungere **? API-Version = 7.0** alla fine dell'URI del segreto.  

    > [!CAUTION]
    > Impostare l'opzione di output sicuro su true per impedire che il valore del segreto venga registrato come testo normale.  In qualsiasi ulteriore attività in cui viene usato questo valore, l'opzione Input sicuro deve essere impostata su true.

5. Per usare il valore in un'altra attività, usare l'espressione di codice seguente **@activity (' Web1'). output. value**.

    ![Espressione codice](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare Azure Key Vault per archiviare le credenziali per gli archivi dati e i calcoli, vedere [archiviare le credenziali in Azure Key Vault](./store-credentials-in-key-vault.md)