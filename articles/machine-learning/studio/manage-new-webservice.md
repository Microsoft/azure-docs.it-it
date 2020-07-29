---
title: 'ML Studio (classico): gestire i servizi Web-Azure'
description: Gestisci i tuoi Machine Learning i servizi Web nuovi e classici usando il portale dei servizi Web di Microsoft Azure Machine Learning. Poiché i servizi Web classici e nuovi sono basati su tecnologie diverse, sono disponibili funzionalità di gestione leggermente diverse.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 246041d1a5f4826344c03b889c26dc381cd6db29
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318505"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning Studio (classico)

**SI APPLICA A:** ![no](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-azure-ml.md) ![sì](../../../includes/media/aml-applies-to-skus/yes.png)Azure Machine Learning Studio (versione classica) 


Il portale dei servizi Web di Microsoft Azure Machine Learning consente di gestire i servizi Web classici e nuovi di Machine Learning. Poiché i servizi Web classici e nuovi sono basati su tecnologie diverse, sono disponibili funzionalità di gestione leggermente diverse.

Nel portale dei servizi Web di Azure Machine Learning è possibile:

* Monitorare la modalità d'uso del servizio Web.
* Configurare la descrizione, aggiornare le chiavi per il servizio Web (solo servizi nuovi), aggiornare la chiave dell'account di archiviazione (solo servizi nuovi), abilitare la registrazione e abilitare o disabilitare i dati di esempio.
* Eliminare il servizio Web.
* Creare, eliminare o aggiornare i piani di fatturazione (solo servizi nuovi).
* Aggiungere ed eliminare gli endpoint (solo servizi classici)

>[!NOTE]
>È anche possibile gestire i servizi Web classici in [Machine Learning Studio (classico)](https://studio.azureml.net) nella scheda **servizi Web** .

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Autorizzazioni per gestire i nuovi servizi Web basati su Resource Manager

I nuovi servizi Web vengono distribuiti come risorse di Azure. Di conseguenza, è necessario disporre delle autorizzazioni corrette per distribuire e gestire i nuovi servizi Web.  Per distribuire o gestire nuovi servizi Web è necessario che all'utente sia assegnato un ruolo di collaboratore o di amministratore nella sottoscrizione in cui viene distribuito il servizio Web. Se si invita un altro utente in un'area di lavoro di Machine Learning, affinché possa distribuire o gestire servizi Web è prima necessario assegnargli un ruolo di collaboratore o di amministratore nella sottoscrizione. 

Se l'utente non dispone delle autorizzazioni corrette per accedere alle risorse nel portale dei servizi Web di Azure Machine Learning, riceverà l'errore seguente quando cercherà di distribuire un servizio Web:

*Distribuzione del servizio Web non riuscita. Questo account non dispone di diritti di accesso sufficienti per la sottoscrizione di Azure che contiene l'area di lavoro. Per distribuire un servizio Web in Azure, è necessario che lo stesso account sia invitato all'area di lavoro e che venga concesso l'accesso alla sottoscrizione di Azure che contiene l'area di lavoro.*

Per ulteriori informazioni sulla creazione di un'area di lavoro, vedere [creare e condividere un'area di lavoro Azure Machine Learning Studio (classica)](create-workspace.md).

Per altre informazioni sull'impostazione delle autorizzazioni di accesso, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Gestire i nuovi servizi Web
Per gestire i nuovi servizi Web:

1. Accedere al [portale dei servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) usando l'account di Microsoft Azure associato alla sottoscrizione di Azure.
2. Nel menu fare clic su **Servizi Web**.

Verrà visualizzato un elenco di servizi Web distribuiti per la sottoscrizione. 

Per gestire un servizio Web, fare clic su Web Services (Servizi Web). Nella pagina Servizi Web è possibile:

* Fare clic sul servizio Web per gestirlo.
* Fare clic sul piano di fatturazione per il servizio Web per aggiornarlo.
* Eliminare un servizio Web.
* Copiare un servizio Web e distribuirlo in un'altra area.

Quando si fa clic su un servizio Web, viene visualizzata la pagina di avvio rapido del servizio Web. Questa pagina contiene due opzioni di menu che consentono di gestire il servizio Web:

* **Dashboard** : consente di visualizzare l'utilizzo del servizio Web.
* **CONFIGURE** (CONFIGURA): consente di aggiungere testo descrittivo, aggiornare la chiave dell'account di archiviazione associato al servizio Web e abilitare o disabilitare i dati di esempio.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitoraggio della modalità d'uso del servizio Web
Fare clic sulla scheda **DASHBOARD** .

Nel dashboard è possibile visualizzare l'utilizzo complessivo del servizio Web in un periodo di tempo. È possibile selezionare il periodo da visualizzare dal menu a discesa Periodo in alto a destra dei grafici di utilizzo. Il dashboard visualizza le informazioni seguenti:

* **Requests Over Time** (Richieste nel tempo) visualizza un grafico con il numero di richieste nel periodo di tempo selezionato. Può aiutare a identificare se si verificano picchi di utilizzo.
* **Request-Response Requests** (Richieste richiesta-risposta) visualizza il numero totale di chiamate di richiesta-risposta ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
* **Average Request-Response Compute Time** (Tempo medio di calcolo richiesta-risposta) visualizza una media del tempo necessario per eseguire le richieste ricevute.
* **Batch Requests** (Richieste batch) visualizza il numero totale di richieste batch ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
* **Average Job Latency** (Latenza processo media) visualizza una media del tempo necessario per eseguire le richieste ricevute.
* **Errors** (Errori) visualizza il numero complessivo di errori che si sono verificati nelle chiamate al servizio Web.
* **Services Costs** (Costi servizi) visualizza le spese per il piano di fatturazione associato al servizio.

### <a name="configuring-the-web-service"></a>Configurazione del servizio Web
Fare clic sull'opzione di menu **CONFIGURA** .

È possibile aggiornare le proprietà seguenti:

* **Description** (Descrizione) consente di immettere una descrizione per il servizio Web.
* **Titolo** consente di immettere un titolo per il servizio Web
* **Chiavi** consente di ruotare le chiavi API primarie e secondarie.
* La **chiave dell'account di archiviazione** consente di aggiornare la chiave per l'account di archiviazione associato alle modifiche del servizio Web. 
* **Enable Sample data** (Abilita dati di esempio) consente di fornire dati di esempio che è possibile usare per testare il servizio di richiesta-risposta. Se il servizio Web è stato creato in Machine Learning Studio (versione classica), i dati di esempio vengono ricavati dai dati usati per il training del modello. Se il servizio è stato creato a livello di codice, i dati vengono ricavati dai dati di esempio forniti come parte del pacchetto JSON.

### <a name="managing-billing-plans"></a>Gestione dei piani di fatturazione
Fare clic sull'opzione di menu **Piani** della pagina Avvio rapido dei servizi Web. È anche possibile fare clic sul piano associato al servizio Web specifico per gestire tale piano.

* **Nuovo** consente di creare un nuovo piano.
* **Add/Remove Plan instance** (Aggiungi/Rimuovi istanza di piano) consente di aumentare un piano esistente per aggiungere capacità.
* **Upgrade/DownGrade** (Esegui aggiornamento/downgrade) consente di aumentare un piano esistente per aggiungere capacità.
* **Elimina** consente di eliminare un piano.

Fare clic su un piano per visualizzare il relativo dashboard. Il dashboard indica l'uso dello snapshot o del piano in un periodo di tempo selezionato. Per selezionare il periodo di tempo da visualizzare, fare clic sull'elenco a discesa **Period** (Periodo) nella parte superiore destra del dashboard. 

Il dashboard del piano contiene le informazioni seguenti:

* **Descrizione piano** visualizza le informazioni sui costi e le capacità associate al piano.
* **Plan Usage** (Uso piano) visualizza il numero di transazioni e di ore di calcolo addebitate in base al piano.
* **Servizi Web** Visualizza il numero di servizi Web che utilizzano questo piano.
* **Top Web Service By Calls** (Primi servizi Web per chiamate) visualizza i primi quattro servizi Web che stanno effettuando chiamate per cui viene applicato un addebito in base al piano.
* **Top Web Services by Compute Hrs** (Primi servizi Web per ore di calcolo) visualizza i primi quattro servizi Web che stanno usando le risorse di calcolo per cui viene applicato un addebito in base al piano.

## <a name="manage-classic-web-services"></a>Gestire i servizi Web classici
> [!NOTE]
> Le procedure di questa sezione si riferiscono alla gestione dei servizi Web classici nel portale dei servizi Web di Azure Machine Learning. Per informazioni sulla gestione dei servizi Web classici tramite il Machine Learning Studio (classico) e il portale di Azure, vedere [gestire un'area di lavoro di Azure Machine Learning Studio (classica)](manage-workspace.md).
> 
> 

Per gestire i servizi Web classici:

1. Accedere al [portale dei servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) usando l'account di Microsoft Azure associato alla sottoscrizione di Azure.
2. Fare clic su **Classic Web Services** (Servizi Web classici).

Per gestire un servizio Web classico, fare clic su **Classic Web Services**(Servizi Web classici). Nella pagina relativa ai servizi Web classici è possibile:

* Fare clic sul servizio Web per visualizzare gli endpoint associati.
* Eliminare un servizio Web.

Quando si gestisce un servizio Web classico, si gestisce ognuno degli endpoint separatamente. Quando si fa clic su un servizio Web nella pagina dei servizi Web, viene aperto l'elenco degli endpoint associati al servizio. 

Nella pagina relativa agli endpoint dei servizi Web classici è possibile aggiungere ed eliminare gli endpoint del servizio. Per altre informazioni sull'aggiunta di endpoint, vedere [Creazione di endpoint](create-endpoint.md).

Fare clic su uno degli endpoint per aprire la pagina di avvio rapido del servizio Web. Questa pagina contiene due opzioni di menu che consentono di gestire il servizio Web:

* **Dashboard** : consente di visualizzare l'utilizzo del servizio Web.
* **Configura** : consente di aggiungere testo descrittivo, attivare e disattivare la registrazione degli errori, aggiornare la chiave per l'account di archiviazione associato al servizio Web e abilitare e disabilitare i dati di esempio.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitoraggio della modalità d'uso del servizio Web
Fare clic sulla scheda **DASHBOARD** .

Nel dashboard è possibile visualizzare l'utilizzo complessivo del servizio Web in un periodo di tempo. È possibile selezionare il periodo da visualizzare dal menu a discesa Periodo in alto a destra dei grafici di utilizzo. Il dashboard visualizza le informazioni seguenti:

* **Requests Over Time** (Richieste nel tempo) visualizza un grafico con il numero di richieste nel periodo di tempo selezionato. Può aiutare a identificare se si verificano picchi di utilizzo.
* **Request-Response Requests** (Richieste richiesta-risposta) visualizza il numero totale di chiamate di richiesta-risposta ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
* **Average Request-Response Compute Time** (Tempo medio di calcolo richiesta-risposta) visualizza una media del tempo necessario per eseguire le richieste ricevute.
* **Batch Requests** (Richieste batch) visualizza il numero totale di richieste batch ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
* **Average Job Latency** (Latenza processo media) visualizza una media del tempo necessario per eseguire le richieste ricevute.
* **Errors** (Errori) visualizza il numero complessivo di errori che si sono verificati nelle chiamate al servizio Web.
* **Services Costs** (Costi servizi) visualizza le spese per il piano di fatturazione associato al servizio.

### <a name="configuring-the-web-service"></a>Configurazione del servizio Web
Fare clic sull'opzione di menu **CONFIGURA** .

È possibile aggiornare le proprietà seguenti:

* **Description** (Descrizione) consente di immettere una descrizione per il servizio Web. La descrizione è un campo obbligatorio.
* **Logging** (Registrazione) consente di abilitare o disabilitare la registrazione nell'endpoint. Per altre informazioni sulla registrazione, vedere abilitare [la registrazione per i servizi web Machine Learning](web-services-logging.md).
* **Enable Sample data** (Abilita dati di esempio) consente di fornire dati di esempio che è possibile usare per testare il servizio di richiesta-risposta. Se il servizio Web è stato creato in Machine Learning Studio (versione classica), i dati di esempio vengono ricavati dai dati usati per il training del modello. Se il servizio è stato creato a livello di codice, i dati vengono ricavati dai dati di esempio forniti come parte del pacchetto JSON.


