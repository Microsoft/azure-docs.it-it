---
title: Mesh
description: Definizione di mesh nell'ambito del rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594519"
---
# <a name="meshes"></a>Mesh

## <a name="mesh-resource"></a>Risorsa mesh

Le mesh sono una [risorsa condivisa](../concepts/lifetime.md)non modificabile, che può essere creata solo tramite la [conversione del modello](../how-tos/conversion/model-conversion.md). Le mesh contengono uno o più *sottomesh* insieme a una rappresentazione fisica per le [query Raycast](../overview/features/spatial-queries.md). Ogni sottomesh fa riferimento a un [materiale](materials.md) per il quale deve essere eseguito il rendering per impostazione predefinita. Per inserire una mesh nello spazio 3D, aggiungere un [MeshComponent](#meshcomponent) a un' [entità](entities.md).

### <a name="mesh-resource-properties"></a>Proprietà risorsa mesh

Le `Mesh` proprietà della classe sono:

* **Materiali:** Matrice di materiali. Ogni materiale viene utilizzato da una sottorete diversa. Più voci nella matrice possono fare riferimento allo stesso [materiale](materials.md). Non è possibile modificare questi dati in fase di esecuzione.

* **Limiti:** Un rettangolo di delimitazione allineato all'asse dello spazio locale (AABB) dei vertici della mesh.

## <a name="meshcomponent"></a>MeshComponent

La `MeshComponent` classe viene usata per inserire un'istanza di una risorsa mesh. Ogni MeshComponent fa riferimento a una singola mesh. Potrebbe ignorare i materiali usati per eseguire il rendering di ogni sottomesh.

### <a name="meshcomponent-properties"></a>Proprietà di MeshComponent

* **Mesh:** Risorsa mesh utilizzata dal componente.

* **Materiali:** Matrice di materiali specificati sul componente mesh stesso. La matrice avrà sempre la stessa lunghezza della matrice di *materiali* sulla risorsa mesh. I materiali che non devono essere sottoposti a override da mesh default sono impostati su *null* in questa matrice.

* **UsedMaterials:** Matrice di materiali effettivamente utilizzati per ogni sottomesh. Sarà identica ai dati nella matrice *Materials* , per i valori non null. In caso contrario, contiene il valore della matrice *Materials* nell'istanza mesh.

### <a name="sharing-of-meshes"></a>Condivisione di mesh

Una `Mesh` risorsa può essere condivisa tra più istanze di componenti mesh. Inoltre, la `Mesh` risorsa assegnata a un componente mesh può essere modificata a livello di codice in qualsiasi momento. Il codice seguente illustra come clonare una mesh:

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>Documentazione dell'API

* [Classe mesh C#](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent (classe)](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Classe mesh C++](/cpp/api/remote-rendering/mesh)
* [Classe C++ MeshComponent](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Passaggi successivi

* [Materiali](materials.md)