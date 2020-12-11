---
title: Pianificare una distribuzione di Azure Service Fabric cluster
description: Informazioni sulla pianificazione e la preparazione per la distribuzione di un cluster Service Fabric di produzione in Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9de59811397eb47809c6d71f608e43beae5bfadb
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109624"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Pianificare e preparare la distribuzione di un cluster

La pianificazione e la preparazione per la distribuzione di un cluster di produzione è molto importante.  Esistono molti fattori da considerare.  Questo articolo illustra i passaggi per preparare la distribuzione del cluster.

## <a name="read-the-best-practices-information"></a>Leggi le informazioni sulle procedure consigliate
Per gestire correttamente le applicazioni e i cluster di Azure Service Fabric, è consigliabile eseguire alcune operazioni per ottimizzare l'affidabilità dell'ambiente di produzione.  Per altre informazioni, vedere [Service Fabric procedure consigliate per le applicazioni e i cluster](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Selezionare il sistema operativo per il cluster
Service Fabric permette di creare cluster di Service Fabric in qualsiasi VM o computer con Windows Server o Linux in esecuzione.  Prima di distribuire il cluster, è necessario scegliere il sistema operativo: Windows o Linux.  Ogni nodo (macchina virtuale) del cluster esegue lo stesso sistema operativo, non è possibile combinare macchine virtuali Windows e Linux nello stesso cluster.

## <a name="capacity-planning"></a>Pianificazione della capacità
La pianificazione della capacità è un passaggio importante per qualsiasi distribuzione di produzione. Di seguito sono elencati alcuni aspetti da considerare nell'ambito di questo processo.

* Numero iniziale di tipi di nodo per il cluster 
* Proprietà di ogni tipo di nodo (dimensioni, numero di istanze, primario, con connessione Internet, numero di macchine virtuali e così via)
* Caratteristiche di affidabilità e durabilità del cluster

### <a name="select-the-initial-number-of-node-types"></a>Selezionare il numero iniziale di tipi di nodo
Prima di tutto è necessario stabilire per che cosa verrà usato il cluster che si sta creando. Che tipo di applicazioni si prevede di distribuire nel cluster? L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet? I servizi (che costituiscono l'applicazione) hanno esigenze diverse per l'infrastruttura, ad esempio cicli di CPU più elevati o una quantità maggiore di RAM? Un cluster Service Fabric può essere costituito da più di un tipo di nodo: un tipo di nodo primario e uno o più tipi di nodo non primari. Di ogni tipo di nodo viene eseguito il mapping a un set di scalabilità di macchine virtuali. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. È possibile configurare [le proprietà dei nodi e i vincoli di posizionamento][placementconstraints] in modo da vincolare servizi specifici a specifici tipi di nodo.  Per ulteriori informazioni, vedere [Service Fabric pianificazione della capacità del cluster](service-fabric-cluster-capacity.md).

### <a name="select-node-properties-for-each-node-type"></a>Selezionare le proprietà dei nodi per ogni tipo di nodo
I tipi di nodo definiscono lo SKU, il numero e le proprietà delle VM nel set di scalabilità associato.

La dimensione minima delle VM per ogni tipo di nodo è determinata dal [livello di durabilità][durability] scelto per il tipo di nodo.

Il numero minimo delle macchine virtuali per il tipo di nodo primario è determinato dal [livello di affidabilità][reliability] scelto.

Vedere le indicazioni minime per i [tipi di nodo primari](service-fabric-cluster-capacity.md#primary-node-type), i [carichi di lavoro con stato su tipi di nodo non primari](service-fabric-cluster-capacity.md#stateful-workloads)e i [carichi di lavoro senza stato in tipi di nodo non primari](service-fabric-cluster-capacity.md#stateless-workloads).

Il numero massimo di nodi deve essere basato sul numero di repliche dell'applicazione o dei servizi che si desidera eseguire in questo tipo di nodo.  La [pianificazione della capacità per le applicazioni di Service Fabric](service-fabric-capacity-planning.md) consente di stimare le risorse necessarie per eseguire le applicazioni. È sempre possibile ridimensionare il cluster in un secondo momento per modificare il carico di lavoro dell'applicazione. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Usare dischi del sistema operativo temporanei per i set di scalabilità di macchine virtuali

I *dischi del sistema operativo temporaneo* sono archiviazione creati nella macchina virtuale locale (VM) e non vengono salvati nell'archiviazione di Azure remota. Sono consigliate per tutti i tipi di nodo di Service Fabric (primario e secondario), perché rispetto ai dischi del sistema operativo persistenti tradizionali, i dischi del sistema operativo temporaneo:

* Ridurre la latenza di lettura/scrittura al disco del sistema operativo
* Abilitare operazioni di gestione dei nodi di ripristino/ricreazione dell'immagine più veloci
* Riduci i costi complessivi (i dischi sono gratuiti e non comportano costi di archiviazione aggiuntivi)

Il disco del sistema operativo temporaneo non è una funzionalità di Service Fabric specifica, bensì una funzionalità dei *set di scalabilità di macchine virtuali* di Azure di cui è stato eseguito il mapping ai tipi di nodo Service Fabric. L'uso di questi elementi con Service Fabric richiede quanto segue nel modello di Azure Resource Manager cluster:

1. Assicurarsi che i tipi di nodo specifichino le [dimensioni delle VM di Azure supportate](../virtual-machines/ephemeral-os-disks.md) per i dischi del sistema operativo temporaneo e che le dimensioni della macchina virtuale siano sufficienti per supportare le dimensioni del disco del sistema operativo. vedere la *Nota* seguente. Per esempio:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Assicurarsi di selezionare una dimensione della macchina virtuale con una dimensione della cache uguale o superiore alle dimensioni del disco del sistema operativo della macchina virtuale stessa. in caso contrario, la distribuzione di Azure potrebbe generare un errore (anche se inizialmente è stata accettata).

2. Specificare una versione del set di scalabilità di macchine virtuali ( `vmssApiVersion` ) di `2018-06-01` o versione successiva:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Nella sezione set di scalabilità di macchine virtuali del modello di distribuzione specificare l' `Local` opzione per `diffDiskSettings` :

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> Le applicazioni utente non devono avere alcuna dipendenza/file/artefatto nel disco del sistema operativo, perché il disco del sistema operativo andrebbe perso nel caso di un aggiornamento del sistema operativo.

> [!NOTE]
> Non è possibile aggiornare sul posto i VMSS non temporanei esistenti per l'uso di dischi temporanei.
> Per eseguire la migrazione, gli utenti dovranno [aggiungere](./virtual-machine-scale-set-scale-node-type-scale-out.md) un nuovo NodeType con dischi temporanei, spostare i carichi di lavoro nel nuovo NodeType & [rimuovere](./service-fabric-how-to-remove-node-type.md) il valore NodeType esistente.
>

Per altre informazioni e altre opzioni di configurazione, vedere [dischi del sistema operativo temporanei per macchine virtuali di Azure](../virtual-machines/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Selezionare i livelli di durabilità e affidabilità per il cluster
Il livello di durabilità viene usato per indicare al sistema i privilegi delle VM rispetto all'infrastruttura di Azure sottostante. Nel tipo di nodo primario questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di VM (ad esempio il riavvio di una VM, il re-imaging di una VM o la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi di sistema e i servizi con stato. Nei tipi di nodo non primari questo privilegio consente a Service Fabric di sospendere le richieste di infrastruttura a livello di macchina virtuale (ad esempio, il riavvio di una VM, il re-imaging di una VM e la migrazione di una VM) che hanno effetto sui requisiti relativi al quorum per i servizi con stato.  Per i vantaggi dei diversi livelli e consigli sul livello da usare e quando, vedere [le caratteristiche di durabilità del cluster][durability].

Il livello di affidabilità viene usato per impostare il numero di repliche dei servizi di sistema che si vuole eseguire in questo cluster nel tipo di nodo primario. I servizi di sistema nel cluster sono tanto più affidabili quanto più elevato è il numero di repliche.  Per i vantaggi dei diversi livelli e consigli sul livello da usare e quando, vedere [le caratteristiche di affidabilità del cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Abilita proxy inverso e/o DNS
I servizi che si connettono tra loro in un cluster possono in genere accedere direttamente agli endpoint di altri servizi perché i nodi di un cluster si trovano nella stessa rete locale. Per semplificare la connessione tra i servizi, Service Fabric fornisce servizi aggiuntivi: un [servizio DNS](service-fabric-dnsservice.md) e un [servizio proxy inverso](service-fabric-reverseproxy.md).  Entrambi i servizi possono essere abilitati quando si distribuisce un cluster.

Poiché molti servizi, in particolare quelli in contenitori, possono avere un nome di URL esistente, la possibilità di risolverli usando il protocollo DNS standard (anziché il protocollo Naming Service) è molto utile, in particolare negli scenari di "Lift-and-Shift" dell'applicazione. Questo è esattamente ciò che fa il servizio DNS. Consente di eseguire il mapping di nomi DNS a nomi di servizi e di conseguenza di risolvere gli indirizzi IP degli endpoint.

Il proxy inverso indirizza i servizi nel cluster che espongono endpoint HTTP (incluso HTTPS). Il proxy inverso semplifica notevolmente la chiamata di altri servizi fornendo un formato URI specifico.  Il proxy inverso gestisce anche i passaggi di risoluzione, connessione e ripetizione dei tentativi necessari per la comunicazione tra un servizio e un altro.

## <a name="prepare-for-disaster-recovery"></a>Preparare l'ambiente per il ripristino di emergenza
Una parte fondamentale della distribuzione a disponibilità elevata consiste nel garantire la resistenza dei servizi a tutti i tipi di errori. Ciò è particolarmente importante per gli errori imprevisti e incontrollabili. [Prepararsi per il ripristino di emergenza](service-fabric-disaster-recovery.md) descrive alcune modalità di errore comuni che possono essere emergenze se non sono modellate e gestite correttamente. Vengono inoltre illustrate le mitigazioni e le azioni da intraprendere in caso di emergenza.

## <a name="production-readiness-checklist"></a>Elenco di controllo per l'idoneità per la produzione
L'applicazione e il cluster sono pronti ad accettare il traffico della produzione? Prima di distribuire il cluster nell'ambiente di produzione, eseguire l' [elenco di controllo della conformità alla produzione](service-fabric-production-readiness-checklist.md). Per garantire la corretta esecuzione dell'applicazione e del cluster, usare gli elementi in questo elenco di controllo. Si consiglia vivamente di disattivare tutti questi elementi prima di passare all'ambiente di produzione.

## <a name="next-steps"></a>Passaggi successivi
* [Creare un cluster Service Fabric che esegue Windows](service-fabric-best-practices-overview.md)
* [Creare un cluster Service Fabric che esegue Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster