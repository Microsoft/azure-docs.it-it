---
title: Abilitare l'ottimizzazione automatica
description: È possibile abilitare facilmente l'ottimizzazione automatica nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299329"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Abilitare l'ottimizzazione automatica monitorare le query e migliorare le prestazioni del carico di lavoro

Il database SQL di Azure è un servizio dati gestito automaticamente che esegue un monitoraggio costante delle query e identifica l'azione che è possibile eseguire per migliorare le prestazioni del carico di lavoro. È possibile esaminare le raccomandazioni e applicarle manualmente oppure delegare al database SQL di Azure l'applicazione automatica delle azioni correttive, condizione nota come **modalità di ottimizzazione automatica**.

L'ottimizzazione automatica può essere abilitata a livello di server o di database dal [portale di Azure](sql-database-automatic-tuning-enable.md#azure-portal) o tramite chiamate [API REST](sql-database-automatic-tuning-enable.md#rest-api) e comandi [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

> [!NOTE]
> Per Istanza gestita, l'opzione supportata FORCE_LAST_GOOD_PLAN può essere configurata solo tramite [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Le opzioni di configurazione basata su portale e di ottimizzazione automatica degli indici descritte in questo articolo non si applicano a Istanza gestita.

> [!NOTE]
> In questo momento la configurazione delle opzioni di ottimizzazione automatica tramite il modello ARM (Azure Resource Manager) non è supportata.

## <a name="enable-automatic-tuning-on-server"></a>Abilitare l'ottimizzazione automatica nel server

A livello di server è possibile scegliere di ereditare o meno la configurazione dell'ottimizzazione automatica da "Impostazioni predefinite di Azure". Le impostazioni predefinite di Azure sono FORCE_LAST_GOOD_PLAN (abilitato), CREATE_INDEX (abilitato) e DROP_INDEX (disabilitato).

> [!IMPORTANT]
> A partire da marzo, 2020 le modifiche apportate alle impostazioni predefinite di Azure per l'ottimizzazione automatica diverranno effettive come segue:
>
> - Le nuove impostazioni predefinite di Azure verranno FORCE_LAST_GOOD_PLAN = Enabled, CREATE_INDEX = disabled e DROP_INDEX = disabled.
> - I server esistenti senza preferenze di ottimizzazione automatica configurata verranno automaticamente configurati per EREDITAre le nuove impostazioni predefinite di Azure. Questo vale per tutti i clienti che dispongono attualmente di impostazioni server per l'ottimizzazione automatica in uno stato non definito.
> - I nuovi server creati verranno automaticamente configurati in modo da EREDITAre le nuove impostazioni predefinite di Azure (a differenza di quanto prima quando la configurazione dell'ottimizzazione automatica era in uno stato non definito durante la creazione di un nuovo server).

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'ottimizzazione automatica nel **server** logico del database SQL di Azure, passare al server nel portale di Azure e quindi selezionare **Ottimizzazione automatica** nel menu.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Si noti che al momento l'opzione **DROP_INDEX** non è compatibile con applicazioni che usano i cambi di partizione e gli hint di indice e non deve essere abilitata in questi casi. L'eliminazione degli indici inutilizzati non è supportata per i livelli di servizio Premium e business critical.
>

Selezionare le opzioni di ottimizzazione automatica che si vuole abilitare e selezionare **applica**.

Le opzioni di ottimizzazione automatica in un server vengono applicate a tutti i database nel server stesso. Per impostazione predefinita, tutti i database ereditano la configurazione dal server padre corrispondente, ma è possibile sostituire questa impostazione e specificarne una distinta per ogni database.

### <a name="rest-api"></a>API REST

Per altre informazioni sull'uso dell'API REST per abilitare l'ottimizzazione automatica in un server, vedere i [metodi HTTP UPDATE e GET per l'ottimizzazione automatica di SQL Server](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Abilitare l'ottimizzazione automatica per un database singolo

Il database SQL di Azure consente di specificare singolarmente la configurazione di ottimizzazione automatica per ogni database. A livello di database è possibile scegliere di ereditare o meno la configurazione dell'ottimizzazione automatica dal server padre o da "Impostazioni predefinite di Azure". Le impostazioni predefinite di Azure sono FORCE_LAST_GOOD_PLAN (abilitata), CREATE_INDEX (abilitata) e DROP_INDEX (disabilitata).

> [!TIP]
> L'indicazione generale è quella di gestire la configurazione di ottimizzazione automatica a **livello di server** , in modo che le stesse impostazioni di configurazione possano essere applicate automaticamente a ogni database. Configurare l'ottimizzazione automatica in un singolo database solo se è necessario che per tale database le impostazioni siano diverse rispetto ai database che ereditano le impostazioni dallo stesso server.
>

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'ottimizzazione automatica per un **singolo database**, passare al database nel portale di Azure e selezionare **Ottimizzazione automatica**.

Singole impostazioni di ottimizzazione automatica possono essere configurate separatamente per ogni database. È possibile configurare manualmente una singola opzione di ottimizzazione automatica o specificare che un'opzione deve ereditare le impostazioni dal server.

![Database](./media/sql-database-automatic-tuning-enable/database.png)

Si noti che al momento l'opzione DROP_INDEX non è compatibile con applicazioni che usano i cambi di partizione e gli hint di indice e non deve essere abilitata in questi casi.

Dopo aver selezionato la configurazione desiderata, fare clic su **Applica**.

### <a name="rest-api"></a>API REST

Per altre informazioni sull'uso dell'API REST per abilitare l'ottimizzazione automatica in un database singolo, vedere i [metodi HTTP UPDATE e GET per l'ottimizzazione automatica del database SQL](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Per abilitare l'ottimizzazione automatica per un singolo database con Transact-SQL, connettersi al database ed eseguire la query seguente:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

L'impostazione dell'ottimizzazione automatica su AUTO verrà applicata alle impostazioni predefinite di Azure. Se viene impostata su INHERIT, la configurazione dell'ottimizzazione automatica verrà ereditata dal server padre. Se si sceglie CUSTOM, l'ottimizzazione automatica dovrà essere configurata manualmente.

Per configurare le singole opzioni di ottimizzazione automatica con Transact-SQL, connettersi al database ed eseguire la query seguente:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Impostare l'opzione di ottimizzazione specifica su ON per eseguire l'override di qualsiasi impostazione ereditata dal database e abilitare l'opzione di ottimizzazione specifica. Se viene impostata su OFF, verrà eseguito l'override di qualsiasi impostazione ereditata dal database, ma l'opzione di ottimizzazione specifica verrà disabilitata. L'opzione di ottimizzazione automatica, per la quale viene specificato il valore predefinito, erediterà la configurazione di ottimizzazione automatica dalle impostazioni a livello di server.  

> [!IMPORTANT]
> In caso di [replica geografica attiva](sql-database-auto-failover-group.md), l'ottimizzazione automatica deve essere configurata solo nel database primario. Le azioni di ottimizzazione applicate automaticamente, ad esempio la creazione o l'eliminazione dell'indice, verranno replicate automaticamente nel database secondario di sola lettura. Se si tenta di abilitare l'ottimizzazione automatica tramite T-SQL nel database secondario di sola lettura, si verificherà un errore perché una configurazione di ottimizzazione diversa per il database secondario di sola lettura non è supportata.
>

Per altre informazioni sulle opzioni di T-SQL per configurare l'ottimizzazione automatica, vedere [Opzioni ALTER DATABASE SET (Transact-SQL) per server di database SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Disabilitazione da parte del sistema

L'ottimizzazione automatica esegue il monitoraggio di tutte le azioni eseguite a livello di database e in alcuni casi potrebbe non funzionare correttamente nel database. In questo caso, l'opzione di ottimizzazione viene disabilitata dal sistema. Nella maggior parte dei casi ciò accade perché Query Store non è abilitato o è in stato di sola lettura in un database specifico.

## <a name="permissions"></a>Autorizzazioni

Poiché l'ottimizzazione automatica è la funzionalità di Azure, per usarla è necessario usare i ruoli RBAC predefiniti di Azure. L'utilizzo dell'autenticazione SQL non sarà sufficiente per utilizzare la funzionalità da portale di Azure.

Per usare l'ottimizzazione automatica, l'autorizzazione minima necessaria per concedere all'utente è il ruolo di [collaboratore database SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) predefinito di Azure. È inoltre possibile considerare l'utilizzo di ruoli con privilegi più elevati, ad esempio SQL Server collaboratore, collaboratore e proprietario.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurare notifiche tramite posta elettronica per l'ottimizzazione automatica

Vedere la Guida all' [ottimizzazione automatica delle notifiche tramite posta elettronica](sql-database-automatic-tuning-email-notifications.md) .

## <a name="next-steps"></a>Passaggi successivi

* Leggere l'[articolo sull'ottimizzazione automatica](sql-database-automatic-tuning.md) per altre informazioni sull'ottimizzazione automatica e sull'utilità di questa funzione per il miglioramento delle prestazioni.
* Vedere le [Raccomandazioni per le prestazioni](sql-database-advisor.md) per una panoramica delle raccomandazioni per le prestazioni per il database SQL di Azure.
* Vedere l'articolo [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per informazioni sull'analisi dell'impatto sulle prestazioni delle query principali.
