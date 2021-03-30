---
title: Creare rapidamente un'app .NET su Service Fabric in Azure
description: In questa guida introduttiva viene creata un'applicazione .NET per Azure usando l'applicazione Reliable Services Service Fabric di esempio.
ms.topic: quickstart
ms.date: 06/26/2019
ms.custom: mvc, devcenter, vs-azure
ms.openlocfilehash: 15e2180e44acaa5ebefb403b2da3755396a45ba4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575891"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>Guida introduttiva: distribuire un'applicazione Reliable Services .NET su Service Fabric

Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.

In questa guida introduttiva viene illustrato come distribuire la prima applicazione .NET in Service Fabric. Al termine, sarà disponibile un'applicazione di voto con un front-end Web ASP.NET Core che salva i risultati delle votazioni in un servizio back-end con stato nel cluster.

![Screenshot dell'applicazione](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Usando questa applicazione, si apprenderà come:

* Creare un'applicazione mediante .NET e Service Fabric
* Usare ASP.NET Core come front-end Web
* Archiviare i dati dell'applicazione in un servizio con stato
* Eseguire il debug dell'applicazione in locale
* Scalare orizzontalmente l'applicazione in più nodi
* Eseguire un aggiornamento in sequenza delle applicazioni

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

1. [Installare Visual Studio 2019](https://www.visualstudio.com/) con i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
2. [Installare Git](https://git-scm.com/)
3. [Installare Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Eseguire il comando seguente per consentire a Visual Studio di eseguire la distribuzione nel cluster Service Fabric locale:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
   ```
    
## <a name="build-a-cluster"></a>Creare un cluster

Dopo aver installato il runtime, gli SDK e gli strumenti di Visual Studio e aver avviato il Docker, creare un cluster di sviluppo locale a cinque nodi.

> [!Note]
> Durante questa procedura, è necessario che il Docker sia in esecuzione per fare in modo che il cluster venga creato con le funzionalità del contenitore abilitate. Se il Docker non è in esecuzione, sarà necessario ricreare il cluster per abilitare le funzionalità del contenitore.
> Anche se non è necessaria per questa specifica guida introduttiva, l'istruzione di avere il Docker in esecuzione quando si crea il cluster viene inclusa come procedura consigliata.
> Per testare se Docker è in esecuzione, aprire una finestra del terminale ed eseguire `docker ps` per vedere se si verifica un errore. Se la risposta non indica un errore, Docker è in esecuzione ed è possibile creare un cluster.
>
> [Configurare Windows 10 o Windows Server per i contenitori](/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-10-Client)

1. Aprire una nuova finestra di PowerShell con privilegi elevati come amministratore.
2. Eseguire il comando di PowerShell seguente per creare un cluster di sviluppo:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
   ```
3. Per avviare lo strumento di gestione cluster locale, eseguire il comando seguente:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
   ```

>[!NOTE]
> L'applicazione di esempio in questa guida introduttiva usa funzionalità non disponibili in Windows 7.
>

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale

Fare clic con il pulsante destro del mouse sull'icona di Visual Studio nel menu Start e scegliere **Esegui come amministratore**. Per collegare il debugger ai servizi, è necessario eseguire Visual Studio come amministratore.

Aprire la soluzione di Visual Studio **Voting.sln** dal repository che è stato clonato.

Per impostazione predefinita, l'applicazione Voting ascolta sulla porta 8080.  La porta dell'applicazione è impostata nel file */VotingWeb/PackageRoot/ServiceManifest.xml*.  È possibile cambiare la porta dell'applicazione aggiornando l'attributo **Port** dell'elemento **Endpoint**.  Per distribuire ed eseguire l'applicazione in locale, la porta dell'applicazione deve essere aperta e disponibile nel computer.  Se si cambia la porta dell'applicazione, sostituire "8080" con il nuovo valore della porta dell'applicazione in tutto l'articolo.

Per distribuire l'applicazione, premere **F5**.

> [!NOTE]
> Nella finestra dell'output di Visual Studio verrà visualizzato il messaggio "L'URL dell'applicazione non è impostato oppure non è di tipo HTTP/HTTPS, di conseguenza l'applicazione non verrà aperta nel browser".  Questo messaggio non indica un errore, ma indica che un browser non verrà avviato automaticamente.

Al termine della distribuzione, avviare un browser e aprire `http://localhost:8080` per visualizzare il front-end Web dell'applicazione.

![Front-end dell'applicazione](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

È ora possibile aggiungere un set di opzioni per le votazioni e iniziare a raccogliere i voti. L'applicazione viene eseguita e archivia tutti i dati nel cluster di Service Fabric, senza che sia necessario un database separato.

## <a name="walk-through-the-voting-sample-application"></a>Descrizione dettagliata dell'applicazione di voto di esempio

L'applicazione di voto è costituita da due servizi:

* Il servizio front-end Web (VotingWeb) - Un servizio front-end Web ASP.NET Core che gestisce la pagina Web e che espone le API Web per la comunicazione con il servizio back-end.
* Il servizio back-end (VotingData) - Un servizio Web ASP.NET Core che espone un'API per l'archiviazione dei risultati delle votazioni in un oggetto Reliable Dictionary reso persistente su disco.

![Diagramma dell'applicazione](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Quando si vota nell'applicazione, si verificano gli eventi seguenti:

1. JavaScript invia la richiesta di voto all'API Web nel servizio front-end Web come una richiesta HTTP PUT.

2. Il servizio front-end Web usa un proxy per individuare e inoltrare una richiesta PUT HTTP al servizio back-end.

3. Il servizio back-end accetta la richiesta in ingresso e archivia il risultato aggiornato in un oggetto Reliable Dictionary, che viene replicato in più nodi all'interno del cluster e reso persistente su disco. Tutti i dati dell'applicazione sono archiviati nel cluster, quindi non è necessario alcun database.

## <a name="debug-in-visual-studio"></a>Eseguire il debug in Visual Studio

L'applicazione dovrebbe essere correttamente in esecuzione, ma è possibile usare il debugger per verificare il funzionamento delle parti principali dell'applicazione. Durante il debug dell'applicazione in Visual Studio viene usato un cluster di sviluppo locale di Service Fabric. È possibile modificare l'esperienza di debug in base allo specifico scenario. In questa applicazione i dati vengono archiviati nel servizio back-end tramite un dizionario Reliable. Visual Studio rimuove l'applicazione per impostazione predefinita quando si arresta il debugger. La rimozione dell'applicazione determina la rimozione anche dei dati nel servizio back-end. Per rendere persistenti i dati tra le sessioni di debug, è possibile modificare la proprietà **Modalità di debug applicazione** del progetto **Voting** in Visual Studio.

Per osservare che cosa avviene nel codice, completare la procedura seguente:

1. Aprire il file **/VotingWeb/Controllers/VotesController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web (riga 69). È possibile cercare il file in Esplora soluzioni in Visual Studio.

2. Aprire il file **/VotingData/Controllers/VoteDataController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web (riga 54).

3. Tornare al browser e fare clic su un'opzione di voto oppure aggiungere una nuova opzione di voto. È stato raggiunto il primo punto di interruzione nel controller API del front-end Web.
   * In questo passaggio il codice JavaScript nel browser invia una richiesta al controller API Web nel servizio front-end.

     ![Aggiungere il servizio front-end di voto](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

   * Prima di tutto, costruire l'URL di ReverseProxy per il servizio back-end **(1)**.
   * Inviare quindi la richiesta HTTP PUT a ReverseProxy **(2)**.
   * Infine, restituire la risposta dal servizio back-end al client **(3)**.

4. Premere **F5** per continuare
   - Se richiesto dal browser, assegnare al gruppo ServiceFabricAllowedUsers le autorizzazioni di lettura ed esecuzione per Modalità di debug.
   - Ora ci troviamo in corrispondenza del punto di interruzione nel servizio back-end.

     ![Aggiungere il servizio back-end di voto](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

   - Nella prima riga del metodo **(1)**`StateManager` viene usato per ottenere o aggiungere un dizionario Reliable denominato `counts`.
   - Tutte le interazioni con i valori in un oggetto Reliable Dictionary richiedono una transazione, che viene creata dall'istruzione using **(2)** .
   - Nella transazione aggiornare il valore della chiave pertinente per l'opzione di voto ed eseguire il commit dell'operazione **(3)**. Dopo la restituzione del metodo Commit, i dati vengono aggiornati nel dizionario e replicati negli altri nodi del cluster. A questo punto, i dati sono archiviati in modo sicuro nel cluster e il servizio back-end può eseguire il failover in altri nodi, rendendo comunque disponibili i dati.
5. Premere **F5** per continuare

Per interrompere la sessione di debug, premere **MAIUSC+F5**.

## <a name="perform-a-rolling-application-upgrade"></a>Eseguire un aggiornamento in sequenza delle applicazioni

Durante la distribuzione di nuovi aggiornamenti per l'applicazione, Service Fabric distribuisce gli aggiornamenti in modo sicuro. Gli aggiornamenti in sequenza non comportano tempi di inattività durante l'aggiornamento e consentono il rollback automatico in caso di errori.

Per aggiornare l'applicazione, eseguire le operazioni seguenti:

1. Aprire il file **/VotingWeb/Views/Home/Index.cshtml** in Visual Studio.
2. Modificare l'intestazione della pagina aggiungendo o aggiornando il testo. Ad esempio, modificare il titolo in "Esempio di voto di Service Fabric v2".
3. Salvare il file.
4. Fare clic con il pulsante destro del mouse su **Voting** in Esplora soluzioni e scegliere **Pubblica**. Verrà visualizzata la finestra di dialogo Pubblica.
5. Fare clic sul pulsante **Versione manifesto** per modificare la versione del servizio e dell'applicazione.
6. Modificare la versione dell'elemento **Codice** in **VotingWebPkg**, ad esempio in "2.0.0", e fare clic su **Salva**.

    ![Finestra di dialogo Modifica versione](./media/service-fabric-quickstart-dotnet/change-version.png)
7. Nella finestra di dialogo **Pubblica applicazione di Service Fabric** selezionare la casella di controllo **Aggiorna l'applicazione**.
8.  Modificare **Profilo target** in **PublishProfiles\Local.5Node.xml** e verificare che **Endpoint connessione** sia impostato su **Cluster locale**. 
9. Selezionare **Aggiorna l'applicazione**.

    ![Impostazione di aggiornamento nella finestra di dialogo Pubblica](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. Fare clic su **Pubblica**.

    Durante l'esecuzione dell'aggiornamento, è comunque possibile usare l'applicazione. Poiché nel cluster sono in esecuzione due istanze del servizio, alcune delle richieste potrebbero ottenere la versione aggiornata dell'applicazione, mentre altre potrebbero ancora ottenere la versione precedente.

11. Aprire il browser e passare all'indirizzo del cluster sulla porta 19080. Ad esempio: `http://localhost:19080/`.
12. Fare clic sul nodo **Applicazioni** nella visualizzazione ad albero, quindi su **Upgrades in Progress** (Aggiornamenti in corso) nel riquadro destro. È possibile osservare che l'aggiornamento viene distribuito attraverso i domini di aggiornamento del cluster, verificando l'integrità di ogni dominio prima di procedere a quello successivo. Nell'indicatore di stato viene visualizzato in verde un dominio di aggiornamento dopo aver verificato l'integrità di dominio.
    ![Visualizzazione dell'aggiornamento in Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric garantisce la sicurezza degli aggiornamenti attendendo due minuti dopo l'aggiornamento del servizio in ogni nodo del cluster. L'intero aggiornamento richiederà circa 8 minuti.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come:

* Creare un'applicazione mediante .NET e Service Fabric
* Usare ASP.NET Core come front-end Web
* Archiviare i dati dell'applicazione in un servizio con stato
* Eseguire il debug dell'applicazione in locale
* Scalare orizzontalmente l'applicazione in più nodi
* Eseguire un aggiornamento in sequenza delle applicazioni

Per altre informazioni su Service Fabric e .NET, fare riferimento a questa esercitazione:
> [!div class="nextstepaction"]
> [Applicazione .NET in Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
