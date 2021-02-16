---
title: Distribuire l'esercitazione di C++ nativo in HoloLens
description: Argomento di avvio rapido che illustra come eseguire l'esercitazione di C++ nativo in HoloLens
author: florianborn71
ms.author: flborn
ms.date: 06/08/2020
ms.topic: quickstart
ms.openlocfilehash: b340a180927b3df9ad51295383b09b03dbbb2d98
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530587"
---
# <a name="quickstart-deploy-native-c-sample-to-hololens"></a>Avvio rapido: Distribuire un esempio di C++ nativo in HoloLens

Questo argomento di avvio rapido illustra come distribuire ed eseguire l'applicazione per l'esercitazione di C++ nativo in un HoloLens 2.

In questo argomento di avvio rapido si apprenderà come:

> [!div class="checklist"]
>
>* Compilare l'applicazione per l'esercitazione per HoloLens.
>* Modificare le credenziali ARR nel codice sorgente.
>* Distribuire ed eseguire l'esempio nel dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per ottenere l'accesso al servizio Rendering remoto di Azure, è prima di tutto necessario [creare un account](../../../how-tos/create-an-account.md).

È necessario installare il software seguente:

* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* L'ultima versione di Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools per Realtà mista](/windows/mixed-reality/install-the-tools). In particolare, le installazioni del *carico di lavoro* seguente sono obbligatorie:
  * **Sviluppo per desktop con C++**
  * **Sviluppo per la piattaforma UWP (Universal Windows Platform)**
* GIT [(download)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Clonare il repository di esempi ARR

Come prima cosa occorre clonare il repository Git, che ospita gli esempi di Rendering remoto di Azure globali. Aprire un prompt dei comandi (digitare `cmd` nel menu Start di Windows) e passare a una directory in cui si vuole archiviare il progetto di esempio di Rendering remoto di Azure.

Eseguire i comandi seguenti:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

L'ultimo comando crea una sottodirectory nella directory ARR contenente i vari progetti di esempio per Rendering remoto di Azure.

L'esercitazione su HoloLens per C++ è disponibile nella sottodirectory *NativeCpp/HoloLens*.

## <a name="build-the-project"></a>Compilare il progetto

Aprire il file di soluzione *HolographicApp.sln* nella sottodirectory *NativeCpp/HoloLens* con Visual Studio 2019.

Impostare la configurazione della build su *Debug* (o *Rilascio*) e *ARM64*. Assicurarsi inoltre che la modalità di debug sia impostata su *Dispositivo* invece che su *Computer remoto*:

![Configurazione di Visual Studio](media/vs-config-native-cpp-tutorial.png)

Poiché le credenziali dell'account sono hardcoded nel codice sorgente dell'esercitazione, sostituirle con credenziali valide. Per aprire il file in `HolographicAppMain.cpp` Visual Studio e modificare la parte in cui viene creato il client all'interno del costruttore della classe `HolographicAppMain` :

```cpp
// 2. Create Client
{
    // Users need to fill out the following with their account data and model
    RR::SessionConfiguration init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
}
```

Nello specifico, modificare i valori seguenti:
* `init.AccountId`, `init.AccountKey` e `init.AccountDomain` per usare i dati dell'account. Vedere il paragrafo relativo a come [recuperare le informazioni sull'account](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* Specificare la posizione in cui creare la sessione di rendering remoto modificando la parte relativa all’area della stringa `init.RemoteRenderingDomain` con altre aree diverse da `westus2`, ad esempio `"westeurope.mixedreality.azure.com"`.
* Inoltre, è possibile sostituire `m_sessionOverride` con un ID di sessione esistente. Le sessioni possono essere create all'esterno di questo esempio, ad esempio usando lo [script di PowerShell](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) o usando direttamente l'[API REST della sessione](../../../how-tos/session-rest-api.md).
È consigliabile creare una sessione all'esterno dell'esempio quando l'esempio deve essere eseguito più volte. Se non viene passata alcuna sessione, l'esempio creerà una nuova sessione a ogni avvio, operazione che può richiedere diversi minuti.

A questo punto è possibile compilare l'applicazione.

## <a name="launch-the-application"></a>Avviare l'applicazione

1. Connettere il dispositivo HoloLens con un cavo USB al PC.
1. Accendere il dispositivo HoloLens e attendere che venga visualizzato il menu Start.
1. Avviare il debugger in Visual Studio (F5). L'app verrà distribuita automaticamente nel dispositivo.

Verrà avviata l'app di esempio e verrà visualizzato un pannello di testo con informazioni sullo stato corrente dell'applicazione. Lo stato all'avvio è l'avvio di una nuova sessione o la connessione a una sessione esistente. Al termine del caricamento del modello, il modello del motore incorporato viene visualizzato direttamente nella posizione iniziale. Il modello di motore interagisce correttamente con il cubo rotante di cui viene eseguito il rendering localmente.

 Se si vuole avviare l'esempio una seconda volta in seguito, è possibile trovarlo anche nel menu Start di HoloLens, ma tenere presente che potrebbe contenere un ID di sessione scaduto.

## <a name="next-steps"></a>Passaggi successivi

Questo argomento di avvio rapido è basato sul risultato di un'esercitazione che illustra come integrare tutti i componenti di Rendering remoto in un'*app olografica* standard. Per informazioni sui passaggi necessari, seguire questa esercitazione:

> [!div class="nextstepaction"]
> [Esercitazione: Integrare Rendering remoto in un'app olografica HoloLens](../../../tutorials/native-cpp/hololens/integrate-remote-rendering-into-holographic-app.md)