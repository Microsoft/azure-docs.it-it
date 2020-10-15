---
title: Codici di errore di codifica di Servizi multimediali di Azure | Documentazione Microsoft
description: Questo argomento riporta un elenco dei possibili codici di errore restituiti in caso di errore durante l'esecuzione di attività di codifica.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6e56dbe1d1236a567ed6f59acfcca325a6c9ee7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269030"
---
# <a name="encoding-error-codes"></a>Codici di errore di codifica

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Nella tabella seguente sono elencati i codici di errore che potrebbero essere restituiti quando si verifica un errore durante l'esecuzione di attività di codifica.  Per ottenere i dettagli dell'errore nel codice .NET, usare la classe [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) . Per ottenere i dettagli dell'errore nel codice REST, usare l'API REST [ErrorDetail](/rest/api/media/operations/errordetail) .

| ErrorDetail.Code | Le possibili cause dell'errore |
| --- | --- |
| Sconosciuto |Errore sconosciuto durante l'esecuzione dell'attività |
| ErrorDownloadingInputAssetMalformedContent |Categoria di errori relativa agli errori durante il download di asset di input, ad esempio nomi di file non validi, file di lunghezza zero, formati errati e così via. |
| ErrorDownloadingInputAssetServiceFailure |Categoria di errori relativa a problemi sul lato del servizio, ad esempio errori di rete o archiviazione durante il download. |
| ErrorParsingConfiguration |Categoria di errori in cui l'attività \<see cref="MediaTask.PrivateData"/> (configurazione) non è valida, ad esempio la configurazione non è un set di impostazioni di sistema valido o contiene XML non valido. |
| ErrorExecutingTaskMalformedContent |Categoria di errori durante l'esecuzione dell'attività in cui i problemi nei file multimediali di input causano un errore. |
| ErrorExecutingTaskUnsupportedFormat |Categoria di errori in cui il processore di contenuti multimediali non è in grado di elaborare i file forniti: formato di file multimediale non supportato o non corrispondente alla configurazione. Ad esempio, si tenta di produrre un output solo audio da un asset che ha il solo video |
| ErrorProcessingTask |Categoria di altri errori che il processore di contenuti multimediali rileva durante l'elaborazione dell'attività non correlati al contenuto. |
| ErrorUploadingOutputAsset |Categoria di errori durante il caricamento di asset di output |
| ErrorCancelingTask |Categoria di errori per coprire gli errori durante il tentativo di annullare l'attività |
| TransientError |Categoria di errori sui problemi transitori (es. problemi di rete temporanei con Azure Storage) |

Per ottenere assistenza dal team di **Servizi multimediali** , aprire un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articoli correlati
* [Eseguire attività di codifica avanzata personalizzando i set di impostazioni di Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quote e limitazioni](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
