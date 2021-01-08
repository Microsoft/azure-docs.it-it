---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 81d2804d99896200ea6f68592ea168112e172c20
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762607"
---
Selezionare **Compila**. Nel riquadro visualizzato selezionare una cartella in cui esportare il progetto Xcode.

   Al termine dell'esportazione, viene visualizzata una cartella contenente il progetto Xcode esportato.

   > [!NOTE]
   > Se viene visualizzata una finestra con un messaggio che chiede se sostituire o accodare, è consigliabile selezionare **Accoda** perché è la procedura più veloce. Selezionare **Sostituisci** solo se si cambiano gli asset della scena, ad esempio se si aggiungono, rimuovono o cambiano le relazioni padre/figlio oppure se si aggiungono, rimuovono o cambiano le proprietà. Se si stanno solo apportando modifiche al codice sorgente, l'operazione **Accoda** dovrebbe essere sufficiente.

## <a name="open-the-xcode-project"></a>Aprire il progetto Xcode

Ora è possibile aprire il progetto `Unity-iPhone.xcodeproj` in Xcode. 

È possibile avviare Xcode e aprire il progetto `Unity-iPhone.xcodeproj` esportato oppure avviare il progetto in Xcode eseguendo il comando seguente dalla posizione in cui è stato esportato:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Selezionare il nodo **Unity-iPhone** radice per visualizzare le impostazioni del progetto e selezionare la scheda **General** (Generale).

In **Informazioni sulla distribuzione** assicurarsi che la destinazione della distribuzione sia impostata su **iOS 11.0**.

Selezionare la scheda **Signing & Capabilities** (Firma e funzionalità) e assicurarsi che l'opzione **Automatically manage signing** (Gestisci automaticamente la firma) sia abilitata. Se non lo è, abilitarla e quindi reimpostare le impostazioni di compilazione selezionando **Enable Automatic** (Abilita automatico) nella finestra di dialogo visualizzata.

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuire l'app nel dispositivo iOS

Connettere il dispositivo iOS al Mac e impostare lo **schema attivo** sul dispositivo iOS.

   ![Screenshot del pulsante My iPhone per la selezione del dispositivo.](./media/spatial-anchors-unity/select-device.png)

Selezionare **Build and then run the current scheme** (Compila e quindi esegui lo schema corrente).

   ![Screenshot del pulsante freccia "Deploy and run".](./media/spatial-anchors-unity/deploy-run.png)
