---
title: Aggiornamento automatico del servizio Mobility in Azure Site Recovery
description: Panoramica dell'aggiornamento automatico del servizio Mobility durante la replica delle macchine virtuali di Azure tramite Azure Site Recovery.Overview of automatic update of the Mobility service when replicating Azure VMs by using Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: 67298ecf0c17feee2d36bb8774cae37b1ca81381
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618971"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Aggiornamento automatico del servizio Mobility nella replica da Azure ad AzureAutomatic update of the Mobility service in Azure-to-Azure replication

Azure Site Recovery usa una cadenza di rilascio mensile per risolvere eventuali problemi e migliorare le funzionalità esistenti o aggiungerne di nuove. Per rimanere aggiornati con il servizio, è necessario pianificare la distribuzione delle patch ogni mese. Per evitare l'overhead associato a ogni aggiornamento, è possibile consentire a Site Recovery di gestire gli aggiornamenti dei componenti.

Come indicato nell'architettura di ripristino di emergenza da [Azure ad Azure,](azure-to-azure-architecture.md)il servizio Mobility viene installato in tutte le macchine virtuali di Azure (VM) in cui è abilitata la replica da un'area di Azure a un'altra. Quando si usano gli aggiornamenti automatici, ogni nuova versione aggiorna l'estensione del servizio Mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Funzionamento degli aggiornamenti automatici

Quando si usa Site Recovery per gestire gli aggiornamenti, viene distribuito un runbook globale (usato dai servizi di Azure) tramite un account di automazione, creato nella stessa sottoscrizione dell'insieme di credenziali. Ogni vault utilizza un account di automazione. Per ogni macchina virtuale in un insieme di credenziali, il runbook verifica la presenza di aggiornamenti automatici attivi. Se è disponibile una versione più recente dell'estensione del servizio Mobility, l'aggiornamento viene installato.

La pianificazione predefinita del runbook viene eseguita ogni giorno alle 12:00 nel fuso orario dell'area geografica della macchina virtuale replicata. È inoltre possibile modificare la pianificazione del runbook tramite l'account di automazione.

> [!NOTE]
> A partire [dall'aggiornamento cumulativo 35](site-recovery-whats-new.md#updates-march-2019), è possibile scegliere un account di automazione esistente da utilizzare per gli aggiornamenti. Prima dell'aggiornamento cumulativo 35, Site Recovery creava l'account di automazione per impostazione predefinita. È possibile selezionare questa opzione solo quando si abilita la replica per una macchina virtuale. Non è disponibile per una macchina virtuale in cui è già abilitata la replica. L'impostazione selezionata si applica a tutte le macchine virtuali di Azure protette nello stesso insieme di credenziali.

L'attivazione degli aggiornamenti automatici non richiede il riavvio delle macchine virtuali di Azure o influisce sulla replica in corso.

La fatturazione dei processi nell'account di automazione si basa sul numero di minuti di runtime del processo utilizzati in un mese. L'esecuzione del lavoro richiede da pochi secondi a circa un minuto al giorno ed è coperta come unità libere. Per impostazione predefinita, 500 minuti sono inclusi come unità gratuite per un account di automazione, come illustrato nella tabella seguente:

| Unità gratuite incluse (ogni mese) | Price |
|---|---|
| Runtime del processo 500 minuti | 0,14 USD/minuto

## <a name="enable-automatic-updates"></a>Abilitare gli aggiornamenti automatici

Esistono diversi modi in cui Site Recovery può gestire gli aggiornamenti delle estensioni:

- [Gestire come parte del passaggio di abilitazione della replicaManage as part of the enable replication step](#manage-as-part-of-the-enable-replication-step)
- [Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali](#toggle-the-extension-update-settings-inside-the-vault)
- [Gestire gli aggiornamenti manualmente](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gestire come parte del passaggio di abilitazione della replicaManage as part of the enable replication step

Quando si abilita la replica per una macchina virtuale a partire [dalla vista VM](azure-to-azure-quickstart.md) o [dall'insieme](azure-to-azure-how-to-enable-replication.md)di credenziali dei servizi di ripristino, è possibile consentire a Site Recovery di gestire gli aggiornamenti per l'estensione di Site Recovery o gestirla manualmente.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Impostazioni dell'estensione":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali

1. Nell'insieme di credenziali di Servizi di ripristino passare a **Gestisci** > **infrastruttura di Site Recovery**.
1. In Per le > impostazioni di**aggiornamento delle estensioni** **delle macchine** > virtuali di Azure**Consenti gestione ripristino sito**selezionare **Attivato**.

   Per gestire manualmente l'estensione, selezionare **Off**.

1. Selezionare **Save** (Salva).

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Impostazioni di aggiornamento dell'estensione":::

> [!IMPORTANT]
> Quando si sceglie **Consenti ripristino sito per la gestione**, l'impostazione viene applicata a tutte le macchine virtuali nell'insieme di credenziali.

> [!NOTE]
> Entrambe le opzioni notificano l'account di automazione utilizzato per la gestione degli aggiornamenti. Se si utilizza questa funzione in un vault per la prima volta, viene creato un nuovo account di automazione per impostazione predefinita. In alternativa, è possibile personalizzare l'impostazione e scegliere un account di automazione esistente. Tutti i tak successivi per abilitare la replica nello stesso insieme di credenziali utilizzeranno l'account di automazione creato in precedenza. Attualmente, il menu a discesa elencherà solo gli account di automazione che si trovano nello stesso gruppo di risorse dell'insieme di credenziali.

> [!IMPORTANT]
> Lo script seguente deve essere eseguito nel contesto di un account di automazione.
Per un account di automazione personalizzato, utilizzare lo script seguente:For a custom automation account, use the following script:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

            [Switch]
        $DisplayMessageToUser
        )

    Write-Output $Message

}

function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }

                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion

        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName

            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Gestire gli aggiornamenti manualmente

1. Se sono presenti nuovi aggiornamenti per il servizio Mobility installato nelle macchine virtuali, verrà visualizzata la notifica seguente: **Nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Finestra Elementi replicati":::

1. Selezionare la notifica per aprire la pagina di selezione della macchina virtuale.
1. Scegliere le macchine virtuali da aggiornare e quindi scegliere **OK**. Il servizio Per dispositivi mobili di aggiornamento verrà avviato per ogni macchina virtuale selezionata.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Elenco delle macchine virtuali in Elementi replicati":::

## <a name="common-issues-and-troubleshooting"></a>Problemi comuni e risoluzione dei problemi

Se si verifica un problema con gli aggiornamenti automatici, verrà visualizzata una notifica di errore in Problemi di **configurazione** nel dashboard dell'insieme di credenziali.

Se non è possibile abilitare gli aggiornamenti automatici, vedere gli errori comuni seguenti e le azioni consigliate:If you can't enable automatic updates, see the following common errors and recommended actions:

- **Errore**: non si hanno le autorizzazioni per creare un account RunAs di Azure (entità servizio) e concedere il ruolo di collaboratore all'entità servizio.

  **Azione consigliata:** assicurarsi che l'account connesso sia assegnato come Collaboratore e riprovare. Per altre informazioni sull'assegnazione delle autorizzazioni, vedere la sezione relativa alle autorizzazioni necessarie in [Procedura: Usare il portale per creare un'applicazione Azure AD e un'entità servizio in grado](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)di accedere alle risorse.

  Per risolvere la maggior parte dei problemi dopo aver abilitato gli aggiornamenti automatici, selezionare **Ripristina**. Se il pulsante di ripristino non è disponibile, vedere il messaggio di errore visualizzato nel riquadro delle impostazioni di aggiornamento dell'estensione.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Pulsante Ripristino del servizio Di Saldi sito nelle impostazioni di aggiornamento delle estensioni":::

- **Errore**: l'account RunAs non ha l'autorizzazione per accedere alla risorsa dei servizi di ripristino.

  **Azione consigliata**: Eliminare e [ricreare l'account RunAs](/azure/automation/automation-create-runas-account). In alternativa, assicurarsi che l'applicazione Azure Active Directory dell'account Automation RunAs possa accedere alla risorsa dei servizi di ripristino.

- **Errore**: impossibile trovare l'account RunAs. È possibile che uno degli elementi seguenti (applicazione di Azure Active Directory, entità servizio, ruolo, asset di certificato di Automazione, asset di connessione di Automazione) o l'identificazione personale non siano identici tra certificato e connessione.

  **Azione consigliata**: Eliminare e [ricreare l'account RunAs](/azure/automation/automation-create-runas-account).

- **Errore:** il certificato Esegui come utente dell'account di automazione sta per scadere.

  Il certificato autofirmato creato per l'account RunAs scade un anno dalla data di creazione. È possibile rinnovarlo in qualsiasi momento prima della scadenza. Se ti sei iscritto per le notifiche e-mail, riceverai anche e-mail quando è necessaria un'azione da parte tua. Questo errore verrà visualizzato due mesi prima della data di scadenza e verrà modificato in un errore critico se il certificato è scaduto. Una volta scaduto il certificato, l'aggiornamento automatico non funzionerà fino a quando non si rinnova lo stesso.

  **Azione consigliata**: Per risolvere il problema, selezionare **Ripristina** e quindi **Rinnova certificato**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renew-cert":::

  > [!NOTE]
  > Dopo aver rinnovato il certificato, aggiornare la pagina per visualizzare lo stato corrente.
