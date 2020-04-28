---
title: Correggere le raccomandazioni nel centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra come correggere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse e conformarsi ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77603492"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Correzione delle raccomandazioni nel Centro sicurezza di Azure

Le raccomandazioni forniscono suggerimenti su come proteggere meglio le risorse. Per implementare una raccomandazione, seguire la procedura di correzione fornita nella raccomandazione.

## <a name="remediation-steps"></a>Procedura di correzione<a name="remediation-steps"></a>

Dopo aver esaminato tutte le raccomandazioni, decidere quale risolvere prima di tutto. Si consiglia di usare l' [effetto del Punteggio sicuro](security-center-recommendations.md#monitor-recommendations) per definire la priorità delle operazioni da eseguire prima.

1. Nell'elenco fare clic sull'indicazione.

1. Seguire le istruzioni riportate nella sezione relativa alla **procedura di correzione** . Ogni raccomandazione dispone di un proprio set di istruzioni. Nella schermata seguente vengono illustrati i passaggi correttivi per la configurazione delle applicazioni in modo da consentire solo il traffico su HTTPS.

    ![Dettagli delle raccomandazioni](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Al termine, viene visualizzata una notifica che informa se la correzione ha avuto esito positivo.

## <a name="quick-fix-remediation"></a>Correzione rapida<a name="one-click"></a>

La correzione rapida consente di correggere rapidamente una raccomandazione su più risorse. È disponibile solo per raccomandazioni specifiche. La correzione rapida semplifica la correzione e consente di aumentare rapidamente il Punteggio sicuro, migliorando la sicurezza dell'ambiente.

Per implementare il monitoraggio e l'aggiornamento della correzione rapida:

1. Dall'elenco di raccomandazioni con la **correzione rapida.** etichetta, fare clic sull'indicazione.

    [![Selezionare correzione rapida.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Dalla scheda **risorse non integre** selezionare le risorse su cui si desidera implementare l'indicazione e **quindi fare clic su Correggi**.

    > [!NOTE]
    > Alcune delle risorse elencate potrebbero essere disabilitate, perché non si dispone delle autorizzazioni appropriate per modificarle.

1. Nella casella Conferma leggere i dettagli e le implicazioni per la correzione.

    ![Correzione rapida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Le implicazioni sono elencate nella casella grigio della finestra Correggi **risorse** che viene visualizzata dopo aver **fatto clic su**Correggi. Elencano le modifiche che si verificano quando si procede con la correzione rapida.

1. Inserire i parametri rilevanti, se necessario, e approvare la correzione.

    > [!NOTE]
    > Potrebbero essere necessari alcuni minuti dopo il completamento della correzione per visualizzare le risorse nella scheda **risorse integre** . Per visualizzare le azioni correttive, controllare il [log attività](#activity-log).

1. Al termine, viene visualizzata una notifica che informa se la correzione ha avuto esito positivo.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Registrazione correzione rapida nel log attività<a name="activity-log"></a>

Per applicare la configurazione nella risorsa, l'operazione di monitoraggio e aggiornamento usa una chiamata API per la distribuzione di modelli o una PATCH REST. Queste operazioni vengono registrate nel [log attività di Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Raccomandazioni con correzione rapida

|Recommendation|Modo implicito|
|---|---|
|Il controllo sui server SQL deve essere abilitato|Questa azione consentirà il controllo SQL su questi server e i relativi database. <br>**Nota**: <ul><li>Per ogni area dei server SQL selezionati, un account di archiviazione per il salvataggio dei log di controllo verrà creato e condiviso da tutti i server in tale area.</li><li>Per garantire un controllo appropriato, non eliminare o rinominare il gruppo di risorse o gli account di archiviazione.</li></ul>|
|La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite di SQL|Questa azione Abilita SQL Advanced Data Security (ADS) per le istanze gestite di SQL selezionate. <br>**Nota**: <ul><li>Per ogni area e gruppo di risorse delle istanze di SQL gestite selezionate, un account di archiviazione per il salvataggio dei risultati dell'analisi verrà creato e condiviso da tutte le istanze di tale area.</li><li> ADS viene addebitato a $15 per istanza gestita di SQL.</li></ul>|
|La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL|Questa azione consentirà la valutazione della vulnerabilità SQL nelle istanze di SQL gestite selezionate. <br>**Nota**:<ul><li>La valutazione della vulnerabilità SQL fa parte del pacchetto SQL Advanced Data Security (ADS). Se ADS non è già abilitato, verrà abilitato automaticamente nell'istanza gestita.</li><li>Per ogni area e gruppo di risorse delle istanze di SQL gestite selezionate, viene creato un account di archiviazione per archiviare i risultati dell'analisi che verranno condivisi da tutte le istanze di tale area.</li><li>ADS viene addebitato a $15 per SQL Server.</li></ul>||
|È necessario abilitare la sicurezza dei dati avanzata in SQL Server|Questa azione Abilita la sicurezza avanzata dei dati (ADS) sui server selezionati e i relativi database. <br>**Nota**:<ul><li>Per ogni area e gruppo di risorse dei server SQL selezionati, un account di archiviazione per archiviare i risultati dell'analisi verrà creato e condiviso da tutti i server in tale area. <</li><li>ADS viene addebitato a $15 per SQL Server.</li></ul>||
|È necessario abilitare la valutazione della vulnerabilità in SQL Server|Questa azione consentirà la valutazione della vulnerabilità SQL su questi server selezionati e i relativi database. <br>**Nota**:<ul><li>La valutazione della vulnerabilità SQL fa parte del pacchetto SQL Advanced Data Security (ADS). Se ADS non è già abilitato, verrà abilitato automaticamente in SQL Server.</li><li>Per ogni area e gruppo di risorse dei server SQL selezionati, un account di archiviazione per archiviare i risultati dell'analisi verrà creato e condiviso da tutte le istanze di tale area.</li><li>ADS viene addebitato a $15 per SQL Server.</li></ul>||
|È necessario abilitare la funzionalità Transparent Data Encryption nei database SQL|Questa azione Abilita il database SQL Transparent Data Encryption (Transparent Data Encryption) sui database selezionati. <br>**Nota**: per impostazione predefinita, verranno usate le chiavi di crittografia di crittografia gestite dal servizio.
|Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Questa azione Aggiorna la sicurezza dell'account di archiviazione in modo da consentire solo le richieste tramite connessioni sicure. (HTTPS). <br>**Nota**:<ul><li>Tutte le richieste che usano HTTP verranno rifiutate.</li><li>Quando si usa il servizio file di Azure, la connessione senza crittografia avrà esito negativo, inclusi gli scenari che usano SMB 2,1, SMB 3,0 senza crittografia e alcune versioni del client SMB Linux. Altre informazioni.</li></ul>|
|L'applicazione Web deve essere accessibile solo tramite HTTPS|Questa azione consente di reindirizzare tutto il traffico da HTTP a HTTPS, sulle risorse selezionate. <br>**Nota**:<ul><li>Un endpoint HTTPS che non dispone di un certificato SSL verrà visualizzato nel browser con un "errore di privacy". Gli utenti che hanno un dominio personalizzato devono quindi verificare di aver configurato un certificato SSL.</li><li>Verificare che i pacchetti e i firewall applicazione Web proteggano il servizio app, consentono l'invio di sessioni HTTPS.</li></ul>|
|L'app per le funzioni deve essere accessibile solo tramite HTTPS|Questa azione consente di reindirizzare tutto il traffico da HTTP a HTTPS, sulle risorse selezionate. <br>**Nota**:<ul><li>Un endpoint HTTPS che non dispone di un certificato SSL verrà visualizzato nel browser con un "errore di privacy". Gli utenti che hanno un dominio personalizzato devono quindi verificare di aver configurato un certificato SSL.</li><li>Verificare che i pacchetti e i firewall applicazione Web proteggano il servizio app, consentono l'invio di sessioni HTTPS.</li></ul>|
|L'app per le API deve essere accessibile solo tramite HTTPS|Questa azione consente di reindirizzare tutto il traffico da HTTP a HTTPS, sulle risorse selezionate. <br>**Nota**:<ul><li>Un endpoint HTTPS che non dispone di un certificato SSL verrà visualizzato nel browser con un "errore di privacy". Gli utenti che hanno un dominio personalizzato devono quindi verificare di aver configurato un certificato SSL.</li><li>Verificare che i pacchetti e i firewall applicazione Web proteggano il servizio app, consentono l'invio di sessioni HTTPS.</li></ul>|
|Il debug remoto deve essere disattivato per l'applicazione Web|Questa azione Disabilita il debug remoto.|
|Il debug remoto deve essere disattivato per l'app per le funzioni|Questa azione Disabilita il debug remoto.|
|Il debug remoto deve essere disattivato per l'app per le API|Questa azione Disabilita il debug remoto.|
|CORS non deve consentire a ogni risorsa l'accesso all'applicazione Web|Questa azione impedisce ad altri domini di accedere all'applicazione Web. Per consentire domini specifici, immetterli nel campo origini consentite (separate da virgole). <br>**Nota**: se si lascia vuoto il campo, tutte le chiamate tra le origini vengono bloccate .' param Field title:' allowed Origins '|
|Condivisione risorse tra le origini non deve consentire a tutte le risorse di accedere all'app per le funzioni dell'utente|Questa azione impedisce ad altri domini di accedere all'applicazione per le funzioni. Per consentire domini specifici, immetterli nel campo origini consentite (separate da virgole). <br>**Nota**: se si lascia vuoto il campo, tutte le chiamate tra le origini vengono bloccate .' param Field title:' allowed Origins '|
|CORS non deve consentire a tutte le risorse di accedere all'app per le API dell'utente|Questa azione impedisce ad altri domini di accedere all'applicazione API. Per consentire domini specifici, immetterli nel campo origini consentite (separate da virgole). <br>**Nota**: se si lascia vuoto il campo, tutte le chiamate tra le origini vengono bloccate .' param Field title:' allowed Origins '|
|È necessario abilitare l'agente di monitoraggio sulle macchine virtuali|Questa azione consente di installare un agente di monitoraggio sulle macchine virtuali selezionate. Consente di selezionare un'area di lavoro per l'agente a cui creare un report.<ul><li>Se il criterio di aggiornamento è impostato su automatico, verrà distribuito nelle nuove istanze esistenti.</li><li>Se i criteri di aggiornamento sono impostati su manuale e si desidera installare l'agente in istanze esistenti, selezionare l'opzione casella di controllo. [Altre informazioni](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|I log di diagnostica in Key Vault devono essere abilitati|Questa azione Abilita i log di diagnostica negli insiemi di credenziali delle chiavi. I log di diagnostica e le metriche vengono salvati nell'area di lavoro selezionata.|
|I log di diagnostica nel bus di servizio devono essere abilitati|Questa azione Abilita i log di diagnostica sul bus di servizio. I log di diagnostica e le metriche vengono salvati nell'area di lavoro selezionata.|

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come correggere le raccomandazioni nel centro sicurezza. Per ulteriori informazioni sul centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
