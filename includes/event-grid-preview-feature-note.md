---
title: includere file
description: includere file
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814907"
---
Questa funzionalità è in anteprima. Per usarla, è necessario installare un modulo o un'estensione di anteprima.

### <a name="install-extension-for-azure-cli"></a>Installare l'estensione per l'interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, è necessaria l'[estensione Griglia di eventi](/cli/azure/azure-cli-extensions-list).

In [CloudShell](/azure/cloud-shell/quickstart):

* Se si è già installata l'estensione, aggiornarla con il comando `az extension update -n eventgrid`
* Se non si è ancora installata l'estensione, installarla con il comando `az extension add -n eventgrid`

Per un'installazione locale:

1. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) Assicurarsi di avere la versione più recente installata controllando con `az --version`.
1. Disinstallare le versioni precedenti dell'estensione con il comando `az extension remove -n eventgrid`
1. Installare l'estensione `eventgrid` con `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Installare il modulo per PowerShell

Per PowerShell, è necessario il [modulo AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

In [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Installare il modulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Per un'installazione locale:

1. Aprire la console di PowerShell come amministratore
1. Installare il modulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Se il parametro `-AllowPrerelease` non è disponibile, seguire questa procedura:

1. Eseguire `Install-Module PowerShellGet -Force`
1. Eseguire `Update-Module PowerShellGet`
1. Chiudere la console di PowerShell
1. Riavviare PowerShell come amministratore
1. Installare il modulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
