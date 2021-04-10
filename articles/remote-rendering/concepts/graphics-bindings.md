---
title: Binding di grafica
description: Configurazione di binding di grafica e casi d'uso
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 69bcc521b4cd00320a5fbecc5244e913ac16c68b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593909"
---
# <a name="graphics-binding"></a>Binding di grafica

Per poter usare Rendering remoto di Azure in un'applicazione personalizzata, è necessario integrare questo servizio nella pipeline di rendering dell'applicazione. Questa integrazione è compito del binding di grafica.

Dopo che è stato configurato, il binding di grafica consente l'accesso a varie funzioni che interessano l'immagine di cui è stato eseguito il rendering. È possibile dividere queste funzioni in due categorie: funzioni generali sempre disponibili e funzioni specifiche pertinenti solo per l'API `Microsoft.Azure.RemoteRendering.GraphicsApiType` selezionata.

## <a name="graphics-binding-in-unity"></a>Binding di grafica in Unity

In Unity il binding viene interamente gestito dallo struct `RemoteUnityClientInit` passato in `RemoteManagerUnity.InitializeManager`. Per impostare la modalità di grafica, è necessario impostare il campo `GraphicsApiType` sul binding scelto. Il campo verrà popolato automaticamente a seconda della presenza di un XRDevice. È possibile eseguire l'override del comportamento manualmente con i comportamenti seguenti:

* **HoloLens 2**: viene sempre usato il binding di grafica [Windows Mixed Reality](#windows-mixed-reality).
* **App desktop per la piattaforma UWP flat**: [viene sempre usata la simulazione](#simulation).
* **Editor di Unity**: viene sempre usata la [simulazione](#simulation), a meno che non sia connessa una cuffia per la realtà virtuale Windows Mixed Reality, nel qual caso ARR (Application Request Routing) viene disabilitato per consentire il debug delle parti correlate non ARR dell'applicazione. Vedere anche la [comunicazione remota olografica](../how-tos/unity/holographic-remoting.md).

L'unica altra parte pertinente per Unity è l'accesso al [binding di base](#access). Tutte le altre sezioni riportate di seguito possono essere ignorate.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configurazione del binding di grafica nelle applicazioni personalizzate

Per selezionare un binding di grafica, eseguire i due passaggi seguenti: Prima di tutto è necessario che il binding di grafica venga inizializzato in modo statico quando viene inizializzato il programma:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.WmrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::WmrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```

La chiamata precedente è necessaria per inizializzare Rendering remoto di Azure nelle API olografiche. Questa funzione deve essere chiamata prima di una qualsiasi API olografica e prima dell'accesso a qualsiasi altra API di Rendering remoto. Analogamente, la funzione di deinizializzazione corrispondente `RemoteManagerStatic.ShutdownRemoteRendering();` deve essere chiamata quando non vengono più eseguite chiamate alle API olografiche.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Accesso al binding di grafica

Dopo la configurazione di un client, è possibile accedere al binding di grafica di base con il getter `RenderingSession.GraphicsBinding`. È ad esempio possibile recuperare le statistiche dell'ultimo fotogramma come segue:

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>API di grafica

Sono attualmente disponibili per la selezione due API di grafica, `WmrD3D11` e `SimD3D11`. Ne esiste una terza `Headless`, che tuttavia non è ancora supportata sul lato client.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` è il binding predefinito da eseguire in HoloLens 2 e crea il binding `GraphicsBindingWmrD3d11`. In questa modalità, Rendering remoto di Azure si collega direttamente alle API olografiche.

Per accedere ai binding di grafica derivati, è necessario eseguire il cast di `GraphicsBinding` di base.
Per usare il binding Windows Mixed Reality è necessario eseguire due operazioni:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informare Rendering remoto del sistema di coordinate usato

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Dove `ptr` qui sopra deve essere un puntatore a un oggetto `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` nativo che definisce il sistema di coordinate dello spazio globale in cui sono espresse le coordinate nell'API.

#### <a name="render-remote-image"></a>Eseguire il rendering dell'immagine remota

All'inizio di ogni frame, il frame remoto deve essere sottoposto a rendering nel buffer nascosto. Questa operazione viene eseguita chiamando `BlitRemoteFrame` , che riempie le informazioni relative a colore e profondità per entrambi gli occhi nella destinazione di rendering attualmente associata. È quindi importante eseguire questa operazione dopo aver associato il buffer completo al back-down come destinazione di rendering.

> [!WARNING]
> Dopo che l'immagine remota è stata blit nel backBuffer, è necessario eseguire il rendering del contenuto locale usando una tecnica di rendering stereo a passaggio singolo, ad esempio usando **SV_RenderTargetArrayIndex**. L'uso di altre tecniche di rendering stereo, ad esempio il rendering di ogni occhio in un passaggio separato, può comportare un notevole calo delle prestazioni o artefatti grafici e deve essere evitato.

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulazione

`GraphicsApiType.SimD3D11` è il binding della simulazione e se selezionato crea il binding di grafica `GraphicsBindingSimD3d11`. Questa interfaccia viene usata per simulare il movimento della testa, ad esempio in un'applicazione desktop, ed esegue il rendering di un'immagine monoscopica.

Per implementare l'associazione della simulazione, è importante comprendere la differenza tra la fotocamera locale e il frame remoto come descritto nella pagina della [fotocamera](../overview/features/camera.md) .

Sono necessarie due fotocamere:

* **Fotocamera locale**: questa fotocamera rappresenta la posizione corrente della fotocamera basata sulla logica dell'applicazione.
* **Fotocamera proxy**: questa fotocamera corrisponde al *frame remoto* corrente inviato dal server. Poiché si verifica un ritardo tra il client che richiede un frame e il suo arrivo, il *frame remoto* è sempre un po' dietro lo spostamento della fotocamera locale.

L'approccio di base è che l'immagine remota e il contenuto locale vengono sottoposti a rendering in una destinazione fuori schermo usando la fotocamera del proxy. L'immagine proxy viene quindi riproiettata nello spazio locale della fotocamera, descritta ulteriormente nella [riproiezione della fase finale](../overview/features/late-stage-reprojection.md).

`GraphicsApiType.SimD3D11` supporta anche il rendering stereoscopico, che deve essere abilitato durante la `InitSimulation` chiamata di installazione riportata di seguito. La configurazione è un po' più complessa e funziona nel modo seguente:

#### <a name="create-proxy-render-target"></a>Creare la destinazione di rendering proxy

È necessario eseguire il rendering del contenuto remoto e locale in una destinazione di rendering di colore/profondità fuori schermo denominata 'proxy' usando i dati della camera proxy forniti dalla funzione `GraphicsBindingSimD3d11.Update`.

Il proxy deve corrispondere alla risoluzione del buffer nascosto e deve essere int il formato *DXGI_FORMAT_R8G8B8A8_UNORM* o *DXGI_FORMAT_B8G8R8A8_UNORM* . Nel caso del rendering stereoscopico, sia la trama del proxy del colore che, se si usa la profondità, la trama del proxy di profondità deve avere due livelli di matrice anziché uno. Quando la sessione è pronta, è necessario chiamare `GraphicsBindingSimD3d11.InitSimulation` prima della connessione:

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

La funzione init deve essere dotata di puntatori al dispositivo D3D nativo, nonché alla texture di colore e profondità della destinazione di rendering proxy. Dopo l'inizializzazione, `RenderingSession.ConnectAsync` e `Disconnect` possono essere chiamate più volte, ma quando si passa a una sessione diversa è necessario chiamare prima `GraphicsBindingSimD3d11.DeinitSimulation` nella sessione precedente per poter chiamare `GraphicsBindingSimD3d11.InitSimulation` in un'altra sessione.

#### <a name="render-loop-update"></a>Aggiornamento del ciclo di rendering

L'aggiornamento del ciclo di rendering è costituito da più passaggi:

1. Per ogni fotogramma, prima che venga eseguito il rendering, viene chiamata la funzione `GraphicsBindingSimD3d11.Update` con la trasformazione della camera corrente inviata al server di cui eseguire il rendering. Allo stesso tempo, la trasformazione proxy restituita deve essere applicata alla camera proxy per eseguire il rendering nella destinazione di rendering proxy.
Se `SimulationUpdate.frameId` dell'aggiornamento proxy restituito è Null, non sono ancora presenti dati remoti. In questo caso, anziché eseguire il rendering nella destinazione di rendering proxy, è necessario eseguire il rendering del contenuto locale nel buffer nascosto usando direttamente i dati della camera correnti. I due passaggi successivi verranno ignorati.
1. L'applicazione dovrebbe ora eseguire il binding della destinazione di rendering proxy e chiamare `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`. In questo modo le informazioni remote relative a colore e profondità verranno inserite nella destinazione di rendering proxy. È ora possibile eseguire il rendering di qualsiasi contenuto locale nel proxy usando la trasformazione della camera proxy.
1. È quindi necessario eseguire il binding del buffer nascosto come destinazione di rendering e chiamare `GraphicsBindingSimD3d11.ReprojectProxy`. A questo punto è possibile presentare il buffer nascosto.

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>Strutture di aggiornamento della simulazione

Ogni frame, l' **aggiornamento del ciclo di rendering** della sezione precedente richiede l'immissione di un intervallo di parametri della fotocamera corrispondente alla fotocamera locale e restituisce un set di parametri della fotocamera che corrispondono alla fotocamera del frame successiva disponibile. Questi due set vengono acquisiti rispettivamente in `SimulationUpdateParameters` e nelle `SimulationUpdateResult` strutture:

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

I membri della struttura hanno il significato seguente:

| Membro | Descrizione |
|--------|-------------|
| FrameId | Identificatore del frame continuo. Necessaria per l'input di SimulationUpdateParameters e deve essere incrementata continuamente per ogni nuovo frame. Sarà 0 in SimulationUpdateResult se non sono ancora disponibili dati del frame. |
| ViewTransform | Coppia Left-Right-stereo delle matrici di trasformazione della visualizzazione della fotocamera del frame. Per il rendering monoscopiche, solo il `Left` membro è valido. |
| FieldOfView | Coppia Left-Right-stereo dei campi di visualizzazione della fotocamera frame in [OpenXR campo della convenzione di visualizzazione](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles). Per il rendering monoscopiche, solo il `Left` membro è valido. |
| NearPlaneDistance | distanza in prossimità del piano utilizzata per la matrice di proiezione del frame remoto corrente. |
| FarPlaneDistance | distanza del piano lontano utilizzata per la matrice di proiezione del frame remoto corrente. |

Le coppie stereo `ViewTransform` e `FieldOfView` consentono di impostare entrambi i valori della fotocamera a occhio in caso di abilitazione del rendering stereoscopico. In caso contrario, i `Right` membri verranno ignorati. Come si può notare, solo la trasformazione della fotocamera viene passata come matrici di trasformazione 4x4 semplici, mentre non sono specificate matrici di proiezione. Le matrici effettive vengono calcolate tramite il rendering remoto di Azure internamente usando i campi di visualizzazione specificati e il set di piani e di livello più vicino attualmente disponibili nell' [API CameraSettings](../overview/features/camera.md).

Poiché è possibile modificare il piano e il piano più vicino in [CameraSettings](../overview/features/camera.md) durante la fase di esecuzione in base alle esigenze e il servizio applica queste impostazioni in modo asincrono, ogni SimulationUpdateResult porta anche il piano e l'estremo piano specifici usati durante il rendering del frame corrispondente. È possibile usare questi valori del piano per adattare le matrici di proiezione per il rendering di oggetti locali in modo che corrispondano al rendering del frame remoto.

Infine, sebbene la chiamata di **aggiornamento della simulazione** richieda il campo di visualizzazione nella convenzione OpenXR, per motivi di standardizzazione e sicurezza algoritmica, è possibile usare le funzioni di conversione illustrate negli esempi di popolamento della struttura seguenti:

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

Queste funzioni di conversione consentono lo spostamento rapido tra la specifica del campo di visualizzazione e una matrice di proiezione prospettica semplice 4x4, a seconda delle esigenze per il rendering locale. Queste funzioni di conversione contengono la logica di verifica e restituiscono errori, senza impostare un risultato valido, nel caso in cui le matrici di proiezione di input o i campi di input della visualizzazione non siano validi.

## <a name="api-documentation"></a>Documentazione dell'API

* [C# RemoteManagerStatic. StartupRemoteRendering ()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [Classe Graphics di C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GraphicsBindingWmrD3d11 (classe)](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GraphicsBindingSimD3d11 (classe)](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [Struct C++ RemoteRenderingInitialization](/cpp/api/remote-rendering/remoterenderinginitialization)
* [Classe Graphics di C++](/cpp/api/remote-rendering/graphicsbinding)
* [Classe C++ GraphicsBindingWmrD3d11](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [Classe C++ GraphicsBindingSimD3d11](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Passaggi successivi

* [Fotocamera](../overview/features/camera.md)
* [Riproiezione con ritardo della fase](../overview/features/late-stage-reprojection.md)
* [Esercitazione: Visualizzazione di un modello di cui è stato eseguito il rendering in remoto](../tutorials/unity/view-remote-models/view-remote-models.md)