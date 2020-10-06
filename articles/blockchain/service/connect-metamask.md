---
title: Connettere MetaMask a una rete del servizio Azure Blockchain
description: Connettersi a una rete del servizio Azure Blockchain usando MetaMask e distribuire un contratto intelligente.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530438"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Usare MetaMask per connettersi e distribuire un contratto intelligente

In questa guida di avvio rapido si userà MetaMask per connettersi a una rete del servizio Azure Blockchain e Remix per distribuire un contratto intelligente. Metamask è un'estensione del browser per gestire un portafoglio Ether ed eseguire azioni di contratto intelligente.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti 

* Completare [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md)
* Installare l'[estensione del browser MetaMask](https://metamask.io)
* Generare un [portafoglio](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) MetaMask

## <a name="get-endpoint-address"></a>Ottenere l'indirizzo endpoint

È necessario l'indirizzo endpoint del servizio Azure Blockchain per connettersi alla rete blockchain. L'indirizzo endpoint e le chiavi di accesso sono disponibili nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure Blockchain.
1. Selezionare **Transaction nodes** (Nodi transazione) e il collegamento al nodo della transazione predefinito.

    ![Selezionare il nodo della transazione predefinito](./media/connect-metamask/transaction-nodes.png)

1. Selezionare **Connection strings > Access keys** (Stringhe di connessione > Chiavi di accesso).
1. Copiare l'indirizzo endpoint da **HTTPS (Access key 1)**.

    ![Stringa di connessione](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Connettere MetaMask

1. Aprire l'estensione del browser MetaMask e accedere.
1. Nell'elenco a discesa della rete, selezionare **Custom RPC** (RPC personalizzato).

    ![RPC personalizzato](./media/connect-metamask/custom-rpc.png)

1. In **New Network > New RPC URL** (Nuova rete > Nuovo URL RPC) incollare l'indirizzo dell'endpoint copiato in precedenza.
1. Selezionare **Salva**.

    Se connessione è riuscita, la rete privata viene visualizzata nell'elenco a discesa della rete.

    ![New network (Nuova rete)](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Distribuire il contratto intelligente

Remix è un ambiente di sviluppo Solidity basato su browser. L'uso congiunto di MetaMask e Remix permette di distribuire ed eseguire operazioni sui contratti intelligenti.

1. Nel browser passare a `https://remix.ethereum.org`.
1. Nella scheda **Home** selezionare **New file** (Nuovo file) sotto **File**.

    Assegnare al nuovo file il nome `simple.sol`.

    ![Crea file](./media/connect-metamask/create-file.png)

    Selezionare **OK**.
1. Nell'editor Remix, incollare il seguente codice di **contratto intelligente semplice**.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    Il **contratto semplice** dichiara una variabile di stato denominata **balance**. Sono disponibili due funzioni definite. La funzione **add** aggiunge un numero a **balance**. La funzione **get** restituisce il valore di **balance**.
1. Per compilare il contratto, selezionare prima di tutto il riquadro del compilatore Solidity e quindi **Compile simple.sol** (Compila simple.sol).

    ![Screenshot che illustra un contratto compilato.](./media/connect-metamask/compile.png)

1. Selezionare il riquadro **Deploy & Run** (Distribuisci ed esegui) e quindi impostare **Environment** (Ambiente) **Injected Web3** (Web3 inserito) per connettersi tramite MetaMask al membro della blockchain.

    ![Scheda Run (Esegui)](./media/connect-metamask/injected-web3.png)

1. Selezionare il contratto **simple** (semplice) e quindi **Deploy** (Distribuisci).

    ![Screenshot che illustra la distribuzione e l'esecuzione delle transazioni con un contratto e la distribuzione selezionati.](./media/connect-metamask/deploy.png)


1. Una notifica MetaMask avvisa che i fondi sono insufficienti per eseguire la transazione.

    Per una rete pubblica blockchain, sarebbe stato necessario Ether per pagare il costo della transazione. Poiché in questo caso si tratta di una rete privata in un consorzio, è possibile impostare il prezzo del gas su zero.

1.  Selezionare **Gas Fee > Edit > Advanced** (Tariffa gas > Modifica > Avanzata) e impostare il prezzo del gas in **Gas Price** su 0.

    ![Prezzo del gas](./media/connect-metamask/gas-price.png)

    Selezionare **Salva**.

1. Selezionare **Confirm** (Conferma) per distribuire il contratto intelligente nella blockchain.
1. Nella sezione **Deployed Contracts** (Contratti distribuiti), espandere il contratto **semplice**.

    ![Contratto distribuito](./media/connect-metamask/deployed-contract.png)

    Due azioni, **add** e **get**, vengono associate alle funzioni definite nel contratto.

1. Per eseguire una transazione **add** nella blockchain, immettere un numero da aggiungere e quindi selezionare **add**. Remix potrebbe visualizzare un messaggio di errore relativo alla stima del gas simile al seguente: "You are sending the transaction to a private blockchain that does not require gas" (Si sta inviando la transazione a una blockchain privata che non richiede dati relativi al gas). Selezionare **Send Transaction** (Invia transazione) per forzare la transazione.
1. Analogamente a quando è stato distribuito il contratto, una notifica MetaMask avvisa che i fondi sono insufficienti per eseguire la transazione.

    Poiché in questo caso si tratta di una rete privata in un consorzio, è possibile impostare il prezzo del gas su zero.

1. Selezionare **Gas Fee > Edit > Advanced** (Tariffa gas > Modifica > Avanzata), impostare il prezzo del gas in **Gas Price** su 0 e infine selezionare **Save** (Salva).
1. Selezionare **Confirm** (Conferma) per inviare la transazione alla blockchain.
1. Selezionare l'azione **get**. Si tratta di una chiamata per eseguire query sui dati del nodo. Non è necessaria una transazione.

Il riquadro di debug di Remix visualizza i dettagli sulle transazioni nella blockchain:

![Cronologia di debug](./media/connect-metamask/debug.png)

È possibile visualizzare la creazione del contratto **semplice**, la transazione per **simple.add** e la chiamata a **simple.get**.

Per visualizzare la cronologia delle transazioni in MetaMask, aprire l'estensione del browser MetaMask ed esaminare la sezione **Cronologia** per un log dei contratti e delle transazioni distribuite.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata l'estensione del browser MetaMask per connettersi a un nodo della transazione del servizio Azure Blockchain, distribuire un contratto intelligente e inviare una transazione alla blockchain. Provare l'esercitazione successiva per usare Azure Blockchain Development Kit per Ethereum e Truffle per creare, compilare, distribuire ed eseguire una funzione di contratto intelligente tramite una transazione.

> [!div class="nextstepaction"]
> [Creare, compilare e distribuire contratti intelligenti in Servizio Azure Blockchain](send-transaction.md)