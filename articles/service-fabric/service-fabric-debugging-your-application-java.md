---
title: Eseguire il debug dell'applicazione in Eclipse
description: Migliorare l'affidabilità e le prestazioni dei servizi sviluppandoli ed eseguendone il debug in Eclipse in un cluster di sviluppo locale.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-java
ms.author: suhuruli
ms.openlocfilehash: 32472c6caafe62d629ee3a5ffb8c758624228efd
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87316614"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Eseguire il debug dell'applicazione Service Fabric di Java con Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Avviare un cluster di sviluppo locale seguendo la procedura descritta nell'articolo [Configurazione dell'ambiente di sviluppo di Service Fabric](service-fabric-get-started-linux.md).

2. Aggiornare l'elemento entryPoint.sh del servizio di cui eseguire il debug in modo che avvii il processo Java con i parametri di debug remoto. Questo file si trova nel percorso seguente: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`. In questo esempio la porta 8001 è impostata per il debug.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Aggiornare il manifesto dell'applicazione impostando il numero di istanze o di repliche per il servizio di cui eseguire il debug su 1. Questa impostazione evita che si verifichino conflitti per la porta usata per il debug. Per i servizi senza stato, ad esempio, impostare `InstanceCount="1"`, mentre per i servizi con stato impostare la destinazione e le dimensioni minime del set di repliche su 1 nel modo seguente: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Distribuire l'applicazione.

5. Nell'IDE di Eclipse selezionare **Run (Esegui) -> Debug Configurations (Configurazioni di debug) -> Remote Java Application (Applicazione Java remota) e le proprietà di connessione di input** e impostare le proprietà come segue:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Impostare punti di interruzione nei punti desiderati ed eseguire il debug dell'applicazione.

Se l'applicazione si arresta in modo anomalo, è possibile abilitare elementi core dump. Eseguire `ulimit -c` in una shell e, se viene restituito 0, gli elementi core dump non sono abilitati. Per abilitare elementi core dump illimitati, eseguire questo comando: `ulimit -c unlimited`. È anche possibile verificare lo stato usando il comando `ulimit -a`.  Se si vuole aggiornare il percorso di generazione degli elementi core dump, eseguire `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`. 

### <a name="next-steps"></a>Passaggi successivi

* [Raccogliere log con Diagnostica di Azure](./service-fabric-diagnostics-event-aggregation-lad.md).
* [Monitorare e diagnosticare i servizi in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
