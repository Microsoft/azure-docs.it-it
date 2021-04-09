---
title: Monitoraggio dell'integrità dei file nel Centro sicurezza di Azure
description: Informazioni su come confrontare le basi di riferimento con il monitoraggio dell'integrità dei file nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f160948a06d8a3175158e0100345ee2a330048c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634719"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Confrontare le baseline con il monitoraggio dell'integrità dei file

Il monitoraggio dell'integrità dei file informa l'utente quando vengono apportate modifiche alle aree sensibili delle risorse, in modo che sia possibile analizzare e risolvere attività non autorizzate. FIM monitora i file di Windows, i registri di Windows e i file Linux.

In questo argomento viene illustrato come abilitare FIM nei file e nei registri. Per altre informazioni su FIM, vedere [monitoraggio dell'integrità dei file nel centro sicurezza di Azure](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Perché usare FIM?

Il sistema operativo, le applicazioni e le configurazioni associate controllano il comportamento e lo stato di sicurezza delle risorse. Pertanto, gli utenti malintenzionati hanno come destinazione i file che controllano le risorse, in modo da superare il sistema operativo di una risorsa e/o eseguire attività senza essere rilevati.

Infatti, molti standard di conformità normativi, come PCI-DSS & ISO 17799, richiedono l'implementazione di controlli FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Abilita i controlli del registro di sistema ricorsivi predefiniti

Le impostazioni predefinite hive del registro di sistema FIM rappresentano un modo pratico per monitorare le modifiche ricorsive all'interno di aree di sicurezza comuni.  Un antagonista può, ad esempio, configurare uno script da eseguire nel contesto LOCAL_SYSTEM configurando un'esecuzione all'avvio o all'arresto.  Per monitorare le modifiche di questo tipo, abilitare il controllo predefinito.  

![Registro](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> I controlli ricorsivi si applicano solo agli hive di sicurezza consigliati e non ai percorsi del registro di sistema personalizzati.  

## <a name="add-a-custom-registry-check"></a>Aggiungere un controllo del registro di sistema personalizzato

Le basi di riferimento FIM iniziano con l'identificazione delle caratteristiche di uno stato valido noto per il sistema operativo e l'applicazione di supporto.  Per questo esempio, si concentreranno le configurazioni dei criteri password per Windows Server 2008 e versioni successive.


|Nome criterio                 | Impostazione del Registro di sistema|
|---------------------------------------|-------------|
|Controller di dominio: rifiuta cambi password account computer| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Membro di dominio: aggiunta crittografia o firma digitale ai dati del canale sicuro (sempre)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Membro di dominio: aggiunta crittografia o firma digitale ai dati del canale sicuro (quando possibile)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Membro di dominio: aggiunta firma digitale ai dati del canale sicuro (quando possibile)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Controller di dominio: disabilita cambi password account computer|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Controller di dominio: validità massima password account computer|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Membro di dominio: richiedi chiave di sessione avanzata (Windows 2000 o versioni successive)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Sicurezza di rete: limitazione di NTLM: autenticazione NTLM nel dominio|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Sicurezza di rete: limitazione di NTLM: aggiungi eccezioni dei server nel dominio|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Sicurezza di rete: limitazione di NTLM: controlla autenticazione NTLM nel dominio|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Per altre informazioni sulle impostazioni del registro di sistema supportate da diverse versioni del sistema operativo, vedere il foglio di calcolo di riferimento per le [impostazioni criteri di gruppo](https://www.microsoft.com/download/confirmation.aspx?id=25250).

Per configurare FIM per il monitoraggio delle linee di base del registro di sistema:

1. Nella casella di testo **chiave del registro di sistema** di Windows della finestra **Aggiungi registro di sistema di Windows per rilevamento modifiche** immettere la chiave del registro di sistema seguente:

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="Abilitare FIM in un registro":::

## <a name="track-changes-to-windows-files"></a>Tenere traccia delle modifiche apportate ai file di Windows

1. Nella finestra di dialogo **Aggiungi file di Windows per rilevamento modifiche** , nella casella di testo **immettere il percorso** immettere la cartella che contiene i file che si desidera rilevare. Nell'esempio riportato nella figura seguente, l' **app Web di Contoso** risiede nella D:\ unità all'interno della struttura di cartelle **ContosWebApp** .  
1. Creare una voce di file di Windows personalizzata fornendo un nome della classe di impostazioni, abilitando la ricorsione e specificando la cartella top con un suffisso (*) con carattere jolly.

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="Abilitare FIM in un file":::

## <a name="retrieve-change-data"></a>Recuperare i dati delle modifiche

I dati di monitoraggio dell'integrità dei file si trovano nel set di tabelle Log Analytics/ConfigurationChange di Azure.  

 1. Impostare un intervallo di tempo per recuperare un riepilogo delle modifiche per risorsa.
Nell'esempio seguente vengono recuperate tutte le modifiche negli ultimi quattordici giorni nelle categorie del registro di sistema e dei file:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Per visualizzare i dettagli delle modifiche al registro di sistema:

    1. Rimuovere **i file** dalla clausola **where** , 
    1. Rimuovere la linea di riepilogo e sostituirla con una clausola di ordinamento:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

I report possono essere esportati in formato CSV per l'archiviazione e/o il canale in un report Power BI.  

![Dati FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)