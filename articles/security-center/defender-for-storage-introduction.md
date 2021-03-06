---
title: Azure Defender per Archiviazione - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Archiviazione.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 42aa07ccf8d886dc7eb7109bc405c730331b2c3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095630"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introduzione ad Azure Defender per Archiviazione

**Azure Defender per Archiviazione** offre un livello di intelligence di sicurezza nativo di Azure che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione. Sfrutta le funzionalità avanzate di intelligenza artificiale e di [intelligence sulle minacce Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) per fornire avvisi e raccomandazioni contestuali sulla sicurezza.

Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. Questi avvisi sono integrati con il Centro sicurezza di Azure e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sull'attività sospetta e le raccomandazioni su come analizzare e correggere le minacce.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|**Azure Defender per l'archiviazione** viene fatturato come indicato nei [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/)|
|Tipi di archiviazione protetti:|[Archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/)<br>[File di Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Quali sono i vantaggi di Azure Defender per Archiviazione?

Azure Defender per Archiviazione offre:

- **Sicurezza nativa di Azure**: Defender per Archiviazione consente di abilitare con un solo clic la protezione dei dati archiviati in BLOB di Azure, File di Azure e data lake. In quanto servizio nativo di Azure, Defender per Archiviazione garantisce una sicurezza centralizzata per tutti gli asset di dati gestiti da Azure ed è integrato con altri servizi di sicurezza di Azure come Azure Sentinel.
- **Soluzioni di rilevamento avanzate**: basati sulla tecnologia di intelligence sulle minacce Microsoft, i rilevamenti in Defender per Archiviazione interessano le principali minacce per l'archiviazione, come l'accesso anonimo, la compromissione delle credenziali, il social engineering, l'abuso dei privilegi e i contenuti dannosi.
- **Risposta su larga scala**: grazie agli strumenti di automazione del Centro sicurezza, è più facile prevenire e rispondere alle minacce identificate. Per altre informazioni, vedere [Automatizzare le risposte ai trigger del Centro sicurezza](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Panoramica generale delle funzionalità di Azure Defender per Archiviazione":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quali tipi di avvisi vengono forniti da Azure Defender per Archiviazione?

Gli avvisi di sicurezza vengono attivati in caso di:

- **Modelli di accesso sospetti**, ad esempio l'accesso riuscito da un nodo di uscita Tor o da un indirizzo IP considerato sospetto dall'intelligence sulle minacce Microsoft
- **Attività sospette**, ad esempio l'estrazione anomala di dati o una modifica insolita delle autorizzazioni di accesso
- **Upload di contenuto dannoso**, ad esempio potenziali file di malware (in base all'analisi della reputazione hash) o hosting di contenuto di phishing

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati e raccomandazioni su come analizzare e risolvere le minacce. Gli avvisi possono essere esportati in Azure Sentinel o in qualsiasi altro sistema SIEM di terze parti o strumento esterno.

> [!TIP]
> Come procedura consigliata, [configurare Azure Defender per Archiviazione](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) a livello di sottoscrizione, sebbene sia possibile [configurarlo anche nei singoli account di archiviazione](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Cos'è l'analisi della reputazione hash per il malware?

Per determinare se un file caricato è sospetto, Azure Defender per Archiviazione usa l'analisi della reputazione hash supportata dalla funzionalità di [intelligence sulle minacce Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). Gli strumenti di protezione dalle minacce non analizzano i file caricati, bensì esaminano i log di archiviazione e confrontano gli hash dei nuovi file caricati con quelli di virus, trojan, spyware e ransomware noti. 

Se sospetta che un file contenga malware, il Centro sicurezza visualizza un avviso e può facoltativamente inviare un messaggio di posta elettronica al proprietario dell'archiviazione per chiedere l'approvazione per eliminare il file sospetto. Per configurare questa rimozione automatica dei file che l'analisi della reputazione hash indica come contenenti malware, distribuire un'[automazione del flusso di lavoro che venga attivata da avvisi che contengono "Potenziale malware caricato in un account di archiviazione"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza, è necessario abilitare Azure Defender nella sottoscrizione contenente i carichi di lavoro applicabili.
>
> È possibile abilitare **Azure Defender per Archiviazione** a livello di sottoscrizione o di risorsa.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Attivare un avviso di test per Azure Defender per l'archiviazione

Per testare gli avvisi di sicurezza da Azure Defender per l'archiviazione nell'ambiente, generare l'avviso "accesso da un nodo di uscita Tor a un account di archiviazione" con i passaggi seguenti:

1. Aprire un account di archiviazione con Azure Defender per l'archiviazione abilitata.
1. Dalla barra laterale selezionare "contenitori" e aprire un contenitore esistente o crearne uno nuovo.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Apertura di un contenitore BLOB da un account di archiviazione di Azure" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Caricare un file in tale contenitore.

    > [!CAUTION]
    > Non caricare un file contenente dati riservati.

1. Usare il menu di scelta rapida del file caricato per selezionare "genera SAS".

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="Opzione genera firma di accesso condiviso per un file in un contenitore BLOB":::

1. Lasciare le opzioni predefinite e selezionare **genera token SAS e URL**.

1. Copiare l'URL SAS generato.

1. Nel computer locale aprire il browser Tor.

    > [!TIP]
    > È possibile scaricare Tor dal sito del progetto Tor [https://www.torproject.org/download/](https://www.torproject.org/download/) .

1. Nel browser Tor passare all'URL di firma di accesso condiviso.

1. Scaricare il file caricato nel passaggio 3.

    Entro due ore si otterrà il seguente avviso di sicurezza dal centro sicurezza:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Avviso di sicurezza relativo all'accesso da un nodo di uscita Tor":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per Archiviazione.

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).
- [Come abilitare Azure Defender per Archiviazione](../storage/common/azure-defender-storage-configure.md)
- [Elenco degli avvisi di Azure Defender per Archiviazione](alerts-reference.md#alerts-azurestorage)
- [Funzionalità dell'intelligence sulle minacce di Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)