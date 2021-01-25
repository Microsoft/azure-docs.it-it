---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a6a01b3a71a7e391c680e6c9547ccd9649dc0e89
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605092"
---
Questa sezione illustra come creare un hub IoT usando il [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella home page di Azure selezionare il pulsante **+ Crea una risorsa** e quindi immettere *Hub IoT* nel campo **Cerca nel Marketplace**.

1. Nei risultati della ricerca selezionare **Hub IoT** e quindi fare clic su **Crea**.

1. Nella scheda **Informazioni di base** completare i campi come indicato di seguito:

   - **Sottoscrizione** Selezionare la sottoscrizione da usare per l'hub.

   - **Gruppo di risorse**: selezionare un gruppo di risorse o crearne uno nuovo. Per crearne uno nuovo, fare clic su **Crea nuovo** e specificare il nome da usare. Per usare un gruppo di risorse esistente, selezionarlo. Per altre informazioni, vedere l'articolo su come [gestire gruppi di risorse di Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Area**: selezionare l'area in cui si vuole collocare l'hub. Selezionare la località più vicina. Alcune funzionalità, ad esempio i [flussi dei dispositivi dell'hub IoT](../articles/iot-hub/iot-hub-device-streams-overview.md), sono disponibili solo in aree specifiche. Per queste funzionalità limitate, è necessario selezionare una delle aree supportate.

   - **Nome hub IoT**: immettere un nome per l'hub. Il nome deve essere univoco a livello globale.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Creare un hub nel portale di Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Selezionare **Avanti: Rete** per continuare a creare l'hub.

   Scegliere gli endpoint che possono connettersi all'hub IoT. È possibile selezionare l'impostazione predefinita **Endpoint pubblico (tutte le reti)** oppure scegliere **Endpoint pubblico (intervalli IP selezionati)** o **Endpoint privato**. Accettare l'impostazione predefinita per questo esempio.

   ![Scegliere gli endpoint che possono connettersi](./media/iot-hub-include-create-hub/iot-hub-create-network-screen.png)

1. Selezionare **Avanti: Gestione** per continuare a creare l'hub.

    ![Configurare le dimensioni e la scalabilità per un nuovo hub con il portale di Azure](./media/iot-hub-include-create-hub/iot-hub-management-screen.png)

    In questa schermata è possibile accettare le impostazioni predefinite. Se si preferisce, è possibile modificare uno dei campi seguenti:

    - **Piano tariffario e livello di scalabilità**: il piano e il livello selezionati. È possibile scegliere tra livelli diversi a seconda del numero di funzionalità che si desidera e della quantità di messaggi che si inviano al giorno tramite la soluzione. Il livello gratuito è utilizzabile a scopo di test e valutazione. Consente la connessione di 500 dispositivi all'hub e un massimo di 8.000 messaggi al giorno. Per ogni sottoscrizione di Azure è possibile creare un solo hub IoT nel livello gratuito.

      Se si sta eseguendo un Avvio rapido per flussi dei dispositivi dell'hub IoT, selezionare il livello gratuito.

    - **Unità di hub IoT**: Il numero di messaggi consentiti per unità al giorno dipende dal piano tariffario dell'hub. Se ad esempio si vuole che l'hub supporti 700.000 messaggi in ingresso, selezionare due unità del piano S1.
    Per informazioni dettagliate sulle altre opzioni relative al livello, vedere [Scegliere il livello più adatto di hub IoT](../articles/iot-hub/iot-hub-scaling.md).

    - **Defender per IoT**: attivare questa opzione per aggiungere un livello aggiuntivo di protezione dalle minacce a IoT e ai dispositivi. Questa opzione non è disponibile per gli hub del livello gratuito. Per altre informazioni su questa funzionalità, vedere [Centro sicurezza di Azure per IoT](/azure/asc-for-iot/).

    - **Impostazioni avanzate** > **Partizioni da dispositivo a cloud**: questa proprietà associa i messaggi da dispositivo a cloud al numero di lettori simultanei di tali messaggi. La maggior parte degli hub richiede solo quattro partizioni.

1.  Selezionare **Avanti: Tag** per passare alla schermata successiva.

    I tag sono coppie nome/valore. È possibile assegnare lo stesso tag a più risorse e gruppi di risorse per classificare le risorse e consolidare la fatturazione. Per altre informazioni, vedere [Usare tag per organizzare le risorse di Azure](../articles/azure-resource-manager/management/tag-resources.md).

    ![Assegnare tag per l'hub con il portale di Azure](./media/iot-hub-include-create-hub/iot-hub-create-tags.png)

1.  Selezionare **Avanti: Rivedi e crea** per rivedere le scelte effettuate. Viene visualizzata una schermata simile a questa, ma con i valori selezionati durante la creazione dell'hub. 

    ![Rivedere le informazioni per la creazione del nuovo hub](./media/iot-hub-include-create-hub/iot-hub-review-and-create.png)

1.  Fare clic su **Crea** per creare il nuovo hub. La creazione dell'hub richiede alcuni minuti.