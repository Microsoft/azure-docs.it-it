---
title: Creare certificati usando Microsoft Azure Stack strumento di controllo dello stato di preparazione dell'hub | Microsoft Docs
description: Viene descritto come creare richieste di certificato e quindi ottenere e installare i certificati sul dispositivo GPU Azure Stack Edge Pro usando lo strumento di controllo della conformità dell'hub Azure Stack.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/24/2020
ms.author: alkohli
ms.openlocfilehash: ab9559e1e8265b3adf08b36d1a8097a00297c61a
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96606991"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Creare certificati per il Azure Stack Edge Pro usando lo strumento di controllo della conformità dell'hub Azure Stack 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive come creare certificati per il Azure Stack Edge Pro usando lo strumento di controllo della conformità dell'hub Azure Stack. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Uso dello strumento di controllo della conformità dell'hub Azure Stack

Usare lo strumento di controllo dello stato di preparazione dell'hub Azure Stack per creare richieste di firma del certificato (I CSR) per una distribuzione di dispositivi Pro Azure Stack Edge. È possibile creare queste richieste dopo aver inserito un ordine per il dispositivo Azure Stack Edge Pro e attendere l'arrivo del dispositivo.

> [!NOTE]
> Usare questo strumento solo a scopo di test o sviluppo e non per i dispositivi di produzione. 

Per richiedere i certificati seguenti, è possibile usare lo strumento di controllo di conformità dell'hub Azure Stack (AzsReadinessChecker):

- Certificato Azure Resource Manager
- Certificato dell'interfaccia utente locale
- Certificato del nodo
- Certificato BLOB
- Certificato VPN


## <a name="prerequisites"></a>Prerequisiti

Per creare I CSR per la distribuzione di dispositivi Pro Azure Stack Edge, verificare che: 

- È disponibile un client che esegue Windows 10 o Windows Server 2016 o versione successiva. 
- È stato scaricato lo strumento di controllo dello stato di preparazione dell'Hub Microsoft Azure Stack [dal PowerShell Gallery](https://aka.ms/AzsReadinessChecker) nel sistema.
- Per i certificati sono disponibili le informazioni seguenti:
  - Nome del dispositivo
  - Numero di serie del nodo
  - Nome di dominio completo (FQDN) esterno

## <a name="generate-certificate-signing-requests"></a>Genera richieste di firma del certificato

Seguire questa procedura per preparare i certificati del dispositivo Azure Stack Edge Pro:

1. Eseguire PowerShell come amministratore (5,1 o versione successiva).
2. Installare lo strumento di controllo dello stato di preparazione dell'hub Azure Stack. Al prompt di PowerShell digitare: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Per ottenere la versione installata, digitare:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Se non ne è già presente uno, creare una directory per tutti i certificati. Digitare: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Per creare una richiesta di certificato, fornire le informazioni seguenti. Se si genera un certificato VPN, alcuni di questi input non si applicano.
    
    |Input |Descrizione  |
    |---------|---------|
    |`OutputRequestPath`|Percorso del file nel client locale in cui si desidera creare le richieste di certificato.        |
    |`DeviceName`|Il nome del dispositivo nella pagina **dispositivi** nell'interfaccia utente Web locale del dispositivo. <br> Questo campo non è obbligatorio per un certificato VPN.         |
    |`NodeSerialNumber`|Il numero di serie del nodo del dispositivo nella pagina **rete** nell'interfaccia utente Web locale del dispositivo. <br> Questo campo non è obbligatorio per un certificato VPN.       |
    |`ExternalFQDN`|Il valore DNSDomain nella pagina **dispositivi** dell'interfaccia utente Web locale del dispositivo.         |
    |`RequestType`|Il tipo di richiesta può essere per `MultipleCSR` certificati diversi per i vari endpoint o per `SingleCSR` un singolo certificato per tutti gli endpoint. <br> Questo campo non è obbligatorio per un certificato VPN.     |

    Per tutti i certificati ad eccezione del certificato VPN, digitare: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Se si sta creando un certificato VPN, digitare: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. I file della richiesta di certificato sono disponibili nella directory specificata nel parametro OutputRequestPath. Quando si usa il `MultipleCSR` parametro, verranno visualizzati i quattro file seguenti con l' `.req` estensione:

    
    |Nomi di file  |Tipo di richiesta di certificato  |
    |---------|---------|
    |A partire da `DeviceName`     |Richiesta di certificato dell'interfaccia utente Web locale      |
    |A partire da `NodeSerialNumber`     |Richiesta di certificato del nodo         |
    |A partire da `login`     |Richiesta certificato endpoint Azure Resource Manager       |
    |A partire da `wildcard`     |Richiesta di certificato di archiviazione BLOB. Contiene un carattere jolly perché copre tutti gli account di archiviazione che è possibile creare nel dispositivo.          |
    |A partire da `AzureStackEdgeVPNCertificate`     |Richiesta di certificato client VPN.         |

    Verrà visualizzata anche una cartella INF. Contiene un file di informazioni di Management. <Edge-DeviceName> in testo non crittografato che illustra i dettagli del certificato.  


6. Inviare i file all'autorità di certificazione (interna o pubblica). Assicurarsi che la CA generi certificati, usando la richiesta generata, che soddisfino i requisiti dei certificati Pro Azure Stack Edge per i [certificati del nodo](azure-stack-edge-j-series-manage-certificates.md#node-certificates), i [certificati dell'endpoint](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)e i [certificati dell'interfaccia utente locale](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates).

## <a name="prepare-certificates-for-deployment"></a>Preparare i certificati per la distribuzione

I file del certificato che si ottengono dall'autorità di certificazione (CA) devono essere importati ed esportati con proprietà corrispondenti ai requisiti del certificato del dispositivo Azure Stack Edge Pro. Completare i passaggi seguenti nello stesso sistema in cui sono state generate le richieste di firma del certificato.

- Per importare i certificati, seguire la procedura descritta in [importare i certificati nei client che accedono al dispositivo Azure stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Per esportare i certificati, seguire la procedura descritta in [esportare i certificati dal client che accede al dispositivo Azure stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Convalida certificati

Innanzitutto, verrà generata una struttura di cartelle appropriata e i certificati verranno posizionati nelle cartelle corrispondenti. Verranno convalidati solo i certificati utilizzando lo strumento.

1. Eseguire PowerShell come amministratore.

2. Per generare la struttura di cartelle appropriata, al tipo di messaggio di richiesta:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Convertire la password PFX in una stringa sicura. Digitare:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Successivamente, convalidare i certificati. Digitare:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
