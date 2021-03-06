---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ca060e75e50a3e2327fc0516c3cfc9550afbf90f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582183"
---
1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare `Get-HcsApplianceInfo` per ottenere le informazioni per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Ecco una tabella che riepiloga alcune informazioni importanti sul dispositivo:

    | Parametro | Descrizione |
    |-----------|-------------|
    | FriendlyName                   | Nome descrittivo del dispositivo configurato tramite l'interfaccia utente Web locale durante la distribuzione del dispositivo. Il nome descrittivo predefinito è il numero di serie del dispositivo.  |
    | SerialNumber                   | Il numero di serie del dispositivo è un numero univoco assegnato alla Factory.                                                                             |
    | Modello                          | Modello per il dispositivo. Il modello è virtuale per Data Box Gateway.                   |
    | FriendlySoftwareVersion        | Stringa descrittiva che corrisponde alla versione del software del dispositivo. Per un sistema che esegue l'anteprima, la versione del software descrittivo sarà Data Box Edge 1902. |
    | HcsVersion                     | Versione del software HCS in esecuzione nel dispositivo. Ad esempio, la versione del software HCS corrispondente a Data Box Edge 1902 è 1.4.771.324.            |
    | LocalCapacityInMb              | Capacità locale totale del dispositivo in megabit.                                                                                                        |
    | IsRegistered                   | Questo valore indica se il dispositivo è attivato con il servizio.                                                                                         |


