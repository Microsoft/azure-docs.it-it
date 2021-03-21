---
title: Come arrestare il monitoraggio del cluster Azure e Red Hat OpenShift V4 | Microsoft Docs
description: Questo articolo descrive come arrestare il monitoraggio del cluster Azure Red Hat OpenShift e Red Hat OpenShift versione 4 con informazioni sul contenitore.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731800"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Come arrestare il monitoraggio del cluster Azure e Red Hat OpenShift V4

Dopo aver abilitato il monitoraggio del cluster Azure Red Hat OpenShift e Red Hat OpenShift versione 4. x, è possibile arrestare il monitoraggio del cluster con informazioni dettagliate sul contenitore se si decide che non si vuole più monitorarlo. Questo articolo illustra come eseguire questa operazione.  

## <a name="how-to-stop-monitoring-using-helm"></a>Come arrestare il monitoraggio con Helm

1. Per identificare prima di tutto la versione del grafico Helm di container Insights installata nel cluster, eseguire il comando Helm seguente.

    ```
    helm list
    ```

    L'output sarà simile al seguente:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-container-Release-1* rappresenta il rilascio del grafico Helm per il contenitore Insights.

2. Per eliminare la versione del grafico, eseguire il comando Helm seguente.

    `helm delete <releaseName>`

    Esempio:

    `helm delete azmon-containers-release-1`

    La versione verrà rimossa dal cluster. È possibile verificare eseguendo il `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Poiché Helm tiene traccia dei rilasci anche dopo averli eliminati, è possibile controllare la cronologia di un cluster e persino annullare l'eliminazione di una versione con `helm rollback` .

## <a name="next-steps"></a>Passaggi successivi

Se l'area di lavoro Log Analytics è stata creata solo per supportare il monitoraggio del cluster e non è più necessaria, è necessario eliminarla manualmente. Se non si ha familiarità con la modalità di eliminazione di un'area di lavoro, vedere [eliminare un'area di lavoro di Azure log Analytics](../logs/delete-workspace.md).