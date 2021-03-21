---
title: Gestire i dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come gestire i dispositivi nell'applicazione Azure IoT Central come operatore. Informazioni su come gestire singoli dispositivi ed eseguire l'importazione e l'esportazione bulk dei dispositivi nell'applicazione.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2f0b6feea5e586c87191b22f42e3ab86e85ba7b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97032525"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gestire i dispositivi nell'applicazione Azure IoT Central

Questo articolo descrive come un operatore gestisce i dispositivi nell'applicazione IoT Central di Azure. Un operatore può:

- Usare la pagina **dispositivi** per visualizzare, aggiungere ed eliminare i dispositivi connessi all'applicazione IoT Central di Azure.
- Importare ed esportare in blocco i dispositivi.
- Mantenere aggiornato un inventario di dispositivi.
- Per aggiornare i metadati del dispositivo, modificare i valori archiviati nelle proprietà del dispositivo dalle viste.
- Controllare il comportamento dei dispositivi aggiornando un'impostazione in un dispositivo specifico dalle visualizzazioni.

Per informazioni su come gestire gruppi di dispositivi personalizzati, vedere [esercitazione: usare i gruppi di dispositivi per analizzare i dati di telemetria del dispositivo](tutorial-use-device-groups.md).

## <a name="view-your-devices"></a>Visualizzare i dispositivi

Per visualizzare un singolo dispositivo:

1. Scegliere **dispositivi** nel riquadro sinistro. Qui viene visualizzato un elenco di tutti i dispositivi e dei modelli di dispositivo.

1. Scegliere un modello di dispositivo.

1. Nel riquadro di destra della pagina **dispositivi** viene visualizzato un elenco di dispositivi creati da tale modello di dispositivo. Scegliere un singolo dispositivo per visualizzare la pagina dei dettagli dispositivo per questo dispositivo:

    ![Pagina dei dettagli dispositivo](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Aggiungere un dispositivo

Per aggiungere un dispositivo all'applicazione Azure IoT Central:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo da cui si vuole creare un dispositivo.

1. Scegliere + **New** (Nuovo).

1. Attivare **o** **disattivare** l'interruttore **simulato** . Un dispositivo reale indica un dispositivo fisico che si connette a un'applicazione Azure IoT Central. Un dispositivo simulato contiene dati di esempio generati automaticamente da Azure IoT Central.

1. Selezionare **Crea**.

1. Questo dispositivo viene ora visualizzato nell'elenco dei dispositivi per questo modello. Selezionare il dispositivo per visualizzare la pagina dei dettagli del dispositivo che contiene tutte le visualizzazioni del dispositivo.

## <a name="import-devices"></a>Importare dispositivi

Per connettere un numero elevato di dispositivi all'applicazione, è possibile importare in blocco i dispositivi da un file CSV. Il file CSV deve includere le colonne e le intestazioni seguenti:

* **IOTC_DeviceID** : l'ID del dispositivo può contenere lettere, numeri e il `-` carattere.
* **IOTC_DeviceName**: questa colonna è facoltativa.

Per registrare in blocco i dispositivi nell'applicazione:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Nel riquadro di sinistra scegliere il modello di dispositivo per il quale si vuole creare in blocco i dispositivi.

    > [!NOTE]
    > Se non si dispone ancora di un modello di dispositivo, è possibile importare **i dispositivi in tutti i dispositivi** e registrarli senza un modello. Dopo aver importato i dispositivi, è possibile eseguirne la migrazione a un modello.

1. Selezionare **Importa**.

    ![Azione di importazione](./media/howto-manage-devices/bulkimport1a.png)


1. Selezionare il file CSV contenente l'elenco di ID dispositivo da importare.

1. Al termine del caricamento del file viene avviata l'importazione dei dispositivi. È possibile tenere traccia dello stato dell'importazione nel pannello operazioni del dispositivo. Questo pannello viene visualizzato automaticamente dopo l'avvio dell'importazione oppure è possibile accedervi tramite l'icona a campana nell'angolo superiore destro.

1. Al termine dell'importazione, nel pannello operazioni dispositivo viene visualizzato un messaggio di operazione completata.

    ![Importazione riuscita](./media/howto-manage-devices/bulkimport3a.png)

Se l'operazione di importazione del dispositivo non riesce, viene visualizzato un messaggio di errore nel pannello operazioni del dispositivo. Verrà generato un file di log scaricabile in cui vengono acquisiti tutti gli errori.

## <a name="migrate-devices-to-a-template"></a>Eseguire la migrazione di dispositivi a un modello

Se si registrano i dispositivi avviando l'importazione in **tutti i dispositivi**, i dispositivi vengono creati senza alcuna associazione di modelli di dispositivo. I dispositivi devono essere associati a un modello per esplorare i dati e altri dettagli relativi al dispositivo. Per associare i dispositivi a un modello attenersi a questa procedura:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Nel pannello sinistro scegliere **tutti i dispositivi**:

    ![Dispositivi non associati](./media/howto-manage-devices/unassociateddevices1a.png)

1. Usare il filtro sulla griglia per determinare se il valore nella colonna **modello di dispositivo** non è **associato** ad alcun dispositivo.

1. Selezionare i dispositivi da associare a un modello:

1. Selezionare **Esegui migrazione**:

    ![Associare i dispositivi](./media/howto-manage-devices/unassociateddevices2a.png)

1. Scegliere il modello dall'elenco dei modelli disponibili e selezionare **migrazione**.

1. I dispositivi selezionati verranno associati al modello di dispositivo scelto.

## <a name="export-devices"></a>Esportare dispositivi

Per connettere un dispositivo reale a IoT Central, è necessario disporre della stringa di connessione corrispondente. È possibile esportare i dettagli del dispositivo in blocco per ottenere le informazioni necessarie per creare stringhe di connessione del dispositivo. Il processo di esportazione crea un file CSV con l'identità del dispositivo, il nome del dispositivo e le chiavi per tutti i dispositivi selezionati.

Per esportare in blocco i dispositivi dall'applicazione:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Nel riquadro sinistro scegliere il modello di dispositivo da cui si desidera esportare i dispositivi.

1. Selezionare i dispositivi che si desidera esportare, quindi selezionare l'azione **Esporta** .

    ![Esportazione](./media/howto-manage-devices/export1a.png)

1. Verrà avviato il processo di esportazione. È possibile tenere traccia dello stato usando il pannello operazioni del dispositivo.

1. Al termine dell'esportazione verrà visualizzato un messaggio di operazione completata insieme a un collegamento per scaricare il file generato.

1. Selezionare il collegamento **Scarica file** per scaricare il file in una cartella locale sul disco.

    ![Esportazione riuscita](./media/howto-manage-devices/export2a.png)

1. Il file CSV esportato contiene le colonne relative all'ID di dispositivo, al nome di dispositivo, alle chiavi di dispositivo e alle identificazioni personali del certificato X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Per altre informazioni sulle stringhe di connessione e sulla connessione di dispositivi reali all'applicazione IoT Central, vedere [connettività dei dispositivi in Azure IOT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Eliminare un dispositivo

Per eliminare un dispositivo simulato o reale dall'applicazione Azure IoT Central:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo del dispositivo da eliminare.

1. Usare gli strumenti di filtro per filtrare e cercare i dispositivi. Selezionare la casella accanto ai dispositivi da eliminare.

1. Scegliere **Elimina**. È possibile tenere traccia dello stato di questa eliminazione nel pannello operazioni del dispositivo.

## <a name="change-a-property"></a>Modificare una proprietà

Le proprietà del cloud sono i metadati del dispositivo associati al dispositivo, ad esempio la città e il numero di serie. Le proprietà del cloud esistono solo nell'applicazione IoT Central e non sono sincronizzate con i dispositivi. Le proprietà scrivibili controllano il comportamento di un dispositivo e consentono di impostare lo stato di un dispositivo in modalità remota, ad esempio impostando la temperatura di destinazione di un dispositivo termostato.  Le proprietà del dispositivo sono impostate dal dispositivo e sono di sola lettura all'interno IoT Central. È possibile visualizzare e aggiornare le proprietà nelle visualizzazioni dei **Dettagli del dispositivo** .

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo del dispositivo di cui si desidera modificare le proprietà e selezionare il dispositivo di destinazione.

1. Scegliere la vista che contiene le proprietà per il dispositivo. questa visualizzazione consente di immettere valori e selezionare **Salva** nella parte superiore della pagina. Qui vengono visualizzate le proprietà del dispositivo e i relativi valori correnti. Le proprietà del cloud e le proprietà scrivibili hanno campi modificabili, mentre le proprietà del dispositivo sono di sola lettura. Per le proprietà scrivibili, è possibile visualizzare lo stato di sincronizzazione nella parte inferiore del campo. 

1. Modificare le proprietà con i valori necessari. È possibile modificare più proprietà alla volta e aggiornarle tutte contemporaneamente.

1. Scegliere **Salva**. Se sono state salvate proprietà scrivibili, i valori vengono inviati al dispositivo. Quando il dispositivo conferma la modifica della proprietà scrivibile, lo stato torna a **sincronizzato**. Se è stata salvata una proprietà cloud, il valore viene aggiornato.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire i dispositivi nell'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come[configurare le regole](howto-configure-rules.md) per i dispositivi.
