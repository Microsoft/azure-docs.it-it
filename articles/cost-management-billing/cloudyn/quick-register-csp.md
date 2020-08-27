---
title: Eseguire la registrazione in Gestione costi di Azure usando Cloudyn in Azure
description: Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 518d4d0f672ae0e83748dbf7dc0a87097ca7bbf7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688165"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Eseguire la registrazione al programma per i partner CSP e visualizzare i dati sui costi

Come partner CSP è possibile eseguire la registrazione in Cloudyn. La registrazione consente di accedere al portale di Cloudyn. Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale. Descrive anche come iniziare a visualizzare da subito i dati sui costi.


[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

> [!NOTE]
>
> Solo i partner diretti CSP e i provider indiretti CSP possono completare la registrazione a Cloudyn.
>
> Per l'autenticazione e l'accesso ai dati è necessario configurare la Partner Center API. Per eseguire il provisioning dell'accesso API, è necessario un account amministratore globale del Partner Center.
> Per altre informazioni, vedere [Connect to the Partner Center API](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx) (Connettersi all'API del Centro per i partner).
>
> L'accesso a Cloudyn può essere reso disponibile per i rivenditori indiretti CSP dopo che il provider indiretto CSP si è registrato con Cloudyn. I rivenditori indiretti CSP possono quindi consentire l'accesso Cloudyn ai clienti e alle sottoscrizioni di Azure.
>
>Cloudy è conforme al modello di applicazione sicura Microsoft. Per altre informazioni, vedere [Abilitazione del framework del modello di applicazione sicura](/partner-center/develop/enable-secure-app-model).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="register-with-cloudyn"></a>Registrarsi in Cloudyn

1. Nel portale di Azure fare clic su **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. In **Panoramica** fare clic su **Cloudyn**  
    ![Pagina di Cloudyn mostrata nel portale di Azure](./media/quick-register-csp/cost-mgt-billing-service.png)
3. Nella pagina **Cloudyn** fare clic su **Vai a Cloudyn** per aprire la pagina di registrazione di Cloudyn in una nuova finestra.
4. Nella pagina di registrazione di valutazione del portale di Cloudyn digitare il nome della società, selezionare **Microsoft CSP Partner Program Administrator** (Amministratore del programma per i partner CSP di Microsoft) e fare clic su **Next** (Avanti).  
5. Immettere un **ID applicazione**, un **ID commercio**, una **chiave privata dell'applicazione**e selezionare il **piano tariffario predefinito**. Se le informazioni necessarie non sono disponibili, accedere al portale Centro per i partner all'indirizzo [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) usando l'account amministratore principale e seguire questa procedura:
   1. Passare a **Dashboard**, fare clic sul simbolo **Impostazioni**, fare clic su **Partner settings** (Impostazioni partner) e quindi su **Gestione app**.
   2. Se è già stata creata un'app Web, ignorare questo passaggio. In caso contrario, fare clic su **Add new web app** (Aggiungi nuova app Web) nella sezione **App Web**.
   3. Copiare il GUID dell'**ID app** dall'applicazione Web.
   4. Copiare il GUID dell'**ID Commerce** dall'applicazione Web.
   5. Selezionare il periodo di validità delle chiavi, uno o due anni in base alle necessità. Selezionare **Aggiungi chiave**, copiare e salvare il valore della chiave privata.  
    ![Dashboard del partner in cui vengono copiate informazioni sulle credenziali](./media/quick-register-csp/csp-partner-center.png)
   6. Tornare alla pagina di registrazione di Cloudyn e incollare le informazioni.  
      ![Incollare le informazioni sulle credenziali nella pagina di registrazione di Cloudyn](./media/quick-register-csp/csp-reg.png)
6. Accettare le condizioni per l'utilizzo e convalidare le informazioni. Fare clic su **Avanti** per autorizzare Cloudyn a raccogliere i dati sulle risorse di Azure. Dalla sottoscrizione vengono raccolti dati riguardanti l'uso, le prestazioni, la fatturazione e dati dei tag.  
7. In **Invite other stakeholder** (Invita altri stakeholder) è possibile aggiungere utenti digitando i loro indirizzi di posta elettronica. Al termine, fare clic su **Avanti**. I dati di fatturazione saranno aggiunti a Cloudyn in circa due ore.
8. Fare clic su **Go to Cloudyn** (Passa a Cloudyn) per aprire il portale di Cloudyn. Nella pagina **Cloud Accounts Management** (Gestione account cloud), saranno visualizzate le informazioni sugli account CSP registrati.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurare l'accesso CSP indiretto in Cloudyn

Per impostazione predefinita, l'API del Centro per i partner è accessibile solo ai partner CSP diretti. Un provider CSP diretto può tuttavia configurare l'accesso per i propri clienti o partner CSP indiretti usando i gruppi di entità in Cloudyn.

Per consentire l'accesso ai clienti o ai partner CSP indiretti, eseguire i passaggi descritti in [Creare una registrazione con Cloudyn](#register-with-cloudyn) per configurare una registrazione di valutazione. Completare quindi i passaggi seguenti per segmentare i dati CSP indiretti usando i gruppi di entità Cloudyn. Assegnare poi le autorizzazioni utente appropriate ai gruppi di entità.

1. Creare un gruppo di entità con le informazioni disponibili in [Creare entità](tutorial-user-access.md#create-and-manage-entities).
2. Seguire i passaggi del video [Assigning subscriptions to Cost Entities](https://www.youtube.com/watch?v=d9uTWSdoQYo) (Assegnazione di sottoscrizioni alle entità di costo). Associare l'account del cliente CSP indiretto e le relative sottoscrizioni di Azure all'entità creata in precedenza.
3. Seguire i passaggi in [Creare un utente con accesso amministrativo](tutorial-user-access.md#create-a-user-with-admin-access) per creare un account utente con accesso amministrativo. Assicurarsi quindi che l'account utente abbia l'accesso amministrativo alle entità specifiche create in precedenza per l'account indiretto.

I partner CSP indiretti accedono al portale di Cloudyn usando gli account creati per loro.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state usate le informazioni CSP per eseguire la registrazione in Cloudyn. È stato anche eseguito l'accesso al portale di Cloudyn ed è stata avviata la visualizzazione dei dati sui costi. Per altre informazioni su Cloudyn, passare all'esercitazione su Cloudyn.

> [!div class="nextstepaction"]
> [Esaminare l'utilizzo e i costi](tutorial-review-usage.md)
