---
title: 'Avvio rapido: Creare una VM di SQL Server Linux in Azure'
description: Questa esercitazione illustra come creare una macchina virtuale Linux di SQL Server 2017 nel portale di Azure.
services: virtual-machines-linux
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fd5c289f2b441b5862d863d9a390a1cd054acbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790152"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Effettuare il provisioning di una macchina virtuale Linux che esegue SQL Server nel portale di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

In questa esercitazione di avvio rapido si usa il portale di Azure per creare una macchina virtuale Linux con SQL Server 2017 installato. Verranno illustrate le operazioni seguenti: 


* [Creare una VM Linux che esegue SQL Server dalla raccolta](#create)
* [Connettersi alla nuova VM con ssh](#connect)
* [Modificare la password dell'amministratore di sistema](#password)
* [Configurare connessioni remote](#remote)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> Creare una VM Linux con SQL Server installato

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel riquadro a sinistra selezionare **Crea risorsa**.

1. Nel riquadro **Crea una risorsa** selezionare **Calcolo**.

1. Selezionare **Visualizza tutto** accanto all'intestazione **In primo piano**.

   ![Visualizzare tutte le immagini di VM](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. Nella casella di ricerca digitare **SQL Server 2019** e selezionare **INVIO** per avviare la ricerca.

1. Limitare i risultati della ricerca selezionando **Sistema operativo** > **RedHat**.

    ![Filtro di ricerca per le immagini di VM di SQL Server 2019](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Selezionare un'immagine Linux di SQL Server 2019 nei risultati della ricerca. Questa esercitazione usa **SQL Server 2019 su RHEL74**.

   > [!TIP]
   > L'edizione Developer consente di testare o sviluppare con le funzionalità dell'edizione Enterprise, ma senza i costi di licenza per SQL Server. Si paga solo il costo dell'esecuzione della VM Linux.

1. Selezionare **Crea**. 


### <a name="set-up-your-linux-vm"></a>Configurare la macchina virtuale Linux

1. Nella scheda **Informazioni di base** selezionare le opzioni per **Sottoscrizione** e **Gruppo di risorse**. 

    ![Finestra Informazioni di base](./media/sql-vm-create-portal-quickstart/basics.png)

1. In **Nome macchina virtuale** immettere un nome per la nuova macchina virtuale Linux.
1. Digitare o selezionare quindi i valori seguenti:
   * **Area**: selezionare l'area di Azure appropriata.
   * **Opzioni di disponibilità**: scegliere l'opzione di ridondanza e disponibilità più appropriata per le app e i dati in uso.
   * **Modifica dimensioni**: selezionare questa opzione per scegliere una dimensione di macchina e al termine scegliere **Seleziona**. Per altre informazioni sulle dimensioni delle VM, vedere [Dimensioni delle macchine virtuali](../../../virtual-machines/sizes.md).

     ![Scegliere le dimensioni per la macchina virtuale](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > Per lo sviluppo e i test funzionali, usare la dimensione di macchina virtuale **DS2** o superiore. Per test delle prestazioni, usare **DS13** o una dimensione superiore.

   * **Tipo di autenticazione**: selezionare **Chiave pubblica SSH**.

     > [!Note]
     > Per l'autenticazione, si può scegliere di usare una chiave pubblica SSH o una password. L'opzione più sicura è SSH. Per istruzioni su come generare una chiave SSH, vedere l'articolo su come [creare chiavi SSH in Linux e Mac per le VM Linux in Azure](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Nome utente**: immettere il nome dell'amministratore della macchina virtuale.
   * **Chiave pubblica SSH**: immettere la chiave pubblica RSA.
   * **Porte in ingresso pubbliche**: scegliere **Consenti porte selezionate** e selezionare la porta **SSH (22)** nell'elenco **Selezionare le porte in ingresso pubbliche**. In questo argomento di avvio rapido questo passaggio è necessario per connettersi e completare la configurazione di SQL Server. Per connettersi in remoto a SQL Server, è necessario consentire manualmente il traffico alla porta predefinita (1433) usata da Microsoft SQL Server per le connessioni su Internet dopo la creazione della macchina virtuale.

     ![Porte in ingresso](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Apportare le modifiche desiderate alle impostazioni nelle schede aggiuntive seguenti o mantenere le impostazioni predefinite.
    * **Dischi**
    * **Rete**
    * **Gestione**
    * **Configurazione guest**
    * **Tag**

1. Selezionare **Rivedi e crea**.
1. Nel riquadro **Rivedi e crea** selezionare **Crea**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Connettersi alla VM Linux

Se si usa già una shell BASH, connettersi alla VM di Azure con il comando **ssh**. Nel comando seguente, sostituire il nome utente e l'indirizzo IP della VM per connettersi alla propria VM Linux.

```bash
ssh azureadmin@40.55.55.555
```

L'indirizzo IP della VM è riportato nel portale di Azure.

![Indirizzo IP nel portale di Azure](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Se l'esecuzione avviene in Windows e non si ha una shell BASH, installare un client SSH, ad esempio PuTTY.

1. [Scaricare e installare PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Eseguire PuTTY.

1. Nella schermata di configurazione di PuTTY immettere l'indirizzo IP pubblico della VM.

1. Selezionare **Apri** e immettere nome utente e password quando richiesto.

Per altre informazioni sulla connessione alle VM Linux, vedere l'articolo su come [creare una VM Linux in Azure con il portale](../../../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Se viene visualizzato un avviso di sicurezza PuTTY relativo alla mancata memorizzazione nella cache della chiave host del server, scegliere tra le opzioni seguenti. Se si considera attendibile l'host, selezionare **Sì** per aggiungere la chiave nella cache di PuTTy e continuare a la procedura di connessione. Se si vuole eseguire la connessione una sola volta, senza aggiungere la chiave nella cache, selezionare **No**. Se non si considera attendibile l'host, selezionare **Annulla** per abbandonare la connessione.

## <a name="change-the-sa-password"></a><a id="password"></a> Cambiare la password dell'amministratore di sistema

La nuova macchina virtuale installa SQL Server con una password dell'amministratore di sistema casuale. Prima di connettersi a SQL Server con l'account di accesso dell'amministratore di sistema, reimpostare questa password.

1. Dopo la connessione alla VM Linux, aprire un nuovo terminale di comando.

1. Modificare la password dell'amministratore di sistema con i comandi seguenti:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Immettere una nuova password dell'amministratore di sistema e la conferma della password quando richiesto.

1. Riavviare il servizio SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Aggiungere gli strumenti al percorso (facoltativo)

Per impostazione predefinita vengono installati diversi [pacchetti](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) di SQL Server, incluso il pacchetto degli strumenti da riga di comando di SQL Server. Questo pacchetto contiene gli strumenti **sqlcmd** e **bcp**. Per praticità, si può facoltativamente aggiungere il percorso degli strumenti, `/opt/mssql-tools/bin/`, alla variabile di ambiente **PATH**.

1. Eseguire i comandi seguenti per modificare la variabile **PATH** sia per le sessioni di accesso che per le sessioni interattive/non di accesso:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Configurare connessioni remote

Se è necessario connettersi in remoto a SQL Server nella VM di Azure, è necessario configurare una regola in ingresso nel gruppo di sicurezza di rete. La regola consentirà il traffico sulla porta su cui è in ascolto SQL Server (per impostazione predefinita, la porta 1433). La procedura seguente illustra come usare il portale di Azure per questo passaggio.

> [!TIP]
> Se è stata selezionata la porta in ingresso **MS SQL (1433)** nelle impostazioni durante il provisioning, queste modifiche sono state effettuate automaticamente. È possibile passare alla sezione successiva di configurazione del firewall.

1. Nel portale selezionare **Macchine virtuali** e quindi la propria macchina virtuale di SQL Server.
1. Nel riquadro di spostamento a sinistra, in **Impostazioni** selezionare **Rete**.
1. Nella finestra Rete selezionare **Aggiungi porta in ingresso** in **Regole porta in ingresso**.

   ![Regole porta in ingresso](./media/sql-vm-create-portal-quickstart/networking.png)

1. Nell'elenco **Servizio** selezionare **MS SQL**.

    ![Regola del gruppo di sicurezza per MS SQL](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. Fare clic su **OK** per salvare la regola creata per la macchina virtuale.

### <a name="open-the-firewall-on-rhel"></a>Aprire il firewall in RHEL

Questa esercitazione ha descritto come creare una VM Red Hat Enterprise Linux (RHEL). Per connettersi in remoto alle VM RHEL, è necessario anche aprire la porta 1433 nel firewall Linux.

1. [Connettersi](#connect) alla VM RHEL.

1. Nella shell BASH eseguire questi comandi:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Passaggi successivi

Ora che è disponibile una macchina virtuale di SQL Server 2017 in Azure, è possibile connettersi in locale con **sqlcmd** per eseguire query Transact-SQL.

Se la macchina virtuale di Azure è stata configurata per le connessioni remote a SQL Server, sarà possibile connettersi in remoto. Per un esempio di come connettersi in remoto a SQL Server in Linux da Windows, vedere l'articolo su come [usare SSMS in Windows per connettersi a SQL Server in Linux](/sql/linux/sql-server-linux-develop-use-ssms). Per connettersi con Visual Studio Code, vedere [Usare Visual Studio Code per creare ed eseguire script Transact-SQL per SQL Server](/sql/linux/sql-server-linux-develop-use-vscode).

Per altre informazioni generali su SQL Server in Linux, vedere la [panoramica di SQL Server 2017 in Linux](/sql/linux/sql-server-linux-overview). Per altre informazioni sull'uso di macchine virtuali Linux di SQL Server 2017, vedere la [panoramica delle macchine virtuali di SQL Server 2017 in Azure](sql-server-on-linux-vm-what-is-iaas-overview.md).