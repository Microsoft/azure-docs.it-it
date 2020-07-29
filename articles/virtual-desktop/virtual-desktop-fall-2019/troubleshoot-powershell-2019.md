---
title: PowerShell per desktop virtuali Windows (classico)-Azure
description: Come risolvere i problemi relativi a PowerShell quando si configura un ambiente tenant di desktop virtuale Windows (classico).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04ef12d6de8820740be687bd09ffaa5a8e69eb86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87269615"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>PowerShell per desktop virtuale Windows (classico)

>[!IMPORTANT]
>Questo contenuto si applica a desktop virtuale Windows (classico), che non supporta Azure Resource Manager oggetti desktop virtuali di Windows. Se si sta tentando di gestire Azure Resource Manager oggetti desktop virtuali di Windows, vedere [questo articolo](../troubleshoot-powershell.md).

Usare questo articolo per risolvere errori e problemi quando si usa PowerShell con desktop virtuale di Windows. Per ulteriori informazioni su Servizi Desktop remoto PowerShell, vedere la pagina relativa al [desktop virtuale di Windows PowerShell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Comandi di PowerShell usati durante l'installazione di desktop virtuali Windows

Questa sezione elenca i comandi di PowerShell che vengono in genere utilizzati durante la configurazione di desktop virtuale di Windows e fornisce modi per risolvere i problemi che possono verificarsi durante l'uso.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Errore: comando Add-RdsAppGroupUser--il UserPrincipalName specificato è già assegnato a un gruppo di app RemoteApp nel pool host specificato

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Motivo:** Il nome utente usato è già stato assegnato a un gruppo di app di un tipo diverso. Gli utenti non possono essere assegnati a un desktop remoto e a un gruppo di app Remote nello stesso pool host della sessione.

**Correzione:** Se l'utente necessita sia di app Remote che di desktop remoto, creare pool host diversi o concedere all'utente l'accesso al desktop remoto, che consente l'uso di qualsiasi applicazione nella macchina virtuale host sessione.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Errore: comando Add-RdsAppGroupUser--il UserPrincipalName specificato non esiste nell'Azure Active Directory associato al tenant Desktop remoto

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Motivo:** Impossibile trovare l'utente specificato da-UserPrincipalName nel Azure Active Directory associato al tenant di desktop virtuale di Windows.

**Correzione:** Confermare gli elementi nell'elenco seguente.

- L'utente viene sincronizzato con Azure Active Directory.
- L'utente non è legato a business to consumer (B2C) o commercio business-to-business (B2B).
- Il tenant desktop virtuale di Windows è associato a Azure Active Directory corretti.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Errore: Get-RdsDiagnosticActivities--l'utente non è autorizzato a eseguire una query sul servizio di gestione

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Motivo:** -TenantName parametro

**Correzione:** Eseguire Get-RdsDiagnosticActivities con-TenantName \<TenantName> .

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Errore: Get-RdsDiagnosticActivities--l'utente non è autorizzato a eseguire una query sul servizio di gestione

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Motivo:** Con l'opzione-Deployment.

**Correzione:** l'opzione di distribuzione può essere usata solo dagli amministratori della distribuzione. Questi amministratori sono in genere membri del team di desktop virtuali Servizi Desktop remoto/Windows. Sostituire l'opzione-Deployment con-TenantName \<TenantName> .

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Errore: New-RdsRoleAssignment--l'utente non è autorizzato a eseguire una query sul servizio di gestione

**Cause 1:** L'account usato non dispone delle autorizzazioni Servizi Desktop remoto proprietario per il tenant.

**Correzione 1:** Un utente con autorizzazioni Servizi Desktop remoto proprietario deve eseguire l'assegnazione di ruolo.

**Motivo 2:** L'account usato ha Servizi Desktop remoto autorizzazioni del proprietario, ma non fa parte del Azure Active Directory del tenant o non ha le autorizzazioni necessarie per eseguire query sul Azure Active Directory in cui si trova l'utente.

**Correzione 2:** Un utente con autorizzazioni Active Directory deve eseguire l'assegnazione di ruolo.

>[!Note]
>New-RdsRoleAssignment non può concedere autorizzazioni a un utente che non esiste nel Azure Active Directory (AD).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview-2019.md).
- Per risolvere i problemi durante la creazione di un tenant e di un pool di host in un ambiente di Desktop virtuale Windows, vedere [Creazione di pool di host e tenant](troubleshoot-set-up-issues-2019.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration-2019.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection-2019.md).
- Per risolvere i problemi relativi ai client di Desktop remoto, vedere [risoluzione dei problemi del client di desktop remoto](../troubleshoot-client.md)
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](environment-setup-2019.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../../azure-resource-manager/templates/deployment-history.md).
