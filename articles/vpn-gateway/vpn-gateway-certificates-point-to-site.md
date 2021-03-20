---
title: 'Generare ed esportare i certificati per P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client per P2S usando PowerShell in Windows 10 o Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: c1c69b301199b054fe6b1ef42cfcf7878a7a161c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306685"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generare ed esportare i certificati per le connessioni da punto a sito usando PowerShell

Le connessioni da punto a sito usano certificati per l'autenticazione. Questo articolo illustra come creare un certificato radice autofirmato e generare i certificati client usando PowerShell in Windows 10 o Windows Server 2016. Se si stanno cercando le istruzioni di certificato diverse, consultare [Certificati - Linux](vpn-gateway-certificates-point-to-site-linux.md) oppure [Certificati - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

È necessario eseguire i passaggi descritti in questo articolo in un computer che esegue Windows 10 o Windows Server 2016. I cmdlet di PowerShell usati per generare i certificati fanno parte del sistema operativo e non funzionano in altre versioni di Windows. Il computer con Windows 10 o Windows Server 2016 è richiesto solo per generare i certificati. Dopo aver generato i certificati, è possibile caricarli o installarli in qualsiasi sistema operativo client supportato.

Se non si ha accesso a un computer con Windows 10 o Windows Server 2016, è possibile usare [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) per generare i certificati. I certificati generati con uno dei due metodi possono essere installati in qualsiasi sistema operativo client [supportato](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Installare un certificato client esportato

Ogni client che si connette alla rete virtuale su una connessione P2S richiede l'installazione locale di un certificato client.

Per installare un certificato client, vedere [Installare un certificato client per le connessioni da punto a sito](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Passaggi successivi

Continuare con la configurazione da punto a sito.

* Per i passaggi del modello di distribuzione di **Resource Manager**, vedere [Configurare una connessione da punto a sito con l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Per i passaggi del modello di distribuzione **classico**, vedere [Configurare una connessione VPN da punto a sito a una rete virtuale (classico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).