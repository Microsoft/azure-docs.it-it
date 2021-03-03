---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f64bb0dd0841e89d05a4399db4373a9eaaec48a2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751075"
---
È possibile distribuire i profili per i client VPN di Azure (Windows 10) usando Microsoft Intune. Questo articolo consente di creare un profilo di Intune usando le impostazioni personalizzate.

## <a name="prerequisites"></a>Prerequisiti

* I dispositivi sono già registrati con la soluzione MDM di Intune.
* Il client VPN di Azure per Windows 10 è già distribuito nel computer client.
* È supportata solo la versione 19H2 o successiva di Windows.

## <a name="modify-xml"></a><a name="xml"></a>Modifica XML

Nei passaggi seguenti viene usato un esempio di codice XML per un profilo URI OMA personalizzato per Intune con le impostazioni seguenti:

* Connessione automatica in
* Rilevamento della rete attendibile abilitato.

Per altre opzioni supportate, vedere l'articolo relativo a [VPNV2 CSP](/windows/client-management/mdm/vpnv2-csp) .

1. Scaricare il profilo VPN dalla portale di Azure ed estrarre il file di *azurevpnconfig.xml* dal pacchetto.
1. Copiare e incollare il testo seguente in un nuovo file dell'editor di testo.

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. Modificare la voce tra ```<ServerUrlList>``` e ```</ServerUrlList>``` con la voce del profilo scaricato (azurevpnconfig.xml). Modificare il nome di dominio completo "TrustedNetworkDetection" per adattarlo all'ambiente.
1. Aprire il profilo scaricato di Azure (azurevpnconfig.xml) e copiare l'intero contenuto negli appunti evidenziando il testo e premendo CTRL + C. 
1. Incollare il testo copiato dal passaggio precedente nel file creato nel passaggio 2 tra i ```<CustomConfiguration>  </CustomConfiguration>``` tag. Salvare il file con un'estensione XML.
1. Annotare il valore nei ```<name>  </name>``` tag. Nome del profilo. Questo nome sarà necessario quando si crea il profilo in Intune. Chiudere il file e ricordare il percorso in cui viene salvato.

## <a name="create-intune-profile"></a>Crea profilo di Intune

In questa sezione viene creato un profilo di Microsoft Intune con impostazioni personalizzate.

1. Accedere a Intune e passare a **dispositivi-> profili di configurazione**. Selezionare **+ Crea profilo**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-profile.png" alt-text="Profili di configurazione":::
1. In **Piattaforma** selezionare **Windows 10 e versioni successive**. Per **profilo** selezionare **personalizzato**. Scegliere quindi **Create** (Crea).
1. Assegnare al profilo un nome e una descrizione, quindi fare clic su **Avanti**.
1. Nella scheda **impostazioni di configurazione** selezionare **Aggiungi**.

    * **Nome:** Immettere un nome per la configurazione.
    * **Descrizione:** Descrizione facoltativa.
    * **URI OMA:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` Queste informazioni sono reperibili nel file azurevpnconfig.xml nel <name></name> tag.
    * **Tipo di dati:** Stringa (file XML).

   Selezionare l'icona della cartella e selezionare il file salvato nel passaggio 6 della procedura [XML](#xml) . Selezionare **Aggiungi**.

   :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png" alt-text="Impostazioni di configurazione" lightbox="./media/vpn-gateway-virtual-wan-vpn-profile-intune/configuration-settings.png":::
1. Selezionare **Avanti**.
1. In **assegnazioni** selezionare il gruppo al quale si desidera effettuare il push della configurazione. Quindi selezionare **Avanti**.
1. Le regole di applicabilità sono facoltative. Definire eventuali regole, se necessario, quindi fare clic su **Avanti**.
1. Nella pagina **Verifica e crea** selezionare **Crea**.

    :::image type="content" source="./media/vpn-gateway-virtual-wan-vpn-profile-intune/create-profile.png" alt-text="Creare il profilo":::
1. Il profilo personalizzato è ora stato creato. Per la procedura Microsoft Intune per la distribuzione di questo profilo, vedere [assegnare profili utente e dispositivo](/mem/intune/configuration/device-profile-assign).