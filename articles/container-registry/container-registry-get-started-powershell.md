---
title: 'Avvio rapido: Creare un registro - PowerShell'
description: Imparare rapidamente a creare un registro Docker privato in Registro Azure Container con PowerShell
ms.date: 01/22/2019
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: bd9b93e22081c43dfa3fd934f13da3713120aadb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537391"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Guida introduttiva: creare un registro contenitori privato usando Azure PowerShell

Registro Azure Container è un servizio gestito e privato di registri contenitori Docker usato per compilare, archiviare e servire immagini del contenitore Docker. In questa guida introduttiva si apprenderà come creare un Registro Azure Container usando PowerShell. Usare quindi i comandi di Docker per eseguire il push di un'immagine del contenitore nel registro e infine eseguire il pull ed eseguire l'immagine dal registro.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per questa guida introduttiva è richiesto il modulo Azure PowerShell. Per determinare la versione installata eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

È anche necessario avere Docker installato localmente. Docker offre pacchetti per i sistemi [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

Poiché Azure Cloud Shell non include tutti i componenti di Docker necessari, ovvero il daemon `dockerd`, non è possibile usare Cloud Shell lo per questa guida rapida.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzAccount][Connect-AzAccount] e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Dopo aver eseguito l'autenticazione con Azure, creare un gruppo di risorse con il comando [New-AzResourceGroup][New-AzResourceGroup]. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Creare un registro contenitori

Successivamente creare un registro contenitori nel nuovo gruppo di risorse con il comando [New-AzContainerRegistry][New-AzContainerRegistry].

Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. L'esempio seguente crea un registro denominato "myContainerRegistry007". Sostituire *myContainerRegistry007* nel comando seguente, quindi eseguirlo per creare il registro:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

In questa guida introduttiva viene creato un registro *Basic*, ovvero un'opzione ottimizzata in termini di costo per sviluppatori che iniziano a usare Registro Azure Container. Per informazioni dettagliate sui livelli di servizio disponibili, vedere [Livelli di servizio del registro contenitori][container-registry-skus].

## <a name="log-in-to-registry"></a>Accedere al registro

Prima di eseguire il push e il pull delle immagini del contenitore, è necessario accedere al registro. Per brevità, abilitare l'utente amministratore nel registro con il [comando Get-AzContainerRegistryCredential.][Get-AzContainerRegistryCredential] Negli scenari di produzione è consigliabile usare un metodo di autenticazione alternativo [per l'accesso](container-registry-authentication.md) al Registro di sistema, ad esempio un'entità servizio. 

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Successivamente eseguire [docker login][docker-login] per eseguire l'accesso:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Il comando restituisce `Login Succeeded` al termine dell'esecuzione.

> [!TIP]
> L'interfaccia della riga di comando di Azure fornisce il comando , un modo pratico per accedere a un registro contenitori usando la singola identità, senza `az acr login` passare le credenziali docker. [](container-registry-authentication.md#individual-login-with-azure-ad)


[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo avere usato le risorse create in questa guida di avvio rapido, usare il comando [Remove-AzResourceGroup][Remove-AzResourceGroup] per rimuovere il gruppo di risorse, il registro contenitori e le immagini del contenitore archiviate in tale registro:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza di Registro Azure Container con Azure PowerShell, è stato eseguito il push di un'immagine del contenitore e quindi è stato eseguito il pull per eseguire l'immagine dal registro. Per maggiori informazioni su Registro Azure Container, passare alle relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni su Registro Azure Container][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Esercitazioni su Attività del Registro Azure Container][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
