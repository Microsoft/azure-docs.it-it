---
title: Monitorare l'attività di migrazione-servizio migrazione del database di Azure
description: Informazioni sull'uso del Servizio Migrazione del database di Azure per monitorare l'attività di migrazione.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 31b49cdd9e0e5569981b2a0b0c6efcab7239e019
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77648513"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorare l'attività di migrazione con il servizio migrazione del database di Azure
Questo articolo illustra come monitorare lo stato di avanzamento di una migrazione sia a livello di database sia a livello di tabella.

## <a name="monitor-at-the-database-level"></a>Monitoraggio a livello di database
Per monitorare l'attività a livello di database, visualizzare il pannello corrispondente:

![Pannello a livello di database](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Se si seleziona il collegamento ipertestuale del database, verrà visualizzato l'elenco di tabelle e lo stato di avanzamento della relativa migrazione.

Nella tabella seguente sono elencati i campi del pannello a livello di database e sono descritti i diversi valori di stato associati a ogni campo.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nome campo</strong></th>
      <th><strong>Stato secondario campo</strong></th>
      <th><strong>Descrizione</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Stato attività</strong></td>
      <td>In esecuzione</td>
      <td>Attività di migrazione in esecuzione.</td>
    </tr>
    <tr>
      <td>Operazione riuscita</td>
      <td>Attività di migrazione completata senza problemi.</td>
    </tr>
    <tr>
      <td>Con errori</td>
      <td>Migrazione non riuscita. Selezionare il collegamento "Vedere i dettagli dell'errore" nei dettagli della migrazione per visualizzare il messaggio di errore completo.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Inizializzazione in corso</td>
      <td>Il Servizio Migrazione del database sta configurando la pipeline di migrazione.</td>
    </tr>
    <tr>
      <td>In esecuzione</td>
      <td>La pipeline del Servizio Migrazione del database è in esecuzione e sta eseguendo la migrazione.</td>
    </tr>
    <tr>
      <td>Operazione completata</td>
      <td>Migrazione completata.</td>
    </tr>
    <tr>
      <td>Operazione non riuscita</td>
      <td>Migrazione non riuscita. Fare clic sui dettagli della migrazione per visualizzare gli errori di migrazione.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Dettagli della migrazione</strong></td>
      <td>Inizializzazione della pipeline di migrazione</td>
      <td>Il Servizio Migrazione del database sta configurando la pipeline di migrazione.</td>
    </tr>
    <tr>
      <td>Caricamento completo dei dati in corso</td>
      <td>Il Servizio Migrazione del database sta eseguendo il caricamento iniziale.</td>
    </tr>
    <tr>
      <td>Pronto per il cutover</td>
      <td>Dopo aver completato il caricamento iniziale, il Servizio Migrazione del database contrassegnerà il database come pronto per la migrazione completa. È necessario controllare se i dati sono aggiornati rispetto alla sincronizzazione continua.</td>
    </tr>
    <tr>
      <td>Tutte le modifiche sono state applicate</td>
      <td>Il caricamento iniziale e la sincronizzazione continua sono stati completati. Questo stato si verifica anche quando la migrazione completa del database è avvenuta correttamente.</td>
    </tr>
    <tr>
      <td>Vedere i dettagli dell'errore</td>
      <td>Fare clic sul collegamento per visualizzare i dettagli dell'errore.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Duration</strong></td>
      <td>N/D</td>
      <td>Tempo totale dall'inizializzazione dell'attività di migrazione al completamento della migrazione con o senza errori.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitoraggio a livello di tabella: riepilogo rapido
Per monitorare l'attività a livello di tabella, visualizzare il pannello corrispondente. Nella parte superiore del pannello è indicato il numero dettagliato di righe di cui è stata eseguita la migrazione durante il caricamento completo e gli aggiornamenti incrementali. 

Nella parte inferiore del pannello sono elencate le tabelle ed è presente un breve riepilogo dello stato di avanzamento della migrazione.

![Pannello a livello di tabella: riepilogo rapido](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

La tabella seguente descrive i campi visualizzati nei dettagli a livello di tabella.

| Nome campo        | Descrizione       |
| ------------- | ------------- |
| **Caricamento completo completato**      | Numero di tabelle per cui il caricamento completo dei dati è stato completato. |
| **Caricamento completo accodato**      | Numero di tabelle in coda per il caricamento completo.      |
| **Caricamento completo in corso** | Numero di tabelle per cui il caricamento completo non è riuscito.      |
| **Aggiornamenti incrementali**      | Numero di aggiornamenti CDC (Change Data Capture) nelle righe applicati alla destinazione. |
| **Inserimenti incrementali**      | Numero di inserimenti CDC nelle righe applicati alla destinazione.      |
| **Eliminazioni incrementali** | Numero di eliminazioni CDC nelle righe applicate alla destinazione.      |
| **Modifiche in sospeso**      | Numero di CDC nelle righe in attesa di essere applicati alla destinazione. |
| **Modifiche applicate**      | Totale di aggiornamenti, inserimenti ed eliminazioni CDC nelle righe applicati alla destinazione.      |
| **Tabelle in stato di errore** | Numero di tabelle che si trovano in stato di errore durante la migrazione. Le tabelle possono trovarsi in stato di errore quando ad esempio vengono identificati dei duplicati nella destinazione o quando i dati non sono compatibili con il caricamento nella tabella di destinazione.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitoraggio a livello di tabella: riepilogo dettagliato
Sono presenti due schede che mostrano lo stato di avanzamento della migrazione per Caricamento completo e Sincronizzazione incrementale dei dati.
    
![Scheda Caricamento completo](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Scheda Sincronizzazione dei dati incrementale](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

La tabella seguente descrive i campi visualizzati nello stato di avanzamento della migrazione a livello di tabella.

| Nome campo        | Descrizione       |
| ------------- | ------------- |
| **Stato - Sincronizzazione**      | Sincronizzazione continua in esecuzione. |
| **Inserimento**      | Numero di inserimenti CDC nelle righe applicati alla destinazione.      |
| **Update** | Numero di aggiornamenti CDC nelle righe applicati alla destinazione.      |
| **Eliminazione**      | Numero di eliminazioni CDC nelle righe applicate alla destinazione. |
| **Totale applicato**      | Totale di aggiornamenti, inserimenti ed eliminazioni CDC nelle righe applicati alla destinazione. |
| **Errori dei dati** | Numero di errori di dati che si sono verificati nella tabella. Alcuni esempi di errori sono: *511: Cannot create a row of size %d which is greater than the allowable maximum row size of %d (Non è possibile creare una riga di dimensione %d, perché tale valore è maggiore della dimensione di riga massima consentita %d), 8114: Error converting data type %ls to %ls (Errore durante la conversione del tipo di dati %ls in %ls).*  Per visualizzare i dettagli dell'errore, è necessario eseguire una query dalla tabella dms_apply_exceptions nella destinazione di Azure.    |

> [!NOTE]
> I valori CDC di Inserimento, Aggiornamento, Eliminazione e Totale applicato possono diminuire quando viene eseguita la migrazione completa del database o quando la migrazione viene riavviata.

## <a name="next-steps"></a>Passaggi successivi
- Rivedere le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft.