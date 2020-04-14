---
title: Configurare Blockchain Data Manager usando il portale di Azure - Servizio Blockchain di AzureConfigure Blockchain Data Manager using Azure portal - Azure Blockchain Service
description: Creare e gestire il servizio Blockchain Data Manager per Azure Blockchain usando il portale di Azure.Create and manage Blockchain Data Manager for Azure Blockchain Service using the Azure portal.
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 08f5a4a807087afce13dd4a6e96c0e9dd0a36103
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260599"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurare Blockchain Data Manager con il portale di Azure

Configurare Il servizio Blockchain Data Manager per Azure Per acquisire i dati blockchain e inviarli a un argomento griglia di eventi di Azure.Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data and send it to an Azure Event Grid Topic.

Per configurare un'istanza di Blockchain Data Manager, è necessario:

* Creare un'istanza di Blockchain Data Manager per un nodo di transazione del servizio Blockchain di AzureCreate a Blockchain Data Manager instance for an Azure Blockchain Service transaction node
* Aggiungi le tue applicazioni blockchain

## <a name="prerequisites"></a>Prerequisiti

* Guida [introduttiva completa: Creare un membro blockchain usando il portale di Azure](create-member.md) o Guida introduttiva: Creare un membro blockchain del servizio Blockchain di Azure usando l'interfaccia della riga di comando di Azure.Complete Quickstart: Create a blockchain member using the Azure portal or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI.](create-member-cli.md) Il livello Standard di servizio di Azure Blockchain è consigliato quando si usa Blockchain Data Manager.Azure Blockchain Service *Standard* tier is recommended when using Blockchain Data Manager.
* Creare un [argomento della griglia di eventiCreate](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) an Event Grid Topic
* Informazioni su [Gestori eventi di Griglia di eventi di Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Creare un'istanza

Un'istanza di Blockchain Data Manager connette e monitora un nodo della transazione del servizio Azure Blockchain. Solo gli utenti con accesso al nodo della transazione possono creare una connessione. Un'istanza acquisisce tutti i dati di blocchi e transazioni non elaborati dal nodo della transazione. Blockchain Data Manager pubblica un messaggio **RawBlockAndTransactionMsg** che è un superset di informazioni restituite da web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) e [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) query.

Una connessione in uscita invia i dati della blockchain a Griglia di eventi di Azure. Quando si crea l'istanza, viene configurata una singola connessione in uscita. Blockchain Data Manager supporta più connessioni in uscita per gli argomenti di Griglia di eventi per qualsiasi istanza di Blockchain Data Manager specificata. È possibile inviare i dati della blockchain a una o più destinazioni. Per aggiungere un'altra destinazione, è sufficiente aggiungere altre connessioni in uscita all'istanza.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Blockchain di Azure che si vuole connettere a Blockchain Data Manager.Go to the Azure Blockchain Service member you want to connect to Blockchain Data Manager. Selezionare **Blockchain Data Manager**.
1. Selezionare **Aggiungi**.

    ![Aggiungere Blockchain Data Manager](./media/data-manager-portal/add-instance.png)

    Immettere i dettagli seguenti:

    Impostazione | Descrizione
    --------|------------
    Nome | Immettere un nome univoco per un'istanza di Blockchain Data Manager connessa. Il nome Blockchain Data Manager può contenere lettere minuscole e numeri e ha una lunghezza massima di 20 caratteri.
    Nodo di transazione | Scegliere un nodo di transazione. Vengono elencati solo i nodi di transazione di cui si dispone dell'accesso in lettura.
    Nome connessione | Immettere un nome univoco della connessione in uscita a cui vengono inviati i dati della transazione blockchain.
    Endpoint di Griglia di eventi | Scegliere un argomento della griglia di eventi nella stessa sottoscrizione dell'istanza di Blockchain Data Manager.Choose an event grid topic in the same subscription as the Blockchain Data Manager instance.

1. Selezionare **OK**.

    La creazione di un'istanza di Blockchain Data Manager richiede meno di un minuto. Una volta distribuita, l'istanza viene avviata automaticamente. Un'istanza di Blockchain Data Manager in esecuzione acquisisce gli eventi blockchain dal nodo della transazione e invia i dati alle connessioni in uscita.

    La nuova istanza viene visualizzata nell'elenco delle istanze di Blockchain Data Manager per il membro del servizio Blockchain di Azure.The new instance appears in the list of Blockchain Data Manager instances for the Azure Blockchain Service member.

    ![Elenco delle istanze di Blockchain Data Member](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Aggiungi applicazione blockchain

Se si aggiunge un'applicazione blockchain, Blockchain Data Manager decodifica lo stato dell'evento e della proprietà per l'applicazione. In caso contrario, vengono inviati solo i dati non elaborati relativi ai blocchi e alle transazioni non elaborati. Blockchain Data Manager scopre anche gli indirizzi del contratto quando il contratto viene distribuito. È possibile aggiungere più applicazioni blockchain a un'istanza di Blockchain Data Manager.You can add multiple blockchain applications to a Blockchain Data Manager instance.

> [!IMPORTANT]
> Attualmente, le applicazioni blockchain che dichiarano tipi di [matrice](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) Solidity o tipi di [mapping](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) non sono completamente supportate. Le proprietà dichiarate come tipi di matrice o mapping non verranno decodificate nei messaggi *ContractPropertiesMsg* o *DecodedContractEventsMsg.*

Blockchain Data Manager richiede un contratto intelligente ABI e distribuito file bytecode per aggiungere l'applicazione.

### <a name="get-contract-abi-and-bytecode"></a>Ottenere l'ABI del contratto e il bytecodeGet Contract ABI and bytecode

L'interfaccia ABI (Application Binary Interface) del contratto definisce le interfacce del contratto intelligente. Descrive come interagire con il contratto intelligente. È possibile usare l'[estensione Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) per copiare l'ABI del contratto negli Appunti.

1. Nel riquadro di Esplora risorse di Visual Studio Code espandere la cartella **build/contracts** del progetto Solidity.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Selezionare **Copy contract ABI** (Copia interfaccia ABI contratto).

    ![Riquadro di Visual Studio Code con l'opzione Copia interfaccia ABI contratto selezionata](./media/data-manager-portal/abi-devkit.png)

    L'interfaccia ABI del contratto viene copiata negli Appunti.

1. Salvare la matrice **abi** come file JSON. Ad esempio, *abi.json*. Questo file verrà usato in un passaggio successivo.

Blockchain Data Manager richiede il bytecode distribuito per il contratto intelligente. Il bytecode distribuito è diverso dal bytecode del contratto intelligente. È possibile usare l'estensione del kit di sviluppo di Azure Blockchain per copiare il bytecode negli Appunti.

1. Nel riquadro di Esplora risorse di Visual Studio Code espandere la cartella **build/contracts** del progetto Solidity.
1. Fare clic con il pulsante destro del mouse sul file JSON dei metadati del contratto. Il nome del file è il nome del contratto intelligente seguito dall'estensione **.json**.
1. Selezionare **Copy Transaction Bytecode** (Copia bytecode della transazione).

    ![Riquadro di Visual Studio Code con l'opzione Copy Transaction Bytecode selezionata](./media/data-manager-portal/bytecode-devkit.png)

    Il bytecode viene copiato negli Appunti.

1. Salvare il valore **bytecode** come file JSON. Ad esempio, *bytecode.json*. Questo file verrà usato in un passaggio successivo.

L'esempio seguente mostra i file *abi.json* e *bytecode.json* aperti nell'editor di VS Code. I file dovrebbero essere simili.

![Esempio di file abi.json e bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Creare l'URL dell'ABI e del bytecode del contratto

Blockchain Data Manager richiede che i file ABI e bytecode del contratto siano accessibili da un URL durante l'aggiunta di un'applicazione. È possibile usare un account di archiviazione di Azure per fornire un URL accessibile privatamente.

#### <a name="create-storage-account"></a>Crea account di archiviazione

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Caricare i file del contratto

1. Creare un nuovo contenitore per l'account di archiviazione. Selezionare **Contenitori > Contenitore**.

    ![Creare un contenitore dell'account di archiviazione](./media/data-manager-portal/create-container.png)

    | Campo | Descrizione |
    |-------|-------------|
    | Nome  | Assegnare un nome al contenitore. Ad esempio, *smartcontract* |
    | Livello di accesso pubblico | Scegliere *Privato (nessun accesso anonimo)* |

1. Fare clic su **OK** per creare il contenitore.
1. Selezionare il contenitore e quindi **Carica**.
1. Scegliere entrambi i file JSON creati nella sezione [Ottenere l'ABI e il bytecode del contratto](#get-contract-abi-and-bytecode).

    ![Caricamento del BLOB](./media/data-manager-portal/upload-blobs.png)

    Selezionare **Carica**.

#### <a name="generate-url"></a>Generare l'URL

Per ogni BLOB, generare una firma di accesso condiviso.

1. Selezionare il BLOB JSON dell'ABI.
1. Selezionare **Genera firma di accesso condiviso**
1. Impostare la scadenza desiderata per la firma di accesso, quindi selezionare **Genera URL e token di firma di accesso condiviso del BLOB**.

    ![Generare il token di firma di accesso condiviso](./media/data-manager-portal/generate-sas.png)

1. Copiare il valore di **URL di firma di accesso condiviso del BLOB** e salvarlo per la sezione successiva.
1. Ripetere la procedura [Generare l'URL](#generate-url) per il BLOB JSON del bytecode.

### <a name="add-application-to-instance"></a>Aggiungi applicazione all'istanza

1. Selezionare l'istanza di Blockchain Data Manager nell'elenco di istanze.
1. Selezionare **Blockchain applications** (Applicazioni blockchain).
1. Selezionare **Aggiungi**.

    ![Aggiungere un'applicazione blockchain](./media/data-manager-portal/add-application.png)

    Immettere il nome dell'applicazione blockchain e gli URL dell'ABI e del bytecode del contratto intelligente.

    Impostazione | Descrizione
    --------|------------
    Nome | Immettere un nome univoco per l'applicazione blockchain da monitorare.
    Contract ABI (ABI contratto) | Percorso URL del file ABI del contratto. Per altre informazioni, vedere [Creare l'URL dell'ABI e del bytecode del contratto](#create-contract-abi-and-bytecode-url).
    Contract bytecode (Bytecode contratto) | Percorso URL del file bytecode. Per altre informazioni, vedere [Creare l'URL dell'ABI e del bytecode del contratto](#create-contract-abi-and-bytecode-url).

1. Selezionare **OK**.

    Una volta creata, l'applicazione viene visualizzata nell'elenco di applicazioni blockchain.

    ![Elenco di applicazioni blockchain](./media/data-manager-portal/artifact-list.png)

È possibile eliminare l'account di archiviazione di Azure o usarlo per configurare altre applicazioni blockchain. Se si vuole eliminare l'account di archiviazione di Azure, è possibile eliminare il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminati anche l'account di archiviazione associato e tutte le altre risorse correlate al gruppo di risorse.

## <a name="stop-instance"></a>Interrompi istanza

Arrestare l'istanza di Blockchain Manager quando si desidera interrompere l'acquisizione di eventi blockchain e l'invio di dati alle connessioni in uscita. Quando l'istanza viene arrestata, non vengono addebitati addebiti per Blockchain Data Manager. Per ulteriori informazioni, vedere [prezzi](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Passare a **Panoramica** e selezionare **Interrompi**.

    ![Interrompi istanza](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione successiva sulla creazione di un esploramessaggi di transazioni blockchain usando Blockchain Data Manager e Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Usare Blockchain Data Manager per inviare dati ad Azure Cosmos DB](data-manager-cosmosdb.md)