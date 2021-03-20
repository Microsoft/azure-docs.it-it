---
title: Sostituire i materiali durante la conversione di modelli
description: Illustra il flusso di lavoro di sostituzione del materiale al momento della conversione
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576643"
---
# <a name="override-materials-during-model-conversion"></a>Sostituire i materiali durante la conversione di modelli

Le impostazioni relative al materiale nel modello di origine vengono utilizzate per definire i [materiali PBR](../../overview/features/pbr-materials.md) utilizzati dal renderer.
A volte la [conversione predefinita](../../reference/material-mapping.md) non fornisce i risultati desiderati ed è necessario apportare modifiche.
Quando un modello viene convertito per l'uso nel rendering remoto di Azure, è possibile fornire un file di override del materiale per personalizzare il modo in cui la conversione del materiale viene eseguita in base al materiale.
Se un file chiamato `<modelName>.MaterialOverrides.json` viene trovato nel contenitore di input accanto al modello di input `<modelName>.<ext>` , verrà usato come file di sostituzione del materiale.

## <a name="the-override-file-used-during-conversion"></a>File di sostituzione usato durante la conversione

Come esempio semplice, supponiamo che un modello di riquadro disponga di un unico materiale, denominato "default".
Si direbbe inoltre che il colore dell'albedo deve essere regolato per l'uso in ARR.
In questo caso, `box.MaterialOverrides.json` è possibile creare un file nel modo seguente:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Il `box.MaterialOverrides.json` file viene inserito nel contenitore di input accanto `box.fbx` a, che indica al servizio di conversione di applicare le nuove impostazioni.

### <a name="color-materials"></a>Materiali a colori

Il modello [Material color](../../overview/features/color-materials.md) descrive una superficie costantemente ombreggiata che è indipendente dall'illuminazione.
I materiali colori sono utili per gli asset creati dagli algoritmi fotogrammetria, ad esempio.
Nei file di override del materiale un materiale può essere dichiarato come materiale a colori impostando `unlit` su `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignora mappe di trama specifiche

In alcuni casi potrebbe essere necessario che il processo di conversione ignori mappe di trama specifiche. Questo potrebbe essere il caso in cui il modello è stato generato da uno strumento che genera mappe speciali non riconosciute correttamente dal renderer. Ad esempio, un "OpacityMap" usato per definire un valore diverso da Opacity o un modello in cui "NormalMap" viene archiviato come "sbalzo". Nel secondo caso si vuole ignorare "NormalMap", che farà sì che il convertitore usi "sbalzo" come "NormalMap".

Il principio è semplice. È sufficiente aggiungere una proprietà denominata `ignoreTextureMaps` e aggiungere qualsiasi mappa di trama che si desidera ignorare:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Per l'elenco completo delle mappe di trama che è possibile ignorare, vedere lo schema JSON riportato di seguito.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Applicazione delle stesse sostituzioni a più materiali

Per impostazione predefinita, una voce nel file di override del materiale si applica quando il nome corrisponde esattamente al nome del materiale.
Poiché è piuttosto comune che la stessa sostituzione debba essere applicata a più materiali, facoltativamente è possibile specificare un'espressione regolare come nome della voce.
Il campo `nameMatching` ha un valore predefinito `exact` , ma può essere impostato su `regex` per indicare che la voce deve essere applicata a ogni materiale corrispondente.
La sintassi utilizzata è identica a quella usata per JavaScript. Nell'esempio seguente viene illustrata una sostituzione che si applica ai materiali con nomi quali "Material2", "Material01" e "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Al massimo una voce in un file di override del materiale si applica a un singolo materiale.
Se è presente una corrispondenza esatta (ovvero `nameMatching` è assente o uguale `exact` a) per il nome del materiale, viene scelta tale voce.
In caso contrario, viene scelta la prima voce Regex nel file che corrisponde al nome del materiale.

### <a name="getting-information-about-which-entries-applied"></a>Recupero delle informazioni sulle voci applicate

Il [file di informazioni](get-information.md#information-about-a-converted-model-the-info-file) scritto nel contenitore di output contiene informazioni sul numero di sostituzioni fornito e sul numero di materiali sottoposti a override.

## <a name="json-schema"></a>Schema JSON

Di seguito è riportato lo schema JSON completo per i file di materiali. Ad eccezione di `unlit` e `ignoreTextureMaps` , le proprietà disponibili sono un subset delle proprietà descritte nelle sezioni sui modelli di materiali [color Material](../../overview/features/color-materials.md) e [PBR](../../overview/features/pbr-materials.md) .

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Materiali a colori](../../overview/features/color-materials.md)
* [Materiali PBR](../../overview/features/pbr-materials.md)
