---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180152"
---
## <a name="use-the-azure-portal"></a>Usare il portale di Azure
1. Selezionare la VM di cui si vuole eseguire di nuovo la distribuzione, quindi selezionare il pulsante *Ridistribuisci* nel pannello *Impostazioni*. Potrebbe essere necessario scorrere fino alla sezione **Supporto e risoluzione dei problemi** che contiene il pulsante "Ridistribuisci", come nell'esempio seguente:
   
    ![Pannello VM di Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Per confermare l'operazione, selezionare il pulsante *Ridistribuisci*:
   
    ![Pannello Ridistribuire una VM](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. Lo **Stato** della VM passa ad *Aggiornamento* mentre la VM si prepara per la ridistribuzione, come mostrato nell'esempio seguente:
   
    ![Aggiornamento di una VM](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. Lo **Stato** passa poi ad *Avvio in corso* mentre la VM si avvia su un nuovo host Azure, come mostrato nell'esempio seguente:
   
    ![Avvio di una VM](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Al termine del processo di avvio della VM, lo **stato** torna a *In esecuzione*, a indicare che la VM è stata ridistribuita:
   
    ![Esecuzione di una VM](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

