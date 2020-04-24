---
title: Introduzione a Xamarin.Forms
description: Seguire questa esercitazione per iniziare a usare le app per dispositivi mobili per lo sviluppo per Xamarin.Forms.
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9ae97ccbcc358a150e914a6e950579a972fef0bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461335"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Creare un'app Xamarin.Forms con Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Xamarin.Forms mediante la funzionalità App per dispositivi mobili del Servizio app di Azure come back-end. Vengono creati un nuovo back-end di app per dispositivi mobili e una semplice app Xamarin.Forms di tipo elenco attività che archivia dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni relative alle app per dispositivi mobili per Xamarin.Forms.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non è disponibile un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 app per dispositivi mobili gratuite che potranno essere usate anche dopo il termine del periodo di valutazione. Per altre informazioni, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* Strumenti di Visual Studio per Novell, in Visual Studio 2017 o versioni successive o Visual Studio per Mac. Vedere la [pagina di installazione di Xamarin][Install Xamarin] per le istruzioni.

* (facoltativo) Per compilare un'app iOS, è necessario un Mac con Xcode 9.0 o versione successiva. È possibile usare Visual Studio per Mac per sviluppare app iOS oppure è possibile usare Visual Studio 2017 o versione successiva (purché il Mac sia disponibile in rete).

## <a name="create-a-new-mobile-apps-back-end"></a>Creare un nuovo back-end di App per dispositivi mobili
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creare una connessione al database e configurare il progetto client e server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Eseguire la soluzione Novell. Forms

Per aprire la soluzione sono necessari gli Strumenti di Visual Studio per Xamarin. Vedere le [istruzioni di installazione di Xamarin][Install Xamarin]. Se gli strumenti sono già installati, seguire questa procedura per scaricare e aprire la soluzione:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows e Mac)

1. Passare alla [portale di Azure](https://portal.azure.com/) e passare all'app per dispositivi mobili creata. `Overview` Nel pannello cercare l'URL che rappresenta l'endpoint pubblico per l'app per dispositivi mobili. Esempio: il SiteName per il nome dell'app "test123" https://test123.azurewebsites.netsarà.

2. Aprire il file `Constants.cs` in questa cartella: Novell. Forms/ZUMOAPPNAME. Il nome dell'applicazione `ZUMOAPPNAME`è.

3. Nella `Constants.cs` classe sostituire `ZUMOAPPURL` variable con l'endpoint public sopra.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    diventa

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Seguire le istruzioni riportate di seguito per eseguire i progetti Android o Windows e, se nella rete è disponibile un computer Mac, il progetto iOS.

## <a name="optional-run-the-android-project"></a>(Facoltativo) Eseguire il progetto Android

In questa sezione viene eseguito il progetto Xamarin.Android. Se non si usano dispositivi Android, è possibile ignorare questa sezione.

### <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto Android (Droid) e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto Android e verificare che la casella di controllo **Compila** sia selezionata per il progetto di codice condiviso.

4. Per compilare il progetto e avviare l'app in un emulatore Android, premere **F5** o fare clic sul pulsante **Avvia**.

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Fare clic con il pulsante destro del mouse sul progetto Android e quindi scegliere **Imposta come progetto di avvio**.

2. Per compilare il progetto e avviare l'app in un emulatore Android, scegliere **Avvia debug** dal menu **Esegui**.

Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

![App elenco attività Android][11]

Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.

> [!NOTE]
> Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file C# **TodoItemManager.cs** del progetto di codice condiviso della soluzione.
>

## <a name="optional-run-the-ios-project"></a>(Facoltativo) Eseguire il progetto iOS

In questa sezione viene eseguito il progetto Xamarin.iOS per dispositivi iOS. Se non si usano dispositivi iOS, è possibile ignorare questa sezione.

### <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto iOS e verificare che la casella di controllo **Compila** sia selezionata per il progetto di codice condiviso.

4. Per compilare il progetto e avviare l'app nell'emulatore iPhone, premere il tasto **F5**.

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Fare clic con il pulsante destro del mouse sul progetto iOS e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Start Debugging** (Avvia debug) dal menu **Run** (Esegui) per compilare il progetto e avviare l'app nell'emulatore iPhone.

Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

![App elenco attività iOS][10]

Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.

> [!NOTE]
> Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file C# **TodoItemManager.cs** del progetto di codice condiviso della soluzione.
>

## <a name="optional-run-the-windows-project"></a>(Facoltativo) Eseguire il progetto Windows

In questa sezione viene eseguito il progetto Xamarin.Forms della piattaforma UWP (Universal Windows Platform) per dispositivi Windows. Se non si usano dispositivi Windows, è possibile ignorare questa sezione.

### <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse su un progetto della piattaforma UWP e quindi scegliere **Imposta come progetto di avvio**.

2. Scegliere **Configuration Manager** dal menu **Compila**.

3. Nella finestra di dialogo **Configuration Manager** selezionare le caselle di controllo **Compila** e **Distribuisci** accanto al progetto Windows scelto e verificare che la casella di controllo **Compila** sia selezionata per il progetto di codice condiviso.

4. Per compilare il progetto e avviare l'app in un emulatore Android, premere **F5** o fare clic sul pulsante **Avvia** (che dovrebbe indicare **Computer locale**).

> [!NOTE]
> Non è possibile eseguire il progetto Windows su macOS.

Nell'app digitare un testo significativo, ad esempio *Learn Xamarin*, e quindi selezionare il segno più (**+**).

Questa azione invia una richiesta POST al nuovo back-end di App per dispositivi mobili ospitato in Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal back-end di App per dispositivi mobili e i dati vengono visualizzati nell'elenco.

![App elenco attività UWP][12]

> [!NOTE]
> Il codice che accede al back-end di App per dispositivi mobili è disponibile nel file C# **TodoItemManager.cs** del progetto di libreria di classi portabile della soluzione.
>

## <a name="troubleshooting"></a>Risoluzione dei problemi

In caso di problemi di compilazione della soluzione, eseguire Gestione pacchetti NuGet, effettuare l'aggiornamento alla versione più recente di `Xamarin.Forms` e nel progetto Android aggiornare i pacchetti di supporto di `Xamarin.Android`. I progetti di avvio rapido potrebbero non includere sempre le versioni più recenti.

Si noti che tutti i pacchetti di supporto a cui fa riferimento il progetto Android devono avere la stessa versione. Il [pacchetto NuGet di App per dispositivi mobili di Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) presenta la dipendenza `Xamarin.Android.Support.CustomTabs` per la piattaforma Android, perciò se il progetto usa pacchetti di supporto più recenti è necessario installare questo pacchetto direttamente con la versione richiesta per evitare conflitti.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
