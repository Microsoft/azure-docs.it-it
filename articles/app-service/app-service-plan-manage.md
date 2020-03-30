---
title: Gestire il piano di servizio appManage App Service plan
description: Informazioni su come eseguire diverse attività per gestire un piano di servizio app, ad esempio creare, spostare, ridimensionare ed eliminare.
keywords: servizio app, servizio app di azure, ridimensionare, piano di servizio app, modificare, creare, gestire, gestione
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 68cabbd00dd0b738590109cc39d8df82f5b7362d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280755"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gestire un piano di servizio app in Azure

Un [piano di servizio app di Azure](overview-hosting-plans.md) fornisce le risorse che un'app del servizio app deve eseguire. Questa guida illustra come gestire un piano di servizio app.

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

> [!TIP]
> Se è disponibile l'opzione Ambiente del servizio app di Azure, vedere [Creare un'app Web in un ambiente del servizio app](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

È possibile creare un piano di servizio app vuoto, o creare un piano come parte della creazione di app.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa**.

   ![Creare una risorsa nel portale di Azure.Create a resource in the Azure portal.][createResource] 

1. Selezionare **Nuova** > **app Web** o un altro tipo di app di servizio app.

   ![Creare un'app nel portale di Azure.][createWebApp] 

2. Configurare la sezione **Dettagli istanza** prima di configurare il piano di servizio app. Impostazioni come **Pubblica** e **sistemi operativi** possono modificare i piani tariffari disponibili per il piano di servizio app. **L'area** determina dove viene creato il piano di servizio app. 
   
3. Nella sezione **Piano di servizio app** selezionare un piano esistente oppure creare un piano selezionando Crea **nuovo**.

   ![Creare un piano di servizio app.][createASP] 

4. Quando si crea un piano, è possibile selezionare il piano tariffario del nuovo piano. In **Sku e size**selezionare Modifica **dimensione** per modificare il piano tariffario. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Spostare un'app in un altro piano di servizio app

È possibile spostare un'app in un altro piano di servizio app, purché il piano di origine e il piano di destinazione si trovino _nello stesso gruppo di risorse e nella stessa area geografica_.

> [!NOTE]
> Azure distribuisce ogni nuovo piano di servizio app in un'unità di distribuzione chiamata internamente "spazio Web". Ogni area geografica può avere molti spazi Web, ma l'app può essere spostata solo tra piani creati nello stesso spazio Web. Un ambiente del servizio app è uno spazio Web isolato; le app, quindi, possono essere spostate tra piani dello stesso ambiente del servizio app, ma non tra piani di ambienti del servizio app diversi.
>
> Non è possibile specificare lo spazio Web desiderato quando si crea un piano, ma è possibile fare in modo che un piano venga creato nello stesso spazio Web di un piano esistente. In breve, tutti i piani creati con la stessa combinazione di gruppo di risorse e area geografica vengono distribuiti nello stesso spazio Web. Se, ad esempio, è stato creato un piano nel gruppo di risorse A e nell'area geografica B, qualsiasi piano successivamente creato nel gruppo di risorse A e nell'area geografica B verrà distribuito nello stesso spazio Web. Un piano non può essere spostato in uno spazio Web dopo essere stato creato, pertanto non è possibile spostare un piano nello "stesso spazio Web" di un altro spostandolo in un altro gruppo di risorse.
> 

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **Servizi app** e selezionare l'app da spostare.

2. Nel menu a sinistra selezionare **Cambia piano di servizio app**.

3. Nell'elenco a discesa **Piano di servizio app** selezionare un piano esistente in cui spostare l'app. L'elenco a discesa mostra solo i piani che si trovano nello stesso gruppo di risorse e nello stesso area geografica del piano di servizio app corrente. Se tale piano non esiste, consente di creare un piano per impostazione predefinita. È inoltre possibile creare manualmente un nuovo piano selezionando **Crea nuovo**.

4. Se si crea un piano, è possibile selezionare il piano tariffario del nuovo piano. In **Livello di prezzo**selezionare il livello esistente per modificarlo. 
   
   > [!IMPORTANT]
   > Se si sposta un'app da un piano di livello superiore a un piano di livello inferiore, ad esempio da **D1** a **F1,** l'app potrebbe perdere determinate funzionalità nel piano di destinazione. Ad esempio, se l'app usa certificati SSL, potresti visualizzare questo messaggio di errore:For example, if your app uses SSL certificates, you might see this error message:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Al termine selezionare **OK**.
   
   ![Pannello di selezione Piano di servizio app.][change] 

## <a name="move-an-app-to-a-different-region"></a>Spostare un'app in un'area diversa

L'area in cui viene eseguita l'app è l'area del piano di servizio app. Tuttavia, non è possibile modificare l'area di un piano servizio app. Se si vuole eseguire l'app in un'area diversa, un'alternativa consiste nel clonarla. Con la clonazione si crea una copia dell'app in un piano di servizio app nuovo o esistente di qualsiasi area.

**Clona app** è disponibile nella sezione **Strumenti di sviluppo** del menu.

> [!IMPORTANT]
> La clonazione presenta alcune limitazioni. Per altre informazioni vedere [Clonazione di app del servizio app di Azure con PowerShell](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Scalare un piano di servizio app

Per passare a un piano tariffario superiore per un piano di servizio app, vedere [Aumentare le prestazioni di un'app in Azure](manage-scale-up.md).

Per scalare il numero di istanze di un'app, vedere [Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminare un piano di servizio app

Per evitare costi imprevisti, quando si elimina l'ultima app in un piano di servizio app, servizio app elimina anche il piano per impostazione predefinita. Se invece si sceglie di mantenere il piano, si consiglia di cambiare il suo livello in **Gratuito**, in modo che non venga addebitato alcun costo.

> [!IMPORTANT]
> Anche i piani di servizio app a cui non sono associate app vengono addebitati, in quanto continuano a riservare le istanze VM configurate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aumentare le prestazioni di un'app in Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
