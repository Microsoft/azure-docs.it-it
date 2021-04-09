---
title: Crittografia doppia dell'infrastruttura-portale di Azure-database di Azure per PostgreSQL
description: Informazioni su come configurare e gestire la crittografia doppia dell'infrastruttura per il database di Azure per PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 03/14/2021
ms.openlocfilehash: 0b1079a084072556ae79a18a12f6b35f74c92775
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771652"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Crittografia doppia infrastruttura per database di Azure per PostgreSQL

Informazioni su come usare la crittografia doppia per la configurazione e la gestione dell'infrastruttura per il database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di una sottoscrizione di Azure e avere il ruolo di amministratore di tale sottoscrizione.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Creare un database di Azure per il server PostgreSQL con infrastruttura doppia crittografia-portale

Seguire questa procedura per creare un database di Azure per il server PostgreSQL con l'infrastruttura di crittografia doppia da portale di Azure:

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).

2. Selezionare **Database** > **Database di Azure per PostgreSQL**. È anche possibile immettere PostgreSQL nella casella di ricerca per trovare il servizio. È stata abilitata l'opzione di distribuzione a **server singolo** .

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Database di Azure per PostgreSQL nel menu":::

3. Fornire le informazioni di base del server. Selezionare **Impostazioni aggiuntive** e abilitare la casella di controllo **crittografia doppia infrastruttura** per impostare il parametro.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="Selezioni per database di Azure per PostgreSQL":::

4. Selezionare **Rivedi e crea** per effettuare il provisioning del server.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="Riepilogo di database di Azure per PostgreSQL":::

5. Una volta creato il server, è possibile convalidare la crittografia a doppia infrastruttura controllando lo stato nel pannello **Data Encryption** server.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="Convalida di database di Azure per MySQL":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Creare un database di Azure per il server PostgreSQL con infrastruttura doppia crittografia-interfaccia della riga di comando

Seguire questa procedura per creare un database di Azure per il server PostgreSQL con l'infrastruttura doppia crittografia dall'interfaccia della riga di comando:

Questo esempio crea un gruppo di risorse denominato `myresourcegroup` nella `westus` posizione.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
L'esempio seguente crea un server PostgreSQL 11 negli Stati Uniti occidentali denominato `mydemoserver` nel gruppo di risorse `myresourcegroup` con l'account di accesso amministratore del server `myadmin` . Questo è un server per **utilizzo generico di** **generazione 4** con **2 vCore**. Verrà abilitata anche la crittografia doppia dell'infrastruttura per il server creato. Sostituire `<server_admin_password>` con il proprio valore.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla crittografia dei dati, vedere [crittografia doppia dell'infrastruttura dati di database di Azure per PostgreSQL](concepts-Infrastructure-double-encryption.md).

