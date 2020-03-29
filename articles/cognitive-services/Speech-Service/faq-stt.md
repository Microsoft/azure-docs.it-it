---
title: Domande frequenti sul Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Risposte alle domande frequenti sul servizio Di sintesi vocale.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168454"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Domande frequenti sul Servizio di riconoscimento vocale

Se in questo documento non sono presenti risposte alle domande di proprio interesse, vedere le [altre opzioni di supporto](support.md).

## <a name="general"></a>Generale

**D: Qual è la differenza tra un modello di base e un modello di riconoscimento vocale personalizzato?**

**R**: Un modello di base è stato sottoposto a training con dati di proprietà di Microsoft ed è già stato distribuito nel cloud. È possibile usare un modello personalizzato per adattare un modello a un determinato ambiente con una lingua o rumori di fondo particolari. Fabbriche, automobili o strade rumorose richiederebbero un modello acustico adattato. Argomenti come biologia, fisica, radiologia, nomi di prodotti e acronimi personalizzati richiederebbero un modello di lingua adattato.

**D: Da dove è necessario iniziare per usare un modello di base?**

**R**: Prima di tutto è necessario ottenere una [chiave di sottoscrizione](get-started.md). Per effettuare chiamate REST ai modelli di base predistribuiti, vedere le [API REST](rest-apis.md). Se si desidera usare WebSocket, [scaricare l'SDK](speech-sdk.md).

**: È sempre necessario compilare un modello conversione voce/testo personalizzato?**

**R:** No. Se l'applicazione usa un linguaggio quotidiano generico, non è necessario personalizzare un modello. Se l'applicazione viene usata in un ambiente in cui il rumore di fondo è scarso o addirittura assente, non è necessario personalizzare un modello.

È possibile distribuire modelli di base e personalizzati nel portale e quindi sottoporli a test di accuratezza. Questa funzionalità può essere usata per misurare l'accuratezza di un modello di base rispetto a un modello personalizzato.

**D: Come è possibile sapere quando l'elaborazione del set di dati o del modello è completa?**

**R**: Attualmente l'unica indicazione è lo stato del modello o del set di dati nella tabella. Quando l'elaborazione è completa, lo stato è **Succeded** (Operazione completata).

**D: È possibile creare più modelli?**

**R**: Non sono previsti limiti al numero di modelli che si può avere nella propria raccolta.

**D: Mi sono reso conto di aver commesso un errore. Come si annulla l'importazione dei dati o la creazione del modello in corso?**

**R**: Al momento non è possibile eseguire il rollback di un processo di adattamento di un modello acustico o linguistico. I modelli e i dati importati possono essere eliminati quando sono in uno stato terminale.

**D: Qual è la differenza tra i modelli Search and Dictation (Ricerca e dettatura) e Conversational (Colloquiale)?**

**R**: Sono disponibili più modelli di base tra cui scegliere nel Servizio di riconoscimento vocale. Il modello Conversational (Colloquiale) è utile per il riconoscimento vocale di una conversazione. Questo modello è ideale per la trascrizione delle chiamate telefoniche. Il modello Search and Dictation (Ricerca e dettatura) è ideale per le app con attivazione vocale. Il modello Universal (Universale) è un nuovo modello che mira a risolvere entrambi questi scenari. Il modello universale è attualmente a un livello di qualità pari o superiore rispetto al modello colloquiale nella maggior parte delle impostazioni locali.

**D: È possibile aggiornare un modello esistente (stacking di modelli)?**

**R**: Non è possibile aggiornare un modello esistente. Come soluzione, combinare il set di dati precedente con quello nuovo e riadattarlo.

I set di dati precedente e nuovo devono essere combinati in un unico file ZIP (per dati acustici) o in un file con estensione txt (per dati linguistici). Dopo aver terminato l'adattamento, per ottenere un nuovo endpoint è necessario ridistribuire il nuovo modello aggiornato

**D: Quando è disponibile una nuova versione di una linea di base, la distribuzione viene aggiornata automaticamente?**

**R**: Le distribuzioni NON vengono aggiornate automaticamente.

Se è stato adattato e distribuito un modello con baseline V1.0, tale distribuzione rimarrà invariata. I clienti possono rimuovere le autorizzazioni del modello distribuito, riadattarlo utilizzando la versione più recente della linea di base e ridistribuire.

**D: È possibile scaricare il modello ed eseguirlo in locale?**

**R**: Non è possibile scaricare i modelli ed eseguirli in locale.

**D: Le richieste vengono registrate?**

**R**: È possibile scegliere di disattivare la traccia quando si crea la distribuzione. Così facendo audio e trascrizioni non verranno registrati. In caso contrario, le richieste vengono in genere registrate nel servizio di archiviazione sicura in Azure.

**D: Le richieste sono limitate?**

**R**: l'API REST limita le richieste a 25 ogni 5 secondi. Informazioni dettagliate sono disponibili nelle pagine relative al [Riconoscimento vocale](speech-to-text.md).

**D: Come vengono addebitati i messaggi audio a doppio canale?**

**R:** Se invii ogni canale separatamente (ogni canale nel proprio file), ti verrà addebitato per la durata di ogni file. Se invii un singolo file con ogni canale multiplexed together, ti verrà addebitato per la durata del singolo file. Per informazioni dettagliate sui prezzi, fare riferimento alla pagina dei prezzi di [Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)di Azure.

> [!IMPORTANT]
> In caso di altri problemi di privacy che impediscono l'uso del Servizio di riconoscimento vocale personalizzato, contattare uno dei canali di supporto.

## <a name="increasing-concurrency"></a>Aumento della concorrenza

**D: Che cosa è necessario fare se serve una concorrenza più elevata per il modello distribuito rispetto a ciò che viene offerto nel portale?**

**R**: È possibile aumentare le prestazioni del modello in incrementi di 20 richieste simultanee.

Con le informazioni necessarie, creare una richiesta di supporto nel portale di supporto di Azure.With the required information, create a support request in the [Azure support portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Non pubblicare le informazioni su nessuno dei canali pubblici (GitHub, Stackoverflow, ...) menzionati nella pagina di [supporto](support.md).

Per aumentare la concorrenza per un ***modello personalizzato,*** sono necessarie le informazioni seguenti:To increase concurrency for a custom model , we need the following information:

- L'area in cui viene distribuito il modello,
- l'ID dell'endpoint del modello distribuito:
  - Arrivato al [portale di riconoscimento vocale personalizzato](https://aka.ms/customspeech),
  - accedere (se necessario),
  - selezionare il progetto e la distribuzione,
  - selezionare l'endpoint per il cui è necessario l'aumento della concorrenza,
  - copiare `Endpoint ID`il file .

Per aumentare la concorrenza per un modello di ***base,*** sono necessarie le informazioni seguenti:To increase concurrency for a base model , we need the following information:

- La regione del vostro servizio,

e sia

- un token di accesso per la sottoscrizione (vedere [qui),](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)

o

- l'ID risorsa per la sottoscrizione:
  - Vai al portale di [Azure,](https://portal.azure.com)
  - selezionare `Cognitive Services` nella casella di ricerca,
  - dai servizi visualizzati selezionare il servizio di riconoscimento vocale per il quale si desidera aumentare la concorrenza,
  - visualizzare `Properties` il per questo servizio,
  - copiare `Resource ID`il file complete .

## <a name="importing-data"></a>Importazione di dati

**D: Qual è il limite di dimensione per un set di dati e perché esiste tale limite?**

**R**: Il limite attuale per un set di dati è 2 GB. Tale limite è dovuto ai vincoli di dimensione dei file per il caricamento HTTP.

**D**: È possibile comprimere i file di testo per poter caricare file più grandi?

**R:** No. Attualmente sono ammessi solo file di testo non compressi.

**D: Il rapporto dati indica che sono state superate le espressioni. Qual è il problema?**

**R**: Se non viene caricato il 100% delle espressioni di un file, non è un problema. Se la grande maggioranza, ad esempio più del 95%, delle espressioni in un set di dati acustico o linguistico viene importata correttamente, il set di dati può essere utilizzabile. È tuttavia consigliabile cercare di comprendere il motivo dell'esito negativo delle espressioni e risolvere i problemi. I problemi più comuni, ad esempio gli errori di formattazione, sono facili da risolvere.

## <a name="creating-an-acoustic-model"></a>Creazione di un modello acustico

**D: Quanti dati acustici sono necessari?**

**R**: È consigliabile iniziare con dati acustici di lunghezza compresa tra 30 minuti e un'ora.

**D: Quali dati è necessario raccogliere?**

**R**: Raccogliere dati più vicini possibile allo scenario e al caso d'uso dell'applicazione. La raccolta dei dati deve corrispondere all'applicazione e agli utenti di destinazione in termini di dispositivo o dispositivi, ambienti e tipi di parlanti. In genere, è consigliabile raccogliere dati dalla più ampia varietà di parlanti possibile.

**D: Come si deve raccogliere i dati acustici?**

**R**: È possibile creare un'applicazione di raccolta dati autonoma o usare un software di registrazione audio standard. È anche possibile creare una versione dell'applicazione in grado di registrare e quindi usare i dati audio.

**D: È necessario trascrivere personalmente i dati di adattamento?**

**R:** Sì! È possibile farlo personalmente o usando un servizio di trascrizione professionale. Alcuni utenti preferiscono usare sistemi di trascrizione professionali, mentre altri usano il crowdsourcing o eseguono le trascrizioni autonomamente.

## <a name="accuracy-testing"></a>Test di accuratezza

**D: È possibile eseguire il testing offline del modello acustico personalizzato usando un modello linguistico personalizzato?**

**R**: Sì, è sufficiente selezionare il modello linguistico personalizzato nel menu a discesa durante la configurazione del test offline.

**D: È possibile eseguire il testing offline del modello linguistico personalizzato usando un modello acustico personalizzato?**

**R**: Sì, è sufficiente selezionare il modello acustico personalizzato nel menu a discesa durante la configurazione del test offline.

**D: Che cos'è la frequenza degli errori di parola (WER) e come viene calcolata?**

**R**: La frequenza degli errori di parola (WER) è la metrica di valutazione per il riconoscimento vocale. Viene calcolata come numero totale di errori, inclusi inserimenti, eliminazioni e sostituzioni, diviso per il numero totale di parole nella trascrizione di riferimento. Per altre informazioni, vedere [frequenza degli errori di parola](https://en.wikipedia.org/wiki/Word_error_rate).

**D: Come si determina se i risultati di un test di accuratezza sono positivi?**

**R**: I risultati mostrano un confronto tra il modello di base e quello personalizzato. Perché la personalizzazione sia proficua, è necessario che sia più efficiente del modello di base.

**D: Come si determina la frequenza degli errori di parola di un modello di base in modo da rendersi conto se c'è stato un miglioramento?**

**R**: I risultati dei test offline mostrano l'accuratezza di base del modello personalizzato e il miglioramento rispetto al modello di base.

## <a name="creating-a-language-model"></a>Creazione di un modello linguistico

**D**: Quanti dati di testo è necessario caricare?

**R**: Dipende da quanto il vocabolario e le frasi utilizzate nell'applicazione differiscono dai modelli linguistici iniziali. Per tutte le parole nuove è utile specificare il maggior numero possibile di esempi di utilizzo. Per le frasi frequenti usate nell'applicazione è utile anche includere frasi nei dati linguistici perché indicano al sistema di rimanere in ascolto anche di questi termini. È frequente che nel set di dati linguistici siano presenti almeno 100 espressioni, di solito ce ne sono almeno diverse centinaia o più. Se inoltre si prevede che alcuni tipi di query siano più comuni di altri, è possibile inserire nel set di dati varie copie delle query più comuni.

**D: È possibile caricare semplicemente un elenco di parole?**

**R**: Il caricamento di un elenco di parole consente di aggiungere le parole nel vocabolario ma non di insegnare al sistema come le parole stesse vengono generalmente usate. Se si specificano espressioni complete o parziali, ovvero frasi o espressioni di probabile uso da parte degli utenti, il modello linguistico può apprendere le nuove parole e come vengono usate. Il modello linguistico personalizzato è utile non solo per aggiungere nuove parole nel sistema ma anche per modificare la probabilità di parole note per l'applicazione. L'indicazione di espressioni complete favorisce l'apprendimento da parte del sistema.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Modello di tenant (riconoscimento vocale personalizzato con dati di Office 365)

**D: Quali informazioni sono incluse nel modello tenant e come vengono create?**

**A:** Un modello di tenant viene creato utilizzando messaggi di posta elettronica e documenti di [gruppo pubblico](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) che possono essere visualizzati da chiunque nell'organizzazione.

**D: Quali esperienze vocali vengono migliorate dal modello Tenant?**

**A:** Quando il modello tenant viene abilitato, creato e pubblicato, viene utilizzato per migliorare il riconoscimento per tutte le applicazioni aziendali create utilizzando il servizio di riconoscimento vocale. che passano anche un token AAD utente che indica l'appartenenza all'azienda.

Le esperienze vocali incorporate in Office 365, ad esempio Dettatura e Sottotitoli di PowerPoint, non vengono modificate quando si crea un modello tenant per le applicazioni del servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi](troubleshooting.md)
- [Note sulla versione](releasenotes.md)
