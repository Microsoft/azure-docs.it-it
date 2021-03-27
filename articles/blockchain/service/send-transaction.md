---
title: 'Esercitazione: Creare, compilare e distribuire contratti intelligenti - Servizio Azure Blockchain'
description: Esercitazione sull'utilizzo dell'estensione Azure Blockchain Development Kit per Ethereum in Visual Studio Code per creare, compilare e distribuire un contratto intelligente con il servizio Azure Blockchain.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628646"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Esercitazione: Creare, compilare e distribuire contratti intelligenti in Servizio Azure Blockchain

In questa esercitazione si usa l'estensione Azure Blockchain Development Kit per Ethereum in Visual Studio Code per creare, compilare e distribuire un contratto intelligente con il servizio Azure Blockchain. Il kit di sviluppo si usa anche per eseguire una funzione del contratto intelligente tramite una transazione.

È possibile usare Azure Blockchain Development Kit per Ethereum per:

> [!div class="checklist"]
> * Creare un contratto intelligente
> * Distribuire un contratto intelligente
> * Eseguire una funzione del contratto intelligente tramite una transazione

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare [Avvio rapido: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Estensione Azure Blockchain Development Kit per Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x o versione successiva](https://nodejs.org/download)
* [Git 2.10.x o versione successiva](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfaccia della riga di comando Ganache 6.0.0](https://github.com/trufflesuite/ganache-cli)

In Windows è necessario un compilatore C++ installato per il modulo node-gyp. È possibile usare gli strumenti MSBuild:

* Se è installato Visual Studio 2017, configurare npm per l'uso degli strumenti MSBuild con il comando `npm config set msvs_version 2017 -g`
* Se è installato Visual Studio 2019, impostare il percorso degli strumenti MSBuild per npm. Ad esempio, usare `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* In caso contrario, installare gli strumenti autonomi di compilazione di Visual Studio usando `npm install --global windows-build-tools` in una shell di comandi con privilegi elevati *Esegui come amministratore*.

Per altre informazioni su node-gyp, vedere il [repository node-gyp in GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Creare un contratto intelligente

Azure Blockchain Development Kit per Ethereum usa i modelli di progetto e gli strumenti di Truffle per semplificare lo scaffolding, la compilazione e la distribuzione dei contratti. Prima di iniziare, completare [Avvio rapido: Usare Visual Studio Code per connettersi alla rete di un consorzio del servizio Azure Blockchain](connect-vscode.md). Questa guida di avvio rapido illustra l'installazione e la configurazione di Azure Blockchain Development Kit per Ethereum.

1. Dal riquadro comandi di VS Code scegliere **Blockchain: New Solidity Project** (Nuovo progetto Solidity).
1. Scegliere **Create basic project** (Crea progetto di base).
1. Creare una nuova cartella denominata `HelloBlockchain` e scegliere **Select new project path** (Selezionare nuovo percorso progetto).

Azure Blockchain Development Kit crea e inizializza automaticamente un nuovo progetto Solidity. Il progetto di base include un contratto intelligente di esempio denominato **HelloBlockchain** e tutti i file necessari per eseguire la compilazione e la distribuzione al membro del consorzio nel servizio Azure Blockchain. La creazione del progetto può richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento nel pannello del terminale di VS Code selezionando l'output per Azure Blockchain.

La struttura del progetto avrà un aspetto simile al seguente:

   ![Progetto Solidity](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Compilare un contratto intelligente

I contratti intelligenti si trovano nella directory **contracts** del progetto. È necessario compilare i contratti intelligenti prima della distribuzione a una blockchain. Usare il comando **Build Contracts** (Compila contratti) per compilare tutti i contratti intelligenti nel progetto.

1. Nella barra laterale di Explorer di VS Code espandere la cartella **contracts** nel progetto.
1. Fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Build Contracts** (Compila contratti) dal menu.

    ![Scegliere il menu Build contracts (Compila contratti) ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit usa Truffle per compilare i contratti intelligenti.

![Output del compilatore Truffle](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Distribuire un contratto intelligente

Truffle usa gli script di migrazione per distribuire i contratti a una rete Ethereum. Le migrazioni sono file JavaScript che si trovano nella directory **migrations** del progetto.

1. Per distribuire un contratto intelligente, fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Deploy Contracts** (Distribuisci contratti) dal menu.
1. Scegliere la rete per consorzi Azure Blockchain nel riquadro comandi. La rete blockchain per consorzi è stata aggiunta al file di configurazione Truffle del progetto al momento della creazione del progetto.
1. Scegliere **Generate mnemonic** (Genera tasto di scelta). Scegliere un nome file e salvare il file dei tasti di scelta nella cartella del progetto. Ad esempio: `myblockchainmember.env`. Il file dei tasti di scelta viene usato per generare una chiave privata Ethereum per il membro della blockchain.

Azure Blockchain Development Kit usa Truffle per eseguire lo script di migrazione per distribuire i contratti alla blockchain.

![Distribuzione del contratto riuscita](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Chiamare una funzione del contratto
La funzione **SendRequest** del contratto **HelloBlockchain** modifica la variabile di stato **RequestMessage**. La modifica dello stato di una rete blockchain viene eseguita tramite una transazione. È possibile creare uno script per eseguire la funzione **SendRequest** tramite una transazione.

1. Creare un nuovo file nella radice del progetto Truffle e denominarlo `sendrequest.js`. Aggiungere il codice JavaScript Web3 seguente al file.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Quando Azure Blockchain Development Kit crea un progetto, il file di configurazione Truffle viene generato con i dettagli dell'endpoint della rete blockchain per consorzi. Aprire **truffle-config.js** nel progetto. Il file di configurazione elenca due reti: una denominata development e una con lo stesso nome del consorzio.
1. Nel riquadro del terminale di VS Code usare Truffle per eseguire lo script nella rete blockchain per consorzi. Nella barra dei menu del riquadro del terminale selezionare la scheda **Terminale** e **PowerShell** nell'elenco a discesa.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Sostituire \<blockchain network\> con il nome della rete blockchain definita nella **truffle-config.js**.

Truffle esegue lo script nella rete blockchain.

![L'output che mostra la transazione è stato inviato](./media/send-transaction/execute-transaction.png)

Quando si esegue una funzione del contratto tramite una transazione, la transazione non viene elaborata finché non viene creato un blocco. Le funzioni destinate a essere eseguite tramite una transazione restituiscono un ID transazione anziché un valore restituito.

## <a name="query-contract-state"></a>Eseguire una query sullo stato del contratto

Le funzioni dei contratti intelligenti possono restituire il valore corrente delle variabili di stato. Aggiungere una funzione per restituire il valore di una variabile di stato.

1. In **HelloBlockchain.sol** aggiungere una funzione **getMessage** al contratto intelligente **HelloBlockchain**.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    La funzione restituisce il messaggio archiviato in una variabile di stato in base allo stato corrente del contratto.

1. Fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Build Contracts** (Compila contratti) dal menu per compilare le modifiche apportate al contratto intelligente.
1. Per eseguire la distribuzione, fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Deploy Contracts** (Distribuisci contratti) dal menu. Quando richiesto, scegliere la rete per consorzi Azure Blockchain nel riquadro comandi.
1. Successivamente, creare uno script per chiamare la funzione **getMessage**. Creare un nuovo file nella radice del progetto Truffle e denominarlo `getmessage.js`. Aggiungere il codice JavaScript Web3 seguente al file.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Nel riquadro del terminale di VS Code usare Truffle per eseguire lo script nella rete blockchain. Nella barra dei menu del riquadro del terminale selezionare la scheda **Terminale** e **PowerShell** nell'elenco a discesa.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Sostituire \<blockchain network\> con il nome della rete blockchain definita nella **truffle-config.js**.

Lo script esegue una query sul contratto intelligente chiamando la funzione getMessage. Viene restituito il valore corrente della variabile di stato **RequestMessage**.

![Output della query GetMessage che mostra il valore corrente della variabile di stato RequestMessage](./media/send-transaction/execute-get.png)

Si noti che il valore non è **Hello, blockchain!**. Il valore restituito è un segnaposto. Quando si cambia e si distribuisce il contratto, il contratto modificato viene distribuito a un nuovo indirizzo e alle variabili di stato vengono assegnati i valori nel costruttore del contratto intelligente. Lo script di migrazione di esempio Truffle **2_deploy_contracts.js** distribuisce il contratto intelligente e passa un valore segnaposto come argomento. Il costruttore imposta la variabile di stato **RequestMessage** sul valore segnaposto, ovvero il valore restituito.

1. Per impostare la variabile di stato **RequestMessage** ed eseguire una query sul valore, eseguire di nuovo gli script **sendrequest.js** e **getmessage.js**.

    ![L'output degli script SendRequest e GetMessage che mostrano RequestMessage è stato impostato](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** imposta la variabile di stato **RequestMessage** su **Hello, blockchain!** e **getmessage.js** esegue una query sul contratto per ottenere il valore della variabile di stato **RequestMessage** e restituisce **Hello, blockchain!**.
## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato nell'avvio rapido *Creare un membro della blockchain* eseguito come parte dei prerequisiti.

Per eliminare il gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
1. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un progetto Solidity di esempio usando Azure Blockchain Development Kit. È stato compilato e distribuito un contratto intelligente e quindi è stata chiamata una funzione tramite una transazione in una rete blockchain per consorzi ospitata nel servizio Azure Blockchain.

> [!div class="nextstepaction"]
> [Sviluppo di applicazioni blockchain con il servizio Azure Blockchain](develop.md)
