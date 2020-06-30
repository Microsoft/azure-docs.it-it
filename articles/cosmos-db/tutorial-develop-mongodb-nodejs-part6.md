---
title: Aggiungere funzioni CRUD un'app Angular con l'API di Azure Cosmos DB per MongoDB
description: Parte 6 della serie di esercitazioni sulla creazione di un'app MongoDB con Angular e Node in Azure Cosmos DB mediante le stesse API usate per MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: de9e7fd605a1d53d1078523c77cd33065c03ca85
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118968"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Aggiungere funzioni CRUD all'app

Questa esercitazione in più parti illustra come creare una nuova app scritta in Node.js con Express e Angular e quindi connetterla all'[account Cosmos configurato con l'API di Cosmos DB per MongoDB](mongodb-introduction.md). La Parte 6 dell'esercitazione è basata sulla [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) e illustra le attività seguenti:

> [!div class="checklist"]
> * Creare funzioni Post, Put e Delete per il servizio hero
> * Eseguire l'app

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa parte dell'esercitazione, assicurarsi di avere completato le procedure illustrate nella [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) dell'esercitazione.

> [!TIP]
> Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) in GitHub.

## <a name="add-a-post-function-to-the-hero-service"></a>Aggiungere una funzione Post al servizio hero

1. In Visual Studio Code aprire **routes.js** e **hero.service.js** affiancati facendo clic sul pulsante **Dividi editor**![Pulsante Dividi editor in Visual Studio](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png).

    Si noti che la riga 7 di routes.js chiama la funzione `getHeroes` nella riga 5 di **hero.service.js**.  È necessario creare questa stessa associazione per le funzioni post, put e delete. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png" alt-text="routes.js e hero.service.js in Visual Studio Code":::
    
    Per iniziare, sarà necessario codificare il servizio hero. 

2. Copiare il codice seguente in **hero.service.js** dopo la funzione `getHeroes` e prima di `module.exports`. Questo codice:  
   * Usa il modello hero per pubblicare un nuovo elemento hero.
   * Controlla le risposte per verificare se è presente un errore e restituisce un valore di stato 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. In **hero.service.js** aggiornare `module.exports` per includere la nuova funzione `postHero`. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. In **routes.js** aggiungere un router per la funzione `post` dopo il router `get`. Questo router pubblica un elemento hero alla volta. Questa struttura del file del router mostra chiaramente tutti gli endpoint API disponibili e lascia eseguire le attività vere e proprie al file **hero.service.js**.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Controllare che sia tutto a posto eseguendo l'app. In Visual Studio Code salvare tutte le modifiche, fare clic sul pulsante **Debug**![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png) a sinistra, quindi sul pulsante **Avvia debug**![Icona Avvia debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png).

6. Tornare ora al browser Internet e aprire la scheda Network (Rete) degli strumenti di sviluppo premendo F12 per la maggior parte dei computer. Passare a `http://localhost:3000` per controllare le chiamate eseguite in rete.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png" alt-text="Scheda Networking (Rete) in Chrome che mostra l'attività della rete":::

7. Aggiungere un nuovo elemento hero facendo clic sul pulsante **Add New Hero** (Aggiungi nuovo elemento hero). Immettere l'ID "999", il nome "Fred" e il testo "Hello", quindi fare clic su **Save** (Salva). La scheda Networking (Rete) indica che è stata inviata una richiesta POST per un nuovo elemento hero. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png" alt-text="Scheda Networking (Rete) in Chrome che mostra l'attività della rete per le funzioni Get e Post":::

    Tornare ora indietro e aggiungere le funzioni Put e Delete all'app.

## <a name="add-the-put-and-delete-functions"></a>Aggiungere le funzioni Put e Delete

1. In **routes.js** aggiungere i router `put` e `delete` dopo il router post.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Copiare il codice seguente in **hero.service.js** dopo la funzione `checkServerError`. Questo codice:
   * Crea le funzioni `put` e `delete`
   * Controlla se hero è stato trovato
   * Esegue la gestione degli errori 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. In **hero.service.js** esportare i nuovi moduli:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Ora che il codice è stato aggiornato, fare clic sul pulsante **Riavvia**![Pulsante Riavvia in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png) in Visual Studio Code.

5. Aggiornare la pagina nel browser Internet e fare clic sul pulsante **Add New Hero** (Aggiungi nuovo elemento hero). Aggiungere un nuovo elemento hero con ID "9", nome "Starlord" e testo "Hi". Fare clic sul pulsante **Save** (Salva) per salvare il nuovo elemento hero.

6. Selezionare ora l'elemento hero **Starlord** e cambiare il testo da "Hi" a "Bye" quindi fare clic sul pulsante **Save** (Salva). 

    È ora possibile selezionare l'ID nella scheda Network (Rete) per visualizzare il payload. Il payload mostra che il testo ora è impostato su "Bye".

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png" alt-text="App Heroes e scheda Networking (Rete) indicante il payload"::: 

    È anche possibile eliminare uno degli elementi hero nell'interfaccia utente e verificare il tempo necessario per completare l'operazione di eliminazione. Per provare, fare clic sul pulsante "Delete" (Elimina) per l'elemento hero denominato "Fred".

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/times.png" alt-text="App Heroes e scheda Networking (Rete) indicante il tempo necessario per il completamento delle funzioni"::: 

    Se si aggiorna la pagina, la scheda Network (Rete) indica il tempo necessario per ottenere gli elementi hero. Anche se i tempi sono rapidi, molto dipende dalla posizione geografica dei dati e dalla possibilità di eseguirne la replica geografica in un'area vicina agli utenti. Altre informazioni sulla replica geografica saranno disponibili nella prossima esercitazione, che verrà presto rilasciata.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Sono state aggiunte le funzioni Post, Put e Delete all'app 

Ricontrollare a breve se sono disponibili altri video in questa serie di esercitazioni.

