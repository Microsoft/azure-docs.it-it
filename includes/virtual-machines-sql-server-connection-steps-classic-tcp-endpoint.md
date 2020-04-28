---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180090"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Creare un endpoint TCP per la macchina virtuale
Per poter accedere a SQL Server da internet, nella macchina virtuale deve essere presente un endpoint per l'ascolto delle comunicazioni TCP in entrata. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

> [!NOTE]
> Se ci si connette all'interno della stesso servizio cloud o della stessa rete virtuale, non è necessario creare un endpoint accessibile pubblicamente. In tal caso, è possibile continuare al passaggio successivo. Per altre informazioni, vedere [Scenari di connessione](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. Nel portale di Azure selezionare **Macchine virtuali (versione classica)**.
2. Quindi selezionare la macchina virtuale di SQL Server.
3. Selezionare **Endpoint** e quindi fare clic sul pulsante **Aggiungi** nella parte superiore del pannello Endpoint.
   
    ![Passaggi all'interno del portale per la creazione dell'endpoint](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. Nel pannello **Aggiungi endpoint** specificare un valore in **Nome**, ad esempio SQLEndpoint.
5. Selezionare **TCP** per il **protocollo**.
6. Nel campo **Porta pubblica** specificare un numero di porta, ad esempio **57500**.
7. Nel campo **Porta privata** specificare la porta in ascolto di SQL Server che per impostazione predefinita è **1433**.
8. Fare clic su **OK** per creare l'endpoint.

