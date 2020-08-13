---
title: Uso del servizio Active Directory Connected (Visual Studio)
description: Aggiungere un'istanza di Azure Active Directory usando la finestra di dialogo Aggiungi Servizi connessi di Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: a1ba7db72743ac122a697bf271e783ec64e041e8
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165482"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Aggiungere un Azure Active Directory usando Servizi connessi in Visual Studio

Utilizzando Azure Active Directory (Azure AD), è possibile supportare l'accesso Single Sign-on (SSO) per le applicazioni Web ASP.NET MVC o Active Directory l'autenticazione nei servizi API Web. Con l'autenticazione di Azure AD, gli utenti possono usare gli account di Azure Active Directory per connettersi alle applicazioni Web. I vantaggi di Autenticazione di Azure AD con l'API Web includono la sicurezza dei dati migliorata quando si espone un'API da un'applicazione Web. Con Azure AD, non è necessario gestire un sistema di autenticazione distinto con la gestione di account e utente.

Questo articolo e gli articoli correlati forniscono i dettagli dell'uso del servizio connesso di Visual Studio per Active Directory. La funzionalità è disponibile in Visual Studio 2015 e versioni successive.

Al momento, il servizio connesso di Active Directory non supporta applicazioni ASP.NET Core.

## <a name="prerequisites"></a>Prerequisiti

- Account di Azure: se non si ha un account di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i vantaggi della sottoscrizione di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** o versione successiva. [Scarica subito Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Connettersi ad Azure Active Directory usando la finestra di dialogo dei servizi connessi

1. In Visual Studio creare o aprire un progetto ASP.NET MVC o un progetto API Web ASP.NET. È possibile usare i modelli MVC, API Web, Applicazione a pagina singola, App per le API di Azure, App per dispositivi mobili di Azure e Servizio mobile di Azure.

1. Selezionare il comando di menu **Progetto > Aggiungi servizio connesso** o fare doppio clic sul nodo **Servizi connessi** sotto il progetto in Esplora soluzioni.

1. Nella pagina di **Servizi connessi** selezionare **Autenticazione con Azure Active Directory**.

    ![Pagina di Servizi connessi](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Nella pagina **Introduzione** selezionare **Avanti**. Se si verificano errori in questa pagina, vedere [Diagnosi degli errori con il servizio connesso di Azure Active Directory](vs-active-directory-error.md).

    ![Pagina Introduzione](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Nella pagina **Single-Sign On** selezionare un dominio nell’elenco a discesa **Dominio**. L'elenco contiene tutti i domini accessibili dagli account elencati nella finestra di dialogo Impostazioni account di Visual Studio (**File > impostazioni account...**). In alternativa, è possibile immettere un nome di dominio se non si trova quello che si sta cercando, ad esempio `mydomain.onmicrosoft.com` . È possibile scegliere l'opzione per creare una nuova app di Azure Active Directory o usare le impostazioni di un'app di Azure Active Directory esistente. Al termine scegliere **Avanti**.

    ![Pagina Single Sign-On](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Nella pagina **Accesso alla directory** selezionare l'opzione **Lettura dati directory**, se lo si desidera. In genere gli sviluppatori includono questa opzione.

    ![Pagina di accesso alla directory](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Selezionare **Fine** per avviare le modifiche al progetto e abilitare l'autenticazione di Azure AD. Visual Studio visualizza lo stato di avanzamento durante questa fase:

    ![Stato del servizio connesso di Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Una volta completato il processo, Visual Studio apre nel browser uno degli articoli seguenti, a seconda del tipo di progetto:

    - [Introduzione ai progetti MVC .NET](vs-active-directory-dotnet-getting-started.md)
    - [Introduzione ai progetti WebAPI](vs-active-directory-webapi-getting-started.md)

1. È anche possibile visualizzare il dominio di Active Directory nel [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Come viene modificato il progetto

Quando si aggiunge il servizio connesso, Visual Studio aggiunge Azure Active Directory e i riferimenti associati al progetto. Anche i file di configurazione e i file di codice nel progetto vengono modificati per aggiungere supporto per Azure AD. Le modifiche specifiche apportate in Visual Studio dipendono dal tipo di progetto. Per informazioni dettagliate, vedere gli articoli seguenti:

- [Modifiche apportate al progetto MVC .NET](vs-active-directory-dotnet-what-happened.md)
- [Cosa è successo al progetto API Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Passaggi successivi

- [Scenari di autenticazione per Azure Active Directory](./authentication-vs-authorization.md)
- [Aggiungere l'accesso con Microsoft a un'app Web ASP.NET](quickstart-v2-aspnet-webapp.md)