---
title: Esercitazione per esportare i dati da Azure Data Box | Microsoft Docs
description: Informazioni sui prerequisiti di distribuzione e su come esportare i dati da un Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 42476e2689cc503edc19e8e299a01ce922f1bf42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789196"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Esercitazione: creare un ordine di esportazione per Azure Data Box

Azure Data Box è una soluzione ibrida che consente di spostare i dati da Azure nella propria posizione. In questa esercitazione viene descritto come creare un ordine di esportazione per Azure Data Box. Il motivo principale per cui si crea un ordine di esportazione è per il ripristino di emergenza, nel caso in cui l'archiviazione locale venga compromessa ed è necessario ripristinare un backup.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti per l'esportazione
> * Ordinare un Data Box per l'esportazione
> * Tenere traccia dell'ordine di esportazione
> * Annulla l'ordine di esportazione

## <a name="prerequisites"></a>Prerequisiti

Completare i prerequisiti di configurazione seguenti per Data Box servizio e il dispositivo prima di ordinare il dispositivo.

### <a name="for-service"></a>Per il servizio

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Assicurarsi di disporre di un gruppo di risorse esistente che è possibile usare con il Azure Data Box.

* Verificare che l'account di archiviazione di Azure da cui si vuole esportare i dati sia uno dei tipi di account di archiviazione supportati come [account di archiviazione supportati per data box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Per il dispositivo

Prima di iniziare, verificare che:

* Sia disponibile un computer host connesso alla rete del data center. I dati vengono copiati da Azure Data Box a questo computer. Il computer host deve eseguire un sistema operativo supportato come descritto nei [requisiti di sistema per Azure Data Box](data-box-system-requirements.md).

* Il data center disponga di una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà.

## <a name="order-data-box-for-export"></a>Data Box di ordine per l'esportazione

Seguire questa procedura nel portale di Azure per ordinare un dispositivo.

1. Usare le credenziali di Microsoft Azure per accedere all'URL [https://portal.azure.com](https://portal.azure.com).

2. Selezionare **+ Crea una risorsa** e cercare *Azure Data Box*. Selezionare **Azure Data Box**.

   ![Creare una risorsa](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Selezionare **Crea**.

   ![Creare una risorsa Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Controllare se Azure Data Box servizio è disponibile nella propria area. Immettere o selezionare le informazioni seguenti e quindi selezionare **Applica**.

    |Impostazione  |valore  |
    |---------|---------|
    |Tipo di trasferimento     | Selezionare **Esporta in Azure**.        |
    |Subscription     | Selezionare una sottoscrizione di tipo Contratto Enterprise, CSP o Azure Sponsorship per il servizio Data Box. <br> La sottoscrizione viene collegata all'account di fatturazione.       |
    |Resource group     |    Selezionare un gruppo di risorse esistente. <br> Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme.         |
    |Area di Azure di origine    |    Selezionare l'area di Azure in cui sono attualmente presenti i dati.         |
    |Paese di destinazione     |     Selezionare il paese in cui si vuole spedire il dispositivo.        |

   ![Selezionare le impostazioni di Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Selezionare **Data Box**. La capacità massima che è possibile usare per un singolo ordine è di 80 TB. È possibile creare più ordini per volumi di dati maggiori.

   ![Selezione della capacità di Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. Specificare i **Dettagli dell'ordine di** **base** . Immettere o selezionare le informazioni seguenti.

    |Impostazione  |Valore  |
    |---------|---------|
    |Sottoscrizione     | La sottoscrizione viene popolata automaticamente in base alla selezione precedente.|
    |Resource group | Il gruppo di risorse selezionato in precedenza. |
    |Nome dell'ordine di esportazione     |  Specificare un nome descrittivo per tenere traccia dell'ordine. <br> Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. <br> Il nome deve iniziare e terminare con una lettera o un numero.      |

    ![Nozioni fondamentali sull'ordine di esportazione](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Selezionare **Avanti: selezione dati** per continuare.

7. In **selezione dati** selezionare **Aggiungi account di archiviazione ed Esporta tipo**.

    ![Aggiungere un account di archiviazione e un tipo di esportazione](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. In **Seleziona l'opzione Esporta** specificare i dettagli dell'opzione di esportazione. Immettere o selezionare le informazioni seguenti e selezionare **Aggiungi**.

    |Impostazione  |Valore  |
    |---------|---------|
    |Account di archiviazione     | L'account di archiviazione di Azure da cui si desidera esportare i dati. |
    |Tipo di esportazione     | Specifica il tipo di dati da esportare da **tutti gli oggetti** e **utilizzare il file XML**.<ul><li> **Tutti gli oggetti** : specifica che il processo Esporta tutti i dati a seconda della selezione effettuata per le **Opzioni di trasferimento**.</li><li> **Usa file XML** : specifica un file XML che contiene un set di percorsi e prefissi per i BLOB e/o i file da esportare dall'account di archiviazione. Il file XML deve trovarsi nel contenitore dell'account di archiviazione selezionato e la selezione da condivisioni file non è attualmente supportata. Il file deve essere un file XML non vuoto.</li></ul>        |
    |Opzioni di trasferimento     |  Specifica le opzioni di trasferimento dati da **Seleziona tutto**, **tutti i BLOB** e **tutti i file**. <ul><li> **Select All** : specifica che tutti i blob e file di Azure vengono esportati. Se si usa un account di archiviazione che supporta solo BLOB (account di archiviazione BLOB), l'opzione **tutti i file** non sarà selezionabile.</li><li> **All Blobs** : specifica che vengono esportati solo i BLOB in blocchi e di pagine.</li><li> **Tutti i file** : specifica che tutti i file vengono esportati, esclusi i BLOB. Il tipo di account di archiviazione in cui si dispone (utilizzo generico V1 e GPv2, archiviazione Premium o archiviazione BLOB) determina i tipi di dati che è possibile esportare. Per altre informazioni, vedere [account di archiviazione supportati per l'esportazione](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Includi log dettagliato     | Indica se si desidera un file di log dettagliato contenente un elenco di tutti i file che sono stati esportati correttamente.        |

    > [!NOTE]
    >
    > Se si seleziona **Usa file XML** per l'impostazione del **tipo di esportazione** , è necessario assicurarsi che il codice XML contenga percorsi e/o prefissi validi. È necessario creare e fornire il file XML.  Se il file non è valido o non è presente alcun dato corrispondente ai percorsi specificati, l'ordine termina con dati parziali o senza dati esportati.

    Per informazioni su come aggiungere un file XML a un contenitore, vedere [esportare l'ordine usando il file XML](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Seleziona l'opzione di esportazione](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Per un esempio di input XML, vedere [input XML di esempio](data-box-deploy-export-ordered.md#sample-xml-file)

9. In **selezione dati** esaminare le impostazioni e fare clic su **avanti: sicurezza>** per continuare.

   ![Ordine di esportazione, selezione dati](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    La schermata **sicurezza** consente di usare la propria chiave di crittografia e di scegliere di usare la crittografia doppia.

    Tutte le impostazioni nella schermata **Sicurezza** sono facoltative. Se non si modifica alcuna impostazione, vengono applicate le impostazioni predefinite.

    ![Schermata sicurezza in Importazione guidata ordine di Data Box](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Se si vuole usare la propria chiave gestita dal cliente per proteggere la passkey di sblocco per la nuova risorsa, espandere **Tipo di crittografia**.

    La configurazione di una chiave gestita dal cliente per Azure Data Box è facoltativa. Per impostazione predefinita, Data Box usa una chiave gestita da Microsoft per proteggere la passkey di sblocco.

    Una chiave gestita dal cliente non influisce sul modo in cui il dispositivo viene crittografato. Viene usata solo per crittografare la passkey di sblocco del dispositivo.

    Se non si vuole usare una chiave gestita dal cliente, andare al passaggio 16.

    ![Schermata Sicurezza che mostra le impostazioni di Tipo di crittografia](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Selezionare **Chiave gestita dal cliente** come tipo di chiave. Quindi selezionare **Seleziona un insieme di credenziali delle chiavi e la chiave**.
   
    ![Schermata Sicurezza, impostazioni per la chiave gestita dal cliente](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. Nella schermata **Seleziona chiave da Azure Key Vault** la sottoscrizione viene popolata automaticamente.

    - Per **Insieme di credenziali delle chiavi** è possibile selezionare un insieme di credenziali delle chiavi esistente nell'elenco a discesa.

      ![Schermata Selezionare chiave da Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - È anche possibile selezionare **Crea nuovo** per creare un nuovo insieme di credenziali delle chiavi. Nella schermata **Crea insieme di credenziali delle chiavi** immettere il gruppo di risorse e il nome dell'insieme di credenziali delle chiavi. Assicurarsi che le opzioni **Eliminazione temporanea** e **Protezione dall'eliminazione** siano abilitate. Accettare tutte le altre impostazioni predefinite e selezionare **Rivedi e crea**.

      ![Impostazioni per la creazione di un nuovo insieme di credenziali delle chiavi di Azure](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Esaminare le informazioni per l'insieme di credenziali delle chiavi e selezionare **Crea**. Attendere un paio di minuti mentre viene completata la creazione dell'insieme di credenziali delle chiavi.

      ![Schermata di revisione del nuovo insieme di credenziali delle chiavi di Azure](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. Nella schermata **Seleziona chiave da Azure Key Vault** è possibile selezionare una chiave esistente nell'insieme di credenziali delle chiavi.

    ![Selezionare una chiave esistente da Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Se si vuole creare una nuova chiave, selezionare **Crea nuovo**. È necessario usare una chiave RSA. Le dimensioni possono essere maggiori o uguali a 2048. Immettere un nome per la nuova chiave, accettare le altre impostazioni predefinite e selezionare **Crea**.

      ![Opzione per creare una nuova chiave](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Dopo che la chiave viene creata nell'insieme di credenziali delle chiavi si riceve una notifica.

14. Selezionare un valore di **Versione** da usare per la chiave, quindi scegliere **Seleziona**.

      ![Nuova chiave creata nell'insieme di credenziali delle chiavi](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Se si vuole creare una nuova versione della chiave, selezionare **Crea nuovo**.

    ![Aprire una finestra di dialogo per creare una nuova versione della chiave](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    Nella schermata **Crea nuova chiave** scegliere impostazioni per la nuova versione della chiave e selezionare **Crea**.

    ![Creare una nuova versione della chiave](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    Le impostazioni di **Tipo di crittografia** nella schermata **Sicurezza** mostrano l'insieme di credenziali delle chiavi e la chiave.

    ![Chiave e insieme di credenziali delle chiavi per una chiave gestita dal cliente](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Selezionare un'identità utente da usare per gestire l'accesso a questa risorsa. Scegliere **Select a user identity** (Selezionare un'identità utente). Nel pannello a destra selezionare la sottoscrizione e l'identità gestita da usare. Quindi scegliere **Seleziona**.

    Un'identità gestita assegnata dall'utente è una risorsa di Azure autonoma che è possibile usare per gestire più risorse. Per altre informazioni, vedere [Tipi di identità gestita](../active-directory/managed-identities-azure-resources/overview.md).  

    Se è necessario creare una nuova identità gestita, seguire le istruzioni riportate in [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente tramite il portale di Azure](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Selezionare un'identità utente](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    L'identità utente viene visualizzata nelle impostazioni di **Tipo di crittografia**.

    È possibile comprimere ora le impostazioni del **tipo di crittografia** .

    ![Un'identità utente selezionata mostrata nelle impostazioni di Tipo di crittografia](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Se si vuole abilitare la crittografia doppia basata su software, espandere **crittografia doppia (per ambienti con sicurezza elevata)** e selezionare **Abilita crittografia doppia per l'ordine**. 

    La crittografia basata su software viene eseguita oltre alla crittografia AES a 256 bit dei dati nel Data Box.

    > [!NOTE]
    > Se si abilita questa opzione, l'elaborazione dll'ordine e la copia dei dati potrebbero richiedere più tempo. Non è possibile modificare questa opzione dopo aver creato l'ordine.

    ![Schermata di sicurezza per l'importazione di data box, crittografia doppia](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Selezionare **Avanti: Dettagli contatto** per continuare.

11. Nei **Dettagli del contatto** selezionare **+ Aggiungi indirizzo di spedizione** per immettere le informazioni sulla spedizione.

    ![Aggiungi indirizzo di spedizione](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. In **Aggiungi indirizzo di spedizione** specificare il nome e il cognome, il nome e l'indirizzo postale della società e un numero di telefono valido. Selezionare **Convalida**. Il servizio convalida l'indirizzo di spedizione per la disponibilità del servizio. Se il servizio è disponibile per l'indirizzo di spedizione specificato, si riceve una notifica in tal senso.

    ![Convalidare l'indirizzo di spedizione](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Se si sta ordinando in un'area in cui è disponibile la spedizione autonoma, è possibile selezionare questa opzione. Per altre informazioni sulla spedizione autogestita, vedere [Usare la spedizione autogestita](data-box-portal-customer-managed-shipping.md).

13. Selezionare **Aggiungi indirizzo di spedizione** una volta che i dettagli di spedizione sono stati convalidati correttamente.

14. In **Dettagli contatto** esaminare l'indirizzo di spedizione e l'indirizzo di posta elettronica. Il servizio invia notifiche tramite posta elettronica per qualsiasi aggiornamento dello stato dell'ordine agli indirizzi di posta elettronica specificati.

    È consigliabile usare un indirizzo di posta elettronica di gruppo in modo da continuare a ricevere le notifiche anche se un amministratore del gruppo non è disponibile.

    ![Dettagli contatto](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Selezionare **Avanti: rivedere + Order>**. Per procedere con la creazione dell'ordine, è necessario accettare i termini e le condizioni.

16. Selezionare **Ordina**. Per la creazione dell'ordine sono richiesti pochi minuti.

    ![Ordine di commit](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Esporta ordine tramite file XML

Se si seleziona **Usa file XML**, è possibile specificare contenitori e BLOB specifici (pagina e blocco) che si desidera esportare. È necessario seguire le specifiche della [tabella dei file XML di esempio](#sample-xml-file) per la formattazione del codice XML. La procedura seguente illustra come usare un file XML per esportare i dati:

1. In **tipo di esportazione** selezionare **Usa file XML**. Si tratta del file XML che specifica i BLOB specifici e i file di Azure che si vuole esportare. Per aggiungere il file XML, selezionare **fare clic qui per selezionare un file XML**.

     ![Selezionare l'opzione di esportazione, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Selezionare **+ contenitore** per creare un contenitore.

    ![Seleziona l'opzione di esportazione, contenitori](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Nella scheda **nuovo contenitore** visualizzata dal lato destro del portale di Azure aggiungere un nome per il contenitore. Il nome deve essere in lettere minuscole ed è possibile includere numeri e trattini '-'. Selezionare quindi il **livello di accesso pubblico** dalla casella di riepilogo a discesa. Si consiglia di scegliere **privato (accesso non anonimo)** per impedire ad altri utenti di accedere ai dati. Per altre informazioni sui livelli di accesso ai contenitori, vedere [autorizzazioni di accesso al contenitore](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Selezionare l'opzione di esportazione, nuove impostazioni del contenitore](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Selezionare **Crea**.

   ![Selezionare l'opzione Esporta, crea nuovo contenitore.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Se il contenitore viene creato correttamente, verrà visualizzato il messaggio seguente:

   ![Creazione del contenitore completata](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Selezionare il contenitore creato e fare doppio clic su di esso.

   ![Visualizza i dettagli del contenitore](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Facendo doppio clic sul contenitore, viene visualizzata la visualizzazione delle proprietà del contenitore. A questo punto si vuole alleghi (o individuare) il file XML che contiene l'elenco di BLOB e/o File di Azure che si vuole esportare. Selezionare **Carica**.

   ![Caricare un BLOB nel contenitore](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Il file XML è stato aggiunto al contenitore. Verranno esportati solo i BLOB e File di Azure specificati in questo XML.

   ![File XML aggiunto al contenitore](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>Monitorare l'ordine

Dopo aver inserito l'ordine, è possibile monitorare lo stato dell'ordine dal portale di Azure. Passare all'ordine del Data Box e quindi a **Panoramica** per visualizzare lo stato. Il portale mostra l'ordine con stato **Ordinato**.

Al termine della preparazione del dispositivo, la copia dei dati inizierà dagli account di archiviazione selezionati. Il portale Mostra l'ordine nello stato **di copia dei dati in corso** .

![Ordine di esportazione Data Box, copia dati in corso](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box copia i dati dagli account di archiviazione di origine. Una volta completata la copia dei dati, Data Box è bloccata e il portale visualizzerà l'ordine nello stato di **copia completata** .

![Data Box ordine di esportazione, copia dati completata](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Se il dispositivo non è disponibile, si riceverà una notifica. Se il dispositivo è disponibile, Microsoft identifica il dispositivo per la spedizione e lo prepara. Durante la preparazione del dispositivo si verificano le azioni seguenti:

* Vengono create condivisioni SMB per ogni account di archiviazione associato al dispositivo.
* Per ogni condivisione vengono generate le credenziali di accesso, come nome utente e password.
* Il dispositivo è bloccato ed è possibile accedervi solo tramite la password di sblocco del dispositivo. Per recuperare la password, è necessario accedere all'account di portale di Azure e selezionare **i dettagli del dispositivo**.

Microsoft prepara e invia il dispositivo tramite un vettore regionale. Si riceverà un numero di tracciabilità dopo la spedizione del dispositivo. Il portale mostra l'ordine con lo stato **Spedito**.

![Ordine di esportazione Data Box inviato](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Se è stata selezionata l'opzione self-Managed shipping, si riceverà una notifica tramite posta elettronica con i passaggi successivi quando il dispositivo è pronto per essere prelevato dal Data Center. Per ulteriori informazioni relative alla spedizione autonoma, vedere la pagina relativa a [spedizioni](data-box-portal-customer-managed-shipping.md)autogestite.

![Spedizione self-Managed pronta per il ritiro](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Annullare l'ordine

Per annullare l'ordine, nel portale di Azure passare a **Panoramica** e fare clic su **Annulla** sulla barra dei comandi.

Dopo l'inserimento di un ordine, è possibile annullarlo in qualsiasi momento prima dell'avvio dell'elaborazione dell'ordine.

Per eliminare un ordine annullato, passare a **Panoramica** e fare clic su **Elimina** sulla barra dei comandi.

## <a name="sample-xml-file"></a>File XML di esempio

Nel codice XML seguente viene illustrato un esempio di nomi di BLOB, prefissi di BLOB e File di Azure contenuti nel formato XML utilizzato dall'ordine di esportazione quando si seleziona l'opzione **utilizza file XML** :

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Alcuni punti importanti rispetto ai file XML:

* I tag XML fanno distinzione tra maiuscole e minuscole e devono corrispondere esattamente a quanto specificato nell'esempio precedente.
* I tag di apertura e di chiusura devono corrispondere.
* La formattazione o i tag XML non corretti possono causare un errore di esportazione dei dati.
* Non verranno esportati dati se il prefisso del file e/o del BLOB non è valido.

### <a name="examples-of-valid-blob-paths"></a>Esempi di percorsi BLOB validi

La tabella seguente mostra alcuni esempi di percorsi BLOB validi:

   | Selettore | Percorso BLOB | Descrizione |
   | --- | --- | --- |
   | Inizia con |/ |Esporta tutti i BLOB nell'account di archiviazione |
   | Inizia con |/$root/ |Esporta tutti i BLOB nel contenitore radice |
   | Inizia con |/containers |Esporta tutti i BLOB in qualsiasi contenitore che inizia con i **contenitori** di prefisso |
   | Inizia con |/container-name/ |Esporta tutti i BLOB nel contenitore contenitore **-nome** |
   | Inizia con |/container-name/prefix |Esporta tutti i BLOB nel contenitore contenitore **-nome** che inizia con **prefisso** prefisso |
   | Uguale a |$root/logo.bmp |Esporta il BLOB **logo.bmp** nel contenitore radice |
   | Uguale a |8tbpageblob/mydata.txt |Esporta **mydata.txt** BLOB nel contenitore **8tbpageblob** |

## <a name="sample-log-files"></a>File di log di esempio

In questa sezione vengono forniti i file di log di esempio che vengono generati durante l'esportazione. I log degli errori vengono generati automaticamente. Per generare il file di log dettagliato, è necessario selezionare **Includi log dettagliato** in portale di Azure durante la configurazione dell'ordine di esportazione.
Per altre informazioni sui log di copia e dettagliati, vedere [copiare i log](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
>
> * Prerequisiti per l'esportazione
> * Ordinare un Data Box per l'esportazione
> * Tenere traccia dell'ordine di esportazione
> * Annulla l'ordine di esportazione

Passare all'esercitazione successiva per informazioni su come configurare il Data Box.

> [!div class="nextstepaction"]
> [Configurare Azure Data Box](./data-box-deploy-set-up.md)