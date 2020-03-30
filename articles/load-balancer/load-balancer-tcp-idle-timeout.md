---
title: Configurare il timeout di inattività TCP del servizio di bilanciamento del carico in AzureConfigure load balancer TCP idle timeout in Azure
titleSuffix: Azure Load Balancer
description: In questo articolo viene illustrato come configurare il timeout di inattività TCP di Azure Load Balancer.In this article, learn how to configure Azure Load Balancer TCP idle timeout.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456829"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configurazione del timeout di inattività TCP di Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="tcp-idle-timeout"></a>Timeout di inattività TCP
Azure Load Balancer ha un'impostazione di timeout di inattività di 4 minuti a 30 minuti. Per impostazione predefinita, è impostato su 4 minuti. Se un periodo di inattività è più lungo del valore di timeout, non ci sono garanzie che venga mantenuta la sessione TCP o HTTP tra il client e il servizio cloud.

Quando la connessione viene chiusa, l'applicazione client potrebbe ricevere il messaggio di errore seguente: "Connessione sottostante chiusa: una connessione che doveva restare attiva è stata chiusa dal server in modo imprevisto".

Una prassi comune consiste nell'usare una connessione TCP keep-alive per mantenere la connessione attiva per un periodo più lungo. Per altre informazioni, vedere questi [esempi .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Con la connessione keep-alive abilitata, i pacchetti vengono inviati durante i periodi di inattività della connessione. I pacchetti keep-alive assicurano che il valore di timeout di inattività non venga raggiunto e che la connessione venga mantenuta per un lungo periodo.

L'impostazione funziona solo per le connessioni in ingresso. Per evitare di perdere la connessione, configurare il keep-alive TCP con un intervallo inferiore all'impostazione del timeout di inattività o aumentare il valore di timeout di inattività. Per supportare questi scenari, è stato aggiunto il supporto per un timeout di inattività configurabile.

Il keep-alive TCP funziona per scenari in cui la durata della batteria non è un vincolo. Non è consigliabile per le applicazioni mobili. L'uso di un'impostazione keep-alive TCP in un'applicazione per dispositivi mobili può far scaricare più velocemente la batteria del dispositivo.

![Timeout TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Nelle sezioni seguenti viene descritto come modificare le impostazioni di timeout di inattività per le risorse IP e di bilanciamento del carico pubbliche.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurazione del timeout TCP per l'IP pubblico a livello di istanza su 15 minuti

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` è facoltativo. Se non impostato, il timeout predefinito è 4 minuti. L'intervallo di timeout accettabile è compreso tra 4 e 30 minuti.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>Impostare il timeout TCP su una regola con carico bilanciato su 15 minutiSet the TCP timeout on a load-balanced rule to 15 minutes

Per impostare il timeout di inattività per un servizio di bilanciamento del carico, 'IdleTimeoutInMinutes' è impostato sulla regola con carico bilanciato. Ad esempio:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>Passaggi successivi

[Panoramica del bilanciamento del carico interno](load-balancer-internal-overview.md)

[Introduzione alla configurazione del bilanciamento del carico Internet](quickstart-create-standard-load-balancer-powershell.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)
