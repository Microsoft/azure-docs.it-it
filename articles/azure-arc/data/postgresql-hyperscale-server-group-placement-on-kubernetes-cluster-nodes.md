---
title: Selezione host di un gruppo di server di iperscala PostgreSQL nei nodi del cluster Kubernetes
description: Illustra il modo in cui le istanze di PostgreSQL che formano un gruppo di server di iperscala PostgreSQL vengono inserite nei nodi del cluster Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b88b36ba8ec1d2d612adbbf19a6cf1e91fbb2cfd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377755"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Selezione host del gruppo di server con iperscalabilità PostgreSQL abilitata per Azure Arc

In questo articolo viene illustrato un esempio per illustrare il modo in cui le istanze di PostgreSQL del gruppo di server con iperscalabilità di PostgreSQL abilitato in Azure Arc vengono inserite nei nodi fisici del cluster Kubernetes che li ospita. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Configurazione

In questo esempio viene usato un cluster di Azure Kubernetes Service (AKS) con quattro nodi fisici. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="cluster AKS a 4 nodi in portale di Azure":::

Elencare i nodi fisici del cluster Kubernetes. Eseguire il comando:

```console
kubectl get nodes
```

`kubectl` restituisce quattro nodi fisici all'interno del cluster Kubernetes:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

L'architettura può essere rappresentata come:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Rappresentazione logica di 4 nodi raggruppati in un cluster Kubernetes":::

Il cluster Kubernetes ospita un controller di dati di Azure Arc e un gruppo di server di scalabilità di PostgreSQL abilitato per Azure Arc. Questo gruppo di server è costituito da tre istanze di PostgreSQL: uno coordinatore e due ruoli di lavoro.

Elencare i pod con il comando:

```console
kubectl get pods -n arc3
```
`kubectl` Restituisce

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
Ognuno di questi Pod ospita un'istanza di PostgreSQL. Insieme, i pod formano il gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc:

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>Selezione host
Viene ora esaminato il modo in cui Kubernetes inserisce i pod del gruppo di server. Descrivere ogni pod e identificare in quale nodo fisico del cluster Kubernetes vengono inseriti. Per il coordinatore, ad esempio, eseguire il comando seguente:

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl` Restituisce

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

Quando si esegue questo comando per ognuno dei Pod, viene riepilogata la posizione corrente come:

| Ruolo del gruppo di server|Pod gruppo di server|Nodo fisico Kubernetes che ospita il Pod |
|--|--|--|
| Worker|postgres01-1|AKS-agentpool-42715708-vmss000002 |
| Worker|postgres01-2|AKS-agentpool-42715708-vmss000003 |

Si noti inoltre che, nella descrizione dei Pod, i nomi dei contenitori ospitati da ogni pod. Per il secondo thread di lavoro, ad esempio, eseguire il comando seguente:

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl` Restituisce

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Ogni pod che fa parte del gruppo di server con iperscalabilità PostgreSQL abilitata per Azure Arc ospita i tre contenitori seguenti:

|Contenitori|Descrizione
|----|----|
|`Fluentbit` |Raccolta dati * log: https://fluentbit.io/
|`Postgres`|Parte dell'istanza di PostgreSQL del gruppo di server con iperscalabilità abilitata per Azure Arc PosgreSQL
|`Telegraf` |Agente di raccolta metriche: https://www.influxdata.com/time-series-platform/telegraf/

L'architettura è simile a quanto segue:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3 Pod ognuno posizionati in nodi separati":::

Ciò significa che, a questo punto, ogni istanza di PostgreSQL che costituisce il gruppo di server di scalabilità di PostgreSQL abilitato per Azure Arc è ospitata in un host fisico specifico all'interno del contenitore Kubernetes. Questa configurazione offre le prestazioni più elevate dal gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc, perché ogni ruolo (coordinatore e thread di lavoro) usa le risorse di ogni nodo fisico. Queste risorse non sono condivise tra diversi ruoli PostgreSQL.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Scalabilità orizzontale dell'iperscalabilità con Azure Arc abilitata per PostgreSQL

A questo punto, è possibile aggiungere un terzo nodo di lavoro al gruppo di server e osservare cosa accade. Verrà creata una quarta istanza di PostgreSQL che verrà ospitata in un quarto pod.
Per eseguire la scalabilità orizzontale, eseguire il comando:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Che produce l'output seguente:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Elencare i gruppi di server distribuiti nel controller di dati di Azure Arc e verificare che il gruppo di server venga ora eseguito con tre ruoli di lavoro. Eseguire il comando:

```console
azdata arc postgres server list
```

E osservare che ha effettuato la scalabilità orizzontale da due ruoli di lavoro a tre ruoli di lavoro:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Come è stato fatto in precedenza, si osservi che il gruppo di server ora usa un totale di quattro Pod:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

E descrivono il nuovo pod per identificare i nodi fisici del cluster Kubernetes in cui sono ospitati.
Eseguire il comando:

```console
kubectl describe pod postgres01w-2 -n arc3
```

Per identificare il nome del nodo di hosting:

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

Il posizionamento delle istanze di PostgreSQL nei nodi fisici del cluster è ora:

|Ruolo del gruppo di server|Pod gruppo di server|Nodo fisico Kubernetes che ospita il Pod
|-----|-----|-----
|Coordinator|postgres01-0|AKS-agentpool-42715708-vmss000000
|Worker|postgres01-1|AKS-agentpool-42715708-vmss000002
|Worker|postgres01-2|AKS-agentpool-42715708-vmss000003
|Worker|postgres01-3|AKS-agentpool-42715708-vmss000000

Si noti che il pod del nuovo ruolo di lavoro (postgres01w-2) è stato inserito nello stesso nodo del coordinatore. 

L'architettura è simile a quanto segue:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Quarto Pod nello stesso nodo del coordinatore":::

Perché il nuovo Worker/Pod non è inserito nel nodo fisico rimanente del cluster Kubernetes AKS-agentpool-42715708-vmss000003?

Il motivo è che l'ultimo nodo fisico del cluster Kubernetes ospita in realtà diversi pod che ospitano componenti aggiuntivi necessari per l'esecuzione di Azure Arc Enabled Data Services. Kubernetes ha valutato che il candidato migliore, al momento della pianificazione, per ospitare il ruolo di lavoro aggiuntivo è il nodo fisico AKS-agentpool-42715708-vmss000000. 

Utilizzando gli stessi comandi indicati in precedenza; si noterà che ogni nodo fisico ospita:

|Altri nomi di Pod\* |Utilizzo|Nodo fisico Kubernetes che ospita i Pod
|----|----|----
|programma di avvio automatico-jh48b|Un servizio che gestisce le richieste in ingresso per la creazione, la modifica e l'eliminazione di risorse personalizzate, ad esempio istanze gestite di SQL, gruppi di server di iperscala PostgreSQL e controller dati|AKS-agentpool-42715708-vmss000003
|Control-gwmbs||AKS-agentpool-42715708-vmss000002
|controldb-0|Archivio dati del controller utilizzato per archiviare la configurazione e lo stato del controller di dati.|AKS-agentpool-42715708-vmss000001
|controlwd-zzjp7|Il servizio "Watch Dog" del controller che consente di tenere sotto controllo la disponibilità del controller di dati.|AKS-agentpool-42715708-vmss000000
|logsdb-0|Istanza di ricerca elastica usata per archiviare tutti i log raccolti in tutti i pod di Arc Data Services. Elasticsearch, riceve i dati dal `Fluentbit` contenitore di ogni pod|AKS-agentpool-42715708-vmss000003
|logsui-5fzv5|Istanza di Kibana che si trova sopra il database di ricerca elastica per presentare un'interfaccia utente grafica di log Analytics.|AKS-agentpool-42715708-vmss000003
|metricsdb-0|Istanza di InfluxDB usata per archiviare tutte le metriche raccolte in tutti i pod di Arc Data Services. InfluxDB, riceve i dati dal `Telegraf` contenitore di ogni pod|AKS-agentpool-42715708-vmss000000
|metricsdc-47d47|Set DAEMON distribuito in tutti i nodi Kubernetes del cluster per raccogliere le metriche a livello di nodo relative ai nodi.|AKS-agentpool-42715708-vmss000002
|metricsdc-864kj|Set DAEMON distribuito in tutti i nodi Kubernetes del cluster per raccogliere le metriche a livello di nodo relative ai nodi.|AKS-agentpool-42715708-vmss000001
|metricsdc-l8jkf|Set DAEMON distribuito in tutti i nodi Kubernetes del cluster per raccogliere le metriche a livello di nodo relative ai nodi.|AKS-agentpool-42715708-vmss000003
|metricsdc-nxm4l|Set DAEMON distribuito in tutti i nodi Kubernetes del cluster per raccogliere le metriche a livello di nodo relative ai nodi.|AKS-agentpool-42715708-vmss000000
|metricsui-4fb7l|Istanza di Grafana che si trova sopra il database InfluxDB per presentare un'interfaccia utente grafica del dashboard di monitoraggio.|AKS-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Livello del proxy dell'applicazione Web che si trova davanti alle istanze Grafana e Kibana.|AKS-agentpool-42715708-vmss000002

> \* Il suffisso nei nomi di Pod può variare in altre distribuzioni. Vengono inoltre elencati solo i pod ospitati all'interno dello spazio dei nomi Kubernetes del controller di dati di Azure Arc.

L'architettura è simile a quanto segue:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Tutti i pod nello spazio dei nomi in diversi nodi":::

Come descritto in precedenza, i nodi coordinatore (pod 1) del gruppo di server con iperscalabilità di Azure Arc abilitati condividono le stesse risorse fisiche del terzo nodo del ruolo di lavoro (Pod 4) del gruppo di server. Questo comportamento è accettabile perché il nodo coordinatore USA in genere poche risorse rispetto a quelle che possono essere usate da un nodo di lavoro. Per questo motivo, scegliere con attenzione:
- dimensioni del cluster Kubernetes e delle caratteristiche di ogni nodo fisico (memoria, vCore)
- il numero di nodi fisici all'interno del cluster Kubernetes
- le applicazioni o i carichi di lavoro ospitati nel cluster Kubernetes.

L'implicazione dell'hosting di troppi carichi di lavoro nel cluster Kubernetes è possibile che si verifichi una limitazione per il gruppo di server con scalabilità PostgreSQL abilitata per Azure Arc. In tal caso, la scalabilità orizzontale non sarà vantaggiosa. Le prestazioni che si ottengono dal sistema non si limitano alla selezione host o alle caratteristiche fisiche dei nodi fisici o del sistema di archiviazione. Per ottenere le prestazioni, è anche possibile configurare ognuna delle risorse in esecuzione all'interno del cluster Kubernetes, inclusa la scalabilità di PostgreSQL abilitata per Azure Arc, ad esempio le richieste e i limiti impostati per la memoria e vCore. La quantità di carico di lavoro che è possibile ospitare in un determinato cluster Kubernetes è relativa alle caratteristiche del cluster Kubernetes, alla natura dei carichi di lavoro, al numero di utenti, al modo in cui vengono eseguite le operazioni del cluster Kubernetes...

## <a name="scale-out-aks"></a>Scale out AKS

Si dimostrerà che la scalabilità orizzontale sia del cluster AKS che del server Azure con iperscalabilità abilitata per Azure Arc è un modo per sfruttare al meglio le prestazioni elevate dell'iperscalabilità di PostgreSQL abilitata per Azure Arc.
Aggiungere un quinto nodo al cluster AKS:

:::row:::
    :::column:::
        Prima
    :::column-end:::
    :::column:::
        After
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="portale di Azure layout prima":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Layout portale di Azure dopo":::
    :::column-end:::
:::row-end:::

L'architettura è simile a quanto segue:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Layout logico nel cluster Kubernetes dopo l'aggiornamento":::

Verranno ora esaminati i pod dello spazio dei nomi del controller di dati Arc ospitati nel nuovo nodo fisico AKS eseguendo il comando:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

Viene ora aggiornata la rappresentazione dell'architettura del sistema:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Tutti i Pod nel diagramma logico del cluster":::

È possibile osservare che il nuovo nodo fisico del cluster Kubernetes ospita solo il Pod metrica necessario per Azure Arc Data Services. Si noti che, in questo esempio, si sta concentrando solo sullo spazio dei nomi del controller di dati Arc, non vengono rappresentati gli altri pod.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Aumentare di nuovo la scalabilità orizzontale con Azure Arc abilitata

Il quinto nodo fisico non ospita ancora un carico di lavoro. Man mano che la scalabilità orizzontale è abilitata per Azure Arc, Kubernetes ottimizza il posizionamento del nuovo pod PostgreSQL e non deve collocarlo nei nodi fisici che ospitano già più carichi di lavoro. Eseguire il comando seguente per ridimensionare l'iperscalabilità di PostgreSQL abilitata per Azure Arc da 3 a 4 processi di lavoro. Al termine dell'operazione, il gruppo di server verrà costituito e distribuito tra cinque istanze di PostgreSQL, un coordinatore e quattro thread di lavoro.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Che produce l'output seguente:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Elencare i gruppi di server distribuiti nel controller dati e verificare che il gruppo di server venga ora eseguito con quattro ruoli di lavoro:

```console
azdata arc postgres server list
```

E osservare che la scalabilità orizzontale è stata eseguita da tre a quattro ruoli di lavoro. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Come è stato fatto in precedenza, osservare che il gruppo di server ora usa quattro Pod:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

La forma del gruppo di server è ora:

|Ruolo del gruppo di server|Pod gruppo di server
|----|-----
|Coordinator|postgres01c-0
|Worker|postgres01w-0
|Worker|postgres01w-1
|Worker|postgres01w-2
|Worker|postgres01w-3

Verrà ora descritto il Pod postgres01w-3 per identificare il nodo fisico in cui è ospitato:

```console
kubectl describe pod postgres01w-3 -n arc3
```

E osservare i pod in esecuzione:

|Ruolo del gruppo di server|Pod gruppo di server| Pod
|----|-----|------
|Coordinator|postgres01c-0|AKS-agentpool-42715708-vmss000000
|Worker|postgres01w-0|AKS-agentpool-42715708-vmss000002
|Worker|postgres01w-1|AKS-agentpool-42715708-vmss000003
|Worker|postgres01w-2|AKS-agentpool-42715708-vmss000000
|Worker|postgres01w-3|AKS-agentpool-42715708-vmss000004

L'architettura ha un aspetto simile al seguente:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes Pianifica il pod più recente nel nodo con utilizzo più basso":::

Kubernetes ha pianificato il nuovo pod PostgreSQL nel nodo fisico meno caricato del cluster Kubernetes.

## <a name="summary"></a>Riepilogo

Per sfruttare al meglio la scalabilità e le prestazioni di scalabilità orizzontale del gruppo di server abilitati per Azure Arc, è consigliabile evitare la contesa di risorse all'interno del cluster Kubernetes:
- tra il gruppo di server con iperscalabilità di Azure Arc abilitato e gli altri carichi di lavoro ospitati nello stesso cluster Kubernetes
- tra tutte le istanze di PostgreSQL che costituiscono il gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

È possibile ottenere questo risultato in diversi modi:
- Scalabilità orizzontale di Kubernetes e Azure Arc abilitata per l'iperscalabilità Postgres: considerare la scalabilità orizzontale del cluster Kubernetes nello stesso modo in cui si esegue il ridimensionamento del gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc. Aggiungere un nodo fisico al cluster per ogni ruolo di lavoro aggiunto al gruppo di server.
- Scalabilità orizzontale di Azure Arc abilitata per Postgres senza scalabilità orizzontale Kubernetes: impostando i vincoli di risorse corretti (richiesta e limiti per la memoria e vCore) sui carichi di lavoro ospitati in Kubernetes (inclusa la scalabilità su PostgreSQL abilitata per Azure Arc), si Abilita la condivisione dei carichi di lavoro in Kubernetes e si riduce il rischio di contesa delle risorse. È necessario assicurarsi che le caratteristiche fisiche dei nodi fisici del cluster Kubernetes possano rispettare i vincoli di risorse definiti. È anche necessario assicurarsi che l'equilibrio rimanga quando i carichi di lavoro si evolvono nel tempo o man mano che vengono aggiunti più carichi di lavoro nel cluster Kubernetes.
- Usare i meccanismi Kubernetes (selettore Pod, affinità, anti-affinità) per influenzare il posizionamento dei pod.

## <a name="next-steps"></a>Passaggi successivi

[Scalabilità orizzontale del gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc aggiungendo più nodi di lavoro](scale-out-postgresql-hyperscale-server-group.md)
