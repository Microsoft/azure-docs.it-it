---
title: Specificare il numero di porta di un servizio utilizzando i parametriSpecify port number of a service using parameters
description: Questo articolo illustra come usare parametri per specificare la porta relativa a un'applicazione in Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609860"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Come specificare tramite parametri il numero di porta di un servizio in Service Fabric

Questo articolo illustra come specificare tramite parametri il numero di porta di un servizio in Service Fabric usando Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedura per specificare tramite parametri il numero di porta di un servizio

In questo esempio si imposta il numero di porta per un'API Web ASP.NET Core usando un parametro.

1. Aprire Visual Studio e creare una nuova applicazione di Service Fabric.
1. Scegliere il modello ASP.NET Core senza stato.
1. Scegliere API Web.
1. Aprire il file ServiceManifest.xml.
1. Prendere nota del nome dell'endpoint specificato per il servizio. Il valore predefinito è `ServiceEndpoint`.
1. Aprire il file ApplicationManifest.xml.
1. Nell'elemento `ServiceManifestImport` aggiungere un nuovo elemento `RessourceOverrides` con un riferimento all'endpoint nel file ServiceManifest.xml.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Nell'elemento `Endpoint` è ora possibile eseguire l'override di qualsiasi attributo usando un parametro. In questo esempio specificare `Port` e impostare l'attributo su un nome di parametro usando le parentesi quadre, ad esempio `[MyWebAPI_PortNumber]`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Sempre nel file ApplicationManifest.xml specificare quindi il parametro nell'elemento `Parameters`.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. Definire `DefaultValue`.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Aprire la cartella ApplicationParameters e il file `Cloud.xml`.
1. Per specificare una porta diversa da usare per la pubblicazione in un cluster remoto, aggiungere al file il parametro con il numero di porta.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Quando si pubblica l'applicazione da Visual Studio usando il profilo di pubblicazione Cloud.xml, il servizio è configurato per l'uso della porta 80. Se si distribuisce l'applicazione senza specificare il parametro MyWebAPI_PortNumber, il servizio usa la porta 8080.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su alcuni dei concetti principali trattati in questo articolo, vedere [Gestire le applicazioni per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di Service Fabric](service-fabric-manage-application-in-visual-studio.md).
