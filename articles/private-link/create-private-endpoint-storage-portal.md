---
title: Connettersi in privato a un account di archiviazione usando un endpoint privato di Azure
description: Informazioni su come connettersi privatamente a un account di archiviazione in Azure usando un endpoint privato.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1ccbb685ceb406fd7a52edf793b53d9e1c32630b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737326"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connettersi in privato a un account di archiviazione usando un endpoint privato di Azure
Endpoint privato di Azure è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse di collegamento privato.

In questa Guida introduttiva si apprenderà come creare una VM in una rete virtuale di Azure, un account di archiviazione con un endpoint privato usando il portale di Azure. Quindi, è possibile accedere in modo sicuro all'account di archiviazione dalla macchina virtuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vm"></a>Creare una macchina virtuale
In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato (un account di archiviazione in questo esempio).

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

In questa sezione si creeranno la rete virtuale e la subnet per ospitare la macchina virtuale usata per accedere alla risorsa di collegamento privato.

In questa sezione è necessario sostituire i parametri seguenti delle procedure con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | Stati Uniti centro-occidentali      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Creare macchina virtuale

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa**  >  **calcolo**  >  **macchina virtuale**.

1. In **Creare una macchina virtuale - Informazioni di base**, immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome macchina virtuale | Immettere *myVm*. |
    | Region | Selezionare **Stati Uniti centro-occidentali**. |
    | Opzioni di disponibilità | Lasciare l'impostazione predefinita **Nessuna ridondanza dell'infrastruttura necessaria**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter**. |
    | Dimensione | Lasciare l'impostazione predefinita **DS1 Standard v2**. |
    | **ACCOUNT AMMINISTRATORE** |  |
    | Username | Immettere un nome utente a scelta. |
    | Password | Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reimmettere la password. |
    | **REGOLE PORTA IN INGRESSO** |  |
    | Porte in ingresso pubbliche | Lasciare il valore predefinito **Nessuna**. |
    | **RISPARMIA** |  |
    | Già in possesso di una licenza di Windows? | Lasciare il valore predefinito **No**. |
    |||

1. Selezionare **Avanti: Dischi**.

1. In **Crea macchina virtuale - Dischi** mantenere le impostazioni predefinite e selezionare **Avanti: Rete**.

1. In **Creare una macchina virtuale - Rete**, selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Rete virtuale | Lasciare l'impostazione predefinita **MyVirtualNetwork**.  |
    | Spazio degli indirizzi | Lasciare l'impostazione predefinita **10.1.0.0/24**.|
    | Subnet | Lasciare l'impostazione predefinita **mySubnet (10.1.0.0/24)** .|
    | IP pubblico | Lasciare l'impostazione predefinita **(nuovo) myVm-ip**. |
    | Porte in ingresso pubbliche | Selezionare **Consenti porte selezionate**. |
    | Selezionare le porte in ingresso | Selezionare **HTTP** e **RDP**.|
    ||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

## <a name="create-your-private-endpoint"></a>Creare l'endpoint privato
In questa sezione verrà creato un account di archiviazione privato utilizzando un endpoint privato. 

1. Sul lato superiore sinistro della schermata nella portale di Azure selezionare **Crea una risorsa**  >  **archiviazione**  >  **account di archiviazione**.

1. In **create storage account-Nozioni di base**immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome dell'account di archiviazione  | Immettere *mystorageaccount*. Se il nome è già usato, creare un nome univoco. |
    | Region | Selezionare **Stati Uniti centro-occidentali**. |
    | Prestazioni| Lasciare l'impostazione predefinita **Standard**. |
    | Tipo di account | Lasciare la risorsa di **archiviazione predefinita (utilizzo generico v2)**. |
    | Replica | Selezionare **archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)**. |
    |||
  
3. Selezionare **Avanti: rete**.
4. In **creare un account di archiviazione-rete**, metodo di connettività selezionare **endpoint privato**.
5. In **creare un account di archiviazione-rete**selezionare **Aggiungi endpoint privato**. 
6. In **Crea endpoint privato**immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.|
    |Location|Selezionare **Stati Uniti centro-occidentali**.|
    |Nome|Immettere *myPrivateEndpoint*.  |
    |Sottorisorsa di archiviazione|Lasciare il **BLOB**predefinito. |
    | **RETE** |  |
    | Rete virtuale  | Selezionare *MyVirtualNetwork* dal gruppo di risorse *myResourceGroup*. |
    | Subnet | Selezionare *mySubnet*. |
    | **INTEGRAZIONE DNS PRIVATO**|  |
    | Integra con la zona DNS privato  | Lasciare l'impostazione predefinita **Sì**. |
    | Zona DNS privato  | Lasciare il valore predefinito **(nuovo) privatelink.blob.Core.Windows.NET**. |
    |||
7. Selezionare **OK**. 
8. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
9. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**. 
10. Passare alla risorsa dell'account di archiviazione appena creata.
11. Selezionare **chiavi di accesso** dal menu contenuto a sinistra.
12. Selezionare **copia** nella stringa di connessione per Key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file *downloaded.rdp*.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-storage-account-privately-from-the-vm"></a>Accedere a un account di archiviazione privatamente dalla macchina virtuale

In questa sezione si effettuerà la connessione privata con l'account di archiviazione usando l'endpoint privato.

1. Nel Desktop remoto di *myVm1* aprire PowerShell.
2. Immettere `nslookup mystorageaccount.blob.core.windows.net` verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Installare [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Selezionare **account di archiviazione** con il pulsante destro del mouse.
5. Selezionare **Connetti a un'archiviazione di Azure**.
6. Selezionare **Usa una stringa di connessione**.
7. Selezionare **Avanti**.
8. Immettere la stringa di connessione incollando le informazioni copiate in precedenza.
9. Selezionare **Avanti**.
10. Selezionare **Connetti**.
11. Esplorare i contenitori BLOB da mystorageaccount 
12. Facoltativamente Creare cartelle e/o caricare file in *mystorageaccount*. 
13. Chiudere la connessione Desktop remoto a *myVM*. 

Opzioni aggiuntive per accedere all'account di archiviazione:
- Microsoft Azure Storage Explorer è un'app gratuita autonoma di Microsoft che consente di lavorare visivamente con i dati di archiviazione di Azure in Windows, macOS e Linux. È possibile installare l'applicazione per esplorare in modo privato il contenuto dell'account di archiviazione. 
 
- L'utilità AzCopy è un'altra opzione per il trasferimento di dati con script a prestazioni elevate per archiviazione di Azure. Usare AzCopy per trasferire i dati da e verso risorse di archiviazione BLOB, file e tabelle. 


## <a name="clean-up-resources"></a>Pulire le risorse 
Al termine dell'uso dell'endpoint privato, dell'account di archiviazione e della macchina virtuale, eliminare il gruppo di risorse e tutte le risorse in esso contenute: 
1. Immettere *myResourceGroup* nella casella **Cerca** nella parte superiore del portale e selezionare *myResourceGroup* nei risultati della ricerca. 
2. Selezionare **Elimina gruppo di risorse**. 
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva è stata creata una VM in una rete virtuale e un account di archiviazione e un endpoint privato. È stata effettuata la connessione a una macchina virtuale da Internet e la comunicazione protetta con l'account di archiviazione tramite un collegamento privato. Per altre informazioni sull'endpoint privato, vedere [che cos'è endpoint privato di Azure?](private-endpoint-overview.md).
