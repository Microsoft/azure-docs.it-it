---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "69520851"
---
Per la procedura seguente è stata usata la configurazione seguente:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |Dependencies| strongSwan |


Per installare la configurazione strongSwan richiesta, eseguire i comandi seguenti:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Usare il comando seguente per installare l'interfaccia della riga di comando di Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Istruzioni aggiuntive su come installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
