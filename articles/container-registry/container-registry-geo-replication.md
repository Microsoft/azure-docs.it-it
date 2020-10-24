---
title: Eseguire la replica geografica di un registro
description: Introduzione alla creazione e alla gestione di un registro contenitori di Azure con replica geografica, che consente di usare il registro per più aree con repliche a livello di area multimaster. La replica geografica è una funzionalità del livello di servizio Premium.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: a26a3a0902b76359dc7441d97fa2516989ec7f0b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486873"
---
# <a name="geo-replication-in-azure-container-registry"></a>Replica geografica nel servizio Registro Azure Container

Le aziende con esigenze di presenza online locale o di backup a caldo scelgono di eseguire i servizi da più aree di Azure. Come procedura consigliata, l'inserimento di un registro contenitori in ogni area in cui vengono eseguite le immagini consente l'esecuzione di operazioni in posizioni di rete vicine e quindi di trasferimenti di livelli di immagine più veloci e affidabili. La replica geografica consente a un Registro Azure Container di fungere da singolo registro in modo da servire più aree con registri regionali multimaster. 

Un registro con replica geografica è caratterizzato dai vantaggi seguenti:

* I nomi di registro/immagine/tag singoli possono essere usati in più aree
* Accesso al registro in una posizione di rete vicina da distribuzioni internazionali
* Nessun costo aggiuntivo per il traffico in uscita perché viene eseguito il pull delle immagini da un registro replicato in locale nella stessa area dell'host del contenitore
* Gestione unica di registro in più aree

> [!NOTE]
> Se è necessario conservare una copia delle immagini del contenitore in più di un registro contenitori di Azure, il Registro Azure Container supporta anche l'[importazione delle immagini](container-registry-import-images.md). In un flusso di lavoro di DevOps, ad esempio, è possibile importare un'immagine da un registro di sviluppo in un registro di produzione anche senza usare comandi Docker.
>

## <a name="example-use-case"></a>Esempio di caso d'uso
Contoso gestisce un sito Web per la presenza online pubblica esteso a Stati Uniti, Canada ed Europa. Per gestire questi mercati con contenuti in locale e in una posizione di rete vicina, Contoso esegue cluster del [servizio Azure Kubernetes](../aks/index.yml) nelle aree Stati Uniti occidentali, Stati Uniti orientali, Canada centrale ed Europa occidentale. L'applicazione del sito Web, distribuita come un'immagine Docker, usa lo stesso codice e la stessa immagine in tutte le aree. Il contenuto locale per un'area specifica viene recuperato da un database, di cui viene eseguito il provisioning in modo univoco in ogni area. Ogni distribuzione regionale dispone della relativa configurazione univoca per risorse quali, ad esempio, il database locale.

Il team di sviluppo si trova a Seattle (Washington) e usa il data center degli Stati Uniti occidentali.

![Esecuzione del push in più registri](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Esecuzione del push in più registri*

Prima di usare le funzionalità di replica geografica, Contoso dispone di un registro situato negli Stati Uniti occidentali e di un registro aggiuntivo situato in Europa occidentale. Per gestire queste due aree, il team di sviluppo ha eseguito il push delle immagini in due registri diversi.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Esecuzione del pool da più registri](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Esecuzione del pool da più registri*

Le tipiche problematiche relative alla presenza di più registri sono descritte di seguito:

* I cluster Stati Uniti orientali, Stati Uniti occidentali e Canada centrale eseguono il pull dal registro nell'area Stati Uniti occidentali. Ciò comporta costi per il traffico in uscita in quanto ciascuno di questi host di contenitori remoti esegue il pull delle immagini dai data center presenti nell'area Stati Uniti occidentali.
* Il team di sviluppo deve eseguire il push delle immagini nei registri presenti nelle aree Stati Uniti occidentali ed Europa occidentale.
* Il team di sviluppo deve configurare e gestire ogni distribuzione regionale mediante nomi di immagini che fanno riferimento al registro in locale.
* L'accesso al registro deve essere configurato per ogni area.

## <a name="benefits-of-geo-replication"></a>Vantaggi della replica geografica

![Esecuzione del pull da un registro con replica geografica](media/container-registry-geo-replication/after-geo-replicate-pull.png)

L'uso della funzionalità di replica geografica di Registro Azure Container è caratterizzato dai vantaggi descritti di seguito:

* Gestione di un unico registro per tutte le aree: `contoso.azurecr.io`
* Gestione di un'unica configurazione per le distribuzioni delle immagini in quanto tutte le aree usano lo stesso URL immagine: `contoso.azurecr.io/public/products/web:1.2`
* Esecuzione del push in un unico registro, mentre il servizio Registro Azure Container gestisce la replica geografica. È possibile configurare [webhook](container-registry-webhook.md) a livello di area per ricevere notifiche degli eventi in repliche specifiche.

## <a name="configure-geo-replication"></a>Configurare la replica geografica

La configurazione della replica geografica è un'operazione semplice basata sulla selezione delle aree mediante clic su una mappa. È anche possibile gestire la replica geografica usando strumenti quali i comandi [az acr replication](/cli/azure/acr/replication) nell'interfaccia della riga di comando di Azure o distribuire un registro abilitato per la replica geografica con un [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

La replica geografica è una funzionalità disponibile per i [registri Premium](container-registry-skus.md). Se la versione del registro non è Premium, è possibile passare dalla versione Basic e quella Standard e infine a quella Premium nel [portale di Azure](https://portal.azure.com):

![Modificare i livelli di servizio nel portale di Azure](media/container-registry-skus/update-registry-sku.png)

Per configurare la replica geografica per un registro Premium, accedere al portale di Azure all'indirizzo https://portal.azure.com.

Passare a Registro Azure Container e selezionare **Repliche**:

![Repliche nell'interfaccia utente del registro contenitori del portale di Azure](media/container-registry-geo-replication/registry-services.png)

Viene visualizzata una mappa che mostra tutte le aree correnti di Azure:

 ![Mappa delle aree nel portale di Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Gli esagoni blu rappresentano le repliche correnti
* Gli esagoni verdi rappresentano le possibili aree di replica
* Gli esagoni grigi rappresentano le aree di Azure non ancora disponibili per la replica

Per configurare una replica, selezionare un esagono verde e quindi fare clic su **Crea**:

 ![Interfaccia utente Crea replica nel portale di Azure](media/container-registry-geo-replication/create-replication.png)

Per configurare repliche aggiuntive, selezionare gli esagoni verdi per le altre aree e quindi fare clic su **Crea**.

Il servizio Registro Azure Container inizia a sincronizzare le immagine tra le repliche configurate. Al termine dell'operazione, nel portale viene visualizzata la dicitura *Pronto*. Lo stato della replica nel portale non viene aggiornato automaticamente. Usare il pulsante Aggiorna per visualizzare lo stato aggiornato.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Considerazioni sull'uso di un registro con replica geografica

* Una volta configurata, ogni area in un registro con replica geografica è indipendente. I contratti di servizio del Registro Azure Container si applicano a ogni area con replica geografica.
* Quando si esegue il push o il pull di immagini da un registro con replica geografica, Gestione traffico di Azure invia in background la richiesta al registro che si trova nell'area più vicina in termini di latenza di rete.
* Una volta eseguito il push dell'aggiornamento di un'immagine o un tag nell'area più vicina, il Registro Azure Container impiega del tempo per replicare i livelli e i manifesti nelle rimanenti aree scelte. La replica delle immagini di grandi dimensioni richiede più tempo rispetto alla replica delle immagini più piccole. Immagini e tag vengono sincronizzati tra le aree di replica con un modello di coerenza finale.
* Per gestire i flussi di lavoro che dipendono da aggiornamenti push a un registro con replica geografica, è consigliabile configurare [webhook](container-registry-webhook.md) per rispondere agli eventi push. È possibile configurare webhook regionali all'interno di un registro con replica geografica per tenere traccia degli eventi push man mano che vengono completati tra le aree con replica geografica.
* Per gestire i BLOB che rappresentano i livelli di contenuto, Azure Container Registry usa gli endpoint di dati. È possibile abilitare gli [endpoint dei dati dedicati](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) per il registro in ognuna delle aree con replica geografica del registro. Questi endpoint consentono la configurazione di regole di accesso del firewall con ambito molto rigorose. Ai fini della risoluzione dei problemi, è possibile [disabilitare facoltativamente il routing a una replica](#temporarily-disable-routing-to-replication) mantenendo i dati replicati.
* Se si configura un [collegamento privato](container-registry-private-link.md) per il registro usando endpoint privati in una rete virtuale, gli endpoint di dati dedicati in ognuna delle aree con replica geografica sono abilitati per impostazione predefinita. 

## <a name="delete-a-replica"></a>Eliminare una replica

Dopo aver configurato una replica per il registro, è possibile eliminarla in qualsiasi momento, se non è più necessaria. Eliminare la replica usando il portale di Azure o altri strumenti, ad esempio il comando [az acr replication delete](/cli/azure/acr/replication#az-acr-replication-delete) nell'interfaccia della riga di comando di Azure.

Per eliminare una replica nel portale di Azure:

1. Passare a Registro Azure Container e selezionare **Repliche**.
1. Selezionare il nome della replica e selezionare **Elimina**. Confermare che si vuole eliminare la replica.

Per usare l'interfaccia della riga di comando di Azure per eliminare una replica di *myregistry* nell'area Stati Uniti orientali:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Prezzi della replica geografica

La replica geografica è una funzionalità del [livello di servizio Premium](container-registry-skus.md) di Registro Azure Container. Quando viene eseguita la replica di un registro nelle aree desiderate, si devono sostenere i costi relativi a un registro Premium per ogni area.

Nell'esempio precedente, Contoso ha unificato due registri mediante il consolidamento e ha aggiunto repliche per le aree Stati Uniti orientali, Canada centrale ed Europa occidentale. Contoso dovrà pagare quattro tariffe Premium al mese, senza costi aggiuntivi per la configurazione e la gestione. Ogni area esegue ora il pull delle relative immagini in locale, migliorando in questo modo prestazioni e affidabilità senza alcun costo aggiuntivo per il traffico in uscita dagli Stati Uniti occidentali al Canada e agli Stati Uniti orientali.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Risolvere i problemi relativi alle operazioni push con registri con replica geografica
 
In caso di push di un'immagine in un registro con replica geografica, un client Docker potrebbe non eseguire il push di tutti i livelli dell'immagine e del relativo manifesto in un'unica area replicata. Questo può verificarsi perché Gestione traffico di Azure indirizza le richieste al registro replicato nella posizione di rete più vicina. Se il registro ha due aree di replica *nelle vicinanze*, i livelli dell'immagine e il manifesto potrebbero essere distribuiti nei due siti e l'operazione push avrà esito negativo quando viene convalidato il manifesto. Questo problema è causato dal modo in cui il nome DNS del registro viene risolto in alcuni host Linux. Non si verifica in Windows, che offre una cache DNS sul lato client.
 
Se il problema si verifica, una soluzione consiste nell'applicare una cache DNS sul lato client, come `dnsmasq`, nell'host Linux. In questo modo, il nome del registro verrà risolto in modo coerente. Se si usa una VM Linux in Azure per il push in un registro, vedere le opzioni illustrate in [Opzioni di risoluzione dei nomi DNS per macchine virtuali Linux in Azure](../virtual-machines/linux/azure-dns.md).

Per ottimizzare la risoluzione DNS nella replica più vicina quando si esegue il push di immagini, configurare un registro con replica geografica nelle stesse aree di Azure dell'origine delle operazioni push oppure nell'area più vicina se si lavora all'esterno di Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Disabilitare temporaneamente il routing alla replica

Per risolvere i problemi relativi alle operazioni con un registro con replica geografica, potrebbe essere necessario disabilitare temporaneamente il routing di gestione traffico a una o più repliche. A partire dall'interfaccia della riga di comando di Azure versione 2,8, è possibile configurare un' `--region-endpoint-enabled` opzione (anteprima) quando si crea o si aggiorna un'area replicata. Quando si imposta l' `--region-endpoint-enabled` opzione di replica su `false` , gestione traffico non instrada più le richieste push o pull di Docker a tale area. Per impostazione predefinita, il routing a tutte le repliche è abilitato e la sincronizzazione dei dati tra tutte le repliche si verifica se il routing è abilitato o disabilitato.

Per disabilitare il routing a una replica esistente, eseguire prima di tutto [AZ ACR Replication list][az-acr-replication-list] per elencare le repliche nel registro di sistema. Eseguire quindi [AZ ACR Replication Update][az-acr-replication-update] e impostare `--region-endpoint-enabled false` per una replica specifica. Ad esempio, per configurare l'impostazione per la replica *westus* in *Registro di sistema*:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Per ripristinare il routing a una replica:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Passaggi successivi

Fare riferimento alla serie di esercitazioni in tre parti relativa alla [replica geografica in Registro Azure Container](container-registry-tutorial-prepare-registry.md). Eseguire la procedura dettagliata relativa alla creazione di un registro con replica geografica, alla compilazione di un contenitore e alla sua distribuzione mediante un unico comando `docker push` in più istanze regionali delle app Web per contenitori.

> [!div class="nextstepaction"]
> [Replica geografica nel servizio Registro Azure Container](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
