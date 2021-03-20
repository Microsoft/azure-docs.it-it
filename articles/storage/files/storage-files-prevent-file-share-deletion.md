---
title: Evitare l'eliminazione accidentale - Condivisioni file di Azure
description: Informazioni sulla funzionalità di eliminazione temporanea per le condivisioni file di Azure e su come è possibile usarla per il ripristino dei dati e la prevenzione dell'eliminazione accidentale.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 0fecc9fc954a1ac648e8f60badf69ad1d2e8f1cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93126941"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Evitare l'eliminazione accidentale di condivisioni file di Azure

Archiviazione di Azure mette ora a disposizione la funzionalità di eliminazione temporanea per le condivisioni file. L'eliminazione temporanea consente di ripristinare la condivisione file quando viene erroneamente eliminata da un'applicazione o da un altro utente dell'account di archiviazione.

## <a name="how-soft-delete-works"></a>Funzionamento dell'eliminazione temporanea

Quando l'eliminazione temporanea per le condivisioni file di Azure è abilitata, se viene eliminata una condivisione file, passa a uno stato eliminato temporaneamente anziché essere cancellata definitivamente. È possibile configurare la quantità di tempo durante il quale i dati eliminati temporaneamente sono ripristinabili prima che vengano eliminati definitivamente ed eliminare la condivisione in qualsiasi momento durante questo periodo di conservazione. Dopo l'annullamento dell'eliminazione, la condivisione e tutti i contenuti, inclusi gli snapshot, verranno ripristinati allo stato in cui si trovava prima dell'eliminazione. L'eliminazione temporanea funziona solo su un livello di condivisione file. i singoli file eliminati verranno comunque cancellati definitivamente.

L'eliminazione temporanea può essere abilitata in condivisioni file nuove o esistenti. ed è anche compatibile con le versioni precedenti. Non è quindi necessario apportare modifiche alle applicazioni per usufruire della protezione offerta da questa funzionalità. 

Per eliminare definitivamente una condivisione file in uno stato di eliminazione temporanea prima della scadenza, è necessario annullare l'eliminazione della condivisione, disabilitare l'eliminazione temporanea ed eliminare di nuovo la condivisione. Quindi, è necessario riabilitare l'eliminazione temporanea, poiché tutte le altre condivisioni file nell'account di archiviazione saranno vulnerabili all'eliminazione accidentale mentre l'eliminazione temporanea è disattivata.

Per le condivisioni file Premium eliminate temporaneamente, la quota di condivisione file (la dimensione di cui è stato effettuato il provisioning per la condivisione file) è compresa nel calcolo della quota totale dell'account di archiviazione fino alla scadenza della condivisione eliminata temporaneamente, quando la condivisione viene eliminata completamente.

## <a name="configuration-settings"></a>Impostazioni di configurazione

### <a name="enabling-or-disabling-soft-delete"></a>Abilitazione o disabilitazione dell'eliminazione temporanea

L'eliminazione temporanea per le condivisioni file è abilitata a livello di account di archiviazione. per questo motivo, le impostazioni di eliminazione temporanea si applicano a tutte le condivisioni file in un account di archiviazione. È possibile abilitare o disabilitare l'eliminazione temporanea in qualsiasi momento. Quando si crea un nuovo account di archiviazione, l'eliminazione temporanea per le condivisioni file è disabilitata per impostazione predefinita e può essere abilitata durante la distribuzione o in un momento successivo. Per impostazione predefinita, l'eliminazione temporanea rimarrà disabilitata per gli account di archiviazione esistenti. Se è stato configurato il [backup della condivisione file](../../backup/azure-file-share-backup-overview.md) di Azure per una condivisione file di Azure, l'eliminazione temporanea per le condivisioni file di Azure verrà abilitata automaticamente nell'account di archiviazione della condivisione.

Se si abilita l'eliminazione temporanea per le condivisioni file, si eliminano alcune condivisioni file e quindi si disabilita l'eliminazione temporanea, è comunque possibile accedere a tali condivisioni file e recuperarle, a condizione che siano state salvate quando l'eliminazione temporanea era abilitata. Quando si abilita l'eliminazione temporanea, è anche necessario configurare il periodo di conservazione.

### <a name="retention-period"></a>Periodo di memorizzazione

Il periodo di memorizzazione è la quantità di tempo per cui le condivisioni file eliminate temporaneamente vengono archiviate e disponibili per il ripristino. Per le condivisioni file eliminate in modo esplicito, il calcolo del periodo di conservazione inizia nel momento in cui i dati vengono eliminati. Attualmente è possibile specificare un periodo di conservazione compreso tra 1 e 365 giorni. È possibile modificare il periodo di conservazione dell'eliminazione temporanea in qualsiasi momento. Un periodo di conservazione aggiornato si applica solo alle condivisioni eliminate dopo l'aggiornamento del periodo di conservazione. Le condivisioni eliminate prima dell'aggiornamento del periodo di conservazione scadono in base al periodo di conservazione configurato al momento dell'eliminazione dei dati.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando vengono eliminate temporaneamente, sia le condivisioni file Standard che le condivisioni file Premium vengono fatturate in base alla capacità usata, anziché in base alla capacità di cui è stato effettuato il provisioning. Le condivisioni file Premium nello stato di eliminazione temporanea, poi, vengono fatturate in base alla frequenza di acquisizione snapshot. Le condivisioni file Standard nello stato di eliminazione temporanea vengono fatturate alla tariffa normale. Non si incorre in alcun addebito per i dati che vengono eliminati definitivamente al termine del periodo di conservazione configurato.

Per altre informazioni sui prezzi di Archiviazione file di Azure in generale, vedere la [pagina Prezzi per File di Azure](https://azure.microsoft.com/pricing/details/storage/files/).

Quando si abilita l'eliminazione temporanea per la prima volta, è consigliabile usare un periodo di conservazione ridotto per comprendere meglio in che modo la funzionalità influisca sui costi.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare e usare l'eliminazione temporanea, continuare ad [abilitare l'eliminazione](storage-files-enable-soft-delete.md)temporanea.
