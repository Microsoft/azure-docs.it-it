---
title: 'Gateway VPN di Azure: risolvere i problemi relativi alle connessioni da punto a sito: Mac OS X client'
description: Procedura per risolvere i problemi di connessione client VPN da punto a sito in Mac OS X
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 8dbed35659e409e5e3bcad74b378dba1f71b1f29
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86998494"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Risolvere i problemi di connessione VPN da punto a sito dai client Mac OS X

Questo articolo consente di risolvere i problemi di connettività da punto a sito da Mac OS X tramite il client VPN nativo e IKEv2. Il client VPN in Mac per IKEv2 è molto semplice e non consente un livello elevato di personalizzazione. È necessario controllare solo quattro impostazioni:

* Server Address
* ID remoto
* ID locale
* Impostazioni di autenticazione
* Versione del sistema operativo: 10.11 o successiva


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> Risolvere i problemi di autenticazione basata su certificati
1. Controllare le impostazioni del client VPN. Passare a **Network Setting** (Impostazioni di rete) premendo Comando + Maiusc e quindi digitare "VPN" per controllare le impostazioni del client VPN. Nell'elenco fare clic sulla voce VPN che deve essere esaminata.

   ![Autenticazione basata su certificati IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Verificare che l'**indirizzo del server** sia il nome di dominio completo e che includa cloudapp.net.
3. L'**ID remoto** deve essere uguale all'indirizzo del server (nome di dominio completo del gateway).
4. L'**ID locale** deve essere uguale al **soggetto** del certificato client.
5. Fare clic su **Authentication Settings** (Impostazioni di autenticazione) per aprire la pagina Authentication Settings (Impostazioni di autenticazione).

   ![Authentication settings](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Verificare che **Certificate** (Certificato) sia stato selezionato dall'elenco a discesa.
7. Fare clic sul pulsante **Select** (Seleziona) e verificare che sia stato selezionato il certificato corretto. Fare clic su **OK** per salvare le modifiche.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Risolvere i problemi di autenticazione di nome utente e password

1. Controllare le impostazioni del client VPN. Passare a **Network Setting** (Impostazioni di rete) premendo Comando + Maiusc e quindi digitare "VPN" per controllare le impostazioni del client VPN. Nell'elenco fare clic sulla voce VPN che deve essere esaminata.

   ![Nome utente e password IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Verificare che l'**indirizzo del server** sia il nome di dominio completo e che includa cloudapp.net.
3. L'**ID remoto** deve essere uguale all'indirizzo del server (nome di dominio completo del gateway).
4. Il campo **Local ID** (ID locale) può essere vuoto.
5. Fare clic sul pulsante **Authentication Setting** (Impostazioni di autenticazione) e verificare che "Username" (Nome utente) sia stato selezionato nell'elenco a discesa.

   ![Authentication settings](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Verificare che siano state immesse le credenziali corrette.

## <a name="additional-steps"></a><a name="additional"></a>Passaggi aggiuntivi

Se si segue la procedura precedente e tutto viene configurato correttamente, scaricare [Wireshark](https://www.wireshark.org/#download) ed eseguire un'acquisizione pacchetti.

1. Filtrare in base a *isakmp* ed esaminare i pacchetti **IKE_SA**. È possibile esaminare i dettagli della proposta SA in **Payload: Security Association** (Payload: associazione di sicurezza). 
2. Verificare che il client e il server dispongano di un set comune.

   ![pacchetto](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Se non è presente alcuna risposta server sulle tracce di rete, verificare di aver abilitato il protocollo IKEv2 nella pagina di configurazione del gateway di Azure nel sito Web portale di Azure.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, contattare [il supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
