---
title: 'Gateway VPN: Azure AD tenant per le connessioni VPN P2S: Azure AD autenticazione'
description: Informazioni su come configurare un tenant di Azure AD per l'autenticazione VPN open P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: bff1eec0152ab0f57edd212adf6b14f7b588fb51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390165"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Creare un tenant Azure Active Directory per le connessioni del protocollo OpenVPN da punto a sito

Quando ci si connette alla VNet, è possibile usare l'autenticazione basata su certificati o l'autenticazione RADIUS. Tuttavia, quando si usa il protocollo VPN aperto, è anche possibile usare l'autenticazione Azure Active Directory. Questo articolo consente di configurare un tenant di Azure AD per l'autenticazione VPN aperta P2S.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. verificare Azure AD tenant

Verificare di disporre di un tenant Azure AD. Se non si ha un tenant di Azure AD, è possibile crearne uno seguendo la procedura descritta nell'articolo [creare un nuovo tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome dell'organizzazione
* Nome di dominio iniziale

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Nuovo tenant di Azure AD" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. creare Azure AD utenti tenant

Il tenant di Azure AD richiede gli account seguenti: un account amministratore globale e un account utente Master. L'account utente master viene usato come account di incorporamento master (account del servizio). Quando si crea un account utente del tenant di Azure AD, si regola il ruolo della directory per il tipo di utente che si desidera creare.

Usare la procedura descritta in [aggiungere o eliminare utenti-Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) per creare almeno due utenti per il tenant di Azure ad. Assicurarsi di modificare il **ruolo della directory** per creare i tipi di account:

* Amministratore globale
* Utente

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. abilitare l'autenticazione Azure AD nel gateway VPN

1. Individuare l'ID directory della directory da usare per l'autenticazione. Viene elencato nella sezione proprietà della pagina Active Directory.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="Screenshot che mostra le proprietà della directory" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Copiare l'ID directory.

1. Accedere al portale di Azure come utente con il ruolo di **amministratore globale**.

1. Accordare quindi il consenso amministratore. Copiare e incollare l'URL relativo alla posizione di distribuzione nella barra degli indirizzi del browser:

   Pubblico

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Microsoft Cloud Germany

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    21Vianet per Azure Cina

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Se si usa un account amministratore globale non nativo per il tenant di Azure AD per fornire il consenso, sostituire "Common" con l'ID di directory Azure AD nell'URL. In alcuni altri casi, potrebbe essere necessario sostituire anche "Common" con l'ID directory.
   >

1. Se richiesto, selezionare l'account di **amministratore globale**.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Scegliere un account" border="false":::
1. Quando richiesto, selezionare **Accetta**.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="Screenshot mostra le autorizzazioni del messaggio accettate per l'organizzazione con i dettagli e l'opzione di accettazione." border="false":::
1. Nel Azure AD, in **applicazioni aziendali**, viene visualizzata la rete **VPN di Azure** elencata.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="Screenshot che mostra la pagina tutte le applicazioni." lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Se non è già presente un ambiente da punto a sito in funzione, seguire le istruzioni per crearne uno. Vedere [creare una VPN da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md) per creare e configurare un gateway VPN da punto a sito.

    > [!IMPORTANT]
    > Lo SKU Basic non è supportato per OpenVPN.

1. Abilitare l'autenticazione Azure AD sul gateway VPN passando alla configurazione da **punto a sito** e selezionando **OpenVPN (SSL)** come **tipo di tunnel**. Selezionare **Azure Active Directory** come **tipo di autenticazione**, quindi immettere le informazioni nella sezione **Azure Active Directory** .

   * **Tenant:** TenantID per il tenant di Azure AD ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Destinatari:** ApplicationID di "VPN di Azure" Azure AD app aziendale ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **Autorità emittente**: URL del servizio token di sicurezza ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="VPN database" border="false":::

   > [!NOTE]
   > Assicurarsi di includere una barra finale alla fine del `AadIssuerUri` valore. In caso contrario, la connessione potrebbe non riuscire.
   >

1. Per creare e scaricare il profilo, fare clic sul collegamento **Scarica client VPN** .

1. Estrarre il file zip scaricato.

1. Passare alla cartella "AzureVPN" decompressa.

1. Prendere nota della posizione del file "azurevpnconfig.xml". Il azurevpnconfig.xml contiene l'impostazione per la connessione VPN e può essere importato direttamente nell'applicazione client VPN di Azure. È anche possibile distribuire questo file a tutti gli utenti che devono connettersi tramite posta elettronica o altri mezzi. Per la corretta connessione, l'utente dovrà disporre di credenziali Azure AD valide.

## <a name="next-steps"></a>Passaggi successivi

Creare e configurare un profilo client VPN. Vedere [configurare un client VPN per le connessioni VPN P2S](openvpn-azure-ad-client.md).
