---
title: Configurare una funzione in Azure per l'elaborazione dei dati
titleSuffix: Azure Digital Twins
description: Vedere come creare una funzione in Azure che può accedere ed essere attivata da dispositivi gemelli digitali.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f491bbe61e8574a7275d9ef5c87d05fa61dc7c4
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035309"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Connetti le app per le funzioni in Azure per l'elaborazione dei dati

L'aggiornamento dei dispositivi gemelli digitali basati sui dati viene gestito usando le [**Route di eventi**](concepts-route-events.md) tramite le risorse di calcolo, ad esempio una funzione eseguita usando [funzioni di Azure](../azure-functions/functions-overview.md). Le funzioni possono essere usate per aggiornare un dispositivo gemello digitale in risposta a:
* dati di telemetria del dispositivo provenienti dall'hub Internet
* modifica delle proprietà o altri dati provenienti da un altro dispositivo gemello digitale all'interno del grafico gemello

Questo articolo illustra la creazione di una funzione in Azure per l'uso con i dispositivi gemelli digitali di Azure. 

Ecco una panoramica dei passaggi che contiene:

1. Creare un progetto Funzioni di Azure in Visual Studio
2. Scrivere una funzione con un trigger di [griglia di eventi](../event-grid/overview.md)
3. Aggiungere il codice di autenticazione alla funzione (per poter accedere ai dispositivi gemelli digitali di Azure)
4. Pubblicare l'app per le funzioni in Azure
5. Configurare l'accesso di [sicurezza](concepts-security.md) per l'app per le funzioni

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Prerequisito: configurare l'istanza di dispositivi gemelli digitali di Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Creare un'app per le funzioni in Visual Studio

In Visual Studio 2019 selezionare _File > nuovo progetto >_ e cercare il modello _funzioni di Azure_ , quindi fare clic su _Avanti_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: finestra di dialogo nuovo progetto":::

Specificare un nome per l'app per le funzioni e selezionare _Crea_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: configurare un nuovo progetto":::

Selezionare il tipo di *trigger griglia di eventi* dell'app per le funzioni e selezionare _Crea_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: finestra di dialogo trigger del progetto di funzioni di Azure":::

Una volta creata l'app per le funzioni, in Visual Studio verrà eseguito l'esempio di codice con popolamento automatico nel file **Function.cs** nella cartella del progetto. Questa funzione breve viene utilizzata per registrare gli eventi.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: finestra del progetto con codice di esempio":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Scrivere una funzione con un trigger di griglia di eventi

È possibile scrivere una funzione aggiungendo SDK all'app per le funzioni. L'app per le funzioni interagisce con i dispositivi gemelli digitali di Azure con [Azure Digital Twins SDK per .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Per usare l'SDK, è necessario includere nel progetto i pacchetti seguenti. È possibile installare i pacchetti usando Gestione pacchetti NuGet di Visual Studio o aggiungere i pacchetti usando `dotnet` lo strumento da riga di comando. Scegliere uno di questi metodi: 

**Opzione 1. Aggiungere pacchetti usando Gestione pacchetti di Visual Studio:**
    
A tale scopo, fare clic con il pulsante destro del mouse sul progetto e scegliere _Gestisci pacchetti NuGet_ dall'elenco. Quindi, nella finestra che viene visualizzata selezionare scheda _Sfoglia_ e cercare i pacchetti seguenti. Selezionare _Installa_ e _accettare_ il contratto di licenza per installare i pacchetti.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Per la configurazione della pipeline di Azure SDK per configurare correttamente per funzioni di Azure, sono necessari anche i pacchetti seguenti. Ripetere lo stesso processo precedente per installare tutti i pacchetti.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Opzione 2. Aggiungere pacchetti utilizzando `dotnet` lo strumento da riga di comando:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Successivamente, nel Esplora soluzioni di Visual Studio aprire il file _Function.cs_ in cui è presente il codice di esempio e aggiungere le istruzioni _using_ seguenti alla funzione. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-function"></a>Aggiungere il codice di autenticazione alla funzione

Verranno ora dichiarate le variabili a livello di classe e verrà aggiunto il codice di autenticazione che consente alla funzione di accedere ai dispositivi gemelli digitali di Azure. Si aggiungerà quanto segue alla funzione nel file {nome funzione}. cs.

* Leggere l'URL del servizio ADT come variabile di ambiente. È consigliabile leggere l'URL del servizio da una variabile di ambiente, anziché impostarlo come hardcoded nella funzione.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Variabile statica per la quale deve essere contenuta un'istanza di HttpClient. HttpClient è relativamente costoso da creare ed è consigliabile evitare di eseguire questa operazione per ogni chiamata di funzione.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* È possibile usare le credenziali di identità gestite in funzioni di Azure.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Aggiungere una variabile locale _DigitalTwinsClient_ all'interno della funzione per ospitare l'istanza del client di Azure Digital Twins nel progetto di funzione. Non *rendere questa* variabile statica all'interno della classe.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Aggiungere un controllo null per _adtInstanceUrl_ ed eseguire il wrapping della logica della funzione in un blocco try catch per intercettare eventuali eccezioni.

Una volta apportate queste modifiche, il codice della funzione sarà simile al seguente:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>Pubblicare l'app per le funzioni in Azure

Per pubblicare il progetto in un'app per le funzioni in Azure, fare clic con il pulsante destro del mouse sul progetto di funzione (non sulla soluzione) in Esplora soluzioni e scegliere **pubblica**.

> [!IMPORTANT] 
> La pubblicazione in un'app per le funzioni in Azure comporta costi aggiuntivi per la sottoscrizione, indipendentemente dai dispositivi gemelli digitali di Azure.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: pubblicare la funzione in Azure":::

Selezionare **Azure** come destinazione di pubblicazione e fare clic su **Avanti**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: pubblicare la finestra di dialogo funzioni di Azure, selezionare Azure ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: finestra di dialogo pubblica funzione, selezionare Azure app per le funzioni (Windows) o (Linux) in base al computer":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: finestra di dialogo pubblica funzione, creare una nuova funzione di Azure":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: finestra di dialogo pubblica funzione, compilare i campi e selezionare Crea":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: finestra di dialogo pubblica funzione, selezionare l'app per le funzioni dall'elenco e terminare":::

Nella pagina seguente immettere il nome desiderato per la nuova app per le funzioni, per un gruppo di risorse e per altri dettagli.
Per consentire all'app per le funzioni di accedere ai dispositivi gemelli digitali di Azure, è necessario disporre di un'identità gestita dal sistema e avere le autorizzazioni per accedere all'istanza di Azure Digital gemelli.

Successivamente, è possibile configurare l'accesso di sicurezza per la funzione usando l'interfaccia della riga di comando o portale di Azure. Scegliere uno di questi metodi:

## <a name="set-up-security-access-for-the-function-app"></a>Configurare l'accesso di sicurezza per l'app per le funzioni
È possibile configurare l'accesso di sicurezza per l'app per le funzioni usando una di queste opzioni:

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Opzione 1: configurare l'accesso di sicurezza per l'app per le funzioni usando l'interfaccia della riga di comando

Per poter eseguire l'autenticazione con i dispositivi gemelli digitali di Azure, la struttura della funzione degli esempi precedenti richiede che venga passata una bearer token. Per assicurarsi che questo bearer token venga passato, è necessario configurare [identità del servizio gestita (MSI)](../active-directory/managed-identities-azure-resources/overview.md) per l'app per le funzioni. Questa operazione deve essere eseguita solo una volta per ogni app per le funzioni.

È possibile creare un'identità gestita dal sistema e assegnare l'identità dell'app per le funzioni al ruolo di _**proprietario dei dati di Digital gemelli di Azure**_ per l'istanza di Digital gemelli di Azure. In questo modo si concede all'app per le funzioni l'autorizzazione per l'esecuzione di attività del piano dati. Quindi, rendere accessibile l'URL dell'istanza di Azure Digital Twins alla funzione impostando una variabile di ambiente.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Usare [Azure cloud Shell](https://shell.azure.com) per eseguire i comandi.

Usare il comando seguente per creare l'identità gestita dal sistema. Prendere nota del campo _principalId_ nell'output.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Usare il valore _PrincipalId_ nel comando seguente per assegnare l'identità dell'app per le funzioni al ruolo di _proprietario dei dati di Azure Digital gemelli_ per l'istanza di Digital gemelli di Azure.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Infine, è possibile rendere accessibile l'URL dell'istanza di Azure Digital Twins alla funzione impostando una variabile di ambiente. Per ulteriori informazioni sull'impostazione di variabili di ambiente, vedere [*variabili di ambiente*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> L'URL dell'istanza di Azure Digital gemelli viene creato aggiungendo *https://* all'inizio del *nome host* dell'istanza di Azure Digital gemelli. Per visualizzare il nome host, insieme a tutte le proprietà dell'istanza, è possibile eseguire `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Opzione 2: configurare l'accesso di sicurezza per l'app per le funzioni usando portale di Azure

Un'identità gestita assegnata dal sistema consente alle risorse di Azure di eseguire l'autenticazione nei servizi cloud, ad esempio Azure Key Vault, senza archiviare le credenziali nel codice. Una volta abilitate, tutte le autorizzazioni necessarie possono essere concesse tramite il controllo degli accessi in base al ruolo di Azure. Il ciclo di vita di questo tipo di identità gestita è associato al ciclo di vita di questa risorsa. Inoltre, ogni risorsa (ad esempio, macchina virtuale) può avere solo un'identità gestita assegnata dal sistema.

Nella [portale di Azure](https://portal.azure.com/)cercare app per le _funzioni_ nella barra di ricerca con il nome dell'app per le funzioni creato in precedenza. Selezionare il *app per le funzioni* dall'elenco. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Portale di Azure: cerca app per le funzioni":::

Nella finestra app per le funzioni selezionare _Identity_ nella barra di spostamento a sinistra per abilitare identità gestita.
Nella scheda _sistema assegnato_ , impostare lo _stato_ su attivato e _salvarlo_ . Viene visualizzato un popup per _abilitare l'identità gestita assegnata dal sistema_.
Selezionare il pulsante _Sì_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Portale di Azure: abilitare l'identità gestita dal sistema":::

È possibile verificare nelle notifiche che la funzione sia stata registrata correttamente con Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Portale di Azure: notifiche":::

Si noti anche l' **ID oggetto** visualizzato nella pagina _identità_ , che verrà usato nella sezione successiva.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Copiare l'ID oggetto da usare in futuro":::

### <a name="assign-access-roles-using-azure-portal"></a>Assegnare i ruoli di accesso utilizzando portale di Azure

Selezionare il pulsante _assegnazioni di ruolo di Azure_ , che consente di aprire la pagina *assegnazioni di ruolo di Azure* . Quindi selezionare _+ Aggiungi assegnazione ruolo (anteprima)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Portale di Azure: aggiungere un'assegnazione di ruolo":::

Nella pagina _Aggiungi assegnazione ruolo (anteprima)_ visualizzata selezionare:

* _Ambito_: gruppo di risorse
* _Sottoscrizione_: selezionare la sottoscrizione di Azure
* _Gruppo di risorse_: selezionare il gruppo di risorse dall'elenco a discesa
* _Ruolo_: selezionare il proprietario di dati di dispositivi _gemelli digitali di Azure_ dall'elenco a discesa

Salvare quindi i dettagli facendo clic sul pulsante _Salva_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Portale di Azure: aggiungere un'assegnazione di ruolo (anteprima) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Configurare le impostazioni dell'applicazione tramite portale di Azure

È possibile rendere accessibile l'URL dell'istanza di Azure Digital Twins alla funzione impostando una variabile di ambiente. Per altre informazioni, vedere variabili di [*ambiente*](/sandbox/functions-recipes/environment-variables). Le impostazioni dell'applicazione sono esposte come variabili di ambiente per accedere all'istanza di dispositivi gemelli digitali. 

È necessario ADT_INSTANCE_URL per creare un'impostazione dell'applicazione.

È possibile ottenere ADT_INSTANCE_URL aggiungendo **_https://_** al nome host dell'istanza. Nella portale di Azure è possibile trovare il nome host dell'istanza di Digital gemelli cercando l'istanza nella barra di ricerca. Selezionare quindi _Panoramica_ sulla barra di spostamento a sinistra per visualizzare il _nome host_. Copiare questo valore per creare un'impostazione dell'applicazione.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Portale di Azure: Panoramica-> copiare il nome host da usare nel campo _Value_.":::

È ora possibile creare un'impostazione dell'applicazione seguendo questa procedura:

* Cercare l'app usando il nome dell'app per le funzioni nella barra di ricerca e selezionare l'app per le funzioni nell'elenco
* Selezionare _configurazione_ nella barra di spostamento a sinistra per creare una nuova impostazione dell'applicazione
* Nella scheda _Impostazioni applicazione_ selezionare _+ nuova impostazione applicazione_

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Portale di Azure: cercare un'app per le funzioni esistente":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Portale di Azure: configurare le impostazioni dell'applicazione":::

Nella finestra visualizzata, usare il valore copiato da sopra per creare un'impostazione dell'applicazione. \
_Nome_  : ADT_SERVICE_URL \
_Valore_ : https://{Your-Azure-Digital-Twins-hostname}

Selezionare _OK_ per creare un'impostazione dell'applicazione.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Portale di Azure: aggiungere le impostazioni dell'applicazione.":::

È possibile visualizzare le impostazioni dell'applicazione con il nome dell'applicazione nel campo _nome_ . Salvare quindi le impostazioni dell'applicazione selezionando il pulsante _Salva_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Portale di Azure: visualizzare l'applicazione creata e riavviare l'applicazione":::

Per tutte le modifiche apportate alle impostazioni dell'applicazione è necessario riavviare l'applicazione. Selezionare _continua_ per riavviare l'applicazione.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Portale di Azure: salvare le impostazioni dell'applicazione":::

È possibile visualizzare le impostazioni dell'applicazione aggiornate selezionando l'icona _notifiche_ . Se non viene creata l'impostazione dell'applicazione, è possibile riprovare ad aggiungere un'impostazione dell'applicazione attenendosi al processo precedente.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Portale di Azure: notifiche per l'aggiornamento delle impostazioni dell'applicazione":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati seguiti i passaggi per configurare un'app per le funzioni in Azure per l'uso con i dispositivi gemelli digitali di Azure. Successivamente, è possibile sottoscrivere la funzione in griglia di eventi per restare in attesa su un endpoint. Questo endpoint può essere:
* Un endpoint di griglia di eventi collegato ai dispositivi gemelli digitali di Azure per elaborare i messaggi provenienti da gemelli digitali di Azure, ad esempio i messaggi di modifica delle proprietà, i messaggi di telemetria generati dai dispositivi [gemelli digitali](concepts-twins-graph.md) nel grafico gemello o i messaggi del ciclo di vita.
* Argomenti di sistema di Internet delle cose usati dall'hub Internet per inviare dati di telemetria e altri eventi del dispositivo
* Un endpoint di griglia di eventi che riceve messaggi da altri servizi

Vedere quindi come compilare la funzione di base per inserire i dati dell'hub Internet in dispositivi gemelli digitali di Azure:
* [*Procedura: inserire dati di telemetria dall'hub Internet*](how-to-ingest-iot-hub-data.md)
