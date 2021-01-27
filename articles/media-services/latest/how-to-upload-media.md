---
title: caricare supporti: Descrizione di servizi multimediali di Azure: informazioni su come caricare i file multimediali per lo streaming o la codifica.
Servizi: Media-Services documentationcenter:'' Author: IngridAtMicrosoft Manager: FEMila Editor:''

ms. Service: Media-Services ms. workload: ms. Topic: How-to MS. Date: 08/31/2020 ms. Author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>Caricare supporti per lo streaming o la codifica

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Servizi multimediali è possibile caricare i file digitali in un contenitore BLOB associato a un asset. L'entità [Asset](/rest/api/media/operations/asset) può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file. Dopo il caricamento dei file nel contenitore dell'asset, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

Prima di iniziare, è necessario raccogliere o considerare alcuni valori.

1. Il percorso del file locale da caricare
1. ID asset (contenitore)
1. Nome da usare per il file caricato compresa l'estensione
1. Nome dell'account di archiviazione usato
1. Chiave di accesso per l'account di archiviazione

## <a name="portal"></a>[Portale](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Dopo aver [creato un asset usando Postman o un altro metodo REST e aver ottenuto l'URL di firma di accesso condiviso per l'asset](how-to-create-asset.md?tabs=rest), usare le API di Archiviazione di Azure o gli SDK (ad esempio, l'[API REST di Archiviazione](../../storage/common/storage-rest-api-auth.md) o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Per altri metodi, vedere la [documentazione di Archiviazione di Azure](../../storage/blobs/index.yml) per l'uso di BLOB in [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md) e [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Passaggi successivi

> [Panoramica di Servizi multimediali](media-services-overview.md)