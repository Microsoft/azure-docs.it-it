---
title: Delegare un sottodominio-DNS di Azure
description: Con questo percorso di apprendimento, iniziare a delegare un sottodominio DNS di Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011541"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegare un sottodominio DNS di Azure

È possibile usare il portale di Azure per delegare un sottodominio DNS. Se ad esempio si è proprietari del dominio contoso.com, è possibile delegare un sottodominio denominato *engineering* a un'altra zona distinta che può essere amministrata separatamente dalla zona di contoso.com.

Se si preferisce, è possibile delegare un sottodominio tramite [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Prerequisiti

Per delegare un sottodominio DNS di Azure occorre prima delegare il dominio pubblico alla zona DNS di Azure. Per istruzioni sulla configurazione dei server dei nomi per la delega, vedere [Esercitazione: Ospitare il dominio in DNS di Azure](./dns-delegate-domain-azure-dns.md). Una volta delegato il dominio alla zona del servizio DNS di Azure, è possibile delegare il sottodominio.

> [!NOTE]
> Contoso.com viene usato come esempio in questo articolo. Sostituire contoso.com con il nome del proprio dominio.

## <a name="create-a-zone-for-your-subdomain"></a>Creare una zona per il sottodominio

Creare prima di tutto la zona per il sottodominio **engineering**.

1. Dal portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di ricerca digitare **DNS** e selezionare **Zona DNS**.
3. Selezionare **Crea**.
4. Nel riquadro **Crea zona DNS** digitare **engineering.contoso.com** nella casella di testo **Nome**.
5. Selezionare il gruppo di risorse della zona. È consigliabile usare lo stesso gruppo di risorse della zona padre per tenere insieme le risorse simili.
6. Fare clic su **Crea**.
7. Al termine della distribuzione, passare alla nuova zona.

## <a name="note-the-name-servers"></a>Prendere nota dei server dei nomi

Prendere nota dei quattro server dei nomi relativi al sottodominio "engineering".

Nel riquadro della zona **engineering** prendere nota dei quattro server dei nomi della zona. Questi server dovranno essere usati più tardi.

## <a name="create-a-test-record"></a>Creare un record di test

Creare un record **A** da usare per il testing. Ad esempio, creare un record A **www** e configurarlo con l'indirizzo IP **10.10.10.10**.

## <a name="create-an-ns-record"></a>Creare un record NS

Creare un record server dei nomi (NS) per la zona **engineering**.

1. Passare alla zona del dominio padre.
2. Selezionare **+ Set di record**.
3. Nel riquadro **Aggiungi set di record** digitare **engineering** nella casella di testo **Nome**.
4. In **Tipo** selezionare **NS**.
5. In **Server dei nomi** immettere i quattro server dei nomi registrati in precedenza dalla zona **engineering**.
6. Fare clic su **OK**.

## <a name="test-the-delegation"></a>Testare la delega

Usare nslookup per testare la delega.

1. Aprire una finestra di PowerShell.
2. Al prompt dei comandi digitare `nslookup www.engineering.contoso.com.`
3. Si dovrebbe ricevere una risposta non autorevole che mostra l'indirizzo **10.10.10.10**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare il DNS inverso per i servizi ospitati in Azure](dns-reverse-dns-for-azure-services.md).