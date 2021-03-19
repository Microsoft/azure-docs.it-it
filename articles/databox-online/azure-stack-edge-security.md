---
title: Sicurezza di Azure Stack Edge Pro | Microsoft Docs
description: Descrive le funzionalità di sicurezza e privacy che proteggono il dispositivo, il servizio e i dati di Azure Stack Edge Pro in locale e nel cloud.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b5c5143c7bc92487aeabc3cdc1afea5061466027
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90903164"
---
# <a name="azure-stack-edge-pro-security-and-data-protection"></a>Sicurezza e protezione dei dati di Azure Stack Edge Pro

La sicurezza è un problema fondamentale quando si adotta una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Azure Stack Edge Pro consente di garantire che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza di Azure Stack Edge Pro che consentono di proteggere ogni componente della soluzione e i dati archiviati.

Azure Stack Edge Pro è costituito da quattro componenti principali che interagiscono tra loro:

- **Servizio Azure stack Edge, ospitato in Azure**. La risorsa di gestione usata per creare l'ordine dei dispositivi, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Dispositivo Azure stack Edge Pro**. Il dispositivo di trasferimento fornito all'utente per poter importare i dati locali in Azure.
- **Client/host connessi al dispositivo**. I client nell'infrastruttura che si connettono al dispositivo Azure Stack Edge Pro e contengono dati che devono essere protetti.
- **Archiviazione cloud**. Posizione della piattaforma cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Azure Stack Edge creata.

## <a name="azure-stack-edge-service-protection"></a>Protezione del servizio Azure Stack Edge

Il servizio Azure Stack Edge è un servizio di gestione ospitato in Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-pro-device-protection"></a>Protezione dei dispositivi Pro Azure Stack Edge

Il dispositivo Azure Stack Edge Pro è un dispositivo locale che consente di trasformare i dati in locale e quindi di inviarli ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio Azure Stack Edge.
- È sempre protetto da una password del dispositivo.
- È un dispositivo bloccato. Il BMC e il BIOS del dispositivo sono protetti da password. Il BIOS è protetto da accesso utente limitato.
- Con avvio protetto abilitato.
- Esegue Windows Defender Device Guard. Device Guard consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.

### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite la chiave di attivazione

Solo un dispositivo Azure Stack Edge Pro autorizzato è autorizzato a partecipare al servizio Azure Stack Edge creato nella sottoscrizione di Azure. Per autorizzare un dispositivo, è necessario usare una chiave di attivazione per attivare il dispositivo con il servizio Azure Stack Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Per altre informazioni, vedere [ottenere una chiave di attivazione](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggi il dispositivo tramite password

Le password assicurano che solo gli utenti autorizzati possano accedere ai dati. I dispositivi Pro Azure Stack Edge vengono avviati in uno stato bloccato.

È possibile:

- Connettersi all'interfaccia utente Web locale del dispositivo tramite un browser e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. La gestione remota è attivata per impostazione predefinita. È quindi possibile specificare la password del dispositivo per accedere al dispositivo. Per altre informazioni, vedere [connettersi in remoto al dispositivo Azure stack Edge Pro](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Usare l'interfaccia utente Web locale per [modificare la password](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access). Se si modifica la password, assicurarsi di notificare a tutti gli utenti di accesso remoto in modo che non si verifichino problemi durante l'accesso.

## <a name="protect-your-data"></a>Proteggere i dati

Questa sezione descrive le funzionalità di sicurezza di Azure Stack Edge Pro che proteggono i dati archiviati e in transito.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 bit Encryption viene usato per proteggere i dati locali.


### <a name="protect-data-in-flight"></a>Proteggere i dati in corso

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Proteggere i dati mediante gli account di archiviazione

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Ruotare e [sincronizzare regolarmente le chiavi dell'account di archiviazione](azure-stack-edge-manage-shares.md#sync-storage-keys) per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestisci informazioni personali

Il servizio Azure Stack Edge raccoglie informazioni personali negli scenari seguenti:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco degli utenti che possono accedere o eliminare una condivisione, seguire i passaggi descritti in [gestire le condivisioni in Azure stack Edge Pro](azure-stack-edge-manage-shares.md).

Per ulteriori informazioni, consultare l'informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
