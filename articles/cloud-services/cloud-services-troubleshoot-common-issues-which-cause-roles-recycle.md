---
title: Cause comuni del riciclo dei ruoli del servizio cloud (classico) | Microsoft Docs
description: Un ruolo del servizio cloud che viene riciclato improvvisamente può causare tempi di inattività significativi. Ecco alcuni problemi comuni che causano il riciclo dei ruoli, che possono essere utili per ridurre i tempi di inattività.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9610b32207f8367b9415c0881e49b54e24c49ad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741163"
---
# <a name="common-issues-that-cause-azure-cloud-service-classic-roles-to-recycle"></a>Problemi comuni che provocano il riciclo dei ruoli del servizio cloud di Azure (versione classica)

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

Questo articolo illustra alcune delle cause comuni dei problemi di distribuzione e fornisce suggerimenti per consentirne la risoluzione. La presenza di un problema con un'applicazione viene evidenziata se l'istanza del ruolo non si avvia o se lo stato passa ciclicamente da inizializzazione a occupato e arresto.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dipendenze di runtime mancanti
Se un ruolo nell'applicazione si basa su un'assembly che non fa parte di .NET Framework o della libreria gestita di Azure, è necessario includere tale assembly in modo esplicito nel pacchetto dell'applicazione. Tenere presente che in Azure non sono disponibili per impostazione predefinita altri framework di Microsoft. Se il ruolo si basa su un framework di questo tipo, è necessario aggiungere tali assembly al pacchetto dell'applicazione.

Prima di compilare e includere in un pacchetto l'applicazione, verificare quanto segue:

* Se si usa Visual Studio, assicurarsi che la proprietà **Copia localmente** sia impostata su **True** per ogni assembly di riferimento nel progetto che non fa parte di Azure SDK o .NET Framework.
* Verificare che il file web.config non faccia riferimento ad alcun assembly inutilizzato nell'elemento di compilazione.
* La proprietà **Azione di compilazione** di ciascun file con estensione cshtml è impostata su **Contenuto**. Ciò garantisce che i file vengano visualizzati correttamente nel pacchetto e consente la visualizzazione anche di altri file di riferimento.

## <a name="assembly-targets-wrong-platform"></a>Riferimento dell'assembly alla piattaforma errata
Azure è un ambiente a 64 bit, pertanto gli assembly .NET compilati per una destinazione a 32 bit non funzioneranno in Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Generazione di eccezioni non gestite da parte del ruolo durante l'inizializzazione o l'arresto
Eventuali eccezioni generate dai metodi della classe [RoleEntryPoint], che include i metodi [OnStart], [OnStop] e [Run], sono eccezioni non gestite. Se si verifica un'eccezione non gestita in uno di questi metodi, il ruolo verrà riciclato. Se il ruolo viene riciclato più volte, è possibile che venga generata un'eccezione non gestita a ogni tentativo di avvio.

## <a name="role-returns-from-run-method"></a>Restituzioni del ruolo dal metodo Run
È previsto che il metodo [Run] , venga eseguito in modo illimitato. Se tramite il codice viene eseguito l'override del metodo [Run] , il codice deve essere sospeso per un periodo illimitato. Se tramite il metodo [Run] viene restituito un valore, il ruolo viene riciclato.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Impostazione di DiagnosticsConnectionString errata
Se l'applicazione usa Diagnostica di Azure, il file cscfg deve specificare l'impostazione di configurazione `DiagnosticsConnectionString` . Questa impostazione deve specificare una connessione HTTPS all'account di archiviazione in Azure.

Per assicurarsi che l'impostazione `DiagnosticsConnectionString` sia corretta, prima di distribuire il pacchetto dell'applicazione in Azure, verificare quanto segue:  

* L'impostazione `DiagnosticsConnectionString` punti a un account di archiviazione valido in Azure.  
  Per impostazione predefinita, questa impostazione punta all'account di archiviazione emulato, pertanto è necessario modificarla in modo esplicito prima di distribuire il pacchetto dell'applicazione. Se non si modifica questa impostazione, viene generata un'eccezione quando l'istanza del ruolo tenta di avviare il monitoraggio di diagnostica. Ciò potrebbe comportare il riciclo illimitato dell'istanza del ruolo.
* La stringa di connessione viene specificata nel [formato](../storage/common/storage-configure-connection-string.md)seguente. Il protocollo deve essere specificato come HTTPS. Sostituire *AccountName* con il nome dell'account di archiviazione e *myAccountKey* con la chiave di accesso:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Se l'applicazione viene sviluppata usando Strumenti di Azure per Microsoft Visual Studio, è possibile usare le pagine delle proprietà per impostare questo valore.

## <a name="exported-certificate-does-not-include-private-key"></a>Chiave privata non inclusa nel certificato esportato
Per eseguire un ruolo Web in TLS, è necessario assicurarsi che il certificato di gestione esportato includa la chiave privata. Se si usa *Gestione certificati di Windows* per esportare il certificato, selezionare **Sì** per l'opzione **Esporta la chiave privata**. Il certificato deve essere esportato nel formato con estensione PFX, l'unico attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi
Altri [articoli sulla risoluzione dei problemi](../index.yml?product=cloud-services&tag=top-support-issue) per i servizi cloud.

Per altri scenari di riciclo dei ruoli, vedere la [serie di blog di Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Esegui]: /previous-versions/azure/reference/ee772746(v=azure.100)