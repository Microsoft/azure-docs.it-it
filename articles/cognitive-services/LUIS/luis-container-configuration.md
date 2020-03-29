---
title: Docker container settings - LUIS
titleSuffix: Azure Cognitive Services
description: L'ambiente di runtime del contenitore LUIS si configura mediante gli argomenti del comando `docker run`. LUIS ha diverse impostazioni necessarie e alcune impostazioni facoltative.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795021"
---
# <a name="configure-language-understanding-docker-containers"></a>Configurare i contenitori Docker di Language Understanding 

L'ambiente di runtime del contenitore Language `docker run` **Understanding** (LUIS) viene configurato utilizzando gli argomenti del comando. LUIS ha diverse impostazioni necessarie e alcune impostazioni facoltative. Sono disponibili numerosi [esempi](#example-docker-run-commands) del comando. Le impostazioni specifiche del contenitore sono le [impostazioni di montaggio](#mount-settings) di input e le impostazioni di fatturazione. 

## <a name="configuration-settings"></a>Impostazioni di configurazione

Questo contenitore ha le impostazioni di configurazione seguenti:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|Sì|[ApiKey (Chiave api)](#apikey-setting)|Si usa per rilevare le informazioni di fatturazione.|
|No|[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Sì|[Fatturazione](#billing-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Sì|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No|[Fluentd](#fluentd-settings)|Scrivere il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No|[Proxy HTTP](#http-proxy-credentials-settings)|Configurare un proxy HTTP per le richieste in uscita.|
|No|[Registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|Sì|[Mounts](#mount-settings)|Leggere e scrivere dati dal computer host al contenitore e dal contenitore al computer host.|

> [!IMPORTANT]
> Le [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting)impostazioni [`Eula`](#eula-setting) , e vengono utilizzate insieme ed è necessario fornire valori validi per tutti e tre; in caso contrario, il contenitore non verrà avviato. Per altre informazioni sull'uso di queste impostazioni di configurazione per creare un'istanza di un contenitore, vedere [Billing](luis-container-howto.md#billing) (Fatturazione).

## <a name="apikey-setting"></a>Impostazione ApiKey

L'impostazione `ApiKey` specifica la chiave di risorsa di Azure utilizzata per tenere traccia delle informazioni di fatturazione per il contenitore. È necessario specificare un valore per ApiKey e il valore deve essere [`Billing`](#billing-setting) una chiave valida per la risorsa _Servizi cognitivi_ specificata per l'impostazione di configurazione.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Gestione delle risorse **di Servizi cognitivi,** in **ChiaviAzure** portal: Cognitive Services Resource Management, under Keys
* Portale LUIS: pagina **delle impostazioni delle chiavi e dell'endpoint.** 

Non usare il tasto starter o la chiave di creazione. 

## <a name="applicationinsights-setting"></a>Impostazione ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Impostazione di fatturazione

L'impostazione `Billing` specifica l'URI dell'endpoint della risorsa _Servizi cognitivi_ in Azure usato per misurare le informazioni di fatturazione per il contenitore. È necessario specificare un valore per questa impostazione di configurazione e il valore deve essere un URI di endpoint valido per una risorsa _di Servizi cognitivi_ in Azure.You must specify a value for this configuration setting, and the value must be a valid endpoint URI for a Cognitive Services resource on Azure. Il contenitore segnala l'utilizzo ogni 10-15 minuti.

Questa impostazione è disponibile nelle posizioni seguenti:

* Portale di Azure: Panoramica dei **servizi cognitivi,** con etichettaAzure portal: Cognitive Services Overview, labeled`Endpoint`
* Portale LUIS: pagina **delle impostazioni delle chiavi e degli endpoint,** come parte dell'URI dell'endpoint.

| Obbligatoria | Nome | Tipo di dati | Descrizione |
|----------|------|-----------|-------------|
| Sì      | `Billing` | string | URI dell'endpoint di fatturazione. Per ulteriori informazioni su come ottenere l'URI di fatturazione, vedere [Raccolta dei parametri obbligatori](luis-container-howto.md#gathering-required-parameters). Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Impostazione Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Impostazioni Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Impostazioni delle credenziali del proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Impostazioni di registrazione
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Impostazioni di montaggio

Usare montaggi di associazione per leggere e scrivere dati da e verso il contenitore. È possibile specificare un montaggio di input o di output specificando l'opzione `--mount` nel comando [docker run](https://docs.docker.com/engine/reference/commandline/run/). 

Il contenitore LUIS non usa montaggi di input o output per archiviare i dati di training o di servizio. 

La sintassi esatta della posizione di montaggio host varia a seconda del sistema operativo host. Inoltre, il percorso di montaggio del [computer host](luis-container-howto.md#the-host-computer)potrebbe non essere accessibile a causa di un conflitto tra le autorizzazioni utilizzate dall'account del servizio docker e le autorizzazioni del percorso di montaggio dell'host. 

La tabella seguente descrive le impostazioni supportate.

|Obbligatoria| Nome | Tipo di dati | Descrizione |
|-------|------|-----------|-------------|
|Sì| `Input` | string | Destinazione del montaggio di input. Il valore predefinito è `/input`. Questo è il percorso del file del pacchetto LUIS. <br><br>Esempio:<br>`--mount type=bind,src=c:\input,target=/input`|
|No| `Output` | string | Destinazione del montaggio di output. Il valore predefinito è `/output`. Questo è il percorso dei log. Include i log di query LUIS e i log dei contenitori. <br><br>Esempio:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Comandi docker run di esempio

Gli esempi seguenti usano le impostazioni di configurazione per illustrare come scrivere e usare i comandi `docker run`.  Quando è in esecuzione, il contenitore continua l'esecuzione finché non lo si [arresta](luis-container-howto.md#stop-the-container).

* In questi esempi viene `C:` utilizzata la directory all'esta dall'unità per evitare conflitti di autorizzazioni in Windows. Se è necessario usare una directory specifica come directory di input, potrebbe essere necessario concedere l'autorizzazione per il servizio Docker. 
* Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.
* Se si utilizza un sistema operativo diverso, utilizzare la console/terminale corretto, la sintassi della cartella per i supporti e il carattere di continuazione di riga per il sistema in uso. In questi esempi si presuppone che `^`una console di Windows con un carattere di continuazione di riga . Poiché il contenitore è un sistema operativo Linux, il mount di destinazione utilizza una sintassi di cartella in stile Linux.Because the container is a Linux operating system, the target mount uses a Linux-style folder syntax.

Sostituire {_nome_argomento_} con i propri valori:

| Segnaposto | valore | Formato o esempio |
|-------------|-------|---|
| **"API_KEY"** | Chiave dell'endpoint `LUIS` della risorsa `LUIS` nella pagina Chiavi di Azure.The endpoint key of the resource on the Azure Keys page. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **ENDPOINT_URI e così via.** | Il valore dell'endpoint di `LUIS` fatturazione è disponibile nella pagina Panoramica di Azure.The billing endpoint value is available on the Azure Overview page.| Vedere [la raccolta dei parametri obbligatori](luis-container-howto.md#gathering-required-parameters) per esempi espliciti. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia. Per altre informazioni, vedere[Fatturazione](luis-container-howto.md#billing).
> Il valore ApiKey è la **chiave** della pagina Chiavi ed endpoint nel portale `Cognitive Services` LUIS ed è disponibile anche nella pagina delle chiavi delle risorse di Azure.The ApiKey value is the Key from the Keys and Endpoints page in the LUIS portal and is also available on the Azure resource keys page. 

### <a name="basic-example"></a>Esempio di base

L'esempio seguente include gli argomenti minimi possibili per eseguire il contenitore:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Esempio di ApplicationInsights

L'esempio seguente imposta l'argomento ApplicationInsights per inviare dati di telemetria ad Application Insights mentre il contenitore è in esecuzione:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Esempio di registrazione 

Il comando riportato di `Logging:Console:LogLevel`seguito consente di [`Information`](https://msdn.microsoft.com)impostare il livello di registrazione , per configurare il livello di registrazione su . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Come installare ed eseguire i contenitori](luis-container-howto.md)
* Per risolvere i problemi correlati alle funzionalità di LUIS, vedere [Risoluzione dei problemi](troubleshooting.md).
* Usare più [contenitori di Servizi cognitiviUse more Cognitive Services Containers](../cognitive-services-container-support.md)
