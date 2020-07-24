---
title: 'Generare ed esportare i certificati per da punto a sito: Linux: interfaccia della riga di comando'
description: Creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client usando l'interfaccia della riga di comando Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: c55c304ae71cba396266c83bcaaa727351dcd677
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87064632"
---
# <a name="generate-and-export-certificates"></a>Generare ed esportare i certificati

Le connessioni da punto a sito usano certificati per l'autenticazione. Questo articolo illustra come creare un certificato radice autofirmato e generare i certificati usando l'interfaccia della riga di comando Linux e strongSwan. Se si cercano diverse istruzioni di certificato, vedere gli articoli [Powershell](vpn-gateway-certificates-point-to-site.md) oppure [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Per informazioni su come installare strongSwan usando l'interfaccia utente grafica anziché l'interfaccia della riga di comando, vedere i passaggi dell'articolo [Configurazione del client](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="install-strongswan"></a>Installare strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generare ed esportare i certificati

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Continuare con la configurazione da punto a sito [Creare e installare i file di configurazione del client VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
