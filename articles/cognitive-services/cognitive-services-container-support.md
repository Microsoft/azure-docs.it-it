---
title: Usare i contenitori di servizi cognitivi di Azure in locale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare i contenitori Docker per usare servizi cognitivi locali.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: locale, Docker, container, Kubernetes
ms.openlocfilehash: 3b0a39e9d84a56bac3117df9279954e3756d465a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065821"
---
# <a name="azure-cognitive-services-containers"></a>Contenitori di Servizi cognitivi di Azure

Servizi cognitivi di Azure offre diversi [contenitori Docker](https://www.docker.com/what-container) che consentono di usare le stesse API disponibili in Azure, in locale. L'uso di questi contenitori ti offre la flessibilità necessaria per avvicinare i servizi cognitivi ai tuoi dati, in modo da garantire conformità, sicurezza o altri motivi operativi. Il supporto dei contenitori è attualmente disponibile per un sottoinsieme di servizi cognitivi di Azure.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

La containerizzazione è un approccio alla distribuzione del software in cui un'applicazione o un servizio, incluse le relative dipendenze e configurazione, viene compresso come immagine del contenitore. L'immagine del contenitore può essere distribuita in un host contenitore senza alcuna variazione o con modifiche minime. I contenitori sono isolati fra loro e dal sistema operativo sottostante, con un footprint inferiore a quello di una macchina virtuale. È possibile creare istanze dei contenitori dalle immagini per attività a breve termine. Tali istanze possono essere rimosse quando non sono più necessarie.

## <a name="features-and-benefits"></a>Funzionalità e vantaggi

- **Infrastruttura non modificabile**: abilitare i team DevOps per sfruttare un set coerente e affidabile di parametri di sistema noti, pur essendo in grado di adattarsi alle modifiche. I contenitori offrono la flessibilità necessaria per passare all'interno di un ecosistema prevedibile ed evitare deviazioni della configurazione.
- **Controllo dei dati**: scegliere la posizione in cui i dati vengono elaborati dai servizi cognitivi. Questo può essere essenziale se non è possibile inviare dati al cloud, ma è necessario accedere a API Servizi cognitivi. È supportata la coerenza in ambienti ibridi, tra i dati e le funzionalità di gestione, identità e sicurezza.
- **Controllo sugli aggiornamenti del modello**: flessibilità nel controllo delle versioni e aggiornamento dei modelli distribuiti nelle soluzioni.
- **Architettura** portabile: consente la creazione di un'architettura di applicazione portatile che può essere distribuita in Azure, in locale e perimetrale. I contenitori possono essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml), in [Istanze di Azure Container](../container-instances/index.yml) o in un cluster [Kubernetes](https://kubernetes.io/) distribuito in [Azure Stack](/azure-stack/operator). Per altre informazioni, vedere [Deploy Kubernetes to Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy) (Distribuire Kubernetes in Azure Stack).
- **Velocità effettiva elevata/bassa latenza**: offrire ai clienti la possibilità di scalare i requisiti di velocità effettiva elevata e bassa latenza, abilitando i servizi cognitivi a funzionare fisicamente vicino alla logica e ai dati dell'applicazione. I contenitori non raggiungono il limite di transazioni al secondo e possono offrire scalabilità sia orizzontale che verticale per gestire la domanda, se si forniscono le risorse hardware necessarie.
- **Scalabilità**: con la popolarità sempre crescente di contenitori e software di orchestrazione dei contenitori, ad esempio Kubernetes; la scalabilità è all'avanguardia dei progressi tecnologici. Basandosi su una base cluster scalabile, lo sviluppo di applicazioni soddisfa la disponibilità elevata.

## <a name="containers-in-azure-cognitive-services"></a>Contenitori in Servizi cognitivi di Azure

I contenitori di servizi cognitivi di Azure forniscono il seguente set di contenitori Docker, ognuno dei quali contiene un subset di funzionalità dei servizi in Servizi cognitivi di Azure. È possibile trovare le istruzioni e i percorsi delle immagini nelle tabelle seguenti. È disponibile anche un elenco di [Immagini del contenitore](containers/container-image-tags.md) .

### <a name="decision-containers"></a>Contenitori decisionali

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|--|
| [Rilevamento anomalie][ad-containers] | **Rilevamento anomalie** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | L'API Rilevamento anomalie permette di monitorare e rilevare le anomalie nei dati di serie temporali con l'apprendimento automatico. | Disponibile a livello generale |

### <a name="language-containers"></a>Contenitori per la lingua

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([immagine](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Carica un modello Language Understanding sottoposto a training o pubblicato, noto anche come app LUIS, in un contenitore Docker e fornisce l'accesso alle stime di query dagli endpoint dell'API del contenitore. È possibile raccogliere i log di query dal contenitore e caricarli nel [portale LUIS](https://www.luis.ai) per migliorare l'accuratezza delle stime dell'app. | Disponibile a livello generale |
| [Analisi del testo][ta-containers-keyphrase] | **Estrazione frasi chiave** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Estrae le frasi chiave per identificare i punti principali. Ad esempio, per il testo di input "Il cibo era delizioso e il personale era meraviglioso", l'API restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso". | Anteprima |
| [Analisi del testo][ta-containers-language] |  **Rilevamento lingua di testo** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Per un massimo di 120 lingue, rileva la lingua in cui è scritto il testo di input e crea un report relativo a un codice lingua singolo per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità. | Anteprima |
| [Analisi del testo][ta-containers-sentiment] | **Analisi del sentiment V3** ([immagine](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analizza testo non elaborato per indicazioni su una valutazione positiva o negativa. Questa versione dell'analisi dei sentimenti restituisce le etichette del sentimento, ad esempio *positivo* o *negativo*, per ogni documento e frase al suo interno. |  Disponibile a livello generale |
| [Analisi del testo][ta-containers-health] |  **Analisi del testo per la sanità** | Estrarre ed etichettare le informazioni mediche da testo clinico non strutturato. | Anteprima gestita. [Richiedere l'accesso][request-access]. |

### <a name="speech-containers"></a>Contenitori per la voce

> [!NOTE]
> Per usare i contenitori di riconoscimento vocale, sarà necessario completare un [modulo di richiesta online](https://aka.ms/csgate).

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|
| [API servizio Voce][sp-containers-stt] |  **Sintesi vocale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Esegue la trascrizione del parlato continuo in tempo reale in testo. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-cstt] | **Da riconoscimento vocale personalizzato a testo** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Trascrive il discorso continuo in tempo reale nel testo usando un modello personalizzato. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-tts] | **Sintesi vocale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Converte il testo scritto in un audio che suona naturale. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-ctts] | **Sintesi vocale personalizzata** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Converte il testo in un riconoscimento vocale naturale usando un modello personalizzato. | Anteprima gestita |
| [API servizio Voce][sp-containers-ntts] | **Sintesi vocale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) neurale | Converte il testo in sintesi vocale naturale usando la tecnologia di rete neurale profonda, consentendo una sintesi vocale più naturale. | Disponibile a livello generale |
| [API servizio Voce][sp-containers-lid] | **Rilevamento della lingua vocale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Determina la lingua dell'audio parlato. | Anteprima gestita |

### <a name="vision-containers"></a>Contenitori per la visione

> [!WARNING]
> L'11 giugno 2020 Microsoft ha annunciato che non venderà la tecnologia di riconoscimento facciale ai dipartimenti di polizia degli Stati Uniti fino a quando non verranno adottate norme rigorose, che tengano conto dei diritti umani. Di conseguenza, i clienti non possono usare le funzionalità di riconoscimento facciale o le funzionalità incluse in Servizi di Azure, ad esempio Viso o Video Indexer, se il cliente è un dipartimento di polizia degli Stati Uniti o consente l'uso di tali servizi a tale dipartimento.

| Servizio |  Contenitore | Descrizione | Disponibilità |
|--|--|--|--|
| [Visione artificiale][cv-containers] | **Lettura OCR** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Il contenitore OCR Read consente di estrarre testo stampato e scritto a mano da immagini e documenti con supporto per formati di file JPEG, PNG, BMP, PDF e TIFF. Per ulteriori informazioni, vedere la [documentazione relativa all'API di lettura](./computer-vision/concept-recognizing-text.md). | Anteprima gestita. [Richiedere l'accesso][request-access]. |
| [Analisi spaziale][spa-containers] | **Analisi spaziale** ([immagine](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analizza i video di streaming in tempo reale per comprendere le relazioni spaziali tra persone, lo spostamento e le interazioni con gli oggetti in ambienti fisici. | Anteprima gestita. [Richiedere l'accesso][request-access]. |
| [Viso][fa-containers] | **Viso** | Rileva i visi umani nelle immagini e identifica gli attributi, ad esempio i punti di riferimento del viso (come naso e occhi), il sesso, l'età e altre caratteristiche facciali con previsioni basate su computer. Oltre al rilevamento, Viso può verificare se due volti nella stessa immagine o in immagini diverse sono uguali mediante un punteggio di attendibilità oppure può confrontare i visi con un database per verificare se esistono già visi simili o identici. È anche possibile organizzare i visi simili in gruppi mediante caratteristiche condivise. | Non disponibile |
| [Riconoscimento moduli][fr-containers] | **Riconoscimento modulo** | La comprensione dei moduli applica la tecnologia di machine learning per identificare ed estrarre le coppie chiave-valore e le tabelle dai moduli. | Non disponibile | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Inoltre, alcuni contenitori sono supportati nell'offerta di [risorse multiservizio](cognitive-services-apis-create-account.md) Servizi cognitivi. È possibile creare una sola risorsa all-in-One di servizi cognitivi e usare la stessa chiave di fatturazione tra i servizi supportati per i servizi seguenti:

* Visione artificiale
* Viso
* LUIS
* Analisi del testo

## <a name="prerequisites"></a>Prerequisiti

Per usare i contenitori di Servizi cognitivi di Azure, è necessario soddisfare i prerequisiti seguenti:

**Motore Docker**: il motore Docker deve essere installato localmente. Docker offre pacchetti per la configurazione dell'ambiente in [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). In Windows Docker deve essere configurato per supportare i contenitori Linux. I contenitori Docker possono anche essere distribuiti direttamente nel [servizio Azure Kubernetes](../aks/index.yml) o in [Istanze di Azure Container](../container-instances/index.yml).

Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure.

**Familiarità con Registro contenitori di Microsoft e Docker**: è opportuno avere una conoscenza di base dei concetti relativi a Registro contenitori di Microsoft e Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.

Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).

Singoli contenitori possono prevedere anche requisiti specifici, tra cui quelli relativi a server e allocazione di memoria.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Esempi per gli sviluppatori

Gli esempi per gli sviluppatori sono disponibili nel [repository GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [ricette del contenitore](containers/container-reuse-recipe.md) che è possibile usare con i servizi cognitivi.

Installare ed esplorare le funzionalità fornite dai contenitori in Servizi cognitivi di Azure:

* [Contenitori dei rilevatori di anomalie][ad-containers]
* [Contenitori di Visione artificiale][cv-containers]
* [Contenitori viso][fa-containers]
* [Contenitori riconoscimento moduli][fr-containers]
* [Contenitori Language Understanding (LUIS)][lu-containers]
* [Contenitori dell'API del servizio vocale][sp-containers]
* [Contenitori di Analisi del testo][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u