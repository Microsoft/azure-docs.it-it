---
title: Connettersi a desktop virtuale Windows da macOS-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client macOS.
author: Heidilohr
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5ee73625963143bb12d0784d52316c65a41e61d8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448033"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>Connettersi al desktop virtuale Windows con il client macOS

> Si applica a: macOS 10,12 o versione successiva

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/connect-macos-2019.md).

È possibile accedere alle risorse di desktop virtuali Windows dai dispositivi macOS con il client scaricabile. Questa guida illustra come configurare il client di.

## <a name="install-the-client"></a>Installare il client

Per iniziare, [scaricare](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e installare il client nel dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivi il feed fornito dall'amministratore per ottenere l'elenco di risorse gestite disponibili nel tuo dispositivo macOS.

Per sottoscrivere un feed:

1. Selezionare **Aggiungi area di lavoro** nella pagina principale per connettersi al servizio e recuperare le risorse.
2. Immetti un valore in Feed URL (URL del feed). Può essere un URL o un indirizzo e-mail:
   - Nel caso di un URL, usa quello fornito dall'amministratore. In genere l'URL è <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Nel caso dell'e-mail, immetti il tuo indirizzo. In tal caso indichi al client di cercare un URL associato al tuo indirizzo e-mail, se l'amministratore ha configurato il server in questo modo.
   - Per connettersi tramite il portale di US Gov, usare <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Selezionare **Aggiungi**.
4. Accedi con l'account utente quando richiesto.

Dopo aver eseguito l'accesso, verrà visualizzato un elenco delle risorse disponibili.

Una volta sottoscritto un feed, il relativo contenuto verrà aggiornato automaticamente a intervalli regolari. È possibile che vengano aggiunte, cambiate o rimosse risorse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul client macOS, vedere la documentazione [introduttiva per il client MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
