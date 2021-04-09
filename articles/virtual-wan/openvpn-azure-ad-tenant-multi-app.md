---
title: 'Rete WAN virtuale: Azure AD tenant per gruppi di utenti diversi: autenticazione Azure AD'
description: Configurare un tenant di Azure AD per l'autenticazione di P2S OpenVPN e creare e registrare più app in Azure AD per consentire l'accesso a diversi utenti e gruppi.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e25ef7f55492be4ee491b9ebbbef4aa1eb03c80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898088"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant di Azure Active Directory (AD) per le connessioni del protocollo OpenVPN P2S

Quando ci si connette alla VNet, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo VPN aperto, è anche possibile usare l'autenticazione Azure Active Directory. Se si vuole che un diverso set di utenti sia in grado di connettersi a Gateway diversi, è possibile registrare più app in Active Directory e collegarle a diversi gateway.

Questo articolo illustra come configurare un tenant di Azure AD per l'autenticazione di P2S OpenVPN e come creare e registrare più app in Azure AD per consentire l'accesso a diversi utenti e gruppi.

> [!NOTE]
> Azure AD autenticazione è supportata solo per le &reg; connessioni di protocollo OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. creare una nuova configurazione di P2S

Una configurazione da punto a sito definisce i parametri per la connessione di client remoti.

1. Impostare le variabili seguenti, sostituendo i valori in base alle esigenze per l'ambiente corrente.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Eseguire i comandi seguenti per creare la configurazione:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Non usare l'ID dell'applicazione del client VPN di Azure nei comandi precedenti: consentirà a tutti gli utenti di accedere al gateway. Usare l'ID dell'applicazione o delle applicazioni registrate.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Modifica assegnazione Hub

1. Passare al pannello **Hub** nella rete WAN virtuale.

2. Selezionare l'hub a cui si vuole associare la configurazione del server VPN e fare clic sui puntini di sospensione (...).

    ![Screenshot Mostra modifica hub virtuale selezionato dal menu.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Fare clic su **Modifica hub virtuale**.

4. Selezionare la casella di controllo **Includi gateway da punto a sito** e selezionare l'**unità di scala gateway** che si vuole usare.

    ![Screenshot mostra la finestra di dialogo modifica hub virtuale in cui è possibile selezionare l'unità di scala del gateway.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Immettere il **Pool di indirizzi** da cui gli indirizzi IP verranno assegnati ai client VPN.

6. Fare clic su **Conferma**.

7. Il completamento dell'operazione può richiedere fino a 30 minuti.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. scaricare il profilo VPN

Usare il profilo VPN per configurare i client.

1. Nella pagina della rete WAN virtuale fare clic su **Configurazioni VPN utente**.

2. Nella parte superiore della pagina fare clic su **Download user VPN config** (Scarica configurazione VPN utente).

3. Al termine della creazione del file è possibile fare clic sul collegamento per scaricarlo.

4. Usare il file del profilo per configurare i client VPN.

5. Estrarre il file zip scaricato.

6. Passare alla cartella "AzureVPN" decompressa.

7. Prendere nota della posizione del file "azurevpnconfig.xml". Il azurevpnconfig.xml contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure. È anche possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. Per la corretta connessione, l'utente dovrà disporre di credenziali Azure AD valide.

## <a name="9-configure-user-vpn-clients"></a>9. Configurare client VPN utente

Per connettersi, è necessario scaricare il client VPN di Azure e importare il profilo client VPN scaricato nei passaggi precedenti in ogni computer che dovrà connettersi alla rete virtuale.

> [!NOTE]
> Azure AD autenticazione è supportata solo per le &reg; connessioni di protocollo OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Per scaricare il client VPN di Azure

Usare questo [collegamento](https://go.microsoft.com/fwlink/?linkid=2117554) per scaricare il client VPN di Azure.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Per importare un profilo client

1. Nella pagina selezionare **Importa**.

    ![Screenshot mostra l'importazione selezionata dal menu più.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Individuare il file XML del profilo e selezionarlo. Con il file selezionato, selezionare **Apri**.

    ![Screenshot mostra una finestra di dialogo aperta in cui è possibile selezionare un file.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Specificare il nome del profilo e selezionare **Salva**.

    ![Screenshot mostra il nome della connessione aggiunto e il pulsante Salva selezionato.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Selezionare **Connetti** per connettersi alla VPN.

    ![Screenshot mostra il pulsante Connetti per la connessione appena creata.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Una volta stabilita la connessione, l'icona diventerà verde mostrerà lo stato **Connesso**.

    ![Screenshot mostra la connessione in uno stato connesso con l'opzione per la disconnessione.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Per eliminare un profilo client

1. Selezionare i puntini di sospensione (...) accanto al profilo client da eliminare. Selezionare quindi **Rimuovi**.

    ![Screenshot mostra Rimuovi selezionato dal menu.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Per procedere all'eliminazione, selezionare **Rimuovi**.

    ![Screenshot Visualizza una finestra di dialogo di conferma con l'opzione per la rimozione o l'annullamento.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Per diagnosticare i problemi di connessione

1. Per diagnosticare i problemi di connessione, è possibile usare lo strumento **Diagnosi**. Selezionare i puntini di sospensione (...) accanto alla connessione VPN che si vuole diagnosticare per visualizzare il menu. Selezionare quindi **Diagnosi**.

    ![Screenshot mostra la diagnostica selezionata dal menu.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Nella pagina **Proprietà connessione** selezionare **Esegui diagnosi**.

    ![Screenshot che mostra il pulsante Esegui diagnosi per una connessione.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Accedere con le credenziali personali.

    ![diagnosi 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Visualizzare i risultati della diagnosi.

    ![Screenshot che mostra il pulsante Esegui diagnosi per una connessione.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Accedere con le credenziali personali.

    ![Screenshot mostra la finestra di dialogo Accedi per questa azione.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Visualizzare i risultati della diagnosi.

    ![Screenshot mostra i risultati della diagnosi.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.

2. Nella pagina Panoramica ogni punto sulla mappa rappresenta un hub.

3. Nella sezione degli hub e delle connessioni è possibile visualizzare lo stato dell'hub, il sito, l'area, lo stato della connessione VPN e i byte in entrata e in uscita.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Sostituire "myResourceGroup" con il nome del gruppo di risorse specifico ed eseguire il comando di PowerShell seguente:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
