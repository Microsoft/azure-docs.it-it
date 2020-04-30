---
title: Esercitazione per preparare il portale di Azure e l'ambiente di datacenter per la distribuzione di Azure Data Box Edge | Microsoft Docs
description: La prima esercitazione sulla distribuzione di Azure Data Box Edge include la preparazione del portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 90ed4bf8f0389619f130e998ed76c720442092b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474476"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Esercitazione: Preparare la distribuzione di Azure Data Box Edge  

Questa è la prima di una serie di esercitazioni sulla distribuzione necessarie per distribuire completamente Azure Data Box Edge. Questa esercitazione descrive come preparare il portale di Azure per la distribuzione di una risorsa Data Box Edge.

Per completare il processo di installazione e configurazione sono necessari privilegi di amministratore. La preparazione del portale richiede meno di 10 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="get-started"></a>Introduzione

Per distribuire Data Box Edge, fare riferimento alle esercitazioni seguenti nella sequenza indicata.

| **#** | **In questo passaggio** | **Usare questi documenti** |
| --- | --- | --- | 
| 1. |**[Preparare il portale di Azure per Data Box Edge](data-box-edge-deploy-prep.md)** |Creare e configurare la risorsa di Data Box Edge prima di installare un dispositivo fisico Data Box Edge. |
| 2. |**[Installare Azure Data Box Edge](data-box-edge-deploy-install.md)**|Disimballare, installare in rack e cablare il dispositivo fisico Data Box Edge.  |
| 3. |**[Connettere, configurare e attivare Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Connettersi all'interfaccia utente Web locale, completare la configurazione del dispositivo e attivare il dispositivo. Il dispositivo è pronto per la configurazione di condivisioni SMB o NFS.  |
| 4. |**[Trasferire dati con Data Box Edge](data-box-edge-deploy-add-shares.md)** |Aggiungere condivisioni e connettersi alle condivisioni da SMB o NFS. |
| 5. |**[Trasformare dati con Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configurare i moduli di calcolo nel dispositivo per trasformare i dati durante lo spostamento in Azure. |

È ora possibile iniziare a configurare il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono indicati i prerequisiti di configurazione per la risorsa Data Box Edge, il dispositivo Data Box Edge e la rete del data center.

### <a name="for-the-data-box-edge-resource"></a>Per la risorsa Data Box Edge

Prima di iniziare, verificare che:

* La sottoscrizione di Microsoft Azure sia abilitata per una risorsa Azure Stack Edge. Assicurarsi di aver usato una sottoscrizione supportata, ad esempio [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) o [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Si abbia accesso di tipo Proprietario o Collaboratore a livello di gruppo di risorse per le risorse Data Box Edge/Data Box Gateway, hub IoT e Archiviazione di Azure.
    - Per creare qualsiasi risorsa Data Box Edge/Data Box Gateway, è necessario disporre di autorizzazioni di Collaboratore o superiori con ambito a livello di gruppo di risorse. È anche necessario assicurarsi che il provider `Microsoft.DataBoxEdge` sia registrato. Per informazioni sulla registrazione, vedere [Registrare i provider di risorse](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Per creare qualsiasi risorsa hub IoT, assicurarsi che il provider Microsoft.Devices sia registrato. Per informazioni sulla registrazione, vedere [Registrare i provider di risorse](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Per creare una risorsa account di archiviazione, è ugualmente necessario l'accesso di tipo Collaboratore o superiore con ambito a livello di gruppo di risorse. Archiviazione di Azure è un provider di risorse registrato per impostazione predefinita.
- Assicurarsi di disporre dell'accesso amministratore o utente all'API Microsoft Graph. Per altre informazioni, vedere le [Informazioni di riferimento per le autorizzazioni dell'API Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).
- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.

### <a name="for-the-data-box-edge-device"></a>Per il dispositivo Data Box Edge

Prima di distribuire un dispositivo fisico, è necessario:

- Aver esaminato le informazioni sulla sicurezza incluse nel pacchetto di spedizione.
- Avere uno slot 1U disponibile in un rack standard da 19 pollici nel data center per montare in rack il dispositivo.
- Avere accesso a una superficie di lavoro orizzontale, stabile e piana su cui collocare il dispositivo in sicurezza.
- Che sia disponibile una fonte di alimentazione CA standard indipendente o un'unità PDU rack con un gruppo di continuità (UPS) nell'ambiente in cui si intende configurare il dispositivo.
- Avere accesso a un dispositivo fisico.


### <a name="for-the-datacenter-network"></a>Per la rete del data center

Prima di iniziare, verificare che:

- La rete nel data center sia configurata in base ai requisiti di rete per il dispositivo Data Box Edge. Per altre informazioni, vedere [Requisiti di sistema di Data Box Edge](data-box-edge-system-requirements.md).

- Per le normali condizioni di funzionamento di Data Box Edge siano disponibili:

    - Larghezza di banda minima di download di 10 Mbps per assicurarsi che il dispositivo rimanga aggiornato.
    - Larghezza di banda minima di upload e download dedicata di 20 Mbps per trasferire i file.

## <a name="create-a-new-resource"></a>Creare una nuova risorsa

Se si ha già una risorsa Data Box Edge per la gestione del dispositivo fisico, saltare questo passaggio e procedere con il passaggio [Ottenere la chiave di attivazione](#get-the-activation-key).

Per creare la risorsa Data Box Edge, completare i passaggi seguenti nel portale di Azure.

1. Usare le credenziali di Microsoft Azure per accedere a: 
    
    - Portale di Azure all'URL [https://portal.azure.com](https://portal.azure.com).
    - Portale di Azure per enti pubblici all'URL [https://portal.azure.us](https://portal.azure.us). Per altri dettagli, vedere l'articolo su come [connettersi ad Azure per enti pubblici con il portale](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. Nel riquadro sinistro selezionare **Crea una risorsa**. Cercare **Data Box Edge/Data Box Gateway**. Selezionare **Data Box Edge/Data Box Gateway**. Selezionare **Create** (Crea).
3. Selezionare la sottoscrizione che si vuole usare per il dispositivo Data Box Edge. Selezionare l'area in cui si vuole distribuire la risorsa Data Box Edge. Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo. L'area archivia solo i metadati per la gestione del dispositivo. I dati effettivi possono essere archiviati in qualsiasi account di archiviazione.
    
    Nell'opzione **Data Box Edge** selezionare **Crea**.

    ![Cercare nel servizio Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Nella scheda **Informazioni di base** immettere o selezionare quanto segue in **Dettagli del progetto**.
    
    |Impostazione  |valore  |
    |---------|---------|
    |Subscription    |Questo campo viene popolato automaticamente in base alla selezione precedente. La sottoscrizione viene collegata all'account di fatturazione. |
    |Resource group  |Selezionare un gruppo esistente o crearne uno nuovo.<br>Altre informazioni sui [gruppi di risorse di Azure](../azure-resource-manager/management/overview.md).     |

4. Immettere o selezionare quanto segue in **Dettagli istanza**.

    |Impostazione  |valore  |
    |---------|---------|
    |Nome   | Nome descrittivo per identificare la risorsa.<br>Il nome deve contenere da 2 a 50 caratteri tra lettere, numeri e trattini.<br> Il nome deve iniziare e finire con una lettera o un numero.        |
    |Region     |Per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se si usa Azure per enti pubblici, sono disponibili tutte le aree per enti pubblici riportate in [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Scegliere la località più vicina all'area geografica in cui si vuole distribuire il dispositivo.|

    ![Dettagli del progetto e dell'istanza](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Selezionare **Avanti: Indirizzo di spedizione**.

    - Se si ha già un dispositivo, selezionare la casella combinata **Ho un dispositivo Data Box Edge**.
    - Se si sta ordinando un nuovo dispositivo, immettere il nome del contatto, la società, l'indirizzo a cui spedire il dispositivo e le informazioni di contatto.

    ![Indirizzo di spedizione per il nuovo dispositivo](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Selezionare **Avanti: Rivedi e crea**.

7. Nella scheda **Rivedi e crea** esaminare i **dettagli sui prezzi**, le **condizioni per l'utilizzo** e i dettagli della risorsa. Selezionare la casella combinata **Dichiaro di aver esaminato le informazioni fornite e di accettare le condizioni relative alla privacy**.

    ![Esaminare i dettagli della risorsa Data Box Edge e le condizioni relative alla privacy](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Selezionare **Create** (Crea).

La creazione della risorsa richiede alcuni minuti. Al termine della creazione e della distribuzione della risorsa, si riceverà una notifica. Selezionare **Vai alla risorsa**.

![Andare alla risorsa Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Dopo che l'ordine è stato inviato, Microsoft lo verifica e invia quindi all'utente un messaggio di posta elettronica con i dettagli della spedizione.

![Notifica della verifica dell'ordine di Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Ottenere la chiave di attivazione

Quando la risorsa Data Box Edge è configurata e operativa, è necessario ottenere la chiave di attivazione. Questa chiave viene usata per attivare il dispositivo Data Box Edge e connetterlo alla risorsa. È possibile ottenere questa chiave ora nel portale di Azure.

1. Selezionare la risorsa creata. Selezionare **Panoramica** e quindi **Configurazione dispositivo**.

    ![Selezionare Configurazione dispositivo](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Nel riquadro **Attiva** selezionare **Genera chiave** per creare una chiave di attivazione. Selezionare l'icona di copia per copiare la chiave e salvarla, in modo da poterla usare in seguito.

    ![Ottenere una chiave di attivazione](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La chiave di attivazione scade tre giorni dopo la generazione.
> - Se la chiave è scaduta, generarne una nuova. La chiave precedente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Edge, ad esempio:

> [!div class="checklist"]
> * Creare una nuova risorsa
> * Ottenere la chiave di attivazione

Passare all'esercitazione successiva per informazioni su come installare Data Box Edge.

> [!div class="nextstepaction"]
> [Installare Data Box Edge](./data-box-edge-deploy-install.md)



