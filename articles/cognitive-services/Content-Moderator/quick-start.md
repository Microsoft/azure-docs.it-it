---
title: 'Avvio rapido: Provare Content Moderator sul Web - Content Moderator'
titleSuffix: Azure Cognitive Services
description: In questo avvio rapido si userà lo strumento di revisione di Content Moderator online per testare le funzionalità di base di Content Moderator senza dover scrivere codice.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 550cec535343d48ad74dd35e6886af78bdd15701
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563282"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Avvio rapido: Provare Content Moderator sul Web

Questa guida di avvio rapido mostra come usare lo strumento di revisione di Content Moderator online per testare le funzionalità di base di Content Moderator senza dover scrivere codice. Se si vuole integrare questo servizio nell'app più rapidamente, vedere le altre guide introduttive elencate nella sezione [Passaggi successivi](#next-steps).

## <a name="prerequisites"></a>Prerequisiti

- Un Web browser

## <a name="set-up-the-review-tool"></a>Configurare lo strumento di revisione
Lo strumento di revisione di Content Moderator è uno strumento basato sul Web che consente ai revisori umani di facilitare il servizio cognitivo nel processo decisionale. In questa Guida esamineremo il breve processo di configurazione dello strumento di revisione in modo da vedere come funziona il servizio Content Moderator. Passare al sito dello [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e registrarsi.

![Home page di Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Creare un team di revisione

A questo punto, creare un team di revisione. In uno scenario operativo si tratta del gruppo di persone che esamina manualmente le decisioni di moderazione del servizio. Per creare un team, è necessario selezionare un'opzione per **Area** e specificare i valori per **Nome team** e **ID team**. Se si intende invitare i colleghi a partecipare al team, immettere in questa finestra i relativi indirizzi di posta elettronica.

> [!NOTE]
> **Nome team** è un nome descrittivo per il team di revisione. Si tratta del nome visualizzato nel portale di Azure. **ID team** è quello usato per identificare il team di revisione a livello di codice.

> [!div class="mx-imgBorder"]
> ![Invitare un membro del team](images/create-team.png)

Se si sceglie di crittografare i dati usando una chiave gestita dal cliente, verrà richiesto di specificare l'**ID risorsa** di Content Moderator nel piano tariffario di E0. La risorsa specificata deve essere nuova. 

> [!div class="mx-imgBorder"]
> ![Invitare un membro del team con una chiave gestita dal cliente](images/create-team-cmk.png)

Se prova a riutilizzare una risorsa di Content Moderator, verrà visualizzato questo avviso: 

> [!div class="mx-imgBorder"]
> ![Errore della chiave gestita dal cliente](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Caricare contenuto di esempio

A questo punto, è possibile caricare contenuto di esempio. Selezionare **Try > Image** (Prova > Immagine), **Try > Text** (Prova > Testo ) o **Try > Video** (Prova > Video).

![Try Image (Prova immagine) o Moderazione testo](images/tryimagesortext.png)

Inviare il contenuto per la moderazione. Lo strumento di revisione chiamerà internamente le API di moderazione per analizzare il contenuto. Al termine dell'analisi viene visualizzato un messaggio in cui si informa che sono disponibili risultati da rivedere.

![Moderare i file](images/submitted.png)

## <a name="review-moderation-tags"></a>Esaminare i tag di moderazione

Esaminare i tag di moderazione applicati. È possibile visualizzare i tag che sono stati applicati al contenuto e il punteggio assegnato in ogni categoria. Vedere gli argomenti di moderazione [Immagine](image-moderation-api.md), [Testo](text-moderation-api.md) e [Video](video-moderation-api.md) per altre informazioni su cosa indicano i diversi contrassegni di contenuto.

![Esaminare i risultati](images/reviewresults_text.png)

In un progetto l'utente o il team di revisione può modificare questi tag o aggiungere altri tag in base alle esigenze. Per inviare le modifiche, fare clic sul pulsante **Next** (Avanti). Mentre l'applicazione aziendale chiama le API di Content Moderator, il contenuto taggato verrà accodato qui, pronto per essere rivisto dai team di revisione umana. Con questo approccio è possibile esaminare rapidamente volumi elevati di contenuto.

Si è quindi usato lo strumento di revisione di Content Moderator per vedere esempi di operazioni che può eseguire il servizio Content Moderator. A questo punto è possibile approfondire la conoscenza dello strumento di revisione e imparare a integrarlo in un progetto software usando le API di revisione oppure passare alla sezione [Passaggi successivi](#next-steps) per ottenere informazioni su come usare le API di moderazione nell'app.

## <a name="learn-more-about-the-review-tool"></a>Altre informazioni sullo strumento di revisione

Per altre informazioni sull'uso dello strumento di revisione di Content Moderator, consultare l'articolo introduttivo allo [strumento di revisione](Review-Tool-User-Guide/human-in-the-loop.md) e vedere le API di questo strumento per comprendere come ottimizzare l'esperienza di revisione umana:
- L'[API Job](try-review-api-job.md) (Processo) esegue l'analisi dei contenuti usando le API di moderazione e genera revisioni nello strumento di revisione. 
- L'[API Review](try-review-api-review.md) (Revisione) crea direttamente revisioni di immagini, testi o video per i moderatori umani senza eseguire prima l'analisi del contenuto. 
- L'[API Workflow](try-review-api-workflow.md) (Flusso di lavoro) crea, aggiorna e ottiene i dettagli sui flussi di lavoro personalizzati creati dal team.

In alternativa, continuare con i passaggi successivi per iniziare a usare le API di moderazione nel codice.

## <a name="next-steps"></a>Passaggi successivi

In questa sezione vengono fornite informazioni su come usare le API di moderazione nell'app.
- Implementare la moderazione delle immagini. Usare la [console API](try-image-api.md) oppure seguire l'[Avvio rapido per .NET SDK](dotnet-sdk-quickstart.md) per analizzare le immagini e rilevare potenziali contenuti spinti e per adulti usando tag, punteggi di attendibilità e altre informazioni estratte.
- Implementare la moderazione del testo. Usare la [console API](try-text-api.md) oppure usare l'[Avvio rapido per .NET SDK](dotnet-sdk-quickstart.md) per analizzare il contenuto del testo per rilevare potenziali contenuti volgari, una classificazione del testo indesiderato automatica (anteprima) e dati personali.
- Implementare la moderazione video. Seguire la [Guida pratica alla moderazione video per C#](video-moderation-api.md) per analizzare i video e rilevare potenziali contenuti spinti e per adulti. 
