---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67180626"
---
Per aprire una sessione SSH diretta con il contenitore, l'app deve essere in esecuzione.

Incollare l'URL seguente nel browser e sostituire \<app-name> con il nome dell'app:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Se non lo si è già fatto, per connettersi è necessario eseguire l'autenticazione con la sottoscrizione di Azure. Al termine dell'autenticazione viene visualizzata una shell nel browser, in cui è possibile eseguire i comandi all'interno del contenitore.

![Connessione SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
