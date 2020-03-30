---
title: Risolvere i problemi comuni
description: Informazioni su come risolvere i problemi comuni durante la distribuzione, l'esecuzione o la gestione delle istanze del contenitore di AzureLearn how to troubleshoot common issues when your deploy, run, or manage Azure Container Instances
ms.topic: article
ms.date: 09/25/2019
ms.custom: mvc
ms.openlocfilehash: 07cdbfb27aaf9076e726ebda861ed24996e10135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533385"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Risolvere i problemi comuni in Istanze di Azure Container

Questo articolo mostra come risolvere i problemi comuni per la gestione o la distribuzione di contenitori in Istanze di Azure Container. Vedere anche [Domande frequenti](container-instances-faq.md).

Se è necessario ulteriore supporto, vedere **Guida disponibile e opzioni** di supporto nel portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="issues-during-container-group-deployment"></a>Problemi durante la distribuzione di Gruppo di contenitori
### <a name="naming-conventions"></a>Convenzioni di denominazione

Quando si definisce la specifica del contenitore, determinati parametri devono essere conformi a limitazioni di denominazione. Nella tabella seguente sono disponibili i requisiti specifici per le proprietà dei gruppi di contenitori. Per altre informazioni sulle convenzioni di denominazione di Azure, vedere [Regole di denominazione e restrizioni][azure-name-restrictions] nel Centro architettura di Azure.

| Scope | Length | Maiuscole/minuscole | Caratteri validi | Schema consigliato | Esempio |
| --- | --- | --- | --- | --- | --- |
| Nome del gruppo di contenitori | 1-64 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e trattino in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nome contenitore | 1-64 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e trattino in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porte del contenitore | Tra 1 e 65535 |Integer |Numero intero compreso tra 1 e 65535 |`<port-number>` |`443` |
| Etichetta del nome DNS | 5-63 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e trattino in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>` |`frontend-site1` |
| Variabile di ambiente | 1-63 |Non fa distinzione tra maiuscole e minuscole |Carattere alfanumerico e carattere di sottolineatura '_' in un punto qualsiasi, tranne il primo o l'ultimo carattere |`<name>` |`MY_VARIABLE` |
| Nome del volume | 5-63 |Non fa distinzione tra maiuscole e minuscole |Lettere minuscole, numeri e trattini in un punto qualsiasi, tranne il primo o l'ultimo carattere. Non può contenere due trattini consecutivi. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>Versione del sistema operativo dell'immagine non supportata

Se si specifica un'immagine non supportata da Istanze di Azure Container, viene restituito un errore `OsVersionNotSupported`. L'errore è simile al seguente, dove `{0}` è il nome dell'immagine che si è tentato di distribuire:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Questo errore si verifica più spesso quando si distribuiscono immagini Windows basate sulla versione 1709 o 1803 del canale semestrale, che non sono supportate. Per le immagini Windows supportate nelle istanze del contenitore di Azure, vedere [Domande frequenti.](container-instances-faq.md#what-windows-base-os-images-are-supported)

### <a name="unable-to-pull-image"></a>Non è possibile eseguire il pull dell'immagine

Se non è inizialmente in grado di eseguire il pull dell'immagine, Istanze di Azure Container ripete il tentativo per un periodo di tempo. Se l'operazione di pull dell'immagine continua a non riuscire, Istanze di contenitore di Azure non esegue correttamente la distribuzione e potrebbe essere visualizzato un errore `Failed to pull image`.

Per risolvere questo problema, eliminare l'istanza di contenitore e ripetere la distribuzione. Verificare che l'immagine esista nel registro e che il nome dell'immagine sia stato digitato correttamente.

Se il pull dell'immagine non può essere eseguito, vengono visualizzati eventi simili al seguente nell'output di [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Errore di risorsa non disponibile

A causa del carico variabile delle risorse delle aree in Azure, quando si cerca di distribuire un'istanza di contenitore, potrebbe verificarsi l'errore seguente:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Questo errore indica che a causa di un carico elevato nell'area in cui si sta cercando di eseguire la distribuzione, le risorse specificate per il contenitore non possono essere al momento allocate. Usare uno o più dei passaggi seguenti per mitigare il problema.

* Verificare che le impostazioni di distribuzione del contenitore rientrino nei parametri definiti in [Disponibilità a livello di area per Istanze di Azure Container](container-instances-region-availability.md)
* Specificare impostazioni di memoria e CPU inferiori per il contenitore
* Eseguire la distribuzione in un'area di Azure diversa
* Eseguire la distribuzione in un secondo momento

## <a name="issues-during-container-group-runtime"></a>Problemi durante il runtime del gruppo di contenitori
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Il contenitore viene continuamente chiuso e riavviato (nessun processo a esecuzione prolungata)

I gruppi di contenitori vengono impostati automaticamente sul [criterio di riavvio](container-instances-restart-policy.md)**Always**, in modo che i contenitori nel gruppo di contenitori eseguano sempre il riavvio dopo il completamento dell'esecuzione. Potrebbe essere necessario impostare questa opzione su **OnFailure** oppure **Never** se si prevede di eseguire i contenitori basati su attività. Se si specifica **OnFailure** e si riscontra una situazione di riavvio continuo, potrebbe essere presente un problema con l'applicazione o lo script eseguito nel contenitore.

Durante l'esecuzione di gruppi di contenitori senza processi a esecuzione prolungata è probabile che si verifichino ripetute uscite e riavvii con le immagini, ad esempio Ubuntu o Alpine. La connessione tramite [EXEC](container-instances-exec.md) non funzionerà in quanto il contenitore non dispone di alcun processo che lo mantiene attivo. Per risolvere questo problema, includere un comando di avvio simile al seguente con la distribuzione del gruppo di contenitori per mantenere il contenitore in esecuzione.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

L'API Istanze di Container e il portale di Azure includono una proprietà `restartCount`. Per controllare il numero di riavvii di un contenitore, è possibile usare il comando [az container show][az-container-show] nell'interfaccia della riga di comando di Azure. Nell'output di esempio seguente (troncato per brevità) la proprietà `restartCount` è visualizzata alla fine dell'output.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> La maggior parte delle immagini di contenitore per le distribuzioni di Linux imposta una shell, ad esempio bash, come comando predefinito. Poiché una shell non è di per sé un servizio a esecuzione prolungata, questi contenitori vengono chiusi immediatamente e sono soggetti a un riavvio ciclico quando sono configurati in base al criterio di riavvio predefinito **Always**.

### <a name="container-takes-a-long-time-to-start"></a>L'avvio di un contenitore richiede molto tempo

I tre fattori principali che contribuiscono all'ora di avvio del contenitore nelle istanze del contenitore di Azure sono:The three primary factors that contribute to container startup time in Azure Container Instances are:

* [Dimensioni dell'immagine](#image-size)
* [Posizione dell'immagine](#image-location)
* [Immagini memorizzate nella cache](#cached-images)

Per le immagini Windows sono necessarie [altre considerazioni](#cached-images).

#### <a name="image-size"></a>Dimensioni dell'immagine

Se per avviare il contenitore è necessario molto tempo, ma alla fine l'operazione ha esito positivo, esaminare innanzitutto la dimensione dell'immagine del contenitore. Poiché Istanze di Azure Container esegue il pull dell'immagine del contenitore su richiesta, il tempo di avvio indicato è direttamente correlato alla dimensione dell'immagine.

Per visualizzare la dimensione dell'immagine del contenitore, è possibile usare il comando `docker images` nell'interfaccia della riga di comando di Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Il modo migliore per limitare le dimensioni delle immagini è quello di evitare che l'immagine finale contenga dati che non sono necessari in fase di esecuzione. A tale scopo è possibile eseguire [compilazioni in più fasi][docker-multi-stage-builds]. Le compilazioni di questo tipo consentono di assicurarsi che l'immagine finale contenga solo gli elementi necessari per l'applicazione, escludendo altro contenuto richiesto in fase di compilazione.

#### <a name="image-location"></a>Posizione dell'immagine

Un altro modo per ridurre l'impatto del pull dell'immagine sul tempo di avvio del contenitore è quello di ospitare l'immagine del contenitore in [Registro Azure Container](/azure/container-registry/) nella stessa area in cui si intende distribuire le istanze del contenitore. Ciò consente di ridurre il percorso dell'immagine del contenitore attraverso la rete e quindi di limitare notevolmente il tempo di download.

#### <a name="cached-images"></a>Immagini memorizzate nella cache

Le istanze del contenitore di Azure usano un meccanismo di memorizzazione `servercore:ltsc2019`nella `servercore:1809`cache per velocizzare il tempo di avvio del contenitore per le immagini basate sulle immagini di [base](container-instances-faq.md#what-windows-base-os-images-are-supported)comuni, tra cui `nanoserver:1809`, e . Immagini Linux di `ubuntu:1604` uso `alpine:3.6` comune, ad esempio e sono anche memorizzati nella cache. Per un elenco aggiornato di immagini e tag memorizzati nella cache, usare l'API [Elenca immagini memorizzate nella cache.][list-cached-images]

> [!NOTE]
> L'uso di immagini basate su Windows Server 2019 in istanze di Azure Container è disponibile in anteprima.

#### <a name="windows-containers-slow-network-readiness"></a>La rete diventa disponibile lentamente per i contenitori Windows

Al momento della creazione iniziale, è possibile che per i contenitori Windows non sia presente la connettività in ingresso o in uscita per un intervallo massimo di 30 secondi (o più lungo in alcuni rari casi). Se per l'applicazione contenitore è necessaria una connessione Internet, aggiungere un ritardo e ripetere la logica per consentire di stabilire la connettività Internet in un intervallo di 30 secondi. Dopo l'installazione iniziale, le funzionalità di rete per i contenitori dovrebbero riprendere in modo appropriato.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Non è possibile connettersi all'API Docker sottostante o eseguire contenitori con privilegi

Istanze di Azure Container non espone l'accesso diretto all'infrastruttura sottostante che ospita i gruppi di contenitori. È incluso l'accesso all'API Docker in esecuzione nell'host del contenitore e che esegue contenitori con privilegi. Se è necessaria l'interazione con Docker, vedere la [documentazione di riferimento su REST](https://aka.ms/aci/rest) per vedere cosa è supportato dall'API di Istanze di contenitore di Azure. Se mancano informazioni, inviare una richiesta al [forum di commenti e suggerimenti per Istanze di contenitore di Azure](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>L'indirizzo IP del gruppo di contenitori potrebbe non essere accessibile a causa di porte non corrispondenti

Le istanze del contenitore di Azure non supportano ancora il mapping delle porte, ad esempio con la normale configurazione della finestra mobile. Se si rileva che l'indirizzo IP di un gruppo di contenitori non è accessibile quando si ritiene che `ports` sia necessario, assicurarsi di aver configurato l'immagine del contenitore per l'ascolto delle stesse porte esposte nel gruppo di contenitori con la proprietà .

Se si vuole verificare che le istanze del contenitore di Azure possano rimanere in ascolto sulla porta configurata nell'immagine del contenitore, testare una distribuzione dell'immagine `aci-helloworld` che espone la porta. Eseguire anche `aci-helloworld` l'app in modo che sia in ascolto sulla porta. `aci-helloworld`accetta una variabile `PORT` di ambiente facoltativa per eseguire l'override della porta predefinita 80 su cui è in ascolto. Ad esempio, per testare la porta 9000, impostare la variabile di ambiente quando si crea il gruppo di contenitori:For example, to test port 9000, set the [environment variable](container-instances-environment-variables.md) when you create the container group:

1. Impostare il gruppo di contenitori per esporre la porta 9000 e passare il numero di porta come valore della variabile di ambiente. L'esempio è formattato per la shell Bash. Se si preferisce un'altra shell, ad esempio PowerShell o Prompt dei comandi, sarà necessario regolare l'assegnazione delle variabili di conseguenza.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Individuare l'indirizzo IP del gruppo di `az container create`contenitori nell'output del comando di . Cercare il valore di **ip**. 
1. Dopo aver eseguito correttamente il provisioning del contenitore, passare all'indirizzo `192.0.2.0:9000`IP e alla porta dell'app contenitore nel browser, ad esempio: . 

    Verrà visualizzato il messaggio "Benvenuti in Istanze del contenitore di Azure!". visualizzato dall'app Web.
1. Al termine dell'operazione con il contenitore, rimuoverlo utilizzando il `az container delete` comando:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come recuperare i log e gli eventi del [contenitore](container-instances-get-logs.md) per facilitare il debug dei contenitori.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
