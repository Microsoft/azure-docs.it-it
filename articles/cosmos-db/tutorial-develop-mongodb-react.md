---
title: Esercitazione su MongoDB, React e Node.js per Azure
description: Informazioni su come creare un'app MongoDB con React e Node.js in Azure Cosmos DB mediante le stesse API usate per MongoDB con questa serie di esercitazioni basate su video.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jopapa
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 06c309de5054887a64e61d61345190c776835709
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476587"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Creare un'app MongoDB con React e Azure Cosmos DB  

Questa esercitazione video in più parti illustra come creare un'app di verifica hero con un front-end React. L'app usa Node ed Express per il server, si connette al database Cosmos con configurato con l'[API di Azure Cosmos DB per MongoDB](mongodb-introduction.md) e quindi connette il front-end React alla parte server dell'app. L'esercitazione illustra anche come puntare e fare clic per eseguire il ridimensionamento di Cosmos DB nel portale di Azure e come distribuire l'app in Internet in modo che ognuna possa tenere traccia degli elementi hero preferiti. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) supporta la compatibilità con il protocollo di collegamento con MongoDB, consentendo ai client di usare Azure Cosmos DB invece di MongoDB.  

Questa esercitazione in più parti illustra le attività seguenti:

> [!div class="checklist"]
> * Introduzione
> * Configurare il progetto
> * Compilare l'interfaccia utente con React
> * Creare un account Azure Cosmos DB mediante il portale di Azure
> * Usare Mongoose per connettersi ad Azure Cosmos DB
> * Aggiungere le operazioni React, Create, Update e Delete all'app

Per compilare questa stessa app con Angular, vedere la [serie di video di esercitazione su Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Prerequisites
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Progetto finito
Ottenere l'applicazione completata [da GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Introduzione 

In questo video Burke Holland presenta Azure Cosmos DB e illustra l'app creata in questa serie di video. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Configurazione del progetto

Questo video illustra come configurare Express e React nello stesso progetto. Burke illustra quindi una procedura dettagliata sul codice nel progetto.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Creare l'interfaccia utente

Questo video illustra come creare l'interfaccia utente dell'applicazione con React. 

> [!NOTE]
> Il foglio di stile CSS a cui si fa riferimento in questo video è disponibile nel [repository di GitHub react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Connettersi ad Azure Cosmos DB

Questo video illustra come creare un account Azure Cosmos DB nel portale di Azure, installare i pacchetti MongoDB e Mongoose e quindi connettere l'app al nuovo account creato usando la stringa di connessione Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Leggere e creare hero nell'app

Questo video illustra come leggere e creare hero nel database di Cosmos e come testare tali metodi usando Postman e l'interfaccia utente di React. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Eliminare e aggiornare hero nell'app

Questo video illustra come eliminare e aggiornare hero dall'app e visualizzare gli aggiornamenti nell'interfaccia utente. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Completare l'app

Questo video illustra come completare l'app e finire di collegare l'interfaccia utente all'API back-end. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di continuare a usare questa app, seguire questa procedura per eliminare tutte le risorse create da questa esercitazione nel portale di Azure. 

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina** , digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina** .

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un'app con React, Node, Express e Azure Cosmos DB 
> * Creare un account Azure Cosmos DB
> * Connettere l'app all'account Azure Cosmos DB
> * Testare l'app usando Postman
> * Eseguire l'applicazione e aggiungere hero al database

È possibile passare all'esercitazione successiva per ottenere informazioni su come importare i dati di MongoDB in Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importare i dati di MongoDB in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)
