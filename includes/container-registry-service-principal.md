---
title: includere il file
description: Includere file
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70032356"
---
## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per creare un'entità servizio con accesso al registro contenitori, eseguire lo script seguente in [Azure Cloud Shell](../articles/cloud-shell/overview.md) o in un'installazione locale dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Lo script viene formattato per la shell Bash.

Prima di eseguire lo script, aggiornare la variabile `ACR_NAME` con il nome del registro contenitori. Il valore `SERVICE_PRINCIPAL_NAME` deve essere univoco all'interno del tenant di Azure Active Directory. Se si riceve un errore "`'http://acr-service-principal' already exists.`", specificare un nome diverso per l'entità servizio.

È facoltativamente possibile modificare il valore `--role` nel comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] se si vogliono concedere autorizzazioni diverse. Per un elenco completo dei ruoli, vedere [ACR roles and permissions](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md) (Ruoli e autorizzazioni del registro Azure Container).

Dopo aver eseguito lo script, prendere nota dell'**ID** e della **password** dell'entità servizio. Dopo aver ottenuto le credenziali, è possibile configurare le applicazioni e i servizi per l'autenticazione nel registro contenitori come entità servizio.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Usare un'entità servizio esistente

Per concedere al registro l'accesso a un'entità servizio esistente, è necessario assegnare un nuovo ruolo all'entità servizio. Come per la creazione di una nuova entità servizio, è possibile concedere, tra gli altri, l'accesso pull, push e pull e proprietario.

Lo script seguente usa il comando [az role assignment create][az-role-assignment-create] per concedere le autorizzazioni *pull* a un'entità servizio specificata nella variabile `SERVICE_PRINCIPAL_ID`. Modificare il valore `--role` se si vuole concedere un livello di accesso diverso.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
