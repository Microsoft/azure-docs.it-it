---
title: includere file
description: File di inclusione
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: f738daab7ddcf0403f546e7c9ffeaeccb66bc6b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355645"
---
## <a name="create-an-azure-signalr-service-instance"></a>Creare un'istanza del servizio Azure SignalR

L'applicazione si connette a un'istanza del servizio SignalR in Azure.

1. Selezionare il pulsante Nuovo nell'angolo superiore sinistro del portale di Azure. Nella nuova schermata digitare *Servizio SignalR* nella casella di ricerca e premere Invio.

    ![Screenshot che illustra i risultati della ricerca del servizio SignalR nel portale di Azure.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Selezionare **Servizio SignalR** dai risultati della ricerca, quindi selezionare **Crea**.

1. Immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome risorsa** | Nome globalmente univoco | Nome che identifica la nuova istanza del servizio SignalR. I caratteri validi sono `a-z`, `0-9` e `-`.  | 
    | **Sottoscrizione** | Sottoscrizione in uso | La sottoscrizione in cui verrà creata questa nuova istanza del servizio SignalR. | 
    | **[Gruppo di risorse](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome del nuovo gruppo di risorse in cui creare l'istanza del servizio SignalR. | 
    | **Posizione** | Stati Uniti occidentali | Scegliere [un'area](https://azure.microsoft.com/regions/) nelle vicinanze. |
    | **Piano tariffario** | Gratuito | Provare il servizio Azure SignalR gratuitamente. |
    | **Unità: conteggio** |  Non applicabile | Numero di unità specifica il numero di connessioni che può accettare l'istanza del servizio SignalR. È configurabili solo nel livello Standard. |
    | **Modalità di servizio** |  Senza server | Per l'uso con Funzioni di Azure o l'API REST. |

    ![Screenshot della scheda Informazioni di base di SignalR con valori.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selezionare **Crea** per iniziare la distribuzione dell'istanza del servizio SignalR.

1. Dopo che l'istanza è stata distribuita, aprirla nel portale e individuare la relativa pagina delle impostazioni. Modificare l'impostazione della modalità di servizio in *Serverless* solo se si usa il servizio Azure SignalR tramite l'associazione di Funzioni di Azure o l'API REST. In caso contrario, mantenere il valore *Classica* o *Predefinita*.