---
title: 'Gateway VPN: modificare le impostazioni degli indirizzi IP del gateway: CLI di Azure'
description: Questo articolo illustra in modo dettagliato come modificare i prefissi degli indirizzi IP per il gateway di rete locale usando l'interfaccia della riga di comando di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: c4809c51a59805ac996bd4c5971ec633ae6c2aed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987069"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modificare le impostazioni del gateway di rete locale usando l'interfaccia della riga di comando di Azure

In alcuni casi le impostazioni per il prefisso indirizzo o l'indirizzo IP gateway del gateway di rete locale subiscono modifiche. Questo articolo illustra come modificare le impostazioni del gateway di rete locale. È anche possibile modificare queste impostazioni con un altro metodo selezionando un'opzione diversa nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare

Installare la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificare i prefissi degli indirizzi IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificare l'indirizzo IP del gateway

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile verificare la connessione al gateway. Vedere [Verificare una connessione al gateway](vpn-gateway-verify-connection-resource-manager.md).

