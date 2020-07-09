---
title: Risoluzione dei problemi di avvio di BitLocker in una VM di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi di avvio di BitLocker in una VM di Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: e0e0d9bfe46a473210b89701b5a8c56e999771d3
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86132949"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Problemi di avvio di BitLocker in una macchina virtuale di Azure

 Questo articolo descrive i potenziali problemi di BitLocker che possono verificarsi all'avvio di una macchina virtuale Windows in Microsoft Azure.

 

## <a name="symptom"></a>Sintomo

 La macchina virtuale Windows non si avvia. Quando si controllano gli screenshot nella finestra [Diagnostica di avvio](../windows/boot-diagnostics.md), viene visualizzato uno dei messaggi di errore seguenti:

- Collegare l'unità USB contenente la chiave BitLocker

- Il tuo account è bloccato. Immetti la chiave di ripristino per continuare (Layout di tastiera: US) Troppi tentativi di accesso con informazioni errate. Il PC è stato bloccato per proteggere la privacy dell'utente. Per recuperare la chiave di ripristino, visitare https://windows.microsoft.com/recoverykeyfaq da un altro PC o dispositivo mobile. Nel caso sia necessario, l'ID della chiave è XXXXXXX. In alternativa, è possibile ripristinare le impostazioni di fabbrica del PC.

- Digitare la password per sbloccare l'unità [ ] Premere INS per visualizzare la password durante la digitazione.
- Immetti la chiave di ripristino Carica la chiave di ripristino da un dispositivo USB.

## <a name="cause"></a>Causa

Questo problema può verificarsi se la macchina virtuale non riesce a individuare il file della chiave di ripristino di BitLocker (BEK) per decrittografare il disco crittografato.

## <a name="solution"></a>Soluzione

Per risolvere questo problema, arrestare e deallocare la macchina virtuale e quindi riavviarla. Questa operazione forza la macchina virtuale a recuperare il file con estensione BEK da Azure Key Vault e quindi lo inserisce nel disco crittografato. 

Se questo metodo non risolve il problema, seguire questa procedura per ripristinare manualmente il file con estensione BEK:

1. Creare uno snapshot del disco di sistema della macchina virtuale in questione come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).
2. [Collegare il disco di sistema a una macchina virtuale di ripristino](troubleshoot-recovery-disks-portal-windows.md). Per eseguire il comando [Manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) nel passaggio 7, è necessario abilitare la funzionalità **crittografia unità BitLocker** nella macchina virtuale di ripristino.

    Quando si collega un disco gestito, potrebbe essere visualizzato un messaggio di errore che indica la presenza di impostazioni di crittografia e l'impossibilità di usare il disco come disco dati. In questo caso, eseguire lo script seguente per provare nuovamente a collegare il disco:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Non è possibile collegare un disco gestito a una macchina virtuale che è stata ripristinata dall'immagine di un BLOB.

3. Dopo aver collegato il disco, creare una connessione desktop remota alla macchina virtuale di ripristino in modo da poter eseguire alcuni script di Azure PowerShell. Verificare di aver installato la [versione più recente di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nella macchina virtuale di ripristino.

4. Aprire una sessione di Azure PowerShell con privilegi elevati (Esegui come amministratore). Eseguire i comandi seguenti per accedere alla sottoscrizione di Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Eseguire lo script seguente per controllare il nome del file con estensione BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Di seguito è riportato un esempio di output. Individuare il nome del file con estensione BEK per il disco collegato. In questo caso si presuppone che la lettera di unità del disco collegato sia F e che il file con estensione BEK sia EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Se vengono visualizzati due volumi duplicati, il volume con il timestamp più recente è il file con estensione BEK corrente usato dalla macchina virtuale di ripristino.

    Se il valore di **Content Type** è **Wrapped BEK**, passare agli [scenari relativi alla chiave di crittografia della chiave (KEK)](#key-encryption-key-scenario).

    Dopo aver ottenuto il nome del file con estensione BEK dell'unità, per sbloccare l'unità è necessario creare il file secret-file-name.BEK.

6.  Scaricare il file con estensione BEK sul disco di ripristino. L'esempio seguente salva il file con estensione BEK nella cartella C:\BEK. Verificare che il percorso `C:\BEK\` esista prima di eseguire gli script.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Per sbloccare il disco collegato utilizzando il file di base, eseguire il comando seguente.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    In questo esempio, il disco del sistema operativo collegato è l'unità F. Verificare di usare la lettera di unità corretta. 

8. Dopo che il disco è stato sbloccato correttamente usando la chiave di il sistema di modifica, scollegare il disco dalla macchina virtuale di ripristino e quindi ricreare la macchina virtuale usando il nuovo disco del sistema operativo.

    > [!NOTE]
    > Lo scambio del disco del sistema operativo non è supportato per le macchine virtuali che usano la crittografia del disco.

9. Se la nuova macchina virtuale non è ancora in grado di eseguire l'avvio normalmente, provare a eseguire una delle operazioni seguenti dopo aver sbloccato l'unità:

    - Sospendere la protezione per disattivare temporaneamente BitLocker eseguendo quanto segue:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Decrittografare completamente l'unità. A tale scopo, usare il comando seguente:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Scenario con chiave di crittografia della chiave

In uno scenario con chiave di crittografia della chiave, seguire questa procedura:

1. Verificare che l'account utente connesso richieda l'autorizzazione senza wrapping nei criteri di accesso dell'insieme di credenziali delle chiavi in **UTENTE|Autorizzazioni chiave| Operazioni crittografiche|Annulla il wrapping della chiave**.
2. Salvare lo script seguente in un. File PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Impostare i parametri. Lo script elabora il segreto della chiave KEK per creare la chiave BEK e quindi lo salva in una cartella locale nella macchina virtuale di ripristino. Se vengono visualizzati errori durante l'esecuzione dello script, vedere la sezione relativa alla [risoluzione dei problemi degli script](#script-troubleshooting) .

4. All'avvio dello script viene visualizzato l'output seguente:

    Percorso versione GAC                                                                              
    ---    -------        --------                                                                              
    False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..  False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..

    Al termine dell'esecuzione dello script viene visualizzato l'output seguente:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. Per sbloccare il disco collegato usando il file con estensione BEK, eseguire il comando seguente:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    In questo esempio, il disco del sistema operativo collegato è l'unità F. Verificare di usare la lettera di unità corretta. 

6. Dopo che il disco è stato sbloccato correttamente usando la chiave di il sistema di modifica, scollegare il disco dalla macchina virtuale di ripristino e quindi ricreare la macchina virtuale usando il nuovo disco del sistema operativo. 

    > [!NOTE]
    > Lo scambio del disco del sistema operativo non è supportato per le macchine virtuali che usano la crittografia del disco.

7. Se la nuova macchina virtuale non è ancora in grado di eseguire l'avvio normalmente, provare a eseguire una delle operazioni seguenti dopo aver sbloccato l'unità:

    - Sospendere la protezione per disattivare temporaneamente BitLocker eseguendo il comando seguente:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Decrittografare completamente l'unità. A tale scopo, usare il comando seguente:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Risoluzione dei problemi relativi agli script

**Errore: Impossibile caricare il file o l'assembly**

Questo errore si verifica perché i percorsi degli assembly ADAL sono errati. Se il modulo AZ è installato solo per l'utente corrente, gli assembly ADAL verranno posizionati in `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>` .

È anche possibile cercare `Az.Accounts` la cartella per trovare il percorso corretto.

**Errore: Get-AzKeyVaultSecret o Get-AzKeyVaultSecret non è riconosciuto come nome di un cmdlet**

Se si usa il vecchio modulo AZ PowerShell, è necessario modificare i due comandi in `Get-AzureKeyVaultSecret` e `Get-AzureKeyVaultSecret` .

**Esempi di parametri**

| Parametri  | Esempio di valore  |Commenti   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Nome dell'insieme di credenziali delle chiavi in cui è archiviata la chiave |
|$kekName   |MyKey   | Nome della chiave usata per crittografare la macchina virtuale|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Nome del segreto della chiave della macchina virtuale|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |Percorso per la scrittura del file di codice.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN o GUID del Azure Active Directory che ospita l'insieme di credenziali delle chiavi |
