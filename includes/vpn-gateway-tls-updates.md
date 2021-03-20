---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179971"
---
>[!NOTE]
>A partire dal 1 luglio 2018, verrà rimosso il supporto per TLS 1.0 e 1.1 da Gateway VPN di Azure. Gateway VPN supporterà solo TLS 1.2. Per gestire il supporto, vedere gli [aggiornamenti per abilitare il supporto per TLS 1.2](#tls1).

Inoltre, anche gli algoritmi legacy seguenti saranno deprecati per TLS dal 1 luglio 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Come si abilita il supporto per TLS 1.2 in Windows 7 e Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
