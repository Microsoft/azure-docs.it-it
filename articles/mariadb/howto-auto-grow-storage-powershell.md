---
title: Aumento automatico delle dimensioni di archiviazione-Azure PowerShell database di Azure per MariaDB
description: Questo articolo descrive come abilitare l'aumento automatico delle dimensioni di archiviazione usando PowerShell nel database di Azure per MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ef495fc884b1930f8510b6b2443bde40e269a8f1
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665158"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Aumento automatico delle dimensioni di archiviazione nel database di Azure per il server MariaDB con PowerShell

Questo articolo descrive come configurare un database di Azure per l'archiviazione del server MariaDB in modo che cresca senza compromettere il carico di lavoro.

L'aumento automatico dell'archiviazione impedisce al server di [raggiungere il limite di archiviazione](concepts-pricing-tiers.md#reaching-the-storage-limit) e di diventare di sola lettura. Per i server con almeno 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione viene aumentata di 5 GB quando lo spazio libero è inferiore al 10%. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione viene aumentata del 5% quando lo spazio libero è inferiore a 10 GB. I limiti di archiviazione massimi vengono applicati come specificato nella sezione archiviazione dei [piani tariffari del database di Azure per MariaDB](concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Tenere presente che lo spazio di archiviazione può essere scalato solo, non inattivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Durante la fase di anteprima del modulo Az.MariaDb PowerShell, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Quando il modulo Az.MariaDb di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-mariadb-server-storage-auto-grow"></a>Abilita aumento automatico dell'archiviazione del server MariaDB

Abilitare l'aumento di dimensioni dell'archiviazione automatica del server in un server esistente con il comando seguente:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Abilitare l'archiviazione automatica delle dimensioni del server durante la creazione di un nuovo server con il comando seguente:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come creare e gestire le repliche di lettura nel database di Azure per MariaDB usando PowerShell](howto-read-replicas-powershell.md).
