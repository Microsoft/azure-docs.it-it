---
title: Abilitare i controlli di sicurezza per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come abilitare i controlli di sicurezza per centralizzare la registrazione degli eventi per l'analisi e gli avvisi in Azure AD Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: b9656b62e2c689d0993fb16c1f1d66b14d3430c6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967733"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Abilitare i controlli di sicurezza per Azure Active Directory Domain Services

I controlli di sicurezza di Azure Active Directory Domain Services (Azure AD DS) consentono agli eventi di sicurezza di Azure di trasmettere risorse di destinazione. Queste risorse includono archiviazione di Azure, aree di lavoro di Azure Log Analytics o hub eventi di Azure. Una volta abilitati gli eventi di controllo di sicurezza, Azure AD DS invia tutti gli eventi controllati per la categoria selezionata alla risorsa di destinazione.

È possibile archiviare gli eventi in archiviazione di Azure e trasmettere gli eventi in un software di gestione di informazioni ed eventi di sicurezza (o equivalente) usando hub eventi di Azure oppure eseguire un'analisi personalizzata e usare le aree di lavoro di Azure Log Analytics dalla portale di Azure.

> [!IMPORTANT]
> I controlli di sicurezza Azure AD DS sono disponibili solo per i domini gestiti basati su Azure Resource Manager. Per informazioni su come eseguire la migrazione, vedere [eseguire la migrazione di Azure AD DS dal modello di rete virtuale classica al gestione risorse][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Destinazioni del controllo di sicurezza

È possibile usare archiviazione di Azure, Hub eventi di Azure o aree di lavoro di Azure Log Analytics come risorsa di destinazione per i controlli di sicurezza di Azure AD DS. Queste destinazioni possono essere combinate. Ad esempio, è possibile usare archiviazione di Azure per archiviare gli eventi di controllo di sicurezza, ma un'area di lavoro di Azure Log Analytics per analizzare e creare un report delle informazioni a breve termine.

Nella tabella seguente vengono illustrati gli scenari per ogni tipo di risorsa di destinazione.

> [!IMPORTANT]
> Prima di abilitare i controlli di sicurezza di Azure AD DS, è necessario creare la risorsa di destinazione. È possibile creare queste risorse usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.

| Risorsa di destinazione | Scenario |
|:---|:---|
|Archiviazione di Azure| Questa destinazione deve essere utilizzata quando la necessità principale è archiviare gli eventi di controllo di sicurezza per scopi di archiviazione. Le altre destinazioni possono essere usate per scopi di archiviazione, ma tali destinazioni forniscono funzionalità oltre la necessità principale di archiviazione. <br /><br />Prima di abilitare gli eventi di controllo di sicurezza Azure AD DS, [creare prima un account di archiviazione di Azure](../storage/common/storage-account-create.md).|
|Hub eventi di Azure| Questa destinazione deve essere utilizzata quando la necessità principale consiste nel condividere gli eventi di controllo di sicurezza con software aggiuntivo, ad esempio software di analisi dei dati o informazioni sulla sicurezza & software di gestione degli eventi (SIEM).<br /><br />Prima di abilitare gli eventi di controllo di sicurezza di Azure AD DS, [creare un hub eventi usando portale di Azure](../event-hubs/event-hubs-create.md)|
|Area di lavoro di Azure Log Analytics| Questa destinazione deve essere usata quando la necessità principale consiste nell'analizzare ed esaminare i controlli protetti direttamente dalla portale di Azure.<br /><br />Prima di abilitare gli eventi di controllo di sicurezza Azure AD DS, [creare un'area di lavoro log Analytics nel portale di Azure.](../azure-monitor/learn/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Abilitare gli eventi di controllo di sicurezza usando il portale di Azure

Per abilitare gli eventi di controllo di sicurezza Azure AD DS usando il portale di Azure, completare i passaggi seguenti.

> [!IMPORTANT]
> I controlli di sicurezza Azure AD DS non sono retroattivi. Non è possibile recuperare o riprodurre gli eventi del passato. Azure AD DS può inviare solo gli eventi che si verificano dopo l'abilitazione di controlli di sicurezza.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Nella parte superiore del portale di Azure cercare e selezionare **Azure ad Domain Services**. Scegliere il dominio gestito, ad esempio *aaddscontoso.com*.
1. Nella finestra Azure AD DS selezionare impostazioni di **diagnostica** nella parte sinistra.
1. Per impostazione predefinita, non è configurata alcuna diagnostica. Per iniziare, selezionare **Aggiungi impostazioni di diagnostica**.

    ![Aggiungere un'impostazione di diagnostica per Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Immettere un nome per la configurazione di diagnostica, ad esempio *aadds-auditing*.

    Selezionare la casella per la destinazione del controllo di sicurezza desiderata. È possibile scegliere da un account di archiviazione di Azure, un hub eventi di Azure o un'area di lavoro Log Analytics. Queste risorse di destinazione devono essere già presenti nella sottoscrizione di Azure. Non è possibile creare le risorse di destinazione in questa procedura guidata.

    ![Abilitare la destinazione richiesta e il tipo di eventi di controllo da acquisire](./media/security-audit-events/diagnostic-settings-page.png)

    * **Archiviazione di Azure**
        * Selezionare **archivia in un account di archiviazione**, quindi scegliere **Configura**.
        * Selezionare la **sottoscrizione** e l' **account di archiviazione** che si vuole usare per archiviare gli eventi di controllo di sicurezza.
        * Quando è pronto, scegliere **OK**.
    * **Hub eventi di Azure**
        * Selezionare **flusso a hub eventi**, quindi scegliere **Configura**.
        * Selezionare la **sottoscrizione** e lo **spazio dei nomi dell'hub eventi**. Se necessario, scegliere anche un **nome per l'hub eventi** e quindi **Nome criterio Hub eventi**.
        * Quando è pronto, scegliere **OK**.
    * **Aree di lavoro analisi log di Azure**
        * Selezionare **Invia a log Analytics**, quindi scegliere la **sottoscrizione** e l' **area di lavoro log Analytics** da usare per archiviare gli eventi di controllo di sicurezza.

1. Selezionare le categorie di log che si desidera includere per la risorsa di destinazione specifica. Se si inviano gli eventi di controllo a un account di archiviazione di Azure, è anche possibile configurare criteri di conservazione che definiscono il numero di giorni di conservazione dei dati. L'impostazione predefinita *0* mantiene tutti i dati e non ruota gli eventi dopo un periodo di tempo.

    È possibile selezionare diverse categorie di log per ogni risorsa di destinazione in una singola configurazione. Questa funzionalità consente di scegliere le categorie di log da archiviare per Log Analytics e le categorie di log che si desidera archiviare, ad esempio.

1. Al termine, selezionare **Salva** per eseguire il commit delle modifiche. Le risorse di destinazione iniziano a ricevere Azure AD gli eventi di controllo di sicurezza DS subito dopo il salvataggio della configurazione.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Abilitare gli eventi di controllo di sicurezza tramite Azure PowerShell

Per abilitare gli eventi di controllo di sicurezza Azure AD DS usando Azure PowerShell, completare i passaggi seguenti. Se necessario, [installare prima il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I controlli di sicurezza Azure AD DS non sono retroattivi. Non è possibile recuperare o riprodurre gli eventi del passato. Azure AD DS può inviare solo gli eventi che si verificano dopo l'abilitazione di controlli di sicurezza.

1. Eseguire l'autenticazione alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) . Quando richiesto, immettere le credenziali dell'account.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Creare la risorsa di destinazione per gli eventi di controllo di sicurezza.

    * **Archiviazione**  -  di Azure [Creare un account di archiviazione usando Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * Hub eventi di **Azure**  -  [Creare un hub eventi usando Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Potrebbe anche essere necessario usare il cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) per creare una regola di autorizzazione che conceda le autorizzazioni di Azure AD DS allo *spazio dei nomi*dell'hub eventi. La regola di autorizzazione deve includere i diritti di **gestione**, **ascolto**e **trasmissione** .

        > [!IMPORTANT]
        > Assicurarsi di impostare la regola di autorizzazione nello spazio dei nomi dell'hub eventi e non nell'hub eventi stesso.

    * **Aree di lavoro**  -  analisi log di Azure [Creare un'area di lavoro log Analytics con Azure PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

1. Ottenere l'ID di risorsa per il dominio gestito di Azure AD DS usando il cmdlet [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) . Creare una variabile denominata *$aadds. ResourceId* per conservare il valore:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Configurare le impostazioni di diagnostica di Azure usando il cmdlet [set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) per usare la risorsa di destinazione per gli eventi di controllo di sicurezza Azure ad Domain Services. Negli esempi seguenti la variabile *$aadds. ResourceId* viene usato nel passaggio precedente.

    * **Archiviazione di Azure** -sostituire *storageAccountId* con il nome dell'account di archiviazione:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Hub eventi di Azure** : sostituire *eventHubName* con il nome dell'hub eventi e *eventHubRuleId* con l'ID della regola di autorizzazione:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Aree di lavoro di analisi dei log di Azure** : sostituire *WORKSPACEID* con l'ID dell'area di lavoro log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Eseguire query e visualizzare gli eventi di controllo di sicurezza con monitoraggio di Azure

Le aree di lavoro di analisi dei log consentono di visualizzare e analizzare gli eventi di controllo di sicurezza usando monitoraggio di Azure e il linguaggio di query kusto. Questo linguaggio di query è progettato per l'utilizzo di sola lettura che offre funzionalità di analisi del risparmio energia con una sintassi di facile lettura. Per altre informazioni su come iniziare a usare i linguaggi di query kusto, vedere gli articoli seguenti:

* [Documentazione di Monitoraggio di Azure](../azure-monitor/index.yml)
* [Introduzione a Log Analytics in Monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md)
* [Introduzione alle query su log in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)
* [Creare e condividere i dashboard dei dati di Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

Per iniziare ad analizzare gli eventi di controllo di sicurezza da Azure AD DS, è possibile usare le query di esempio seguenti.

### <a name="sample-query-1"></a>Query di esempio 1

Visualizzare tutti gli eventi di blocco degli account per gli ultimi sette giorni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Query di esempio 2

Visualizzare tutti gli eventi di blocco degli account (*4740*) tra il 3 giugno 2020 alle 9.00. e il 10 giugno 2020 mezzanotte, ordinati in ordine crescente in base alla data e all'ora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Query di esempio 3

Visualizza gli eventi di accesso agli account sette giorni fa (da adesso) per l'account denominato User:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Query di esempio 4

Visualizza gli eventi di accesso agli account sette giorni fa da adesso per l'account denominato User che ha tentato di eseguire l'accesso con una password non valida (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Esempio di query 5

Visualizza gli eventi di accesso agli account sette giorni fa da adesso per l'account denominato User che ha tentato di accedere mentre l'account è stato bloccato (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Esempio di query 6

Visualizza il numero di eventi di accesso agli account di sette giorni fa da adesso per tutti i tentativi di accesso che si sono verificati per tutti gli utenti bloccati:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Categorie di eventi di controllo

I controlli di sicurezza Azure AD DS si allineano al controllo tradizionale per i controller di dominio di servizi di dominio Active Directory tradizionali. Negli ambienti ibridi è possibile riutilizzare i modelli di controllo esistenti, in modo che sia possibile utilizzare la stessa logica quando si analizzano gli eventi. A seconda dello scenario necessario per la risoluzione dei problemi o l'analisi, è necessario destinare le diverse categorie di eventi di controllo.

Sono disponibili le categorie di eventi di controllo seguenti:

| Nome categoria di controllo | Descrizione |
|:---|:---|
| Accesso account|I controlli tentano di autenticare i dati dell'account in un controller di dominio o in una gestione account di sicurezza locale (SAM).</p>Le impostazioni dei criteri di accesso e di disconnessione ed eventi tengono traccia dei tentativi di accesso a un computer specifico. Le impostazioni e gli eventi in questa categoria si concentrano sul database di account usato. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Convalida credenziali](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Controlla Servizio di autenticazione Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Controlla Operazioni ticket di servizio Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Controlla Altri eventi di accesso/fine sessione](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gestione account|Controlla le modifiche apportate agli account utente e computer e ai gruppi. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Gestione gruppi di applicazioni](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Controlla Gestione account computer](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Controlla Gestione gruppi di distribuzione](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Controlla altra gestione account](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Controlla Gestione gruppi di sicurezza](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Controlla Gestione account utente](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Rilevamento dettagli|Controlla le attività di singoli utenti e applicazioni su tale computer e per comprendere la modalità di utilizzo di un computer. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Attività DPAPI](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Attività PNP di controllo](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Controlla Creazione di processi](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Controlla Chiusura di processi](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Controlla Eventi RPC](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Accesso ai servizi directory|I controlli tentano di accedere agli oggetti e di modificarli in Active Directory Domain Services (AD DS). Questi eventi di controllo vengono registrati solo nei controller di dominio. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Replica dettagliata servizio directory](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Controlla Accesso al servizio directory](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Controlla Modifiche servizio directory](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Controlla Replica servizio directory](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|I controlli tentano di accedere a un computer in modo interattivo o in una rete. Questi eventi sono utili per tenere traccia delle attività degli utenti e identificare potenziali attacchi sulle risorse di rete. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Blocco account](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Controlla attestazioni utente/dispositivo](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Controlla Modalità estesa IPsec](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Controlla appartenenza a gruppo](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Controlla Modalità principale IPsec](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Controlla Modalità rapida IPsec](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Controlla Fine sessione](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Controlla Accesso](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Controlla Server dei criteri di rete](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Controlla Altri eventi di accesso/fine sessione](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Controlla Accesso speciale](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Accesso a oggetti| I controlli tentano di accedere a oggetti specifici o a tipi di oggetti in una rete o in un computer. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Generato dall'applicazione](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Controlla Servizi di certificazione](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Controlla condivisione file dettagliata](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Controlla Condivisione file](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Controllo del file System](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Controlla Connessione a Piattaforma filtro Windows](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Controlla Mancata elaborazione pacchetti Piattaforma filtro Windows](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Controllo Manipolazione handle](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Controlla oggetto kernel](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Controlla Altri eventi di accesso agli oggetti](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registro di controllo](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Controlla archivi rimovibili](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Controlla SAM](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Controlla Gestione temporanea Criteri di accesso centrale](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Modifica dei criteri|Controlla le modifiche apportate ai criteri di sicurezza importanti in un sistema locale o in una rete. I criteri vengono in genere stabiliti dagli amministratori per proteggere le risorse di rete. Il monitoraggio delle modifiche o dei tentativi di modifica di questi criteri può essere un aspetto importante della gestione della sicurezza per una rete. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Controlla modifica ai criteri](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Controlla Modifica criteri di autenticazione](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Controlla Modifica criteri di autorizzazione](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Controlla Modifica criteri Piattaforma filtro Windows](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Controlla Modifica criteri a livello di regola MPSSVC](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Controlla altre modifiche ai criteri](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso dei privilegi| Controlla l'uso di determinate autorizzazioni in uno o più sistemi. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Utilizzo privilegi non sensibili](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Controlla Utilizzo privilegi sensibili](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Controlla Altri eventi di utilizzo dei privilegi](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| Controlla le modifiche a livello di sistema in un computer non incluso in altre categorie e con possibili implicazioni di sicurezza. Questa categoria include le sottocategorie seguenti:<ul><li>[Controlla Driver IPSec](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Controlla Altri eventi di sistema](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Controlla Modifica stato sicurezza](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Controlla Estensione sistema di sicurezza](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Controlla Integrità sistema](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>ID evento per categoria

 Azure AD i controlli di sicurezza DS registrano gli ID evento seguenti quando l'azione specifica attiva un evento controllabile:

| Nome della categoria di eventi | ID evento |
|:---|:---|
|Sicurezza accesso account|4767, 4774, 4775, 4776, 4777|
|Sicurezza della gestione degli account|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Sicurezza del rilevamento dei dettagli|nessuno|
|Sicurezza accesso DS|5136, 5137, 5138, 5139, 5141|
|Sicurezza Logon-Logoff|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Sicurezza dall'accesso agli oggetti|nessuno|
|Sicurezza della modifica dei criteri|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Sicurezza uso dei privilegi|4985|
|Protezione del sistema|4612, 4621|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni specifiche su kusto, vedere gli articoli seguenti:

* [Cenni preliminari](/azure/kusto/query/) sul linguaggio di query kusto.
* [Esercitazione su kusto](/azure/kusto/query/tutorial) per acquisire familiarità con le nozioni di base sulle query.
* [Query di esempio](/azure/kusto/query/samples) che consentono di apprendere nuovi modi per visualizzare i dati.
* Kusto le [procedure consigliate](/azure/kusto/query/best-practices) per ottimizzare le query per l'esito positivo.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md