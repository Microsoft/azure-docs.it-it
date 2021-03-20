---
title: includere file
description: includere file
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3509185baa3a9d7be90c1fa4bd8000da4a8a6fe5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561915"
---
## <a name="prepare-for-akv-integration"></a>Preparare l'integrazione di AKV
Per usare l'integrazione dell'insieme di credenziali delle chiavi di Azure per configurare la macchina virtuale di SQL Server, sono necessari diversi prerequisiti: 

1. [Installare Azure PowerShell](#install)
2. [Creare un'istanza di Azure Active Directory](#register)
3. [Creare un insieme di credenziali delle chiavi](#createkeyvault)

Le sezioni seguenti descrivono tali prerequisiti e le informazioni da raccogliere per eseguire i cmdlet di PowerShell in un secondo momento.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Installa Azure PowerShell
Assicurarsi di aver installato il modulo Azure PowerShell più recente. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Registrare un'applicazione nel Azure Active Directory

Innanzitutto, è necessario che un'istanza di [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) sia presente nella sottoscrizione. AAD offre numerosi vantaggi, ad esempio, consente di concedere l'autorizzazione all'insieme di credenziali delle chiavi per determinati utenti e applicazioni.

Successivamente, registrare un'applicazione con AAD. In questo modo, è disponibile per l'utente un account dell'entità servizio con accesso all'insieme di credenziali delle chiavi necessario per la macchina virtuale. Nell'articolo Azure Key Vault è possibile trovare questi passaggi nella sezione [registrare un'applicazione con Azure Active Directory](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory) oppure è possibile visualizzare i passaggi con screenshot nella **sezione ottenere un'identità per l'applicazione** di [questo post di Blog](/archive/blogs/kv/azure-key-vault-step-by-step). Prima di completare questi passaggi, è necessario raccogliere le informazioni seguenti durante la registrazione, utili in seguito quando si abilita l'integrazione di Azure Key Vault nella macchina virtuale di SQL.

* Dopo aver aggiunto l'applicazione, trovare l' **ID applicazione** (noto anche come client AAD o AppID) nel pannello dell' **app registrata** .
    L'ID applicazione viene assegnato successivamente al parametro **$spName** (nome entità servizio) nello script di PowerShell per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

   ![ID applicazione](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Quando si crea la chiave durante la procedura, copiare il segreto della chiave come illustrato nello screenshot seguente. Tale segreto della chiave viene assegnato successivamente al parametro **$spSecret** (segreto entità servizio) nello script di PowerShell.

   ![Segreto AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* L'ID applicazione e il segreto verranno usati anche per creare le credenziali in SQL Server.

* È necessario autorizzare questo nuovo ID applicazione (o ID client) per avere le autorizzazioni di accesso seguenti: **Get**, **wrapKey**, **unwrapKey**. Questa operazione viene eseguita con il cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Per altre informazioni, vedere [Panoramica di Azure Key Vault](../articles/key-vault/general/overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Creare un insieme di credenziali delle chiavi
Per usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi da usare per la crittografia nella macchina virtuale, è necessario accedere a un insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi non è ancora stato configurato, crearne uno seguendo i passaggi nell'articolo [Introduzione ad Azure Key Vault](../articles/key-vault/general/overview.md). Prima di completare questi passaggi, è necessario raccogliere alcune informazioni durante l'impostazione, utili in seguito quando si abilita l'integrazione di Azure Key Vault nella macchina virtuale di SQL.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Al passaggio Creare un insieme di credenziali delle chiavi, si noti la proprietà **vaultUri** restituita, ovvero l'URL dell'insieme di credenziali delle chiavi. Nell'esempio fornito in questo passaggio, riportato di seguito, il nome dell'insieme di credenziali delle chiavi è ContosoKeyVault, quindi l'URL dell'insieme di credenziali delle chiavi è https://contosokeyvault.vault.azure.net/.

L'URL dell'insieme di credenziali delle chiavi viene assegnato successivamente al parametro **$akvURL** nello script di PowerShell, per abilitare l'integrazione dell'insieme di credenziali delle chiavi di Azure.

Dopo aver creato l'insieme di credenziali delle chiavi, è necessario aggiungere una chiave all'insieme di credenziali delle chiavi. Questa chiave verrà usata successivamente come riferimento durante la creazione di una chiave asimmetrica in SQL Server.