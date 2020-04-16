---
title: Esempi dell'interfaccia della riga di comando di Azure - Installare app
description: Questo script crea un set di scalabilità di macchine virtuali che esegue Ubuntu e usa l'estensione Script personalizzato per installare un'applicazione Web di base.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 815ed75e9bb8d7018ee8d8b5d72c439dd14da81e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011287"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Installare applicazioni in un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
Questo script crea un set di scalabilità di macchine virtuali che esegue Ubuntu e usa l'estensione Script personalizzato per installare un'applicazione Web di base. Dopo aver eseguito lo script, è possibile accedere all'app Web tramite un Web browser.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Eseguire questo comando per rimuovere il gruppo di risorse, il set di scalabilità e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti per creare un gruppo di risorse, il set di scalabilità di macchine virtuali e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/ad/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vmss create](/cli/azure/vmss) | Crea il set di scalabilità di macchine virtuali e lo connette alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Per distribuire il traffico a più istanze di macchina virtuale, viene anche creato un servizio di bilanciamento del carico. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vmss extension set](/cli/azure/vmss/extension) | Installa l'estensione di script personalizzati di Azure per eseguire uno script che prepara i dischi dati in ogni istanza di macchina virtuale. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Crea una regola di bilanciamento del carico per distribuire il traffico della porta TCP 80 alle istanze di macchina virtuale nel set di scalabilità. |
| [az network public-ip show](/cli/azure/network/public-ip) | Ottiene informazioni sull'indirizzo IP pubblico assegnato usato dal servizio di bilanciamento del carico. |
| [az group delete](/cli/azure/ad/group) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Azure per i set di scalabilità di macchine virtuali sono disponibili nella [documentazione dei set di scalabilità di macchine virtuali di Azure](../cli-samples.md).
