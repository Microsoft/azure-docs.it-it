---
title: Esercitazione per ordinare Azure Data Box | Microsoft Docs
description: Informazioni sui prerequisiti di distribuzione e su come ordinare un dispositivo Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: b0204673c0706403c8c5a7367be19e590d9cb134
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "65604083"
---
# <a name="tutorial-order-azure-data-box"></a>Esercitazione: Ordinare Azure Data Box

Azure Data Box è una soluzione ibrida che consente di importare i dati locali in Azure in modo rapido, semplice e affidabile. I dati devono essere trasferiti in un dispositivo di archiviazione da 80 TB (capacità utilizzabile) fornito da Microsoft e quindi il dispositivo deve essere rispedito a Microsoft. Questi dati vengono poi caricati in Azure.

Questa esercitazione illustra come ordinare un dispositivo Azure Data Box. Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
> * Prerequisiti per la distribuzione del Data Box
> * Ordinare un Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

## <a name="prerequisites"></a>Prerequisites

Prima di distribuire il dispositivo, completare i prerequisiti di configurazione seguenti per il servizio e il dispositivo Data Box.

### <a name="for-service"></a>Per il servizio

Prima di iniziare, verificare che:
- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
- La sottoscrizione usata per il servizio Data Box sia di uno dei tipi seguenti:
    - Contratto Enterprise Microsoft. Altre informazioni sui [contratti Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Altre informazioni sul [programma Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship Altre informazioni sul [programma Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Si abbia accesso alla sottoscrizione come proprietario o collaboratore per creare un ordine Data Box.

### <a name="for-device"></a>Per il dispositivo

Prima di iniziare, verificare che:
- Sia disponibile un computer host connesso alla rete del data center. Il servizio Data Box copierà i dati da questo computer. Il computer host deve eseguire un sistema operativo supportato come descritto nei [requisiti di sistema per Azure Data Box](data-box-system-requirements.md).
- Il data center disponga di una rete ad alta velocità. È consigliabile disporre di una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà.


## <a name="order-data-box"></a>Ordinare il Data Box

Seguire questa procedura nel portale di Azure per ordinare un dispositivo.

1. Usare le credenziali di Microsoft Azure per accedere all'URL [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **+ Crea una risorsa** e cercare *Azure Data Box*. Fare clic su **Azure Data Box**.
    
   [![Cercare Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Fare clic su **Crea**.

4. Controllare se il servizio Data Box è disponibile nella propria area. Immettere o selezionare le informazioni seguenti e quindi fare clic su **Applica**. 

    |Impostazione  |valore  |
    |---------|---------|
    |Subscription     | Selezionare una sottoscrizione di tipo Contratto Enterprise, CSP o Azure Sponsorship per il servizio Data Box. <br> La sottoscrizione viene collegata all'account di fatturazione.       |
    |Tipo di trasferimento     | Selezionare **Importa in Azure**.        |
    |Paese di origine     |   Selezionare il paese/area in cui si trovano attualmente i dati.         |
    |Area di Azure di destinazione     |     Selezionare l'area di Azure in cui si vogliono trasferire i dati.        |

5. Selezionare **Data Box**. La capacità massima che è possibile usare per un singolo ordine è di 80 TB. È possibile creare più ordini per volumi di dati maggiori.

      [![Selezione del Data Box - opzione 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. In **Ordine** specificare i **Dettagli ordine**. Immettere o selezionare le informazioni seguenti e fare clic su **Avanti**.
    
    |Impostazione  |valore  |
    |---------|---------|
    |Nome     |  Specificare un nome descrittivo per tenere traccia dell'ordine. <br> Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. <br> Il nome deve iniziare e terminare con una lettera o un numero.      |
    |Resource group     |   Usare un gruppo esistente o crearne uno nuovo. <br> Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme.         |
    |Area di Azure di destinazione     | Selezionare l'area per l'account di archiviazione. <br> Per altre informazioni, vedere [Disponibilità a livello di area](data-box-overview.md#region-availability).        |
    |Destinazione di archiviazione     | Scegliere tra account di archiviazione e/o dischi gestiti. <br> In base all'area di Azure specificata, selezionare uno o più account di archiviazione nell'elenco filtrato di un account di archiviazione esistente. Il Data Box può essere collegato a un massimo di 10 account di archiviazione. <br> È anche possibile creare un nuovo account **Utilizzo generico v1**, **Utilizzo generico v2** o un **account di archiviazione BLOB**. <br>Sono supportati gli account di archiviazione con reti virtuali. Per consentire al servizio Data Box di lavorare con gli account di archiviazione protetti, abilitare i servizi attendibili all'interno delle impostazioni del firewall di rete dell'account di archiviazione. Per altre informazioni, vedere come [Aggiungere Azure Data Box come servizio attendibile](../storage/common/storage-network-security.md#exceptions).|

    Se si usa l'account di archiviazione come destinazione, vedere lo screenshot seguente:

    ![Ordine di Data Box per l'account di archiviazione](media/data-box-deploy-ordered/order-storage-account.png)

    Se si usa Data Box per creare dischi gestiti da dischi rigidi virtuali in locale è necessario anche fornire le informazioni seguenti:

    |Impostazione  |valore  |
    |---------|---------|
    |Gruppi di risorse     | Se si prevede la creazione di dischi gestiti da dischi rigidi virtuali in locale, creare nuovi gruppi di risorse. È possibile usare un gruppo di risorse solo se questo è stato creato in precedenza durante la creazione di un ordine di Data Box per il disco gestito dal servizio Data Box. <br> Specificare più gruppi di risorse separati da punti e virgola. Vengono supportati un massimo di 10 gruppi di risorse.|

    ![Ordine di Data Box per disco gestito](media/data-box-deploy-ordered/order-managed-disks.png)

    L'account di archiviazione specificato per i dischi gestiti viene usato come account di archiviazione di staging. Il servizio Data Box carica i dischi rigidi virtuali come BLOB di pagine per l'account di archiviazione di staging prima di convertirli in dischi gestiti e spostarli nei gruppi di risorse. Per altre informazioni, vedere [Verificare il caricamento dei dati in Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. In **Indirizzo di spedizione** specificare nome e cognome, nome e indirizzo postale della società e un numero di telefono valido. Fare clic su **Convalida indirizzo**. Il servizio convalida l'indirizzo di spedizione per la disponibilità del servizio. Se il servizio è disponibile per l'indirizzo di spedizione specificato, si riceve una notifica in tal senso. Fare clic su **Avanti**.

8. In **Dettagli notifica** specificare gli indirizzi di posta elettronica. Il servizio invia notifiche tramite posta elettronica per qualsiasi aggiornamento dello stato dell'ordine agli indirizzi di posta elettronica specificati.

    È consigliabile usare un indirizzo di posta elettronica di gruppo in modo da continuare a ricevere le notifiche anche se un amministratore del gruppo non è disponibile.

9. Verificare il **riepilogo** delle informazioni relative a ordine, contatti, notifiche e informativa sulla privacy. Selezionare la casella corrispondente per accettare le condizioni per la privacy.

10. Fare clic su **Ordina**. Per la creazione dell'ordine sono richiesti pochi minuti.


## <a name="track-the-order"></a>Monitorare l'ordine

Dopo aver inserito l'ordine, è possibile monitorare lo stato dell'ordine dal portale di Azure. Passare all'ordine del Data Box e quindi a **Panoramica** per visualizzare lo stato. Il portale mostra l'ordine con stato **Ordinato**.

Se il dispositivo non è disponibile, si riceverà una notifica. Se il dispositivo è disponibile, Microsoft identifica il dispositivo per la spedizione e lo prepara. Durante la preparazione del dispositivo vengono eseguite le azioni seguenti:

- Vengono create condivisioni SMB per ogni account di archiviazione associato al dispositivo.
- Per ogni condivisione vengono generate le credenziali di accesso, come nome utente e password.
- Viene generata anche una password per il dispositivo che consente di sbloccare il dispositivo.
- Il Data Box viene bloccato per impedire qualsiasi accesso non autorizzato al dispositivo.

Una volta completata la preparazione del dispositivo, lo stato dell'ordine nel portale diventa **Elaborato**.

![Ordine del Data Box elaborato](media/data-box-overview/data-box-order-status-processed.png)

Microsoft prepara e spedisce quindi il dispositivo tramite un corriere locale. Si riceverà un numero di tracciabilità dopo la spedizione del dispositivo. Il portale mostra l'ordine con lo stato **Spedito**.

![Ordine del Data Box spedito](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Annullare l'ordine

Per annullare l'ordine, nel portale di Azure passare a **Panoramica** e fare clic su **Annulla** sulla barra dei comandi.

Dopo aver effettuato un ordine, è possibile annullarlo in qualsiasi momento prima che il suo stato venga contrassegnato come elaborato.
 
Per eliminare un ordine annullato, passare a **Panoramica** e fare clic su **Elimina** dalla barra dei comandi.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Prerequisiti per la distribuzione del Data Box
> * Ordinare il Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

Passare all'esercitazione successiva per informazioni su come configurare il Data Box.

> [!div class="nextstepaction"]
> [Configurare Azure Data Box](./data-box-deploy-set-up.md)


