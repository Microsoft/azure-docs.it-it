---
title: Preparare la modifica degli indirizzi IP in ingresso
description: Se l'indirizzo IP in ingresso sta per essere modificato, in questo articolo viene spiegato come procedere in modo che l'app continui a funzionare anche dopo la modifica.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74672424"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Come preparare un cambio di indirizzo IP in ingresso

Se si riceve una notifica di modifica l'indirizzo IP in ingresso dell'app Servizio app di Azure, seguire le istruzioni riportate in questo articolo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinare se sia necessario intraprendere qualche operazione

* Opzione 1: se l'app del Servizio app di Azure non ha un dominio personalizzato, non è richiesta alcuna azione.

* Opzione 2: se nel portale di registrazione di dominio (provider DNS di terze parti o servizio DNS di Azure) è configurato solo un record CNAME (record DNS che punta a un URI), non è richiesta alcuna azione.

* Opzione 3: se nel portale di registrazione di dominio (provider DNS di terze parti o servizio DNS di Azure) è configurato un record A (record DNS che punta direttamente all'indirizzo IP dell'utente), sostituire l'indirizzo IP esistente con quello nuovo. È possibile trovare il nuovo indirizzo IP seguendo le istruzioni nella sezione successiva.

* Opzione 4: se l'applicazione si trova dietro a un servizio di bilanciamento del carico, filtro IP o qualsiasi altro meccanismo IP che richieda un indirizzo IP dell'app, sostituire l'indirizzo IP esistente con quello nuovo. È possibile trovare il nuovo indirizzo IP seguendo le istruzioni nella sezione successiva.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Trovare il nuovo indirizzo IP in ingresso nel portale di Azure

Il nuovo indirizzo IP in ingresso che viene assegnato all'app si trova nel portale nel campo **Indirizzo IP virtuale**. A questo punto, sia il nuovo indirizzo IP sia quello precedente sono connessi all'app e in seguito verrà disconnesso quello precedente.

1.  Aprire il [portale di Azure](https://portal.azure.com).

2.  Nel menu di spostamento a sinistra selezionare **Servizi app**.

3.  Selezionare il servizio app nell'elenco.

1.  Se l'app è un'app per le funzioni, vedere [Function app inbound IP address](../azure-functions/ip-addresses.md#function-app-inbound-ip-address) (Indirizzo IP in ingresso dell'app per le funzioni).

4.  Sotto l'intestazione **Impostazioni** fare clic su **Proprietà** nel menu di spostamento a sinistra e individuare la sezione denominata **Indirizzo IP virtuale**.

5. Copiare l'indirizzo IP e riconfigurare il record di dominio o meccanismo di IP.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come preparare un cambio di indirizzo IP avviato da Azure. Per altre informazioni sugli indirizzi IP in Servizio app di Azure, vedere [Indirizzi IP in ingresso e in uscita in Servizio app di Azure](overview-inbound-outbound-ips.md).
