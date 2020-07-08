---
title: Deploy OpenShift in Azure Stack (Distribuire OpenShift in Azure Stack)
description: Distribuire OpenShift in Azure Stack.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 51abfd1cbb438d0987554040867625f7fb71630b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758227"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Distribuire OpenShift Container Platform in Azure Stack

OpenShift può essere distribuito in Azure Stack. Esistono alcune differenze fondamentali tra Azure e Azure Stack perciò anche la distribuzione e le funzionalità saranno leggermente diverse.

Attualmente, il Provider di servizi cloud di Azure non funziona in Azure Stack. Per questo motivo, non è possibile usare disk attach per l'archiviazione permanente in Azure Stack. È invece possibile configurare altre opzioni di archiviazione, ad esempio NFS, iSCSI, GlusterFS e così via. In alternativa, è possibile abilitare CNS e usare GlusterFS per l'archiviazione permanente. Se i CNS sono abilitati, tre nodi aggiuntivi verranno distribuiti con ulteriore spazio di archiviazione per l'utilizzo di GlusterFS.

Esistono diversi metodi per distribuire OpenShift Container Platform o OKD in Azure Stack:

- È possibile distribuire manualmente i componenti necessari dell'infrastruttura di Azure e quindi seguire la [documentazione di OpenShift Container Platform](https://docs.openshift.com/container-platform) o la [documentazione OKD](https://docs.okd.io).
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-container-platform/) esistente che semplifica la distribuzione del cluster OpenShift Container Platform.
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-origin) esistente che semplifica la distribuzione del cluster OKD.

Se si usa il modello di Resource Manager, selezionare il ramo corretto (azurestack-release-3.x). I modelli per Azure non funzioneranno in quanto le versioni dell'API sono diverse tra Azure e Azure Stack. Il riferimento all'immagine RHEL è attualmente hardcoded come variabile nel file azuredeploy.json e dovrà essere modificato in modo da corrispondere l'immagine.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Per tutte le opzioni è necessaria una sottoscrizione di Red Hat. Durante la distribuzione, l'istanza Red Hat Enterprise Linux viene registrata nella sottoscrizione di Red Hat e allegata all'ID del pool che contiene i diritti per OpenShift Container Platform.
Verificare di avere un nome utente, una password e un ID pool validi per Red Hat Subscription Manager (RHSM). In alternativa, è possibile usare una chiave di attivazione, l'ID organizzazione e l'ID pool.  Per verificare le informazioni, eseguire l'accesso a https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Prerequisiti di Azure Stack

Un'immagine RHEL (OpenShift Container Platform) o immagine CentOS (OKD) deve essere aggiunta all'ambiente Azure Stack per distribuire un cluster OpenShift. Contattare l'amministratore di Azure Stack per aggiungere queste immagini. Le istruzioni sono consultabili qui:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Distribuire usando la piattaforma OpenShift o il modello di Resource Manager di OKD

Per distribuire usando il modello di Resource Manager, viene usato un file di parametri per specificare tutti i parametri di input. Per personalizzare ulteriormente la distribuzione, creare una copia tramite fork dell'archivio GitHub e modificare gli elementi appropriati.

Tra le opzioni di personalizzazione comuni sono incluse, ad esempio:

- Dimensioni macchina virtuale bastion (variabile in azuredeploy.json)
- Convenzioni di denominazione (variabile in azuredeploy.json)
- Specifiche del cluster OpenShift - modificate usando il file hosts (deployOpenShift.sh)
- Riferimento immagine RHEL (variabile in azuredeploy.json)

Per i passaggi per distribuire tramite l'interfaccia della riga di comando di Azure, vedere la sezione appropriata [OpenShift Container Platform](./openshift-container-platform-3x.md) o la sezione [OKD](./openshift-okd.md).

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-container-platform-3x-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure](./openshift-container-platform-3x-troubleshooting.md)