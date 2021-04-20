---
title: Connettersi a Desktop virtuale Windows con il client Web - Azure
description: Come connettersi a Desktop virtuale Windows usando il client Web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725009"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Connettersi a Desktop virtuale Windows con il client Web

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/connect-web-2019.md).

Il client Web consente di accedere alle risorse del Desktop virtuale Windows da un Web browser senza il lungo processo di installazione.

>[!NOTE]
>Il client Web attualmente non dispone del supporto per il sistema operativo dei dispositivi mobili.

## <a name="supported-operating-systems-and-browsers"></a>Sistemi operativi e browser supportati

Sebbene dovrebbe funzionare qualsiasi browser compatibile con HTML5, ufficialmente sono supportati i sistemi operativi e i browser seguenti.

| Browser           | Sistema operativo supportato                     | Note               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Versione 11 o successiva |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versione 55 o successiva |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Accedere al feed di risorse remote

In un browser, passare alla versione integrata in Azure Resource Manager del client Web Desktop virtuale Windows all'indirizzo <https://rdweb.wvd.microsoft.com/arm/webclient> e accedere con l'account utente.

>[!NOTE]
>Se si usa Desktop virtuale Windows (versione classica) senza Azure Resource Manager, connettersi alle risorse in <https://rdweb.wvd.microsoft.com/webclient> .
>
> Se si usa il portale US Gov, usare <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Se è già stato eseguito l'accesso con un account Azure Active Directory diverso da quello che si vuole usare per Desktop virtuale Windows, è necessario disconnettersi o usare una finestra del browser privata.

Dopo l'accesso verrà visualizzato un elenco di risorse. Per avviare le risorse, è possibile selezionarle come si fa in una normale app nella scheda **Tutte le risorse**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client Web, vedere [Introduzione al client Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
