---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180812"
---
1. Accedere al dispositivo Data Box. Assicurarsi che sia sbloccato.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Passare a **Imposta interfacce di rete**. Prendere nota dell'indirizzo IP del dispositivo per l'interfaccia di rete usata per la connessione al client.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Passare a **Connetti e copia** e fare clic su **REST**.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Dalla finestra di dialogo **Accedi all'account di archiviazione e carica i dati** copiare il valore di **Endpoint di servizio BLOB**.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Avviare **Blocco note** come amministratore e quindi aprire il file **hosts** che si trova in `C:\Windows\System32\Drivers\etc`.
6. Al file **hosts** aggiungere: `<device IP address> <Blob service endpoint>`
7. Per indicazioni, vedere l'immagine seguente. Salvare il file **hosts**.

    ![Dashboard di Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
