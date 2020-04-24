---
title: Archiviazione BLOB non modificabile-archiviazione di Azure
description: Archiviazione di Azure offre il supporto WORM (Write Once, Read Many) per l'archiviazione di oggetti BLOB, che consente agli utenti di archiviare i dati in uno stato non cancellabile e non modificabile per un intervallo specificato.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367619"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile

L'archiviazione non modificabile per l'archiviazione BLOB di Azure consente agli utenti di archiviare oggetti dati cruciali per l'azienda in un WORM (scrivere una sola volta, leggere molti) stato. Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente. Per la durata dell'intervallo di conservazione, è possibile creare e leggere i BLOB, ma non modificarli o eliminarli. L'archiviazione non modificabile è disponibile per gli account per utilizzo generico V1, per utilizzo generico V2, BlobStorage e BlockBlobStorage in tutte le aree di Azure.

Per informazioni su come impostare e cancellare le condizioni legali o creare criteri di conservazione basati sul tempo usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure, vedere [impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](storage-blob-immutability-policies-manage.md).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Informazioni sull'archiviazione BLOB non modificabile

L'archiviazione non modificabile aiuta l'organizzazione sanitaria, gli istituti&mdash;finanziari e i settori correlati&mdash;, in particolare le organizzazioni broker-dealer a archiviare i dati in modo sicuro. L'archiviazione non modificabile può essere sfruttata anche in qualsiasi scenario per proteggere i dati critici da modifiche o eliminazioni.

Le applicazioni tipiche includono:

- **Conformità alle normative**: l'archiviazione non modificabile per Archiviazione BLOB di Azure aiuta le organizzazioni a soddisfare i requisiti di SEC 17a-4(f), CFTC 1.31(d), FINRA e altre normative. Un white paper tecnico di Cohasset associa informazioni dettagliate su come gli indirizzi di archiviazione non modificabili questi requisiti normativi sono scaricabili tramite il [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). Il [Centro protezione di Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contiene informazioni dettagliate sulle certificazioni di conformità.

- **Conservazione dei documenti sicura**: l'archiviazione non modificabile per l'archiviazione BLOB di Azure garantisce che i dati non possano essere modificati o eliminati da alcun utente, inclusi gli utenti con privilegi amministrativi dell'account.

- **Note legali**: l'archiviazione non modificabile per l'archiviazione BLOB di Azure consente agli utenti di archiviare informazioni riservate cruciali per la controversia o l'uso aziendale in uno stato di prova di manomissione per la durata desiderata fino alla rimozione della sospensione. Questa funzionalità non è limitata solo ai casi d'uso legali, ma può anche essere considerata come un blocco basato su eventi o un blocco aziendale, in cui è necessario proteggere i dati in base ai trigger di evento o ai criteri aziendali.

L'archiviazione non modificabile supporta le funzionalità seguenti:

- **[Supporto dei criteri di conservazione basati sul tempo](#time-based-retention-policies)**: gli utenti possono impostare criteri per archiviare i dati per un intervallo specificato. Quando viene impostato un criterio di conservazione basato sul tempo, è possibile creare e leggere i BLOB, ma non modificarli o eliminarli. Una volta scaduto il periodo di conservazione, i BLOB possono essere eliminati, ma non sovrascritti.

- **[Supporto](#legal-holds)** per i criteri di conservazione legale: se l'intervallo di conservazione non è noto, gli utenti possono impostare le concessioni legali per archiviare dati non modificabili fino a quando non viene deselezionata.  Quando viene impostato un criterio di tenuta legale, è possibile creare e leggere i BLOB, ma non modificarli o eliminarli. Ogni tenuta legale è associata a un tag alfanumerico definito dall'utente, ad esempio un ID del case, un nome di evento e così via, usato come stringa di identificazione. 

- **Supporto per tutti i livelli BLOB**: i criteri WORM sono indipendenti dal livello di Archiviazione BLOB di Azure e si applicano a tutti i livelli (ad accesso frequente, ad accesso sporadico e archivio). Gli utenti possono trasferire i dati nel livello con i costi ottimali per i carichi di lavoro, mantenendo al tempo stesso la non modificabilità dei dati.

- **Configurazione a livello di contenitore**: gli utenti possono configurare criteri di conservazione basati sul tempo e tag di blocco a fini giudiziari a livello di contenitore. Grazie a semplici impostazioni a livello di contenitore, gli utenti possono creare e bloccare i criteri di conservazione basati sul tempo, estendere gli intervalli di conservazione, impostare e rimuovere i blocchi a fini giudiziari e così via. Questi criteri si applicano a tutti i BLOB nel contenitore, nuovi ed esistenti.

- **Supporto**per la registrazione di controllo: ogni contenitore include un log di controllo dei criteri. Mostra fino a sette comandi di conservazione basati sul tempo per i criteri di conservazione basati sul tempo bloccati e contiene l'ID utente, il tipo di comando, i timestamp e l'intervallo di conservazione. Per i blocchi a fini giudiziari, il log contiene l'ID utente, il tipo di comando, i timestamp e i tag di blocco a fini giudiziari. Questo log viene mantenuto per la durata del criterio, in conformità con le linee guida per le normative SEC 17a-4 (f). Il [log attività di Azure](../../azure-monitor/platform/platform-logs-overview.md) Mostra un log più completo di tutte le attività del piano di controllo; Quando si abilitano i [log di diagnostica di Azure](../../azure-monitor/platform/platform-logs-overview.md) , le operazioni del piano dati vengono mantenute. È responsabilità dell'utente archiviare questi log in modo permanente, come potrebbe essere richiesto per scopi legali o di altro tipo.

## <a name="how-it-works"></a>Funzionamento

L'archiviazione non modificabile per Archiviazione BLOB di Azure supporta due tipi di criteri non modificabili o WORM: conservazione basata sul tempo e blocchi a fini giudiziari. Quando si applica un criterio di conservazione basato sul tempo o un periodo di attesa legale a un contenitore, tutti i BLOB esistenti vengono spostati in uno stato non modificabile di un WORM in meno di 30 secondi. Tutti i nuovi BLOB caricati nel contenitore protetto da criteri si sposteranno anche in uno stato non modificabile. Quando tutti i BLOB sono in uno stato non modificabile, i criteri non modificabili vengono confermati e non sono consentite operazioni di sovrascrittura o eliminazione nel contenitore non modificabile.

Anche l'eliminazione di contenitori e account di archiviazione non è consentita se in un contenitore sono presenti BLOB protetti da un blocco o un criterio basato sul tempo. Un criterio di conservazione legale proteggerà da BLOB, contenitori e dall'eliminazione dell'account di archiviazione. I criteri basati sul tempo sbloccato e bloccato proteggeranno dall'eliminazione dei BLOB per il periodo di tempo specificato. I criteri basati sul tempo sbloccato e bloccato proteggeranno dall'eliminazione del contenitore solo se nel contenitore esiste almeno un BLOB. Solo un contenitore con criteri basati sul tempo *bloccato* proteggerà da eliminazioni di account di archiviazione. i contenitori con criteri basati sul tempo sbloccati non offrono la protezione dell'eliminazione dell'account di archiviazione né la conformità.

Per altre informazioni su come impostare e bloccare i criteri di conservazione basati sul tempo, vedere [impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Criteri di conservazione basati sul tempo

> [!IMPORTANT]
> Un criterio di conservazione basato sul tempo deve essere *bloccato* affinché il BLOB sia in uno stato non modificabile conforme (scrittura ed eliminazione protetta) per i secondi 17a-4 (f) e altre normative di conformità. Si consiglia di bloccare il criterio in un periodo di tempo ragionevole, in genere inferiore a 24 ore. Lo stato iniziale di un criterio di conservazione basato sul tempo applicato viene *sbloccato*, consentendo di testare la funzionalità e apportare modifiche ai criteri prima di bloccarlo. Sebbene lo stato *sbloccato* fornisca la protezione dell'immutabilità, non è consigliabile usare lo stato *sbloccato* per qualsiasi scopo, ad eccezione delle versioni di valutazione a breve termine delle funzionalità. 

Quando vengono applicati criteri di conservazione basati sul tempo a un contenitore, tutti i BLOB nel contenitore rimangono nello stato non modificabile per la durata del periodo di conservazione *effettivo*. Il periodo di conservazione effettivo per i BLOB è uguale alla differenza tra l' **ora di creazione** del BLOB e l'intervallo di conservazione specificato dall'utente. Poiché gli utenti possono estendere l'intervallo di conservazione, l'archiviazione non modificabile usa il valore più recente dell'intervallo di conservazione specificato dall'utente per calcolare il periodo di conservazione effettivo.

Si supponga, ad esempio, che un utente crei un criterio di conservazione basato sul tempo con un intervallo di conservazione di cinque anni. Un BLOB esistente nel contenitore, _testblob1_, è stato creato un anno fa; il periodo di conservazione effettivo per _testblob1_ è quindi di quattro anni. Quando un nuovo BLOB, _testblob2_, viene caricato nel contenitore, il periodo di conservazione effettivo per il _testblob2_ è di cinque anni dal momento della creazione.

Un criterio di conservazione basato sul tempo sbloccato è consigliato solo per i test delle funzionalità e i criteri devono essere bloccati per essere conformi ai secondi 17a-4 (f) e ad altri requisiti normativi. Quando un criterio di conservazione basato sul tempo è bloccato, non è possibile rimuovere il criterio e sono consentiti al massimo cinque aumenti del periodo di conservazione effettivo.

Ai criteri di conservazione si applicano i limiti seguenti:

- Per un account di archiviazione, il numero massimo di contenitori con criteri non modificabili basati sul tempo bloccati è 10.000.
- L'intervallo di conservazione minimo è di 1 giorno. Il valore massimo è 146.000 giorni (400 anni).
- Per un contenitore, il numero massimo di modifiche per estendere un intervallo di conservazione per i criteri non modificabili basati sul tempo bloccato è 5.
- Per un contenitore, un massimo di sette log di controllo dei criteri di conservazione basati sul tempo vengono conservati per un criterio bloccato.

### <a name="allow-protected-append-blobs-writes"></a>Consenti Scritture BLOB di Accodamento protette

I BLOB di Accodamento sono costituiti da blocchi di dati e ottimizzati per le operazioni di Accodamento dei dati richieste dagli scenari di controllo e registrazione. Per impostazione predefinita, i BLOB di accodamento consentono solo l'aggiunta di nuovi blocchi alla fine del BLOB. Indipendentemente dall'immutabilità, la modifica o l'eliminazione di blocchi esistenti in un BLOB di Accodamento non è sostanzialmente consentita. Per ulteriori informazioni sui BLOB di Accodamento, vedere [informazioni sui BLOB di Accodamento](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Solo i criteri di conservazione basati sul tempo `allowProtectedAppendWrites` hanno un'impostazione che consente di scrivere nuovi blocchi in un BLOB di Accodamento mantenendo la protezione e la conformità dell'immutabilità. Se abilitata, è possibile creare un BLOB di Accodamento direttamente nel contenitore protetto da criteri e continuare ad aggiungere nuovi blocchi di dati alla fine dei BLOB di Accodamento esistenti usando l'API *AppendBlock* . È possibile aggiungere solo nuovi blocchi ed eventuali blocchi esistenti non possono essere modificati o eliminati. Viene comunque applicata la protezione dell'immutabilità del periodo di conservazione, impedendo l'eliminazione del BLOB di Accodamento finché non è trascorso il periodo di conservazione effettivo. L'abilitazione di questa impostazione non influisce sul comportamento di immutabilità di BLOB in blocchi o BLOB di pagine.

Poiché questa impostazione fa parte di un criterio di conservazione basato sul tempo, i BLOB di Accodamento rimangono nello stato non modificabile per la durata del periodo di conservazione *effettivo* . Poiché i nuovi dati possono essere aggiunti oltre la creazione iniziale del BLOB di Accodamento, esiste una lieve differenza nel modo in cui viene determinato il periodo di conservazione. La conservazione effettiva è la differenza tra l'ora dell' **Ultima modifica** del BLOB e l'intervallo di conservazione specificato dall'utente. Analogamente, quando l'intervallo di conservazione viene esteso, l'archiviazione non modificabile usa il valore più recente dell'intervallo di conservazione specificato dall'utente per calcolare il periodo di conservazione effettivo.

Si supponga, ad esempio, che un utente crei un criterio di conservazione basato `allowProtectedAppendWrites` sul tempo con abilitato e un intervallo di conservazione di 90 giorni. Un BLOB di Accodamento, _logblob1_, viene creato oggi nel contenitore. i nuovi log continuano a essere aggiunti al BLOB di Accodamento per i successivi 10 giorni. il periodo di conservazione effettivo per il _logblob1_ è quindi di 100 giorni da oggi (l'ora dell'ultimo Accodamento + 90 giorni).

I criteri di conservazione basati sul tempo sbloccati consentono di abilitare e disabilitare l' `allowProtectedAppendWrites` impostazione in qualsiasi momento. Quando il criterio di conservazione basato sul tempo è bloccato, `allowProtectedAppendWrites` l'impostazione non può essere modificata.

I criteri di esenzione `allowProtectedAppendWrites` legale non possono essere abilitati e le eventuali esenzioni legali determinino la proprietà' allowProtectedAppendWrites '. Se viene applicata una tenuta legale a un criterio di conservazione basato sul tempo `allowProtectedAppendWrites` con abilitato, l'API *AppendBlock* non riuscirà fino a quando non viene sollevata la tenuta legale.

## <a name="legal-holds"></a>Blocchi a fini giudiziari

Le note legali sono esenzioni temporanee che possono essere usate per scopi di analisi legali o criteri di protezione generale. Ogni criterio di mantenimento delle informazioni legali deve essere associato a uno o più tag. I tag vengono usati come identificatore denominato, ad esempio un ID del case o un evento, per suddividere in categorie e descrivere lo scopo della sospensione.

A un contenitore possono essere applicati contemporaneamente sia criteri di conservazione basati sul tempo che un blocco a fini giudiziari. Tutti i BLOB nel contenitore rimangono nello stato non modificabile finché non vengono rimossi tutti i blocchi a fini giudiziari, anche se il relativo periodo di conservazione effettivo è scaduto. Viceversa, un BLOB rimane in uno stato non modificabile fino alla scadenza del periodo di conservazione effettivo anche se sono stati rimossi tutti i blocchi a fini giudiziari.

I limiti seguenti si applicano alle note legali:

- Per un account di archiviazione, il numero massimo di contenitori con un'impostazione di tenuta legale è 10.000.
- Per un contenitore, il numero massimo di tag di blocco a fini giudiziari è 10.
- La lunghezza minima di un tag di tenuta legale è di tre caratteri alfanumerici. La lunghezza massima è di 23 caratteri alfanumerici.
- Per un contenitore, vengono conservati al massimo 10 log di controllo dei criteri di conservazione per la durata dei criteri.

## <a name="scenarios"></a>Scenari
La tabella seguente illustra i tipi di operazioni di archiviazione BLOB disabilitate per i diversi scenari non modificabili. Per altre informazioni, vedere la documentazione dell' [API REST del servizio BLOB di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Scenario  |Stato BLOB  |Operazioni BLOB negate  |Protezione di contenitori e account
|---------|---------|---------|---------|
|L'intervallo di conservazione effettivo nel BLOB non è ancora scaduto e/o è impostato un blocco a fini giudiziari     |Non modificabile: protetto da eliminazione e scrittura         | Inserire il BLOB<sup>1</sup>, inserire il blocco<sup>1</sup>, inserire l'elenco dei blocchi<sup>1</sup>, eliminare il contenitore, eliminare il BLOB, impostare i metadati dei BLOB, inserire la pagina, impostare le proprietà del BLOB, il BLOB di snapshot, il BLOB di copia incrementale e il blocco<sup>2</sup>         |Eliminazione del contenitore negata; Eliminazione dell'account di archiviazione negata         |
|L'intervallo di conservazione effettivo sul BLOB è scaduto e non è impostata alcuna esenzione legale    |Solo protetto da scrittura (le operazioni di eliminazione sono consentite)         |Inserire il BLOB<sup>1</sup>, inserire il blocco<sup>1</sup>, inserire l'elenco dei blocchi<sup>1</sup>, impostare i metadati del BLOB, inserire la pagina, impostare le proprietà del BLOB, il BLOB di snapshot, il BLOB di copia incrementale, il blocco<sup>2</sup>         |L'eliminazione del contenitore è stata negata se nel contenitore protetto esiste almeno un BLOB; Eliminazione dell'account di archiviazione negata solo per i criteri basati sul tempo *bloccati*         |
|Nessun criterio WORM applicato (nessuna conservazione basata sul tempo e nessun tag di tenuta legale)     |Modificabile         |nessuno         |nessuno         |

<sup>1</sup> il servizio BLOB consente a queste operazioni di creare un nuovo BLOB una sola volta. Non sono consentite tutte le operazioni di sovrascrittura successive in un percorso BLOB esistente in un contenitore non modificabile.

<sup>2</sup> il blocco Append è consentito solo per i criteri di conservazione basati sul `allowProtectedAppendWrites` tempo con la proprietà abilitata. Per ulteriori informazioni, vedere la sezione [Consenti le scritture di Accodamento BLOB protetti](#allow-protected-append-blobs-writes) .

## <a name="pricing"></a>Prezzi

Per l'uso di questa funzionalità non sono previsti costi aggiuntivi. I prezzi dei dati non modificabili sono identici a quelli dei dati modificabili. Per informazioni dettagliate sui prezzi di archiviazione BLOB di Azure, vedere la [pagina dei prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Domande frequenti

**È possibile fornire la documentazione della conformità del WORM?**

Sì. Per documentare la conformità, Microsoft ha mantenuto una società di valutazione indipendente leader specializzata nella gestione dei record e nella governance delle informazioni, Cohasset Associates, per valutare l'archiviazione BLOB non modificabile e la rispettiva conformità con i requisiti specifici del settore dei servizi finanziari. Cohasset convalidato che l'archiviazione BLOB non modificabile, se usata per conservare i BLOB basati sul tempo in uno stato di WORM, soddisfa i requisiti di archiviazione rilevanti della regola CFTC 1.31 (c)-(d), regola FINRA 4511 e regola SEC 17a-4. Microsoft ha mirato a questo set di regole, in quanto rappresentano le linee guida più prescritte a livello globale per la conservazione dei record per gli istituti finanziari. Il report Cohasset è disponibile nel [Centro protezione dei servizi Microsoft](https://aka.ms/AzureWormStorage). Per richiedere una lettera di attestazione da Microsoft relativa alla conformità dell'immutabilità del WORM, contattare il supporto tecnico di Azure.

**La funzionalità è valida solo per i BLOB in blocchi e i BLOB di accodamento o anche per i BLOB di pagine?**

L'archiviazione non modificabile può essere usata con qualsiasi tipo di BLOB perché è impostata a livello di contenitore, ma è consigliabile usare WORM per i contenitori che prevedono principalmente l'archiviazione di BLOB in blocchi e BLOB di Accodamento. I BLOB esistenti in un contenitore saranno protetti da criteri WORM appena impostati. Tuttavia, tutti i nuovi BLOB di pagine devono essere creati all'esterno del contenitore WORM e quindi copiati in. Una volta copiato in un contenitore WORM, non sono consentite altre modifiche a un BLOB di pagine. L'impostazione di un criterio WORM in un contenitore in cui sono archiviati dischi rigidi virtuali (BLOB di pagine) per tutte le macchine virtuali attive è sconsigliata perché bloccherà il disco della VM. È consigliabile esaminare accuratamente la documentazione e testare gli scenari prima di bloccare i criteri basati sul tempo.

**È necessario creare un nuovo account di archiviazione per usare questa funzionalità?**

No, è possibile usare una risorsa di archiviazione non modificabile con gli account per utilizzo generico V1, per utilizzo generico V2, BlobStorage o BlockBlobStorage esistenti o appena creati. Gli account di archiviazione per utilizzo generico V1 sono supportati, ma è consigliabile eseguire l'aggiornamento a utilizzo generico V2, in modo che sia possibile sfruttare altre funzionalità. Per informazioni sull'aggiornamento di un account di archiviazione di uso generico V1 esistente, vedere [aggiornare un account di archiviazione](../common/storage-account-upgrade.md).

**È possibile applicare un criterio di conservazione legale e basato sul tempo?**

Sì, un contenitore può avere contemporaneamente un criterio di conservazione e un criterio di conservazione basato sul tempo; Tuttavia, l'impostazione ' allowProtectedAppendWrites ' non verrà applicata fino a quando non viene cancellata la tenuta legale. Tutti i BLOB nel contenitore rimangono nello stato non modificabile finché non vengono rimossi tutti i blocchi a fini giudiziari, anche se il relativo periodo di conservazione effettivo è scaduto. Viceversa, un BLOB rimane in uno stato non modificabile fino alla scadenza del periodo di conservazione effettivo anche se sono stati rimossi tutti i blocchi a fini giudiziari. 

**I criteri di mantenimento sono validi solo per le procedure legali o esistono altri scenari di utilizzo?**

No, l'esenzione legale è solo il termine generico usato per un criterio di conservazione non basato sul tempo. Non è necessario usarlo solo per le procedure correlate ai contenziosi. I criteri di conservazione validi sono utili per disabilitare la sovrascrittura e le eliminazioni per la protezione di importanti dati WORM aziendali, in cui il periodo di conservazione è sconosciuto. È possibile usarlo come criterio aziendale per proteggere i carichi di lavoro di un WORM cruciale o usarlo come criterio di gestione temporanea prima che un trigger di evento personalizzato richieda l'uso di un criterio di conservazione basato sul tempo. 

**È possibile rimuovere un criterio di conservazione basato sul tempo _bloccato_ o un blocco legale?**

Solo i criteri di conservazione basati sul tempo sbloccati possono essere rimossi da un contenitore. Quando un criterio di conservazione basato sul tempo è bloccato, non può essere rimosso. sono consentite solo le estensioni del periodo di memorizzazione effettivo. I tag di esenzione legali possono essere eliminati. Quando vengono eliminati tutti i tag legali, viene rimossa la tenuta legale.

**Che cosa accade se si tenta di eliminare un contenitore con un criterio di conservazione basato sul tempo o una tenuta legale?**

L'operazione di eliminazione del contenitore avrà esito negativo se almeno un BLOB è presente nel contenitore con un criterio di conservazione basato sul tempo bloccato o sbloccato o se il contenitore dispone di un blocco valido. L'operazione Delete Container avrà esito positivo solo se all'interno del contenitore non sono presenti BLOB e non è presente alcun contenuto valido. 

**Che cosa accade se si tenta di eliminare un account di archiviazione con un contenitore con un criterio di conservazione basato sul tempo o una tenuta legale?**

L'eliminazione dell'account di archiviazione avrà esito negativo se è presente almeno un contenitore con un set di supporti validi o un criterio basato sul tempo **bloccato** . Un contenitore con criteri basati sul tempo sbloccati non protegge dall'eliminazione dell'account di archiviazione. Prima di poter eliminare l'account di archiviazione, è necessario rimuovere tutte le esenzioni legali ed eliminare tutti i contenitori **bloccati** . Per informazioni sull'eliminazione di contenitori, vedere la domanda precedente. È anche possibile applicare ulteriori protezioni di eliminazione per l'account di archiviazione con [blocchi Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).

**È possibile spostare i dati tra livelli BLOB diversi (accesso frequente, accesso sporadico o Archivio) quando il BLOB si trova nello stato non modificabile?**

Sì, è possibile usare il comando Imposta livello BLOB per spostare i dati tra i livelli BLOB mantenendo i dati nello stato non modificabile conforme. L'archiviazione non modificabile è supportata nei livelli BLOB ad accesso frequente, ad accesso sporadico e archivio.

**Che cosa accade se non si effettua il pagamento e il periodo di conservazione non è scaduto?**

In caso di mancato pagamento, verranno applicati i normali criteri di conservazione dati previsti nei termini e nelle condizioni del contratto stipulato con Microsoft. Per informazioni generali, vedere [gestione dei dati in Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**È disponibile una versione di valutazione o un periodo di tolleranza per provare la funzionalità?**

Sì. Quando un criterio di conservazione basato sul tempo viene creato per la prima volta, è in uno stato *sbloccato* . In questo stato è possibile apportare qualsiasi modifica all'intervallo di conservazione, ad esempio aumentandolo o riducendolo, ed è anche possibile eliminare i criteri. Dopo che sono stati bloccati, i criteri rimangono bloccati fino alla scadenza dell'intervallo di conservazione. Questo criterio bloccato impedisce l'eliminazione e la modifica dell'intervallo di conservazione. È consigliabile usare lo stato *sbloccato* solo per scopi di valutazione e bloccare i criteri entro un periodo di 24 ore. Ciò consente la conformità a SEC 17a-4(f) e altre normative.

**È possibile usare l'eliminazione temporanea insieme ai criteri BLOB non modificabili?**

Sì, se i requisiti di conformità consentono l'abilitazione dell'eliminazione temporanea. L' [eliminazione temporanea per l'archiviazione BLOB di Azure](storage-blob-soft-delete.md) si applica a tutti i contenitori in un account di archiviazione indipendentemente da un criterio di conservazione legale o basato sul tempo. È consigliabile abilitare l'eliminazione temporanea per una protezione aggiuntiva prima di applicare e confermare eventuali criteri WORM non modificabili.

## <a name="next-steps"></a>Passaggi successivi

- [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](storage-blob-immutability-policies-manage.md)
- [Impostare regole per eseguire automaticamente il livello ed eliminare i dati BLOB con la gestione del ciclo di vita](storage-lifecycle-management-concepts.md)
- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../blobs/storage-blob-soft-delete.md)
- [Proteggere sottoscrizioni, gruppi di risorse e risorse con Azure Resource Manager blocchi](../../azure-resource-manager/management/lock-resources.md).
