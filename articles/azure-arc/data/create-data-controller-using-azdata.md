---
title: Creare un controller dati usando l'interfaccia della riga di comando di Azure Data (azdata)
description: Creare un controller di dati di Azure Arc in un cluster Kubernetes multinodo tipico che è già stato creato con l'interfaccia della riga di comando di Azure Data CLI (azdata).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: f7bc90f2748d230ad50868cff5d7a8f7b69d850a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029540"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Creare un controller di dati di Azure ARC usando il [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Vedere l'argomento [creare il controller dati di Azure Arc](create-data-controller.md) per informazioni generali.

Per creare il controller di dati di Azure ARC usando il [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] è necessario che sia [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] installato.

   [Installare il [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Indipendentemente dalla piattaforma di destinazione scelta, sarà necessario impostare le variabili di ambiente seguenti prima della creazione dell'utente amministratore del controller di dati. È possibile fornire queste credenziali ad altre persone che devono disporre dell'accesso come amministratore al controller dati in base alle esigenze.

**AZDATA_USERNAME** : nome utente desiderato per l'utente amministratore del controller di dati. Esempio: `arcadmin`

**AZDATA_PASSWORD** : una password di propria scelta per l'utente amministratore del controller di dati. La password deve avere una lunghezza di almeno otto caratteri e contenere caratteri di tre dei quattro set seguenti: lettere maiuscole, lettere minuscole, numeri e simboli.

### <a name="linux-or-macos"></a>Linux o macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Sarà necessario connettersi ed eseguire l'autenticazione a un cluster Kubernetes e selezionare un contesto Kubernetes esistente prima di iniziare la creazione del controller di dati di Azure Arc. La modalità di connessione a un cluster o un servizio Kubernetes varia. Vedere la documentazione per la distribuzione o il servizio Kubernetes usato per la connessione al server dell'API Kubernetes.

È possibile verificare che sia presente una connessione Kubernetes corrente e verificare il contesto corrente con i comandi seguenti.

```console
kubectl get namespace
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Creare il controller di dati di Azure Arc

> [!NOTE]
> È possibile usare un valore diverso per il `--namespace` parametro del comando azdata Arc DC create negli esempi seguenti, ma assicurarsi di usare il nome dello spazio dei nomi per `--namespace parameter` tutti gli altri comandi seguenti.

- [Crea in Azure Kubernetes Service (AKS)](#create-on-azure-kubernetes-service-aks)
- [Creazione nel motore AKS nell'hub Azure Stack](#create-on-aks-engine-on-azure-stack-hub)
- [Creazione su AKS in Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
- [Crea in Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
- [Creazione su Red Hat OpenShift container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
- [Crea su Open Source, upstream Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [Creare nel servizio Kubernetes di AWS elastico (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
- [Creazione sul servizio motore di Google Cloud Kubernetes (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Crea in Azure Kubernetes Service (AKS)

Per impostazione predefinita, il profilo di distribuzione AKS usa la `managed-premium` classe di archiviazione. La `managed-premium` classe di archiviazione funzionerà solo se sono presenti macchine virtuali distribuite usando immagini di VM con dischi Premium.

Se si intende usare `managed-premium` come classe di archiviazione, è possibile eseguire il comando seguente per creare il controller di dati. Sostituire i segnaposto nel comando con il nome del gruppo di risorse, l'ID sottoscrizione e la località di Azure.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Se non si è certi della classe di archiviazione da usare, è necessario usare la `default` classe di archiviazione supportata indipendentemente dal tipo di macchina virtuale in uso. Ma non fornirà le prestazioni più veloci.

Se si vuole usare la `default` classe di archiviazione, è possibile eseguire questo comando:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Creazione nel motore AKS nell'hub Azure Stack

Per impostazione predefinita, il profilo di distribuzione usa la `managed-premium` classe di archiviazione. La `managed-premium` classe di archiviazione funzionerà solo se sono presenti VM di lavoro distribuite usando immagini di VM con dischi Premium nell'Hub Azure stack.

È possibile eseguire il comando seguente per creare il controller dati usando la classe di archiviazione gestita-Premium:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Se non si è certi della classe di archiviazione da usare, è necessario usare la `default` classe di archiviazione supportata indipendentemente dal tipo di macchina virtuale in uso. Nell'hub Azure Stack i dischi Premium e i dischi standard sono supportati dalla stessa infrastruttura di archiviazione. Si prevede pertanto che forniscano le stesse prestazioni generali, ma con limiti di IOPS diversi.

Se si vuole usare la `default` classe di archiviazione, è possibile eseguire questo comando.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Creazione su AKS in Azure Stack HCI

Per impostazione predefinita, il profilo di distribuzione usa una classe di archiviazione denominata `default` e il tipo di servizio `LoadBalancer` .

È possibile eseguire il comando seguente per creare il controller dati usando la `default` classe di archiviazione e il tipo di servizio `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Crea in Azure Red Hat OpenShift (ARO)

Azure Red Hat OpenShift richiede un vincolo di contesto di sicurezza.

#### <a name="apply-the-security-context"></a>Applicare il contesto di sicurezza

Prima di creare il controller dati in Azure Red Hat OpenShift, è necessario applicare specifici vincoli del contesto di sicurezza (SCC). Per la versione di anteprima, questi vincoli di sicurezza sono attenuati. Le versioni future forniranno il SCC aggiornato.

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="create-custom-deployment-profile"></a>Creare un profilo di distribuzione personalizzato

Usare il profilo `azure-arc-azure-openshift` per Azure RedHat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Creare un controller dei dati

È possibile eseguire il comando seguente per creare il controller dati:

> [!NOTE]
> Usare lo stesso spazio dei nomi qui e nei `oc adm policy add-scc-to-user` comandi precedenti. L'esempio è `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Creazione su Red Hat OpenShift container Platform (OCP)

> [!NOTE]
> Se si usa Red Hat OpenShift container Platform in Azure, è consigliabile usare la versione più recente disponibile.

Prima di creare il controller dati in Red Hat OCP, è necessario applicare vincoli specifici per il contesto di sicurezza. 

#### <a name="apply-the-security-context-constraint"></a>Applicare il vincolo del contesto di sicurezza

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="determine-storage-class"></a>Determinare la classe di archiviazione

Sarà inoltre necessario determinare la classe di archiviazione da utilizzare eseguendo il comando seguente.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Creare un profilo di distribuzione personalizzato

Creare un nuovo file del profilo di distribuzione personalizzato in base al `azure-arc-openshift` profilo di distribuzione eseguendo il comando seguente. Questo comando crea una directory `custom` nella directory di lavoro corrente e un file del profilo di distribuzione personalizzato `control.json` in tale directory.

Usare il profilo `azure-arc-openshift` per OpenShift container Platform.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Imposta classe di archiviazione 

A questo punto, impostare la classe di archiviazione desiderata sostituendo `<storageclassname>` nel comando riportato di seguito con il nome della classe di archiviazione che si vuole usare, che è stata determinata eseguendo il `kubectl get storageclass` comando precedente.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Set LoadBalancer (facoltativo)

Per impostazione predefinita, il `azure-arc-openshift` profilo di distribuzione USA `NodePort` come tipo di servizio. Se si usa un cluster OpenShift integrato con un servizio di bilanciamento del carico, è possibile modificare la configurazione per usare il `LoadBalancer` tipo di servizio usando il comando seguente:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Verificare i criteri di sicurezza

Quando si usa OpenShift, è possibile eseguire con i criteri di sicurezza predefiniti in OpenShift o si vuole bloccare in genere l'ambiente in modo più che tipico. Facoltativamente, è possibile scegliere di disabilitare alcune funzionalità per ridurre al minimo le autorizzazioni necessarie in fase di distribuzione e in fase di esecuzione eseguendo i comandi seguenti.

Questo comando Disabilita le raccolte di metriche relative ai pod. Se questa funzionalità viene disabilitata, non sarà possibile visualizzare le metriche sui Pod nei dashboard di Grafana. Il valore predefinito è true.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Questo comando Disabilita le raccolte di metriche sui nodi. Se questa funzionalità viene disabilitata, non sarà possibile visualizzare le metriche sui nodi nei dashboard Grafana. Il valore predefinito è true.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Questo comando Disabilita la possibilità di eseguire i dump della memoria per la risoluzione dei problemi.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Creare un controller dei dati

A questo punto è possibile creare il controller dati usando il comando seguente.

> [!NOTE]
>   Usare lo stesso spazio dei nomi qui e nei `oc adm policy add-scc-to-user` comandi precedenti. L'esempio è `arc` .

> [!NOTE]
>   Il `--path` parametro deve puntare alla _directory_ che contiene il control.jsnel file non al control.jsnel file stesso.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Crea su Open Source, upstream Kubernetes (kubeadm)

Per impostazione predefinita, il profilo di distribuzione kubeadm usa una classe di archiviazione denominata `local-storage` e il tipo di servizio `NodePort` . Se questo è accettabile, è possibile ignorare le istruzioni riportate di seguito per impostare la classe di archiviazione e il tipo di servizio desiderati ed eseguire immediatamente il `azdata arc dc create` comando riportato di seguito.

Se si desidera personalizzare il profilo di distribuzione per specificare una classe di archiviazione e/o un tipo di servizio specifici, iniziare creando un nuovo file del profilo di distribuzione personalizzato in base al profilo di distribuzione kubeadm eseguendo il comando seguente. Questo comando creerà una directory `custom` nella directory di lavoro corrente e un file del profilo di distribuzione personalizzato `control.json` in tale directory.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

È possibile cercare le classi di archiviazione disponibili eseguendo il comando seguente.

```console
kubectl get storageclass
```

A questo punto, impostare la classe di archiviazione desiderata sostituendo `<storageclassname>` nel comando riportato di seguito con il nome della classe di archiviazione che si vuole usare, che è stata determinata eseguendo il `kubectl get storageclass` comando precedente.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Per impostazione predefinita, il profilo di distribuzione kubeadm utilizza `NodePort` come tipo di servizio. Se si usa un cluster Kubernetes integrato con un servizio di bilanciamento del carico, è possibile modificare la configurazione usando il comando seguente.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

A questo punto è possibile creare il controller dati usando il comando seguente.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Creare nel servizio Kubernetes di AWS elastico (EKS)

Per impostazione predefinita, la classe di archiviazione EKS è `gp2` e il tipo di servizio è `LoadBalancer` .

Eseguire il comando seguente per creare il controller dati usando il profilo di distribuzione EKS fornito.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Creazione sul servizio motore di Google Cloud Kubernetes (GKE)

Per impostazione predefinita, la classe di archiviazione GKE è `standard` e il tipo di servizio è `LoadBalancer` .

Eseguire il comando seguente per creare il controller dati usando il profilo di distribuzione GKE fornito.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione del controller potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che siano stati creati un controller dati e uno spazio dei nomi Kubernetes con il nome `arc` . Se è stato utilizzato un nome diverso per lo spazio dei nomi/controller dati, è possibile sostituire `arc` con il nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito. Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).