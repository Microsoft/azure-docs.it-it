---
title: Esercitazione sulla preparazione del portale di Azure per la distribuzione di Data Box Gateway | Microsoft Docs
description: La prima esercitazione per la distribuzione di Azure Data Box Gateway include la preparazione del portale di Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 74fec059bdffb91f5a7774d430e2f1897f0e863c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474459"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Esercitazione: Preparare la distribuzione di Azure Data Box Gateway

Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Data Box Gateway. Descrive come preparare il portale di Azure per la distribuzione della risorsa Data Box Gateway.

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare una nuova risorsa
> * Scaricare l'immagine del dispositivo virtuale
> * Ottenere la chiave di attivazione

## <a name="get-started"></a>Introduzione

Per distribuire il Data Box Gateway, vedere le esercitazioni seguenti nella sequenza indicata.

| **#** | **In questo passaggio** | **Usare questi documenti** |
| --- | --- | --- | 
| 1. |**[Preparare il portale di Azure per Data Box Gateway](data-box-gateway-deploy-prep.md)** |Creare e configurare la risorsa Data Box Gateway prima di effettuare il provisioning di un dispositivo virtuale Data Box Gateway. |
| 2. |**[Effettuare il provisioning di Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Effettuare il provisioning di Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md)**|Per Hyper-V, effettuare il provisioning e connettersi a un dispositivo virtuale Data Box Gateway su un sistema host che esegue Hyper-V in Windows Server 2016 o Windows Server 2012 R2. <br><br><br> Per VMware, effettuare il provisioning e connettersi a un dispositivo virtuale Data Box Gateway in un sistema host che esegue VMware ESXi 6.0, 6.5 o 6.7.<br></br> |
| 3. |**[Connettere, configurare e attivare Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Connettersi all'interfaccia utente Web locale, completare la configurazione del dispositivo e attivare il dispositivo. È quindi possibile eseguire il provisioning delle condivisioni SMB.  |
| 4. |**[Trasferire i dati con Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Aggiungere condivisioni, connettersi alle condivisioni tramite SMB o NFS. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono elencati i prerequisiti di configurazione per la risorsa Data Box Gateway, il dispositivo Data Box Gateway e la rete del data center.

### <a name="for-the-data-box-gateway-resource"></a>Per la risorsa Data Box Gateway

Prima di iniziare, verificare che:

* La sottoscrizione di Microsoft Azure sia abilitata per una risorsa Azure Stack Edge. Assicurarsi di aver usato una sottoscrizione supportata, ad esempio [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) o [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Si abbia accesso di tipo Proprietario o Collaboratore a livello di gruppo di risorse per le risorse Data Box Edge/Data Box Gateway, hub IoT e Archiviazione di Azure.
    - Per creare qualsiasi risorsa Data Box Edge/Data Box Gateway, è necessario disporre di autorizzazioni di Collaboratore o superiori con ambito a livello di gruppo di risorse. È anche necessario assicurarsi che il provider `Microsoft.DataBoxEdge` sia registrato. Per informazioni sulla registrazione, vedere [Registrare i provider di risorse](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Per creare una risorsa account di archiviazione, è ugualmente necessario l'accesso di tipo Collaboratore o superiore con ambito a livello di gruppo di risorse. Archiviazione di Azure è un provider di risorse registrato per impostazione predefinita.
- Assicurarsi di disporre dell'accesso amministratore o utente all'API Microsoft Graph. Per altre informazioni, vedere le [Informazioni di riferimento per le autorizzazioni dell'API Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).
- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

### <a name="for-the-data-box-gateway-device"></a>Per il dispositivo Data Box Gateway

Prima di distribuire un dispositivo virtuale, è necessario:

- Sia possibile accedere a un sistema host che esegue Hyper-V in Windows Server 2012 R2 o versioni successive o VMware (ESXi 6.0, 6.5 o 6.7) utilizzabile per il provisioning di un dispositivo.
- Il sistema host sia in grado di dedicare le risorse seguenti al provisioning del dispositivo virtuale Data Box:
  
  - Almeno 4 processori virtuali.
  - Almeno 8 GB di RAM.
  - Un'interfaccia di rete.
  - Un disco del sistema operativo da 250 GB.
  - Un disco virtuale da 2 TB per i dati di sistema.

### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

- La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Data Box Gateway. Per altre informazioni, vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).

- Per le normali condizioni di funzionamento di Data Box Gateway siano disponibili:

    - Larghezza di banda minima di download di 10 Mbps per assicurarsi che il dispositivo rimanga aggiornato.
    - Larghezza di banda minima di upload e download dedicata di 20 Mbps per trasferire i file.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Se si ha già una risorsa Data Box Gateway per la gestione dei dispositivi virtuali, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Per creare una risorsa Data Box Gateway, seguire questa procedura nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere a:

    - Portale di Azure all'URL [https://portal.azure.com](https://portal.azure.com).
    - Portale di Azure per enti pubblici all'URL [https://portal.azure.us](https://portal.azure.us). Per altri dettagli, vedere l'articolo su come [connettersi ad Azure per enti pubblici con il portale](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Nel riquadro sinistro selezionare **Crea una risorsa**. Cercare **Data Box Edge/Data Box Gateway**. Selezionare Data Box Edge/Data Box Gateway. Selezionare **Create** (Crea).
3. Selezionare la sottoscrizione da usare per il dispositivo Data Box Gateway. Selezionare l'area in cui si vuole distribuire la risorsa Data Box Gateway. Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo. Nell'opzione **Data Box Gateway** selezionare **Crea**.

    ![Cercare nel servizio Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Nella scheda **Informazioni di base** immettere o selezionare quanto segue in **Dettagli del progetto**.
    
    |Impostazione  |valore  |
    |---------|---------|
    |Subscription    |Questo campo viene popolato automaticamente in base alla selezione precedente. La sottoscrizione viene collegata all'account di fatturazione. |
    |Resource group  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/management/overview.md).     |

5. Immettere o selezionare quanto segue in **Dettagli istanza**.

    |Impostazione  |valore  |
    |---------|---------|
    |Nome   | Nome descrittivo per identificare la risorsa.<br>Il nome deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |   
    |Region     |Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Per Azure per enti pubblici sono disponibili tutte le aree per enti pubblici elencate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). <br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|
    
    ![Creazione della risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Selezionare **Rivedi e crea**.
 
7. Nella scheda **Rivedi e crea** esaminare i **dettagli sui prezzi**, le **condizioni per l'utilizzo** e i dettagli della risorsa. Selezionare **Create** (Crea).

    ![Esaminare i dettagli della risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

La creazione della risorsa richiede alcuni minuti. Al termine della creazione e della distribuzione della risorsa, si riceverà una notifica. Selezionare **Vai alla risorsa**.

![Esaminare i dettagli della risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Scaricare l'immagine del dispositivo virtuale

Una volta creata la risorsa Data Box Gateway, scaricare l'immagine del dispositivo virtuale appropriata per effettuare il provisioning di un dispositivo virtuale nel sistema host. Le immagini dei dispositivi virtuali sono specifiche di un sistema operativo.

> [!IMPORTANT]
> Il software in esecuzione nel Data Box Gateway può essere usato solo con la risorsa Data Box Gateway.

Per scaricare un'immagine di dispositivo virtuale, seguire questa procedura nel [portale di Azure](https://portal.azure.com/).

1. Nella risorsa creata selezionare **Panoramica**. Se è disponibile una risorsa Azure Data Box Gateway esistente, selezionare la risorsa e passare a **Panoramica**. Selezionare **Configurazione dispositivo**.

    ![Nuova risorsa Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Nel riquadro **Scarica immagine** selezionare l'immagine di dispositivo virtuale corrispondente al sistema operativo del server host usato per il provisioning della VM. I file di immagine sono di circa 5,6 GB.
   
   * [VHDX per Hyper-V in Windows Server 2012 R2 e versioni successive](https://aka.ms/dbe-vhdx-2012).
   * [VMDK per VMWare ESXi 6.0, 6.5 o 6.7](https://aka.ms/dbe-vmdk).

    ![Scaricare l'immagine del dispositivo virtuale Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Scaricare e decomprimere il file in un'unità locale, prendendo nota della posizione del file decompresso.


## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Data Box Gateway è operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Data Box Gateway e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Selezionare la risorsa creata e quindi selezionare **Panoramica**. In **Configurazione dispositivo** passare al riquadro **Configurare e attivare**.

    ![Riquadro Configurare e attivare](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selezionare **Genera chiave** per creare una chiave di attivazione. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.

    ![Ottenere una chiave di attivazione](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La chiave di attivazione scade tre giorni dopo la generazione.
> - Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Gateway, ad esempio:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Scaricare l'immagine del dispositivo virtuale
> * Ottenere la chiave di attivazione

Passare all'esercitazione successiva per informazioni su come effettuare il provisioning di una macchina virtuale per Data Box Gateway. A seconda del sistema operativo host, vedere le istruzioni dettagliate in:

> [!div class="nextstepaction"]
> [Effettuare il provisioning di Data Box Gateway in Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OR

> [!div class="nextstepaction"]
> [Effettuare il provisioning di Data Box Gateway in VMware](./data-box-gateway-deploy-provision-vmware.md)


