---
title: Configurare il firewall del database di Azure blockchain Workbench
description: Informazioni su come configurare il firewall del database di anteprima di Azure blockchain Workbench per consentire ai client esterni e alle applicazioni di connettersi.
ms.date: 09/09/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: dc7a1dc7e4a083ef8f5f7650f29ed1430b32e88b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214197"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Configurare il firewall del database di Azure Blockchain Workbench

Questo articolo illustra come configurare una regola del firewall tramite il portale di Azure. Le regole del firewall consentono ai client e alle applicazioni esterne di connettersi al database di Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Connettersi al database di Blockchain Workbench

Per connettersi al database in cui si desidera configurare una regola:

1. Accedere al portale di Azure con un account con autorizzazioni **proprietario** per le risorse di Azure blockchain Workbench.
2. Nel riquadro di spostamento a sinistra scegliere **Gruppi di risorse**.
3. Scegliere il nome del gruppo di risorse per la distribuzione di Blockchain Workbench.
4. Selezionare **Tipo** per ordinare l'elenco di risorse e quindi scegliere **SQL server**.
5. L'elenco di risorse di esempio nello screenshot seguente illustra due database: *master* e *lsgn-sdk*. Configurare la regola del firewall in *lsgn-sdk*.

![Elenco delle risorse di Blockchain Workbench](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Creare una regola del firewall del database

Per creare una regola del firewall:

1. Scegliere il collegamento al database "lsgn-sdk".
2. Nella barra dei menu selezionare **Imposta firewall server**.

   ![Impostare il firewall del server](./media/database-firewall/configure-server-firewall.png)

3. Per creare una regola per l'organizzazione:

   * Immettere un **NOME REGOLA**
   * Immettere un indirizzo IP per l'**INDIRIZZO IP INIZIALE** dell'intervallo di indirizzi
   * Immettere un indirizzo IP per l'**INDIRIZZO IP FINALE** dell'intervallo di indirizzi

   ![Creare la regola del firewall](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Se si desidera aggiungere solo l'indirizzo IP del computer, scegliere **+ Aggiungi IP client**.
        
1. Per salvare la configurazione del firewall, selezionare **Salva**.
2. Testare l'intervallo di indirizzi IP configurato per il database eseguendo la connessione da un'applicazione o dallo strumento. Ad esempio, SQL Server Management Studio.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Viste di database in Azure Blockchain Workbench)