---
title: Usare un feed NuGet personalizzato
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Usare un feed NuGet personalizzato per accedere ai pacchetti NuGet e usarli in uno spazio Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, servizio Azure Container, contenitori
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325734"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Usare un feed NuGet personalizzato con Azure Dev Spaces

Un feed NuGet costituisce un modo semplice per includere le origini dei pacchetti in un progetto. Azure Dev Spaces necessario accedere a questo feed per poter installare correttamente le dipendenze nel contenitore docker.

## <a name="set-up-a-nuget-feed"></a>Configurare un feed NuGet

Aggiungere un [riferimento al pacchetto](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) per la dipendenza nel `*.csproj` file nel `PackageReference` nodo. Ad esempio:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Creare un file [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) nella cartella del progetto e impostare le `packageSources` sezioni `packageSourceCredentials` e per il feed NuGet. La `packageSources` sezione contiene l'URL del feed, che deve essere accessibile dal cluster AKS. `packageSourceCredentials` Sono le credenziali per l'accesso al feed. Ad esempio:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Aggiornare il Dockerfile per copiare il `NuGet.Config` file nell'immagine. Ad esempio:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> In Windows, `NuGet.Config` `Nuget.Config`, e `nuget.config` funziona come nomi di file validi. In Linux è solo `NuGet.Config` un nome file valido per il file. Poiché Azure Dev Spaces USA Docker e Linux, questo file deve essere denominato `NuGet.Config`. È possibile correggere la denominazione manualmente o eseguendo `dotnet restore --configfile nuget.config`.


Se si usa Git, non è necessario avere le credenziali per il feed NuGet nel controllo della versione. Aggiungere `NuGet.Config` a `.gitignore` per il progetto in modo che il `NuGet.Config` file non venga aggiunto al controllo della versione. Azure Dev Spaces sarà necessario questo file durante il processo di compilazione dell'immagine del contenitore, ma per impostazione predefinita rispetta le regole `.gitignore` definite `.dockerignore` in e durante la sincronizzazione. Per modificare il valore predefinito e consentire Azure Dev Spaces di sincronizzare `NuGet.Config` il file, aggiornare `azds.yaml` il file:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se non si usa Git, è possibile ignorare questo passaggio.

La volta successiva che si `azds up` esegue o `F5` si raggiunge il Visual Studio Code o Visual Studio, Azure Dev Spaces Sincronizza `NuGet.Config` il file e lo usa per installare le dipendenze del pacchetto.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [NuGet e sul suo funzionamento](https://docs.microsoft.com/nuget/what-is-nuget).