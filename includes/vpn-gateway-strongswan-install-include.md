---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86218112"
---
Per la procedura seguente è stata usata la configurazione seguente:

- Computer: Ubuntu server 18,04
- Dipendenze: strongSwan


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
