---
title: Riflessi del cielo
description: Viene descritto come configurare le mappe ambientali per i riflessi del cielo
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c5ad4b21b428f38bbd4d9f7d19fa633c5161b5c
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594181"
---
# <a name="sky-reflections"></a>Riflessi del cielo

In Rendering remoto di Azure, per illuminare gli oggetti in modo realistico viene usata una trama del cielo. Per le applicazioni di realtà aumentata, questa trama dovrebbe essere simile a quella del mondo reale, in modo da rendere gli oggetti convincenti. Questo articolo descrive come modificare la trama del cielo.

> [!NOTE]
> La trama del cielo è detta anche *mappa ambientale*. Questi termini vengono usati in modo interscambiabile.

## <a name="object-lighting"></a>Illuminazione degli oggetti

Rendering remoto di Azure usa il *Physically Based Rendering* (PBR) per calcolare l'illuminazione in modo realistico. Sebbene sia possibile aggiungere [sorgenti di luce](lights.md) alla scena, l'uso di una buona trama del cielo offre i risultati migliori.

Le immagini seguenti illustrano i risultati dell'illuminazione di superfici diverse solo con una trama del cielo:

| Rugosità  | 0                                        | 0,25                                          | 0.5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Non metallico  | ![Dielettrico, rugosità = 0](media/dielectric-0.png)   | ![Dielettrico, rugosità = 0,25](media/dielectric-0.25.png)  | ![Dielettrico, rugosità = 0,5](media/dielectric-0.5.png)  | ![Dielettrico, rugosità = 0,75](media/dielectric-0.75.png)  | ![Dielettrico, rugosità = 1](media/dielectric-1.png)  |
| Metallico      | ![Metal, rugosità = 0](media/metallic-0.png)  | ![Metallo, rugosità = 0,25](media/metallic-0.25.png)    | ![Metallo, rugosità = 0,5](media/metallic-0.5.png)    | ![Metallo, rugosità = 0,75](media/metallic-0.75.png)    | ![Metal, rugosità = 1](media/metallic-1.png)    |

Per altre informazioni sul modello di illuminazione, vedere il capitolo sui [materiali](../../concepts/materials.md).

> [!IMPORTANT]
> Rendering remoto di Azure usa la trama del cielo solo per i modelli di illuminazione. Non esegue il rendering del cielo come sfondo, perché le applicazioni di realtà aumentata hanno già uno sfondo appropriato, ovvero il mondo reale.

## <a name="changing-the-sky-texture"></a>Modificare la trama del cielo

Per modificare la mappa dell'ambiente è sufficiente [caricare una trama](../../concepts/textures.md) e modificare l'elemento `SkyReflectionSettings`della sessione:

```cs
async void ChangeEnvironmentMap(RenderingSession session)
{
    try
    {
        Texture skyTex = await session.Connection.LoadTextureFromSasAsync(new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap));
        session.Connection.SkyReflectionSettings.SkyReflectionTexture = skyTex;
    }
    catch (RRException exception)
    {
        System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
    }
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<RenderingSession> session)
{
    LoadTextureFromSasOptions params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUri = "builtin://VeniceSunset";
    session->Connection()->LoadTextureFromSasAsync(params, [&](Status status, ApiHandle<Texture> res) {
        if (status == Status::OK)
        {
            ApiHandle<SkyReflectionSettings> settings = session->Connection()->GetSkyReflectionSettings();
            settings->SetSkyReflectionTexture(res);
        }
        else
        {
            printf("Texture loading failed!\n");
        }
    });
}
```

Si noti che qui sopra viene usata la variante `LoadTextureFromSasAsync` perché viene caricata una trama predefinita. Nel caso di caricamento da [risorse di archiviazione BLOB collegate](../../how-tos/create-an-account.md#link-storage-accounts), usare la variante `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Tipi di trama del cielo

È possibile usare sia *[mappe cubi](https://en.wikipedia.org/wiki/Cube_mapping)* che *trame 2D* come mappe dell'ambiente.

Tutte le trame devono essere in un [formato di trama supportato](../../concepts/textures.md#supported-texture-formats). Non è necessario fornire mipmap per le trame del cielo.

### <a name="cube-environment-maps"></a>Mappe ambientali cubiche

Per riferimento, di seguito è riportata un mappa cubi senza wrapping:

![Mappa cubi senza wrapping](media/Cubemap-example.png)

Usare `RenderingSession.Connection.LoadTextureAsync`/ `LoadTextureFromSasAsync` con `TextureType.CubeMap` per caricare trame di mappe cubi.

### <a name="sphere-environment-maps"></a>Mappe ambientali sferiche

Quando si usa una trama 2D come mappa ambientale, l'immagine deve trovarsi in uno [spazio di coordinate sferico](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Immagine del cielo in coordinate sferiche](media/spheremap-example.png)

Usare `RenderingSession.Connection.LoadTextureAsync` con `TextureType.Texture2D` per caricare le mappe ambientali sferiche.

## <a name="built-in-environment-maps"></a>Mappe ambientali predefinite

Rendering remoto di Azure fornisce alcune mappe ambientali predefinite che sono sempre disponibili. Tutte le mappe ambientali predefinite sono mappe cubi.

|Identificatore                         | Descrizione                                              | Illustrazione                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Varie bande di luce, illuminazione interna di base brillante    | ![Autoshop SKYBOX usato per la luce di un oggetto](media/autoshop.png)
|builtin://BoilerRoom               | Illuminazione interna brillante, più sorgenti di luce naturale      | ![BoilerRoom SKYBOX usato per la luce di un oggetto](media/boiler-room.png)
|builtin://ColorfulStudio           | Luci di vari colori in ambiente interno di media luminosità  | ![ColorfulStudio SKYBOX usato per la luce di un oggetto](media/colorful-studio.png)
|builtin://Hangar                   | Illuminazione grande ambiente moderatamente luminoso                     | ![SmallHangar SKYBOX usato per la luce di un oggetto](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Illuminazione interna soffusa con contrasto chiaro/scuro              | ![IndustrialPipeAndValve SKYBOX usato per la luce di un oggetto](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luce diurna naturale, area della finestra con illuminazione brillante     | ![Lebombo SKYBOX usato per la luce di un oggetto](media/lebombo.png)
|builtin://SataraNight              | Cielo notturno scuro e terreno con molte luci circostanti   | ![SataraNight SKYBOX usato per la luce di un oggetto](media/satara-night.png)
|builtin://SunnyVondelpark          | Contrasto luce solare brillante e ombre                      | ![SunnyVondelpark SKYBOX usato per la luce di un oggetto](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Cielo chiaro con illuminazione del terreno moderata            | ![Syferfontein SKYBOX usato per la luce di un oggetto](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Variazione moderata di sole e ombre                         | ![TearsOfSteelBridge SKYBOX usato per la luce di un oggetto](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Luce di tramonto prossimo al crepuscolo                    | ![VeniceSunset SKYBOX usato per la luce di un oggetto](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Toni luminosi di bianco e verde, terreno con luce soffusa | ![WhippleCreekRegionalPark SKYBOX usato per la luce di un oggetto](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Giorno con luce naturale molto brillante sul terreno                 | ![WinterRiver SKYBOX usato per la luce di un oggetto](media/winter-river.png)
|builtin://DefaultSky               | Uguale a TearsOfSteelBridge                               | ![DefaultSky SKYBOX usato per la luce di un oggetto](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>Documentazione dell'API

* [Proprietà C# RenderingConnection. SkyReflectionSettings](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.skyreflectionsettings)
* [C++ RenderingConnection:: SkyReflectionSettings ()](/cpp/api/remote-rendering/renderingconnection#skyreflectionsettings)

## <a name="next-steps"></a>Passaggi successivi

* [Luci](../../overview/features/lights.md)
* [Materiali](../../concepts/materials.md)
* [Trame](../../concepts/textures.md)
