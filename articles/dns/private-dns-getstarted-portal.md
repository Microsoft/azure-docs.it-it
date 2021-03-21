---
title: Avvio rapido - Creare una zona DNS privato di Azure con il portale di Azure
description: In questo avvio rapido verranno creati e testati una zona DNS privato e un record DNS in DNS di Azure. Guida dettagliata che descrive come creare e gestire la prima zona DNS privata e il primo record DNS usando il portale di Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: d298dfd5f3ad0beb56a511c124bab056ca25fd27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310058"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Guida introduttiva: Creare una zona DNS privato di Azure con il portale di Azure

Questo avvio rapido descrive i passaggi necessari per creare la prima zona DNS privato e il primo record DNS con il portale di Azure.

Una zona DNS viene usata per ospitare i record DNS per un particolare dominio. Per iniziare a ospitare il dominio in DNS di Azure, è necessario creare una zona DNS per il nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di questa zona DNS. Per pubblicare una zona DNS privata nella rete virtuale, specificare l'elenco di reti virtuali autorizzate a risolvere i record nella zona.  Tali reti vengono definite reti virtuali *collegate*. Se è abilitata la registrazione automatica, DNS di Azure aggiorna anche i record di zona ogni volta che una macchina virtuale viene creata o eliminata o quando cambia il relativo indirizzo IP.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se si preferisce, è possibile completare questo avvio rapido usando [Azure PowerShell](private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Creare una zona DNS privato

L'esempio seguente crea una zona DNS denominata **private.contoso.com** in un gruppo di risorse denominato **MyAzureResourceGroup**.

Una zona DNS contiene le voci DNS per un dominio. Per iniziare a ospitare il dominio in DNS di Azure, creare una zona DNS per il nome di dominio.

![Ricerca di zone DNS private](media/private-dns-portal/search-private-dns.png)

1. Nella barra di ricerca del portale digitare **zone dns private** nella casella di testo per la ricerca e premere **INVIO**.
1. Selezionare **Zona DNS privato**.
2. Selezionare **Crea zona DNS privato**.

1. Nella pagina **Crea zona DNS privato** digitare o selezionare i valori seguenti:

   - **Gruppo di risorse**: Selezionare **Crea nuovo**, immettere *MyAzureResourceGroup* e selezionare **OK**. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione di Azure.
   -  **Name**: Digitare *private.contoso.com* per questo esempio.
1. Per **Località del gruppo di risorse** selezionare **Stati Uniti centro-occidentali**.

1. Selezionare **Rivedi e crea**.

1. Selezionare **Create** (Crea).

La creazione della zona può richiedere alcuni minuti.

## <a name="virtual-network-and-parameters"></a>Rete virtuale e parametri

In questa sezione è necessario sostituire i parametri seguenti delle procedure con le informazioni riportate di seguito:

| Parametro                   | valore                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (Selezionare un gruppo di risorse esistente) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | Stati Uniti centro-occidentali      |
| **\<IPv4-address-space>**   | 10.2.0.0\16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Collegare la rete virtuale

Per collegare la zona DNS privato a una rete virtuale, è necessario creare un collegamento di rete virtuale.

![Aggiungere un collegamento di rete virtuale](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Aprire il gruppo di risorse **MyAzureResourceGroup** e selezionare la zona privata **private.contoso.com**.
2. Nel riquadro di sinistra selezionare **Collegamenti di rete virtuale**.
3. Selezionare **Aggiungi**.
4. Digitare **myLink** per **Nome collegamento**.
5. Per **Rete virtuale**, selezionare **myAzureVNet**.
6. Selezionare la casella di controllo **Abilita registrazione automatica**.
7. Selezionare **OK**.

## <a name="create-the-test-virtual-machines"></a>Creare le macchine virtuali di test

A questo punto, creare due macchine virtuali in modo da testare la zona DNS privata:

1. In altro a sinistra nella pagina del portale selezionare **Crea una risorsa** e quindi selezionare **Windows Server 2016 Datacenter**.
1. Selezionare **MyAzureResourceGroup** come gruppo di risorse.
1. Digitare **myVM01** come nome della macchina virtuale.
1. Selezionare **Stati Uniti centro-occidentali** per **Area**.
1. Immettere un nome per il nome utente amministratore.
2. Immettere una password e confermarla.
5. In **Porte in ingresso pubbliche** selezionare **Consenti porte selezionate** e quindi selezionare **RDP (3389)** in **Selezionare le porte in ingresso**.
10. Accettare gli altri valori predefiniti nella pagina e quindi fare clic su **Avanti: Dischi >** .
11. Accettare i valori predefiniti nella pagina **Dischi** e quindi fare clic su **Avanti: Rete >** .
1. Assicurarsi che come rete virtuale sia selezionata **myAzureVNet**.
1. Accettare gli altri valori predefiniti nella pagina e quindi fare clic su **Avanti: Gestione >** .
2. Per **Diagnostica di avvio**, selezionare **No**, accettare gli altri valori predefiniti e quindi selezionare **Rivedi e crea**.
1. Controllare le impostazioni e quindi fare clic su **Crea**.

Ripetere questi passaggi e creare un'altra macchina virtuale denominata **myVM02**.

La creazione di entrambe le macchine virtuali richiederà pochi minuti.

## <a name="create-an-additional-dns-record"></a>Creare un record DNS aggiuntivo

 L'esempio seguente crea un record con il nome relativo **db** nella zona DNS **private.contoso.com** nel gruppo di risorse **MyAzureResourceGroup**. Il nome completo del set di record è **db.private.contoso.com**. Il tipo di record è "A", con l'indirizzo IP di **myVM01**.

1. Aprire il gruppo di risorse **MyAzureResourceGroup** e selezionare la zona privata **private.contoso.com**.
2. Selezionare **+ Set di record**.
3. Per **Nome** digitare **db**.
4. Per **Indirizzo IP** digitare l'indirizzo IP visualizzato per **myVM01**. Questo dovrebbe essere stato registrato automaticamente all'avvio della macchina virtuale.
5. Selezionare **OK**.

## <a name="test-the-private-zone"></a>Testare la zona privata

A questo punto è possibile testare la risoluzione dei nomi per la zona privata **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurare le macchine virtuali per consentire il traffico ICMP in entrata

È possibile usare il comando ping per testare la risoluzione dei nomi. Configurare quindi il firewall in entrambe le macchine virtuali per consentire i pacchetti ICMP in ingresso.

1. Connettersi a myVM01 e aprire una finestra di Windows PowerShell con privilegi di amministratore.
2. Eseguire il comando seguente:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Ripetere l'operazione per myVM02.

### <a name="ping-the-vms-by-name"></a>Effettuare il ping delle macchine virtuali in base al nome

1. Dal prompt dei comandi di Windows PowerShell in myVM02, effettuare il ping di myVM01 usando il nome host registrato automaticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   Verrà visualizzato un output simile a questo:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Ora effettuare il ping del nome **db** creato in precedenza:
   ```
   ping db.private.contoso.com
   ```
   Verrà visualizzato un output simile a questo:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse **MyAzureResourceGroup** per eliminare le risorse create in questo avvio rapido.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scenari di Zone private di DNS di Azure](private-dns-scenarios.md)

