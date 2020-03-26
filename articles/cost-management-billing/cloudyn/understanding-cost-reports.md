---
title: Informazioni sui report di gestione dei costi di Cloudyn in Azure
description: Questo articolo contiene informazioni utili per comprendere le funzioni e la struttura di base dei report di gestione dei costi Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 5d4f8e26aef5fbfb20c1721d1851a4940863121b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474544"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Informazioni sui report di gestione dei costi Cloudyn

Questo articolo contiene informazioni utili per comprendere le funzioni e la struttura di base dei report di gestione dei costi Cloudyn. La maggior parte dei report Cloudyn presenta le informazioni in modo intuitivo e con un aspetto grafico uniforme. Dopo avere letto questo articolo, sarà possibile usare qualsiasi report di gestione dei costi. Nei vari report sono disponibili molte funzionalità standard che ne facilitano l'esplorazione. I report sono personalizzabili ed è possibile selezionare diverse opzioni per calcolare e visualizzare i risultati.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-fields-and-options"></a>Campi e opzioni dei report

Ecco un esempio del report Cost Over Time (Costo nel tempo). Il layout è simile a quello della maggior parte dei report Cloudyn.

![Esempio del report Cost Over Time (Costo nel tempo) con aree numerate corrispondenti alle descrizioni](./media/understanding-cost-reports/sample-report.png)

Ogni area numerata nella figura è descritta in dettaglio nei paragrafi seguenti:

1. **Date Range** (Intervallo di date)

    Usare questo elenco per definire un intervallo di tempo nel report usando un valore predefinito o personalizzato.
2. **Saved Filter** (Filtro salvato)

    Usare questo elenco per salvare i gruppi e i filtri correnti applicati al report. I filtri salvati sono disponibili nei report relativi a costi e prestazioni, tra cui:

      - Cost Analysis (Analisi dei costi)
      - Allocation (Allocazione)
      - Asset Management (Gestione degli asset)
      - Optimization

   Digitare un nome di filtro e quindi fare clic su **Save** (Salva).

3. **Tag**

    Usare questa area per raggruppare i dati in base a categorie di tag. I tag elencati nel menu si riferiscono a centri di costo o reparti di Azure oppure a sottoscrizioni o entità di costo di Cloudyn. Selezionare i tag per filtrare i risultati. Per eseguire questa operazione è anche possibile digitare un nome di tag (parola chiave).

    ![Esempio di un elenco di tag in base a cui filtrare i risultati](./media/understanding-cost-reports/select-options.png)

    Fare clic su **Add** (Aggiungi) per aggiungere un nuovo filtro.

    ![Casella Add filter (Aggiungi filtro) che visualizza le opzioni e le condizioni in base a cui filtrare](./media/understanding-cost-reports/add-filter.png)

    La funzionalità per il filtraggio o il raggruppamento in base a tag non è correlata ai tag dei gruppi di risorse o alle risorse di Azure.

    Il filtraggio e il raggruppamento in base a tag di allocazione costi sono disponibili nel menu **Groups** (Gruppi).

4. **Gruppi nei report**

    I gruppi nei report di analisi dei costi mostrano le categorie di dettaglio standard relative ai dati di fatturazione nel report,  mentre i gruppi nei report di allocazione costi mostrano le categorie basate su tag. Queste ultime sono definite nel modello di allocazione costi, diversamente dalle categorie di dettaglio standard, che sono ricavate dai dati di fatturazione.

    ![Primo elenco di esempio di tag in base a cui è possibile raggruppare](./media/understanding-cost-reports/groups-tags01.png)

    ![Secondo elenco di esempio di tag in base a cui è possibile raggruppare](./media/understanding-cost-reports/groups-tags02.png)

    Nei report di allocazione costi i gruppi delle categorie basate su tag possono includere:
      - Tag
      - Tag di gruppi di risorse
      - Tag di entità di costo Cloudyn
      - Tag di sottoscrizione a scopo di allocazione costi

   Ecco alcuni esempi:
   - Centro di costo
   - department
   - Applicazione
   - Environment
   - Codice di costo

     Di seguito è riportato un elenco dei gruppi predefiniti disponibili nei report:

     - **Cost Type** (Tipo di costo)
     - Selezionare un tipo di costo o più tipi di costo oppure selezionarli tutti. I tipi di costo includono:
       - One-Time Fee (Tariffa una tantum)
       - Supporto
       - Usage Cost (Costo di utilizzo)
     - **Cliente**
       - Selezionare un cliente specifico, più clienti oppure selezionarli tutti.
     - **Account Name** (Nome account)
       - Nome dell'account o della sottoscrizione. In Azure si tratta del nome della sottoscrizione di Azure.
     - **Account No** (N. account)
       - Selezionare un account, più account o tutti gli account. In Azure si tratta del GUID della sottoscrizione di Azure.
     - **Parent Account** (Account padre)
       - Selezionare l'account padre, più account oppure selezionarli tutti.
     - **Service**
       - Selezionare un servizio, più servizi o tutti i servizi.
     - **Provider**
       - Provider cloud in cui sono associati asset e spese.
     - **Area**
       - Area in cui la risorsa è ospitata.
     - **Availability Zone** (Zona di disponibilità)
       - Località isolate AWS all'interno di un'area.
     - **Tipo di risorsa**
       - Tipo di risorsa in uso.
     - **Sub-Type** (Sottotipo)
       - Selezionare il sottotipo.
     - **Operazione**
       - Selezionare l'operazione o **Show all** (Mostra tutto).
     - **Price Model** (Modello di prezzo)
       - All Upfront (Tutto anticipato)
       - No Upfront (Nessun anticipo)
       - Partial Upfront (Anticipo parziale)
       - On Demand (Su richiesta)
       - Reservation
       - Spot (Contante)
     - **Charge Type** (Tipo di addebito)
       - Selezionare l'addebito di tipo Negative (Negativo), Positive (Positivo) o entrambi.
     - **Tenancy**
       - Indica se un computer è in esecuzione come computer dedicato.
     - **Tipo di utilizzo**
       - Il tipo di utilizzo può essere impostato su tariffe una tantum o tariffe ricorrenti.

5. **Filtri**

    Usare i filtri di selezione multipla o singola per impostare gli intervalli sui valori selezionati. Per impostare un filtro, fare clic su **Add** (Aggiungi) e quindi selezionare le categorie e i valori del filtro.

6. **Cost Model** (Modello di costi)

    Usare questo elenco per selezionare un modello di costi creato in precedenza con Cost Allocation 360 (Allocazione costi 360). È possibile definire più modelli di costi Cloudyn, a seconda dei propri requisiti di allocazione costi. Alcuni team aziendali possono avere requisiti di allocazione costi diversi da quelli di altri team. Pertanto, ogni team può avere un modello di costi dedicato.

    Per informazioni su come creare una definizione di modello di allocazione costi, vedere [Usare tag personalizzati per allocare i costi](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortization** (Ammortamento)

    Usare questo elenco nei report di allocazione costi per visualizzare le spese di servizio non basate sull'utilizzo o i costi "una tantum" e ripartirli in modo uniforme nel corso della vita utile del servizio. Ecco alcuni esempi di spese "una tantum":
    - Spese annuali per il supporto tecnico
    - Spese annuali per i componenti di sicurezza
    - Spese per l'acquisto di istanze riservate
    - Alcuni elementi di Azure Marketplace

   In Amortization (Ammortamento) selezionare **Amortized cost** (Costo ammortizzato) o **Actual Cost** (Costo effettivo).

8. **Risoluzione**

    Usare questo elenco per selezionare la risoluzione dei dati temporali nell'intervallo di date selezionato. La risoluzione determina il numero di unità visualizzate nel report e può essere:
    - Giornaliera
    - Settimanale
    - Mensile
    - Trimestrale
    - Annuale

9. **Allocation rules** (Regole di allocazione)

    Usare questo elenco per applicare o disabilitare il ricalcolo dell'allocazione costi. È possibile abilitare o disabilitare questa operazione di ricalcolo per i dati di fatturazione. Il ricalcolo si applica alle categorie selezionate nel report. Usando questa opzione è possibile valutare l'impatto del ricalcolo dell'allocazione costi rispetto ai dati di fatturazione non elaborati.

10. **Uncategorized** (Senza categoria)

    Usare questo elenco per includere o escludere i costi non assegnati a una categoria nel report.

11. **Show/Hide Fields** (Mostra/Nascondi campi)

    Questa opzione non ha alcun effetto sui report.

12. **Formati di visualizzazione**

    Usare le icone dei formati di visualizzazione per selezionare varie visualizzazioni di grafici o tabelle.

    ![Simboli di formati di visualizzazione che è possibile selezionare](./media/understanding-cost-reports/display-formats.png)

13. **Multi-color** (Multicolore)

    Usare questa opzione per impostare il colore dei grafici nel report.

14. **Actions**

    Usare questa opzione per salvare, esportare o pianificare il report.

15. **Criteri**

    Anche se non sono illustrati, alcuni report includono criteri di calcolo dei costi previsti. In tali report il criterio **Consolidated** visualizza le indicazioni per tutti gli account e le sottoscrizioni con l'entità corrente, ad esempio la registrazione Microsoft o il conto del pagatore AWS. Il criterio **Standalone** visualizza indicazioni per un solo account o una sottoscrizione come se non esistessero altre sottoscrizioni. I criteri selezionati variano in base alla strategia di ottimizzazione usata dall'organizzazione. Le proiezioni dei costi si basano sugli ultimi 30 giorni di utilizzo.

## <a name="save-and-schedule-reports"></a>Salvare e pianificare report

Dopo avere creato un report, è possibile salvarlo per un uso futuro. I report salvati sono disponibili in **My Tools** (Strumenti personali) > **My Reports** (Report personali). Se si apportano modifiche a un report esistente e lo si salva, il report viene salvato come una nuova versione. In alternativa, è possibile salvarlo come un nuovo report.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Salvare un report nel portale Cloudyn

Mentre si visualizza un report, fare clic su **Actions** (Azioni) e quindi selezionare **Save to my reports** (Salva in report personali). Assegnare un nome al report e quindi aggiungere il proprio URL o usare quello creato automaticamente. È possibile scegliere di **condividere** il report pubblicamente con altri utenti dell'organizzazione o di condividerlo nella propria entità. Se il report non viene condiviso, resta un report personale visualizzabile solo dall'utente. Salvare il report.


### <a name="save-a-report-to-cloud-provider-storage"></a>Salvare un report in una risorsa di archiviazione di un provider di servizi cloud

Per salvare un report con un provider di servizi cloud, è necessario avere già configurato un account di archiviazione. Mentre si visualizza un report, fare clic su **Actions** (Azioni) e quindi selezionare **Schedule report** (Pianifica report). Assegnare un nome al report e quindi aggiungere il proprio URL o usare quello creato automaticamente. Selezionare **Save to storage** (Salva in risorsa di archiviazione) e quindi selezionare l'account di archiviazione o aggiungerne uno nuovo. Immettere un prefisso che viene aggiunto al nome del file di report. Selezionare un formato di file CSV o JSON e quindi salvare il report.

### <a name="schedule-a-report"></a>Pianificare un report

È possibile eseguire i report a intervalli pianificati e inviarli a un elenco di destinatari o a un account di archiviazione di un provider di servizi cloud. Mentre si visualizza un report, fare clic su **Actions** (Azioni) e quindi selezionare **Schedule report** (Pianifica report). È possibile inviare il report tramite posta elettronica e salvarlo in un account di archiviazione. In **Schedule** (Pianificazione) selezionare l'intervallo (giornaliera, settimanale o mensile). Per la pianificazione settimanale e mensile, specificare il giorno o le date e selezionare l'ora. Salvare il report pianificato. Se si seleziona il formato di report in Excel, il report viene inviato come allegato. Quando si seleziona il formato di contenuto del messaggio di posta elettronica, i risultati del report che vengono visualizzati in formato grafico sono presentati sotto forma di grafico.

### <a name="export-a-report-as-a-csv-file"></a>Esportare un report come file CSV

Mentre si visualizza un report, fare clic su **Actions** (Azioni) e quindi selezionare **Export all report data** (Esporta tutti i dati del report). Verrà visualizzata una finestra popup e sarà scaricato un file CSV.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui report inclusi in Cloudyn in [Usare i report di Cloudyn](../../cost-management/use-reports.md).
- Informazioni su come usare i report per creare [dashboard](../../cost-management/dashboards.md).
