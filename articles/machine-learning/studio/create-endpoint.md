---
title: 'ML Studio (classico): creare endpoint di servizio Web-Azure'
description: Creazione di endpoint del servizio Web in Azure Machine Learning Studio (versione classica). Ogni endpoint nel servizio Web viene indirizzato, limitato e gestito in modo indipendente.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: ac5fa2a4eb534a4cc6e7bf9542bfb250f216401c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282501"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Creare endpoint per i servizi Web di Azure Machine Learning Studio distribuiti (classico)

**SI APPLICA A:** ![no](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-azure-ml.md) ![sì](../../../includes/media/aml-applies-to-skus/yes.png)Azure Machine Learning Studio (versione classica) 


> [!NOTE]
> Questo argomento descrive le tecniche applicabili a un servizio Web Machine Learning **classico** .

Dopo avere distribuito un servizio Web, viene creato un endpoint predefinito per tale servizio. L'endpoint predefinito può essere chiamato usando la chiave API. È possibile aggiungere altri endpoint con le rispettive chiavi dal portale dei servizi Web.
Ogni endpoint nel servizio Web viene indirizzato, limitato e gestito in modo indipendente. Ogni endpoint corrisponde a un URL univoco con una chiave di autorizzazione che è possibile distribuire ai clienti.

## <a name="add-endpoints-to-a-web-service"></a>Aggiungere endpoint a un servizio Web

È possibile aggiungere un endpoint a un servizio Web usando il portale dei servizi Web di Azure Machine Learning. Dopo aver creato l'endpoint, è possibile utilizzarlo tramite le API sincrone, le API batch e i fogli di lavoro di Excel.

> [!NOTE]
> Se sono stati aggiunti altri endpoint al servizio Web, non è possibile eliminare l'endpoint predefinito.

1. In Machine Learning Studio (classico), nella colonna di spostamento a sinistra fare clic su servizi Web.
2. Nella parte inferiore del dashboard del servizio Web fare clic su **Gestisci endpoint**. Nel portale dei servizi Web di Azure Machine Learning viene aperta la pagina dedicata agli endpoint del servizio Web.
3. Fare clic su **Nuovo**.
4. Immettere un nome e una descrizione per il nuovo endpoint. I nomi degli endpoint devono contenere al massimo 24 caratteri alfanumerici (con lettere minuscole). Selezionare il livello di registrazione e indicare se i dati di esempio sono abilitati. Per altre informazioni sulla registrazione, vedere [abilitare la registrazione per i servizi web Machine Learning](web-services-logging.md).

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a> Ridimensionare un servizio Web tramite l'aggiunta di altri endpoint

Per impostazione predefinita, ogni servizio Web pubblicato è configurato per supportare 20 richieste simultanee fino a 200 richieste simultanee. Azure Machine Learning Studio (versione classica) ottimizza automaticamente l'impostazione per fornire le prestazioni migliori per il servizio Web e il valore del portale viene ignorato.

Se si prevede di chiamare l'API con un carico superiore a 200 chiamate simultanee, il numero massimo supportato, è consigliabile creare più endpoint nello stesso servizio Web. È quindi possibile distribuire casualmente il carico tra tutti gli endpoint.

Il ridimensionamento di un servizio Web è un'attività comune. Alcuni motivi per il ridimensionamento consistono nella necessità di supportare più di 200 richieste simultanee, aumentare la disponibilità tramite più endpoint o fornire endpoint separati per il servizio Web. È possibile aumentare la scala aggiungendo endpoint aggiuntivi per lo stesso servizio Web tramite il portale del [servizio web Azure Machine Learning](https://services.azureml.net/) .

Tenere presente che l'uso di un numero elevato di chiamate simultanee può essere dannoso se non si chiama l'API con una frequenza altrettanto elevata. Se si inserisce un carico relativamente basso in un'API configurata per un carico elevato, è possibile che si verifichino timeout sporadici e/o picchi della latenza.

Le API sincrone in genere vengono usate nelle situazioni in cui si desidera una bassa latenza. La latenza qui indica il tempo impiegato dall'API per completare una richiesta e non tiene in considerazione i ritardi di rete. Si supponga di avere un'API con una latenza di 50 ms. Per utilizzare appieno la capacità disponibile con il livello di limitazione Elevato e il numero massimo di chiamate simultanee pari a 20, è necessario chiamare l'API 20 * 1000 / 50 = 400 volte al secondo. Estendendo ulteriormente questa capacità, un numero massimo di 200 chiamate simultanee permetterà di chiamare l'API 4000 volte al secondo, supponendo una latenza di 50 ms.

## <a name="next-steps"></a>Passaggi successivi

[Come utilizzare un servizio web Azure Machine Learning](consume-web-services.md).
