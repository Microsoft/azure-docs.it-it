---
title: Risoluzione dei problemi relativi agli agenti di aggiornamento di Windows in Gestione aggiornamenti di Automazione di AzureTroubleshooting Windows Update agent issues in Azure Automation Update Management
description: Informazioni su come risolvere i problemi relativi all'agente di aggiornamento di Windows utilizzando la soluzione Gestione aggiornamenti.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678984"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Risolvere i problemi relativi agli agenti di aggiornamento di WindowsTroubleshoot Windows update agent issues

Ci possono essere molti motivi per cui la macchina non viene visualizzata come pronta (integro) in Gestione aggiornamenti. In Gestione aggiornamenti è possibile controllare l'integrità di un agente di lavoro ibrido per runbook per determinare il problema sottostante. Questo articolo illustra come eseguire lo strumento di risoluzione dei problemi per le macchine di Azure dal portale di Azure e dalle macchine non azure nello [scenario offline.](#troubleshoot-offline)

Di seguito sono riportati i tre stati di preparazione per una macchina:

* Pronto: il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato visualizzato l'ultima volta meno di 1 ora fa.
* Disconnesso: il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato visualizzato l'ultima volta 1 ora fa.
* Non configurato: il ruolo di lavoro ibrido per runbook non viene trovato o non ha completato l'onboarding.

> [!NOTE]
> Può esserci un leggero ritardo tra ciò che viene visualizzato dal portale di Azure e lo stato corrente di una macchina.

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Nel caso di computer Azure, facendo clic sul collegamento **Risoluzione dei problemi** nella colonna **Update Agent Readiness** (Idoneità agente di aggiornamento) nel portale, si apre la pagina Troubleshoot Update Agent (Risoluzione dei problemi dell'agente di aggiornamento). Per le macchine non Azure, il collegamento porta a questo articolo. Vedere le istruzioni offline per risolvere i problemi di un computer non Azure.See the [offline instructions](#troubleshoot-offline) to troubleshoot a non-Azure machine.

![Aggiornare l'elenco di gestione delle macchine virtuali](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Per controllare l'integrità del ruolo di lavoro ibrido per runbook, la macchina virtuale deve essere in esecuzione. Se la macchina virtuale non è in esecuzione, viene visualizzato il pulsante **Avvia macchina virtuale**.

Nella pagina Risoluzione dei problemi dell'agente di aggiornamento fare clic su **Esegui controlli** per avviare la risoluzione dei problemi. Lo strumento di risoluzione dei problemi utilizza [Esegui comando](../../virtual-machines/windows/run-command.md) per eseguire uno script nel computer per verificare le dipendenze. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Pagina Risoluzione dei problemi dell'agente di aggiornamento](../media/update-agent-issues/troubleshoot-page.png)

I risultati vengono visualizzati nella pagina quando sono pronti. Le sezioni dei controlli mostrano il contenuto di ogni controllo.

![Controlli di risoluzione dei problemi dell'agente di aggiornamento](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per runbook è in esecuzione uno dei seguenti sistemi operativi:

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2012 e versioni successive |.NET Framework 4.6 o versione successiva. ([Scaricare .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> È necessario Windows PowerShell 5.1.  ([Scaricare Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

Il controllo di .NET Framework verifica che nel sistema sia installato un minimo di [.NET Framework 4.6.2.](https://www.microsoft.com/en-us/download/details.aspx?id=53345)

### <a name="wmf-51"></a>WMF 5.1

Il controllo WMF verifica che il sistema disponga della versione richiesta di Windows Management Framework (WMF) - [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Questo controllo determina se si usa TLS 1.2 per crittografare le comunicazioni. TLS 1.0 non è più supportato dalla piattaforma. È consigliabile che i client usino TLS 1.2 per comunicare con Gestione aggiornamenti.

## <a name="connectivity-checks"></a>Controlli della connettività

### <a name="registration-endpoint"></a>Endpoint di registrazione

Questo controllo determina se l'agente può comunicare correttamente con il servizio agente.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con l'endpoint di registrazione. Per un elenco di indirizzi e porte da aprire, vedere Pianificazione della rete per i ruoli [di lavoro ibridi](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpoint delle operazioni

Questo controllo determina se l'agente può comunicare correttamente con il servizio dati del processo di runtime.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con il servizio dati del processo di runtime. Per un elenco di indirizzi e porte da aprire, vedere Pianificazione della rete per i ruoli [di lavoro ibridi](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Criteri di integrità del servizio della macchina virtuale

### <a name="monitoring-agent-service-status"></a>Monitoraggio dello stato del servizio agente

Questo controllo determina se l'agente`healthservice`di Log Analytics per Windows ( ) è in esecuzione nel computer. Per ulteriori informazioni sulla risoluzione dei problemi del servizio, vedere [L'agente di Log Analytics per Windows non è in esecuzione](hybrid-runbook-worker.md#mma-not-running).

Per reinstallare l'agente di Log Analytics per Windows, vedere [Installare e configurare l'agente di Log Analytics per Windows.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)

### <a name="monitoring-agent-service-events"></a>Monitoraggio degli eventi del servizio agente

Questo controllo determina se gli eventi 4502 vengono visualizzati nel log di Azure Operations Manager nel computer nelle ultime 24 ore.

Per altre informazioni su questo evento, vedere la [guida alla risoluzione dei problemi](hybrid-runbook-worker.md#event-4502) per questo evento.

## <a name="access-permissions-checks"></a>Controlli sulle autorizzazioni di accesso

### <a name="machinekeys-folder-access"></a>Accesso alla cartella MachineKeys

Il controllo dell'accesso alla cartella Crypto determina se l'account sistema locale ha accesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Risolvere i problemi offline

È possibile usare la risoluzione dei problemi per un ruolo di lavoro ibrido per runbook offline eseguendo lo script in locale. Lo script [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) è disponibile in PowerShell Gallery. Per eseguire lo script, è necessario che sia installato WMF 4.0 o versione successiva. Per scaricare la versione più recente di PowerShell, vedere [Installazione di varie versioni di PowerShell.](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)

L'output di questo script è simile al seguente:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come risolvere altri problemi dei ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi di ruoli di lavoro ibridi per runbook](hybrid-runbook-worker.md).
