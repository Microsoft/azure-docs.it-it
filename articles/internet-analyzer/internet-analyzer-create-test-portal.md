---
title: Creare un test dell'analizzatore Internet tramite il portale | Microsoft Docs
description: Questo articolo spiega come creare il primo test dell'analizzatore Internet.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "73509835"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Creare un test dell'analizzatore Internet tramite il portale (anteprima)

Per creare una risorsa analizzatore Internet è possibile procedere in due modi, ovvero usare il portale di Azure o usare l'[interfaccia della riga di comando](internet-analyzer-cli.md). Questa sezione illustra come creare una nuova risorsa analizzatore Internet di Azure tramite l'esperienza del portale.

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

L'anteprima pubblica è disponibile per l'uso a livello globale. Durante l'anteprima l'archiviazione dei dati è tuttavia limitata all'area *Stati Uniti occidentali 2*.

## <a name="basics"></a>Nozioni di base

1. Ottenere l'accesso all'anteprima dell'analizzatore Internet seguendo le istruzioni **Come si partecipa all'anteprima?** nelle [Domande frequenti sull'analizzatore Internet di Azure](internet-analyzer-faq.md).
2. Nella home page del [portale di Azure](https://preview.portal.azure.com) fare clic su **+ Crea una risorsa**. L'analizzatore Internet attualmente è disponibile solo dalla versione in anteprima del portale di Azure.
3. Nella pagina **Nuova** cercare "analizzatore Internet" nel campo *Cerca nel Marketplace*.
4. Fare clic su **Analizzatore Internet (anteprima)**. Verificare che l'editore sia *Microsoft* e che la categoria sia *Rete*.
5. Nella pagina **Analizzatore Internet (anteprima)** fare clic su **Crea** per visualizzare la pagina **Crea un analizzatore Internet**.
6. Specificare le impostazioni di configurazione seguenti per la risorsa analizzatore Internet:

    * **Sottoscrizione:** Sottoscrizione di Azure per ospitare la nuova risorsa di Internet Analyzer. **_Usare lo stesso ID sottoscrizione usato per richiedere l'accesso in anteprima._**
    * **Gruppo di risorse:** Gruppo di risorse di Azure in cui verrà creata la nuova risorsa di Internet Analyzer. Se non è già disponibile un gruppo di risorse, è possibile crearne uno nuovo.
    * **Nome:** Nome del nuovo profilo risorsa di Internet Analyzer.
    * **Area geografica:** Area pubblica di Azure in cui verrà creata la risorsa. Durante l'anteprima, è disponibile solo l'area *Stati Uniti occidentali 2*.

7. Dopo aver finito di specificare le impostazioni del profilo, fare clic su **Rivedi e crea**.

## <a name="configuration"></a>Configurazione

Il completamento dei passaggi di base costituisce un prerequisito per la configurazione di un test e l'incorporamento del client JavaScript. Dopo aver creato un profilo, passare a **Impostazioni > Configurazione** per configurare il primo test.

1. Assegnare al test un nome digitandolo nella casella **Nome test**.
2. Aggiungere una descrizione del test digitandola nel campo **Descrizione**.
3. Fare clic su **Configura endpoint**. Dal lato destro verrà visualizzata una scheda. Selezionare il tipo di endpoint che si intende configurare, ovvero una singola area di Azure, più aree di Azure o un endpoint personalizzato.

    >
    ***Endpoint preconfigurati: combinazioni di aree di Azure singole o multiple***
    * Selezionare un'area o un set di aree da un [elenco preconfigurato di endpoint di Azure](internet-analyzer-faq.md).
    * Selezionare quindi il tipo di architettura di distribuzione di applicazioni o di contenuti che si intende valutare.
        * Area di Azure singola: accelerazione sito ([porta anteriore di Azure](https://azure.microsoft.com/services/frontdoor/)), memorizzazione nella cache di contenuto statico (rete[CDN di Azure per Microsoft](https://azure.microsoft.com/services/cdn/)) o nessuna.
        * Più aree di Azure: accelerazione sito ([porta anteriore di Azure](https://azure.microsoft.com/services/frontdoor/)), Guida DNS ([Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/))  

    ***Endpoint personalizzati***
    * Seguire le istruzioni contenute nella pagina per la [creazione di un endpoint personalizzato](internet-analyzer-custom-endpoint.md).
    * Incollare il percorso dell'URL HTTPS dell'immagine da un pixel nel portale.
    >

4. Fare clic su **Aggiungi** per aggiungere l'endpoint al test.
5. Ripetere i passaggi da 1 a 4 per configurare il secondo endpoint. Le misurazioni dell'endpoint B vengono sempre effettuate in relazione all'endpoint A. Pertanto, quando si configurano gli endpoint, considerare quale di essi deve essere l'endpoint di controllo del test.
6. Fare clic sul pulsante **Salva** per salvare il test. Dopo aver salvato un test, non è più possibile modificarne gli endpoint.
7. Selezionare uno o più test da avviare e fare clic su **Avvia test**. In questo modo verrà modificato lo **_stato_*_ dei test su _*_Running_**. È possibile avviare i test in qualsiasi momento, ma è necessario incorporare il client JavaScript per consentire al test di iniziare a raccogliere le misurazioni.
8. Aggiungere altri test in qualsiasi punto. Si noti che il client JavaScript specifico verrà generato solo dopo che è stato creato un test.

## <a name="embed-client"></a>Incorporare il client

Per iniziare qualsiasi test, è necessario che il client JavaScript sia incorporato nell'applicazione Web. Dopo aver configurato almeno un test, fare clic su **Rivedi e crea**, passare a **Impostazioni > Configurazione** e copiare il client JavaScript. Per le istruzioni specifiche, vedere la pagina [Incorporare il client dell'analizzatore Internet](internet-analyzer-embed-client.md).  

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
* Vedere le informazioni su come incorporare il [client dell'analizzatore Internet](internet-analyzer-embed-client.md) e su come creare un [endpoint personalizzato](internet-analyzer-custom-endpoint.md).
