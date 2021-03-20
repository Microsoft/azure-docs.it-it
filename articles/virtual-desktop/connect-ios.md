---
title: Connettersi a desktop virtuale Windows da iOS-Azure
description: Come connettersi al desktop virtuale di Windows tramite il client iOS.
author: Heidilohr
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 87bb2cc53ce056552e8f44aac4ade96e603a8787
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230042"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>Connettersi al desktop virtuale Windows con il client iOS

> Si applica a: iOS 13,0 o versione successiva. Compatibile con iPhone, iPad e iPod touch.

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/connect-ios-2019.md).

È possibile accedere alle risorse di desktop virtuali Windows dal dispositivo iOS con il client scaricabile. Questa guida illustra come configurare il client iOS.

## <a name="install-the-ios-client"></a>Installare il client iOS

Per iniziare, [scaricare](https://aka.ms/rdios) e installare il client nel dispositivo iOS.

## <a name="subscribe-to-a-feed"></a>Effettuare la sottoscrizione a un feed

Sottoscrivere il feed fornito dall'amministratore per ottenere l'elenco delle risorse gestite a cui è possibile accedere nel dispositivo iOS.

Per sottoscrivere un feed:

1. Nel Centro connessioni toccare **+** , quindi toccare **Aggiungi area di lavoro**.
2. Immettere l'URL del feed nel campo **feed URL** . L'URL del feed può essere un URL o un indirizzo di posta elettronica.
   - Nel caso di un URL, usa quello fornito dall'amministratore. In genere l'URL è <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Nel caso dell'e-mail, immetti il tuo indirizzo. In tal caso indichi al client di cercare un URL associato al tuo indirizzo e-mail, se l'amministratore ha configurato il server in questo modo.
   - Per connettersi tramite il portale di US Gov, usare <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Tocca **Next** (Avanti).
4. Specifica le credenziali quando richiesto.
   - Per **nome utente**, assegnare al nome utente l'autorizzazione per accedere alle risorse.
   - Per **password**, assegnare la password associata al nome utente.
   - È anche possibile che venga richiesto di fornire altri fattori se l'amministratore ha configurato l'autenticazione in questo modo.
5. Toccare **salvare**.

Al termine, nel centro connessioni verranno visualizzate le risorse remote.

Una volta sottoscritto un feed, il relativo contenuto verrà aggiornato automaticamente a intervalli regolari. È possibile che vengano aggiunte, cambiate o rimosse risorse in base alle modifiche apportate dall'amministratore.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il client iOS, vedere la documentazione [introduttiva per il client iOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
