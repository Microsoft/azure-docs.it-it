---
title: Gestire il cluster Avere vFXT - Azure
description: 'Come gestire il cluster Avere: aggiungere o rimuovere nodi, riavviare, interrompere o eliminare definitivamente il cluster vFXT'
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 94db4a93025b6e3d633368d924e3e0c518d108ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153480"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Gestire il cluster Avere vFXT

A un certo punto del ciclo di vita del cluster di vFXT per Azure, potrebbe essere necessario aggiungere nodi del cluster o avviare o riavviare il cluster. Al termine del progetto, è necessario saper arrestare il cluster e rimuoverlo definitivamente.

Questo articolo illustra come aggiungere o rimuovere nodi del cluster e altre operazioni di base del cluster. Se è necessario modificare le impostazioni del cluster o monitorarne il funzionamento, usare il [Pannello di controllo](avere-vfxt-cluster-gui.md).

A seconda dell'attività di gestione, potrebbe essere necessario usare uno dei tre diversi strumenti: il pannello di controllo, lo script di gestione cluster della riga di comando vfxt.py e il portale di Azure.

Questa tabella offre una panoramica degli strumenti utilizzabili per ogni attività.

| Azione | Pannello di controllo di Avere | vfxt.py  | Portale di Azure |
| --- | --- | --- | --- |
| Aggiungere nodi del cluster | no | sì | no |
| Rimuovere nodi del cluster | sì | no | no |
| Interrompere un nodo del cluster | sì (è anche possibile riavviare i servizi o riavviare il cluster) | no | lo spegnimento di un macchina virtuale del nodo dal portale viene interpretato come un errore del nodo |
| Avviare un nodo interrotto | no | no | sì |
| Eliminare definitivamente un singolo nodo del cluster | no | no | sì |
| Riavviare il cluster |  |  |  |
| Arrestare o interrompere il cluster in modo sicuro | sì | sì | no |
| Eliminare definitivamente il cluster  | no | sì | sì, ma non è garantita l'integrità dei dati |

Di seguito vengono fornite istruzioni dettagliate per ogni strumento.

## <a name="about-stopped-instances-in-azure"></a>Informazioni sulle istanze interrotte in Azure

Quando si arresta o si interrompe una macchina virtuale di Azure, non vengono più addebitati i costi di calcolo ma rimangono comunque applicabili i costi di archiviazione. Se si arresta un nodo vFXT o l'intero cluster vFXT e non si prevede di riavviarlo, si dovrebbe usare il portale di Azure per eliminare le relative macchine virtuali.

Nel portale di Azure, un nodo *interrotto* , che può essere riavviato, Mostra lo stato **arrestato** nell'portale di Azure. Un nodo *eliminato* Mostra lo stato **arrestato (deallocato)** e non comporta più costi di calcolo o di archiviazione.

Prima di eliminare la macchina virtuale, assicurarsi che tutti i dati modificati siano stati scritti dalla cache nell'archiviazione back-end tramite le opzioni di vfxt.py o del pannello di controllo di Avere per interrompere o arrestare il cluster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Gestire il cluster con il pannello di controllo di Avere

Il pannello di controllo di Avere può essere usato per queste attività:

* Interrompere o riavviare singoli nodi
* Rimuovere un nodo dal cluster
* Interrompere o riavviare l'intero cluster

Il pannello di controllo ha priorità all'integrità dei dati, quindi tenta di scrivere i dati modificati nell'archiviazione back-end prima di un'operazione potenzialmente distruttiva. Questo rende un'opzione più sicura rispetto all'portale di Azure.

È possibile accedere al pannello di controllo di Avere da un Web browser. Seguire le istruzioni in [Accedere al cluster vFXT](avere-vfxt-cluster-gui.md) se si ha bisogno di assistenza.

### <a name="manage-nodes-with-avere-control-panel"></a>Gestire i nodi con il pannello di controllo di Avere

La pagina delle impostazioni **FXT Nodes** (Nodi FXT) include controlli per la gestione dei singoli nodi.

Per arrestare, riavviare o rimuovere un nodo, trovare il nodo nell'elenco nella pagina **FXT Nodes** (Nodi FXT) e fare clic sul pulsante appropriato nella colonna **Actions** (Azioni).

> [!NOTE]
> Gli indirizzi IP potrebbero passare da un nodo del cluster a un altro quando il numero di nodi attivi viene modificato.

Leggere [Cluster > FXT Nodes](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) (Cluster > Nodi FXT) nella guida alle impostazioni del cluster Avere per altre informazioni.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Interrompere o riavviare il cluster con il pannello di controllo di Avere

La pagina delle impostazioni **System Maintenance** (Manutenzione del sistema) include comandi per il riavvio dei servizi cluster, il riavvio del cluster o lo spegnimento del cluster in modo sicuro. Leggere [Administration > System Maintenance](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (Amministrazione > Manutenzione del sistema) nella guida alle impostazioni del cluster Avere per maggiori dettagli.

Quando un cluster inizia a arrestarsi, invia messaggi di stato alla scheda **Dashboard** . Dopo alcuni istanti, i messaggi vengono arrestati e alla fine la sessione del pannello di controllo ha smesso di rispondere, il che significa che il cluster è stato arrestato.

## <a name="manage-the-cluster-with-vfxtpy"></a>Gestire il cluster con vfxt.py

vfxt.py è uno strumento da riga di comando per la gestione e la creazione di cluster.

vfxt.py è preinstallato nella macchina virtuale controller del cluster. Se si vuole installarlo in un altro sistema, vedere la documentazione all'indirizzo <https://github.com/Azure/AvereSDK>.

Lo script vfxt.py può essere usato per queste attività di gestione del cluster:

* Aggiungere nuovi nodi a un cluster
* Interrompere o avviare un cluster  
* Eliminare definitivamente un cluster

Analogamente al pannello di controllo di Avere, le operazioni vfxt.py tentano di verificare che i dati modificati siano archiviati in modo permanente nell'archiviazione back-end prima dell'arresto o dell'eliminazione definitiva del cluster o di un nodo. Questo rende un'opzione più sicura rispetto all'portale di Azure.

La guida completa all'utilizzo di vfxt.py è disponibile in GitHub: [Cloud cluster management with vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md) (Gestione del cluster nel cloud con vfxt.py)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Aggiungere nodi del cluster con vfxt.py

Nel controller del cluster è incluso un esempio di script di comando per l'aggiunta di nodi del cluster. Individuare ``./add-nodes`` nel controller e aprirlo in un editor per personalizzarlo con le informazioni sul cluster.

Il cluster deve essere in esecuzione per poter usare questo comando.

Specificare i valori seguenti:

* Nome del gruppo di risorse per il cluster e anche per le risorse di rete e di archiviazione se non si trovano nello stesso gruppo di risorse del cluster
* Percorso del cluster
* Rete e subnet del cluster
* Ruolo di accesso ai nodi del cluster (usare l' [operatore](../role-based-access-control/built-in-roles.md#avere-operator)Role haveing predefinito)
* Indirizzo IP e password amministrativa per la gestione del cluster
* Numero di nodi da aggiungere (1, 2 o 3)
* Tipo di istanza dei nodi e valori delle dimensioni della cache

Se non si usa il prototipo, è necessario creare un comando simile al seguente, che includa tutte le informazioni descritte in precedenza.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Per altre informazioni, leggere [Add nodes to a cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) (Aggiungere nodi a un cluster) nella guida all'utilizzo di vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Interrompere un cluster con vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Avviare un cluster interrotto con vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Dal momento che il cluster è interrotto, è necessario passare gli identificatori di istanza per specificare i nodi del cluster. Leggere [Specifying which cluster to modify](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) (Specificare quale cluster modificare) nella guida all'utilizzo di vfxt.py per altre informazioni.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Eliminare definitivamente un cluster con vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

È possibile ``--quick-destroy`` utilizzare l'opzione se non si desidera salvare i dati modificati dalla cache del cluster.

Fare riferimento alla [guida all'utilizzo di vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) per altre informazioni.

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Gestire le macchine virtuali di un cluster dal portale di Azure

Il portale di Azure può essere usato per eliminare definitivamente le singole macchine virtuali di un cluster, ma l'integrità dei dati non è garantita se il cluster non viene prima arrestato correttamente.

Il portale di Azure può essere usato per queste attività di gestione del cluster:

* Avviare un nodo vFXT interrotto
* Interrompere un singolo nodo vFXT (il cluster interpreta questa operazione come un errore del nodo)
* Eliminare definitivamente un cluster vFXT *se* non è necessario garantire che i dati modificati nella cache del cluster vengano scritti nel core filer
* Rimuovere definitivamente i nodi vFXT e altre risorse del cluster dopo che sono stati arrestati in modo sicuro

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Riavviare le istanze di vFXT dal portale di Azure

Se occorre riavviare un nodo interrotto, è necessario usare il portale di Azure. Selezionare **Macchine virtuali** nel menu a sinistra e quindi fare clic sul nome della macchina virtuale nell'elenco per aprire la pagina di panoramica corrispondente.

Fare clic sul pulsante **Avvia** nella parte superiore della pagina di panoramica per riattivare la macchina virtuale.

![Schermata del portale di Azure che mostra l'opzione per avviare una macchina virtuale interrotta](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Eliminare i nodi del cluster

Se si vuole eliminare un nodo dal cluster vFXT mantenendo il resto del cluster, è innanzitutto necessario [rimuovere il nodo dal cluster](#manage-nodes-with-avere-control-panel) tramite il pannello di controllo di Avere.

> [!CAUTION]
> Se si elimina un nodo senza prima rimuoverlo dal cluster vFXT, i dati potrebbero andare persi.

Per eliminare definitivamente una o più istanze usate come nodo vFXT, usare il portale di Azure.
Selezionare **Macchine virtuali** nel menu a sinistra e quindi fare clic sul nome della macchina virtuale nell'elenco per aprire la pagina di panoramica corrispondente.

Scegliere il pulsante **Elimina** nella parte superiore della pagina di panoramica per eliminare definitivamente la macchina virtuale.

È possibile usare questo metodo per rimuovere definitivamente i nodi del cluster dopo che sono stati arrestati in modo sicuro.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Eliminare definitivamente il cluster dal portale di Azure

> [!NOTE]
> Se si vuole che le restanti modifiche al client nella cache vengano scritte nell'archiviazione back-end, usare l'opzione `--destroy` di vfxt.py o il pannello di controllo di Avere per arrestare il cluster correttamente prima di rimuovere le istanze dei nodi nel portale di Azure.

È possibile eliminare definitivamente le istanze dei nodi eliminandole nel portale di Azure. È possibile eliminarle una alla volta come descritto in precedenza oppure è possibile usare la pagina **Macchine virtuali** per trovare tutte le macchine virtuali del cluster, selezionarle tramite le relative caselle di controllo e fare clic sul pulsante **Elimina** per rimuoverle tutte con un'unica operazione.

![Elenco delle macchine virtuali nel portale, filtrate per il termine "cluster", con tre di esse selezionate ed evidenziate](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Eliminare le risorse aggiuntive del cluster dal portale di Azure

Se sono state create risorse aggiuntive espressamente per il cluster vFXT, potrebbe essere necessario rimuoverle nell'ambito del processo di eliminazione definitiva del cluster. Non eliminare definitivamente gli elementi che contengono i dati necessari o tutti gli elementi condivisi con altri progetti.

Oltre a eliminare i nodi del cluster, considerare di rimuovere questi componenti:

* Macchina virtuale controller del cluster
* Dischi dati associati ai nodi del cluster
* Interfacce di rete e indirizzi IP pubblici associati ai componenti del cluster
* Reti virtuali
* Contenitori di archiviazione e account di archiviazione (**solo** se non contengono dati importanti)
* Set di disponibilità

![Elenco "Tutte le risorse" del portale di Azure che mostra le risorse create per un cluster di test](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Eliminare un gruppo di risorse del cluster dal portale di Azure

Se è stato creato un gruppo di risorse espressamente per ospitare il cluster, è possibile eliminare definitivamente tutte le risorse correlate per il cluster eliminando definitivamente il gruppo di risorse.

> [!Caution]
> Eliminare definitivamente il gruppo di risorse solo se si è sicuri che non contenga elementi indispensabili. Ad esempio, accertarsi di aver spostato tutti i dati necessari da eventuali contenitori di archiviazione all'interno del gruppo di risorse.  

Per eliminare un gruppo di risorse, fare clic su **Gruppi di risorse** nel menu a sinistra del portale e filtrare l'elenco dei gruppi di risorse per trovare quello creato per il cluster vFXT. Selezionare il gruppo di risorse e fare clic sui tre puntini a destra del pannello. Scegliere **Elimina gruppo di risorse**. All'interno del portale verrà chiesta conferma dell'operazione di eliminazione, che è irreversibile.

![Gruppo di risorse che mostra l'azione "Elimina gruppo di risorse"](media/avere-vfxt-delete-resource-group.png)
