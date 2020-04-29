---
title: Configurare il bilanciamento del carico per desktop virtuali Windows-Azure
description: Come configurare il metodo di bilanciamento del carico per un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128301"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows

La configurazione del metodo di bilanciamento del carico per un pool host consente di modificare l'ambiente di desktop virtuale Windows in base alle proprie esigenze.

>[!NOTE]
> Questa operazione non si applica a un pool host del desktop persistente perché gli utenti hanno sempre un mapping 1:1 a un host sessione nel pool host.

## <a name="configure-breadth-first-load-balancing"></a>Configurare la larghezza-primo bilanciamento del carico

Il bilanciamento del carico con un primo respiro è la configurazione predefinita per i nuovi pool host non permanenti. Il bilanciamento del carico per la prima volta distribuisce le nuove sessioni utente in tutti gli host di sessione disponibili nel pool di host. Quando si configura il bilanciamento del carico per il primo livello, è possibile impostare un limite massimo di sessioni per ogni host sessione nel pool host.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows di PowerShell](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto. Successivamente, eseguire il cmdlet seguente per accedere al proprio account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Per configurare un pool di host per eseguire il bilanciamento del carico con ampiezza del primo senza modificare il limite massimo della sessione, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Per configurare un pool di host per eseguire il bilanciamento del carico con ampiezza e per l'uso di un nuovo limite massimo di sessioni, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurare la profondità-primo bilanciamento del carico

Il bilanciamento del carico depth-first distribuisce le nuove sessioni utente a un host di sessione disponibile con il numero massimo di connessioni, ma non ha raggiunto la soglia massima di limite della sessione. Quando si configura il bilanciamento del carico per il primo livello di profondità, è **necessario** impostare un limite massimo di sessioni per host sessione nel pool host.

Per configurare un pool di host per eseguire il bilanciamento del carico per il primo livello di profondità, eseguire il cmdlet PowerShell seguente:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
