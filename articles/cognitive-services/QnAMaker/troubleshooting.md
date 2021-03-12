---
title: Risoluzione dei problemi-QnA Maker
description: L'elenco curato delle domande più frequenti relative al servizio QnA Maker consentirà di adottare il servizio più rapidamente e con risultati migliori.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612695"
---
# <a name="troubleshooting-for-qna-maker"></a>Risoluzione dei problemi per QnA Maker

L'elenco curato delle domande più frequenti relative al servizio QnA Maker consentirà di adottare il servizio più rapidamente e con risultati migliori.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Gestisci stime

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

<details>
<summary><b>Come è possibile migliorare le prestazioni di velocità effettiva per le previsioni delle query?</b></summary>

**Risposta**: i problemi di prestazioni della velocità effettiva indicano che è necessario eseguire la scalabilità verticale sia per il servizio app che per il ricerca cognitiva. Prendere in considerazione l'aggiunta di una replica al ricerca cognitiva per migliorare le prestazioni.

Altre informazioni sui [piani tariffari](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Come ottenere l'endpoint del servizio QnAMaker</b></summary>

**Risposta**: l'endpoint del servizio QnAMaker è utile a scopo di debug quando si contatta il supporto di QnAMaker o UserVoice. L'endpoint è un URL nel formato seguente: `https://your-resource-name.azurewebsites.net` .

1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse Azure per QnA Maker nel portale di Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare il servizio app associato alla risorsa QnA Maker. In genere, i nomi sono gli stessi.

     ![Selezionare il servizio app QnA Maker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. L'URL dell'endpoint è disponibile nella sezione Panoramica

    ![Endpoint QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

<details>
<summary><b>Come è possibile migliorare le prestazioni di velocità effettiva per le previsioni delle query?</b></summary>

**Risposta**: i problemi di prestazioni della velocità effettiva indicano che è necessario aumentare il livello di ricerca cognitiva. Prendere in considerazione l'aggiunta di una replica al ricerca cognitiva per migliorare le prestazioni.

Altre informazioni sui [piani tariffari](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>Gestione della knowledge base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

<details>
<summary><b>Ho accidentalmente eliminato una parte di QnA Maker, cosa devo fare?</b></summary>

**Risposta**: non eliminare i servizi di Azure creati insieme alla risorsa QnA Maker come la ricerca o l'app Web. Per il corretto funzionamento di QnA Maker, è necessario eliminarne uno QnA Maker smette di funzionare correttamente.

Tutte le eliminazioni sono permanenti, incluse le coppie di domande e risposte, i file, gli URL, le domande e risposte personalizzate, le knowledge base e le risorse di Azure. Prima di eliminare qualsiasi parte della knowledge base, quindi, assicurarsi di esportare le knowledge base dalla pagina **Impostazioni**.

</details>

<details>
<summary><b>Perché i miei URL/file non estraggono coppie di domanda/risposta?</b></summary>

**Risposta**: è possibile che QnA Maker non possa estrarre automaticamente il contenuto di domande e risposte (QnA) da URL di domande frequenti valide. In questi casi è possibile incollare il contenuto di domande/risposte in un file TXT e verificare se lo strumento è in grado di inglobarlo. In alternativa è possibile aggiungere contenuti alla Knowledge Base in modo editoriale tramite il [portale QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Quali sono le dimensioni massime consentite per la creazione di Knowledge Base?</b></summary>

**Risposta**: le dimensioni della Knowledge base variano a seconda dello SKU di ricerca di Azure scelto durante la creazione del servizio QnA Maker. Vedere [qui](./concepts/azure-resources.md) per altri dettagli.

</details>

<details>
<summary><b>Perché non vedo nulla nell'elenco a discesa quando cerco di creare una nuova Knowledge Base?</b></summary>

**Risposta**: non sono stati ancora creati servizi di QnA Maker in Azure. Per altre informazioni su come eseguire questa operazione, leggere [qui](./How-To/set-up-qnamaker-service-azure.md).

</details>

<details>
<summary><b>Come si condivide una Knowledge Base?</b></summary>

**Risposta**: la condivisione funziona a livello di servizio di QnA Maker, ovvero tutte le Knowledge base nel servizio verranno condivise. Vedere [qui](./index.yml) per informazioni su come collaborare a una Knowledge Base.

</details>

<details>
<summary><b>È possibile condividere una knowledge base con un collaboratore che non è nello stesso tenant di AAD, per modificare una knowledge base?</b></summary>

**Risposta**: la condivisione si basa sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Se è possibile condividere _qualsiasi_ risorsa in Azure con un altro utente, è anche possibile condividere QnA Maker.

</details>

<details>
<summary><b>Se si dispone di un piano di servizio app con 5 Knowledge base QnAMaker. È possibile assegnare diritti di lettura/scrittura a 5 utenti diversi, in modo che ognuno di essi possa accedere solo a 1 QnAMaker Knowledge base?</b></summary>

**Risposta**: è possibile condividere un intero servizio QnAMaker, non le singole Knowledge base.

</details>

<details>
<summary><b>Come si modifica il messaggio predefinito visualizzato se non viene trovata alcuna corrispondenza?</b></summary>

**Risposta**: il messaggio predefinito fa parte delle impostazioni nel servizio app.
- Passare alla risorsa del servizio app nel portale di Azure

![Servizio app QnA Maker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Fare clic sull'opzione **Impostazioni**

![Impostazioni del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Modificare il valore dell'impostazione **DefaultAnswer**
- Riavviare il servizio app

![Riavvio del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Perché il collegamento SharePoint non viene estratto?</b></summary>

**Risposta**: per ulteriori informazioni, vedere [percorsi origine dati](./concepts/data-sources-and-content.md#data-source-locations) .

</details>

<details>
<summary><b>Gli aggiornamenti apportati alla Knowledge base non si riflettono sulla pubblicazione. Perché no?</b></summary>

**Risposta**: ogni operazione di modifica, in una tabella di aggiornamento, test o impostazione, deve essere salvata prima di poterla pubblicare. Assicurarsi di fare clic sul pulsante **Salva e Train** dopo ogni operazione di modifica.

</details>

<details>
<summary><b>La Knowledge Base supporta i dati avanzati o i contenuti multimediali?</b></summary>

**Risposta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Estrazione automatica multimediale per file e URL

* Funzionalità di conversione da HTML a Markdown limitata agli URL.
* File: non supportato

#### <a name="answer-text-in-markdown"></a>Testo della risposta in Markdown
Quando le coppie di QnA sono nella Knowledge base, è possibile modificare il testo Markdown di una risposta per includere i collegamenti ai supporti disponibili dagli URL pubblici.


</details>

<details>
<summary><b>QnA Maker supporta lingue diverse dall'inglese?</b></summary>

**Risposta**: vedere altri dettagli sulle [lingue supportate](./overview/language-support.md).

Se sono presenti contenuti in più lingue, assicurarsi di creare un servizio separato per ciascuna lingua.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

<details>
<summary><b>Perché i miei URL/file non estraggono coppie di domanda/risposta?</b></summary>

**Risposta**: è possibile che QnA Maker non possa estrarre automaticamente il contenuto di domande e risposte (QnA) da URL di domande frequenti valide. In questi casi è possibile incollare il contenuto di domande/risposte in un file TXT e verificare se lo strumento è in grado di inglobarlo. In alternativa è possibile aggiungere contenuti alla Knowledge Base in modo editoriale tramite il [portale QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Quali sono le dimensioni massime consentite per la creazione di Knowledge Base?</b></summary>

**Risposta**: le dimensioni della Knowledge base variano a seconda dello SKU di ricerca di Azure scelto durante la creazione del servizio QnA Maker. Vedere [qui](./concepts/azure-resources.md) per altri dettagli.

</details>

<details>
<summary><b>Perché non vedo nulla nell'elenco a discesa quando cerco di creare una nuova Knowledge Base?</b></summary>

**Risposta**: non sono stati ancora creati servizi di QnA Maker in Azure. Per altre informazioni su come eseguire questa operazione, leggere [qui](./How-To/set-up-qnamaker-service-azure.md).

</details>

<details>
<summary><b>Come si condivide una Knowledge Base?</b></summary>

**Risposta**: la condivisione funziona a livello di servizio di QnA Maker, ovvero tutte le Knowledge base nel servizio verranno condivise. Vedere [qui](./index.yml) per informazioni su come collaborare a una Knowledge Base.

</details>

<details>
<summary><b>È possibile condividere una Knowledge base con un collaboratore che non si trova nello stesso tenant Azure Active Directory per modificare una Knowledge base?</b></summary>

**Risposta**: la condivisione si basa sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Se è possibile condividere _qualsiasi_ risorsa in Azure con un altro utente, è anche possibile condividere QnA Maker.

</details>

<details>
<summary><b>È possibile assegnare diritti di lettura/scrittura per 5 utenti diversi in modo che ognuno di essi possa accedere solo a 1 knowledge base QnAMaker?</b></summary>

**Risposta**: è possibile condividere un intero servizio QnAMaker, non le singole Knowledge base.

</details>

<details>
<summary><b>Perché il collegamento SharePoint non viene estratto?</b></summary>

**Risposta**: per ulteriori informazioni, vedere [percorsi origine dati](./concepts/data-sources-and-content.md#data-source-locations) .

</details>

<details>
<summary><b>Gli aggiornamenti apportati alla Knowledge base non si riflettono sulla pubblicazione. Perché no?</b></summary>

**Risposta**: ogni operazione di modifica, in una tabella di aggiornamento, test o impostazione, deve essere salvata prima di poterla pubblicare. Assicurarsi di fare clic sul pulsante **Salva e Train** dopo ogni operazione di modifica.

</details>

<details>
<summary><b>La Knowledge Base supporta i dati avanzati o i contenuti multimediali?</b></summary>

**Risposta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Estrazione automatica multimediale per file e URL

* Funzionalità di conversione da HTML a Markdown limitata agli URL.
* File: non supportato

#### <a name="answer-text-in-markdown"></a>Testo della risposta in Markdown
Quando le coppie di QnA sono nella Knowledge base, è possibile modificare il testo Markdown di una risposta per includere i collegamenti ai supporti disponibili dagli URL pubblici.


</details>

<details>
<summary><b>QnA Maker supporta lingue diverse dall'inglese?</b></summary>

**Risposta**: vedere altri dettagli sulle [lingue supportate](./overview/language-support.md).

Se sono presenti contenuti in più lingue, assicurarsi di creare un servizio separato per ciascuna lingua.

</details>

---

## <a name="manage-service"></a>Gestire il servizio

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

<details>
<summary><b>Quando è consigliabile riavviare un servizio app?</b></summary>

**Risposta**: aggiornare il servizio app quando l'icona di attenzione è accanto al valore della versione per la Knowledge base nella tabella **chiavi endpoint** nella pagina **impostazioni utente** [](https://www.qnamaker.ai/UserSettings).

</details>

<details>
<summary><b>Il servizio di ricerca esistente è stato eliminato. Come si può risolvere il problema?</b></summary>

**Risposta**: se si elimina un indice ricerca cognitiva di Azure, l'operazione è finale e non è possibile recuperare l'indice.

</details>

<details>
<summary><b>Ho eliminato l' `testkb` indice nel servizio di ricerca. Come si può risolvere il problema?</b></summary>

**Risposta**: i dati precedenti non possono essere recuperati. Creare una nuova risorsa di QnA Maker e creare di nuovo la Knowledge base.

</details>

<details>
<summary><b>Quando è necessario aggiornare le chiavi endpoint?</b></summary>

**Risposta**: aggiornare le chiavi dell'endpoint se si ritiene che siano state compromesse.

</details>

<details>
<summary><b>È possibile usare la stessa risorsa ricerca cognitiva di Azure per le Knowledge base usando più lingue?</b></summary>

**Risposta**: per usare più lingue e più Knowledge base, l'utente deve creare una risorsa QnA Maker per ciascuna lingua. Verrà creato un servizio di ricerca di Azure separato per ogni lingua. La combinazione di knowledge base in lingue diverse in un singolo servizio di Ricerca di Azure comporterà una ridotta pertinenza dei risultati.

</details>

<details>
<summary><b>Come è possibile modificare il nome della risorsa ricerca cognitiva di Azure usata da QnA Maker?</b></summary>

**Risposta**: il nome della risorsa ricerca cognitiva di Azure è il nome della risorsa QnA Maker con alcune lettere casuali aggiunte alla fine. La distinzione tra più risorse di ricerca per QnA Maker risulta quindi più difficile. Creare un servizio di ricerca separato (denominarlo come si desidera) e connetterlo al servizio QnA. I passaggi sono simili ai passaggi necessari per [aggiornare una ricerca di Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Quando QnA Maker restituisce `Runtime core is not initialized,` come risolvere il problema?</b></summary>

**Risposta**: lo spazio su disco per il servizio app potrebbe essere pieno. Passaggi per la correzione dello spazio su disco:

1. Nella [portale di Azure](https://portal.azure.com)selezionare il servizio App del QnA Maker, quindi arrestare il servizio.
1. Sempre nel servizio app, selezionare strumenti di **sviluppo**, **strumenti avanzati**, quindi **fare** clic su. Verrà visualizzata una nuova finestra del browser.
1. Selezionare **console di debug**, quindi **cmd** per aprire uno strumento da riga di comando.
1. Passare alla directory _site/wwwroot/data/QnAMaker/_ .
1. Rimuovere tutte le cartelle il cui nome inizia con `rd` .

    Non **eliminare** gli elementi seguenti:

    * File KbIdToRankerMappings.txt
    * EndpointSettings.jssu file
    * Cartella EndpointKeys

1. Avviare il servizio app.
1. Accedere alla Knowledge base per verificarne il funzionamento.

</details>
<details>
<summary><b>Perché il Application Insights non funziona?</b></summary>

**Risposta**: per risolvere il problema, eseguire il controllo incrociato e aggiornare i passaggi seguenti:

1. Nel servizio app-> impostazioni gruppo-> sezione di configurazione-> impostazioni applicazione-> nome "UserAppInsightsKey" i parametri sono configurati correttamente e impostati sulla rispettiva scheda Panoramica di Application Insights ("chiave di strumentazione"). 

1. Nel servizio app-gruppo di impostazioni >-> sezione "Application Insights"-> assicurarsi che App Insights sia abilitato e connesso alla risorsa di Application Insights corrispondente.

</details>

<details>
<summary><b>Il Application Insights è abilitato, ma perché non funziona correttamente?</b></summary>

**Risposta**: attenersi ai passaggi indicati di seguito: 

1.  Copiare il valore di "" APPINSIGHTS_INSTRUMENTATIONKEY "Name" nel nome "UserAppInsightsKey" eseguendo l'override se è già presente un valore. 

1.  Se la chiave ' UserAppInsightsKey ' non esiste nelle impostazioni dell'app, aggiungere una nuova chiave con tale nome e copiare il valore.

1.  Salvarlo e il servizio app verrà riavviato automaticamente. Questa operazione dovrebbe risolvere il problema. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)


<details>
<summary><b>Il servizio di ricerca esistente è stato eliminato. Come si può risolvere il problema?</b></summary>

**Risposta**: se si elimina un indice ricerca cognitiva di Azure, l'operazione è finale e non è possibile recuperare l'indice.

</details>

<details>
<summary><b>Ho eliminato l' `testkb` indice nel servizio di ricerca. Come si può risolvere il problema?</b></summary>

**Risposta**: i dati precedenti non possono essere recuperati. Creare una nuova risorsa di QnA Maker e creare di nuovo la Knowledge base.

</details>

<details>
<summary><b>È possibile usare la stessa risorsa ricerca cognitiva di Azure per le Knowledge base usando più lingue?</b></summary>

**Risposta**: per usare più lingue e più Knowledge base, l'utente deve creare una risorsa QnA Maker per ciascuna lingua. Verrà creato un servizio di ricerca di Azure separato per ogni lingua. La combinazione di knowledge base in lingue diverse in un singolo servizio di Ricerca di Azure comporterà una ridotta pertinenza dei risultati.

</details>

<details>
<summary><b>Come è possibile modificare il nome della risorsa ricerca cognitiva di Azure usata da QnA Maker?</b></summary>

**Risposta**: il nome della risorsa ricerca cognitiva di Azure è il nome della risorsa QnA Maker con alcune lettere casuali aggiunte alla fine. La distinzione tra più risorse di ricerca per QnA Maker risulta quindi più difficile. Creare un servizio di ricerca separato (denominarlo come si desidera) e connetterlo al servizio QnA. I passaggi sono simili ai passaggi necessari per [aggiornare una ricerca di Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integrazione con altri servizi, inclusi Bot

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

<details>
<summary><b>È necessario usare Bot Framework per usare QnA Maker?</b></summary>

**Risposta**: No, non è necessario usare [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) con QnA Maker. Tuttavia, QnA Maker è disponibile come uno dei diversi modelli nel [servizio Azure bot](/azure/bot-service/). Il servizio Bot consente di sviluppare rapidamente bot intelligenti con Microsoft Bot Framework e viene eseguito in un ambiente senza server.

</details>

<details>
<summary><b>Come è possibile creare un nuovo bot con QnA Maker?</b></summary>

**Risposta**: seguire le istruzioni riportate in [questa](./Quickstarts/create-publish-knowledge-base.md) documentazione per creare il bot con il servizio Azure bot.

</details>

<details>
<summary><b>Ricerca per categorie usare una Knowledge base diversa con un servizio Azure bot esistente?</b></summary>

**Risposta**: è necessario disporre delle informazioni seguenti sulla Knowledge Base:

* ID della Knowledge base.
* Nome del sottodominio personalizzato dell'endpoint pubblicato della Knowledge base, noto come `host` , disponibile nella pagina **Impostazioni** dopo la pubblicazione.
* Chiave endpoint pubblicata della Knowledge base, disponibile nella pagina **Impostazioni** dopo la pubblicazione.

Con queste informazioni, andare al servizio app del bot nella portale di Azure. In **Impostazioni-> configurazione > impostazioni applicazione** modificare tali valori.

La chiave dell'endpoint della Knowledge base è etichettata `QnAAuthkey` nel servizio ABS.

</details>

<details>
<summary><b>Due o più applicazioni client possono condividere una Knowledge base?</b></summary>

**Risposta**: Sì, la Knowledge base può essere sottoposta a query da un numero qualsiasi di client. Se la risposta dalla Knowledge base sembra essere lenta o timeout, provare ad aggiornare il livello di servizio per il servizio app associato alla Knowledge base.

</details>

<details>
<summary><b>Come si incorpora il servizio QnA Maker nel proprio sito Web?</b></summary>

**Risposta**: attenersi alla procedura seguente per incorporare il servizio QnA Maker come controllo web-chat nel sito Web:

1. Creare il bot di domande frequenti seguendo [queste istruzioni](./Quickstarts/create-publish-knowledge-base.md).
2. Abilitare la chat Web seguendo [questi](/azure/bot-service/bot-service-channel-connect-webchat) passaggi

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)


<details>
<summary><b>È necessario usare Bot Framework per usare QnA Maker?</b></summary>

**Risposta**: No, non è necessario usare [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) con QnA Maker. Tuttavia, QnA Maker è disponibile come uno dei diversi modelli nel [servizio Azure bot](/azure/bot-service/). Il servizio Bot consente di sviluppare rapidamente bot intelligenti con Microsoft Bot Framework e viene eseguito in un ambiente senza server.

</details>

<details>
<summary><b>Come è possibile creare un nuovo bot con QnA Maker?</b></summary>

**Risposta**: seguire le istruzioni riportate in [questa](./Quickstarts/create-publish-knowledge-base.md) documentazione per creare il bot con il servizio Azure bot.

</details>

<details>
<summary><b>Ricerca per categorie usare una Knowledge base diversa con un servizio Azure bot esistente?</b></summary>

**Risposta**: è necessario disporre delle informazioni seguenti sulla Knowledge Base:

* ID della Knowledge base.
* Nome del sottodominio personalizzato dell'endpoint pubblicato della Knowledge base, noto come `host` , disponibile nella pagina **Impostazioni** dopo la pubblicazione.
* Chiave endpoint pubblicata della Knowledge base, disponibile nella pagina **Impostazioni** dopo la pubblicazione.

Con queste informazioni, andare al servizio app del bot nella portale di Azure. In **Impostazioni-> configurazione > impostazioni applicazione** modificare tali valori.

La chiave dell'endpoint della Knowledge base è etichettata `QnAAuthkey` nel servizio ABS.

</details>

<details>
<summary><b>Due o più applicazioni client possono condividere una Knowledge base?</b></summary>

**Risposta**: Sì, la Knowledge base può essere sottoposta a query da un numero qualsiasi di client. Se la risposta dalla Knowledge base sembra essere lenta o timeout, provare ad aggiornare il livello di servizio per il servizio app associato alla Knowledge base.

</details>

<details>
<summary><b>Come si incorpora il servizio QnA Maker nel proprio sito Web?</b></summary>

**Risposta**: attenersi alla procedura seguente per incorporare il servizio QnA Maker come controllo web-chat nel sito Web:

1. Creare il bot di domande frequenti seguendo [queste istruzioni](./Quickstarts/create-publish-knowledge-base.md).
2. Abilitare la chat Web seguendo [questi](/azure/bot-service/bot-service-channel-connect-webchat) passaggi

---

## <a name="data-storage"></a>Archiviazione dei dati

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

<details>
<summary><b>Quali dati vengono archiviati e dove vengono archiviati?</b></summary>

**Risposta**:

Quando si crea un servizio QnA Maker, si seleziona un'area di Azure. Le knowledge base e i file di log vengono archiviati in questa area.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

<details>
<summary><b>Quali dati vengono archiviati e dove vengono archiviati?</b></summary>

**Risposta**:

Quando si crea un servizio QnA Maker, si seleziona un'area di Azure. Le knowledge base e i file di log vengono archiviati in questa area.

</details>

---
