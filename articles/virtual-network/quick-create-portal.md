---
title: 'Avvio rapido: Creare una rete virtuale - Portale di Azure'
titleSuffix: Azure Virtual Network
description: Questa guida introduttiva illustra come creare una rete virtuale usando il portale di Azure.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531184"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Avvio rapido: Creare una rete virtuale usando il portale di Azure

Questo argomento di avvio rapido illustra come creare una rete virtuale con il portale di Azure. Vengono distribuite due macchine virtuali (VM). Successivamente, si stabilisce una comunicazione sicura tra le VM, a cui ci si connette da Internet. Una rete virtuale è il blocco costitutivo fondamentale per una rete privata in Azure. Consente la comunicazione sicura tra le risorse di Azure, come le VM, e con Internet.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale.

2. Nella casella di ricerca immettere **Rete virtuale**. Selezionare **Rete virtuale** nei risultati della ricerca.

3. Nella pagina **Rete virtuale** selezionare **Crea**.

4. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **Crea nuovo**.  </br> Immettere **myResourceGroup**. </br> Selezionare **OK**. |
    | **Dettagli istanza** |   |
    | Nome | Immettere **myVNet**. |
    | Region | Selezionare **Stati Uniti orientali.** |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Creare reti virtuali portale di Azure" border="true":::

5. Selezionare la **scheda Indirizzi IP** o selezionare il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

6. Nello **spazio indirizzi IPv4** selezionare lo spazio indirizzi esistente e modificarlo in **10.1.0.0/16.**

7. Selezionare **+ Aggiungi subnet,** quindi immettere **MySubnet** per **Nome subnet** e **10.1.0.0/24** per **Intervallo di indirizzi subnet.**

8. Selezionare **Aggiungi**.

9. Selezionare la **scheda** Sicurezza o selezionare il **pulsante Avanti: Sicurezza** nella parte inferiore della pagina.

10. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |

11. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

12. Selezionare **Crea**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale:

### <a name="create-the-first-vm"></a>Creare la prima VM

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM1** |
    | Region | Selezionare **(Stati Uniti) Stati Uniti orientali** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |
    | **Regole porta in ingresso** |    |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
    |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **mySubnet** |
    | IP pubblico | Selezionare **Nessuno** |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Basic**|
    | Rete di porte in ingresso pubbliche | Selezionare **Nessuno**. |
   
5. Selezionare la **scheda Rivedi e crea** oppure selezionare il pulsante blu **Rivedi e** crea nella parte inferiore della pagina.
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

### <a name="create-the-second-vm"></a>Creare la seconda VM

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM2** |
    | Area | Selezionare **(Stati Uniti) Stati Uniti orientali** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |
    | **Regole porta in ingresso** |    |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
    |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **mySubnet** |
    | IP pubblico | Selezionare **Nessuno** |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Basic**|
    | Rete di porte in ingresso pubbliche | Selezionare **Nessuno**. |
   
5. Selezionare la **scheda Rivedi e crea** oppure selezionare il pulsante blu **Rivedi e** crea nella parte inferiore della pagina.
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>Connettersi a myVM1

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la macchina virtuale privata. Cercare e selezionare **Macchine virtuali**.

2. Selezionare il nome della macchina virtuale privata **myVM1.**

3. Nella barra dei menu della macchina virtuale selezionare **Connetti** e quindi **Bastion.**

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Connettersi a myVM1 con Azure Bastion" border="true":::

4. Nella pagina **Connetti** selezionare il pulsante blu **Usa Bastion.**

5. Nella pagina **Bastion** immettere il nome utente e la password creati in precedenza per la macchina virtuale.

6. Selezionare **Connetti**.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

1. Nella connessione bastion di **myVM1** aprire PowerShell.

2. Immettere `ping myvm2`.

    Verrà visualizzato un messaggio simile all'output seguente:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Chiudere la connessione bastion a **myVM1**.

4. Completare i passaggi descritti in [Connettersi a myVM1,](#connect-to-myvm1)ma connettersi a **myVM2**.

5. Aprire PowerShell in **myVM2,** immettere `ping myvm1` .

    Si riceverà un messaggio simile al seguente:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Chiudere la connessione bastion a **myVM2**.

## <a name="clean-up-resources"></a>Pulire le risorse

In questa guida introduttiva sono state create una rete virtuale predefinita e due VM. 

È stata effettuata la connessione a una macchina virtuale da Internet ed è stata stabilita una comunicazione sicura tra le due macchine virtuali.

Quando non si ha più bisogno della rete e delle macchine virtuali, rimuovere il gruppo di risorse e tutte le risorse che contiene:

1. Cercare e selezionare **myResourceGroup**.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere **myResourceGroup** in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle impostazioni della rete virtuale, vedere [Creare, modificare o eliminare una rete virtuale](manage-virtual-network.md).

Per altre informazioni sui tipi di comunicazioni di rete delle macchine virtuali, vedere [Filtrare il traffico di rete](tutorial-filter-network-traffic.md).
