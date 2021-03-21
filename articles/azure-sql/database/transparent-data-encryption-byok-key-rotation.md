---
title: Ruotare la protezione Transparent Data Encryption (PowerShell & interfaccia della riga di comando di Azure
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Informazioni su come ruotare la protezione Transparent Data Encryption (Transparent Data Encryption) per un server in Azure usato dal database SQL di Azure e da Azure sinapsi Analytics tramite PowerShell e l'interfaccia della riga di comando di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 37931aee24e2dbcab03bca400d58f236601c87e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321432"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Ruotare la protezione Transparent Data Encryption (Transparent Data Encryption)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Questo articolo descrive la rotazione della chiave per un [Server](logical-servers.md) che usa una protezione Transparent Data encryption da Azure Key Vault. La rotazione della protezione Transparent Transparent per un server comporta il cambio di una nuova chiave asimmetrica che protegge i database nel server. La rotazione delle chiavi è un'operazione online che può richiedere solo alcuni secondi per il completamento, perché questo consente solo di decrittografare e crittografare di nuovo la chiave di crittografia dei dati del database, non l'intero database.

In questa guida vengono illustrate due opzioni per ruotare la protezione TDE nel server.

> [!NOTE]
> Un pool SQL dedicato sospeso in Azure sinapsi Analytics deve essere ripreso prima della rotazione della chiave.

> [!IMPORTANT]
> Non eliminare le versioni precedenti della chiave dopo un rollover. Quando le chiavi vengono ruotate, alcuni dati vengono comunque crittografati con le chiavi precedenti, ad esempio, i backup meno recenti del database.

## <a name="prerequisites"></a>Prerequisiti

- In questa guida si presuppone che si stia già usando una chiave di Azure Key Vault come protezione Transparent Data Encryption per il database SQL di Azure o l'analisi delle sinapsi di Azure. Vedere [Transparent Data Encryption con supporto BYOK](transparent-data-encryption-byok-overview.md).
- È necessario che Azure PowerShell sia installato e in esecuzione.
- [Consigliata ma facoltativa] Creare innanzitutto il materiale della chiave per la protezione TDE in un modulo di protezione hardware (HSM) o in un archivio chiavi locale e importare il materiale della chiave in Azure Key Vault. Seguire le [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../../key-vault/general/overview.md) per altre informazioni.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Per i cmdlet specifici, vedere [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'installazione, vedere [installare l'interfaccia della](/cli/azure/install-azure-cli)riga di comando di Azure.

* * *

## <a name="manual-key-rotation"></a>Rotazione manuale delle chiavi

La rotazione manuale della chiave usa i comandi seguenti per aggiungere una chiave completamente nuova che potrebbe trovarsi in un nuovo nome di chiave o anche in un altro insieme di credenziali delle chiavi. Questo approccio consente di aggiungere la stessa chiave a diversi insiemi di credenziali delle chiavi per supportare scenari di ripristino di emergenza geografico a disponibilità elevata.

> [!NOTE]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare i cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare i comandi [AZ Key Vault create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key create](/cli/azure/sql/server/key#az-sql-server-key-create)e [AZ SQL Server](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) Transparent key set.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Passa alla modalità di protezione Transparent Data Encryption

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Per passare la protezione Transparent Data Encryption dalla modalità gestita da Microsoft a BYOK, usare il cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Per passare la protezione Transparent Data Encryption dalla modalità BYOK a quella gestita da Microsoft, usare il cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Gli esempi seguenti usano [AZ SQL Server](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)Transparent key set.

- Per passare la protezione Transparent Data Encryption dalla modalità gestita da Microsoft a BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Per passare la protezione Transparent Data Encryption dalla modalità BYOK a quella gestita da Microsoft

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Passaggi successivi

- In caso di rischi per la sicurezza, informazioni su come rimuovere una protezione Transparent Data Encryption potenzialmente compromessa: [rimuovere una chiave potenzialmente compromessa](transparent-data-encryption-byok-remove-tde-protector.md).

- Introduzione all'integrazione Azure Key Vault e al supporto Bring Your Own Key per Transparent Data Encryption: attivare Transparent [Data Encryption con la propria chiave da Key Vault usando PowerShell](transparent-data-encryption-byok-configure.md).