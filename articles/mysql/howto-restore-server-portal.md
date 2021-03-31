---
title: Backup e ripristino-portale di Azure-database di Azure per MySQL
description: In questo articolo viene descritta la procedura per ripristinare un server nel Database di Azure per MySQL usando il portale di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 9bc31cf8fee2669634ff366caac77cb090baf075
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000301"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Come eseguire il backup e il ripristino di un server in Database di Azure per MySQL tramite il portale di Azure

## <a name="backup-happens-automatically"></a>Il backup viene eseguito automaticamente
Il backup dei server Database di Azure per MySQL viene eseguito periodicamente per abilitare le funzionalità di ripristino. L'uso di questa funzionalità consente di ripristinare il server e tutti i suoi database a un momento precedente nel nuovo server.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- [Server e database di database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Impostare la configurazione del backup

La scelta tra backup con ridondanza locale o ridondanza geografica si effettua al momento della creazione del server, nella finestra **Piano tariffario**.

> [!NOTE]
> Dopo aver creato il server, il tipo di ridondanza (locale o geografica) non può essere modificato.
>

Quando si crea un server nel portale di Azure, la finestra **Piano tariffario** consente di selezionare backup **con ridondanza locale** o **ridondanza geografica** per il server. In questa finestra è anche possibile specificare il **periodo di conservazione dei backup**, ovvero per quanti giorni si vogliono archiviare i backup del server.

   :::image type="content" source="./media/howto-restore-server-portal/pricing-tier.png" alt-text="Piano tariffario: scegliere la ridondanza del backup":::

Per altre informazioni sull'impostazione di questi valori durante la creazione, vedere la [guida introduttiva del server Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

È possibile modificare il periodo di conservazione dei backup per un server seguendo questa procedura:
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare il server del Database di Azure per MySQL. Questa azione apre la pagina **Panoramica**.
3. Selezionare **Piano tariffario** nel menu in **IMPOSTAZIONI**. Con il dispositivo di scorrimento è possibile modificare il **periodo di conservazione dei backup** impostandolo su un numero di giorni compreso tra 7 e 35.
Nello screenshot seguente, il periodo è stato aumentato a 34 giorni.
:::image type="content" source="./media/howto-restore-server-portal/3-increase-backup-days.png" alt-text="Periodo di conservazione dei backup aumentato":::

4. Fare clic su **OK** per confermare la modifica.

Il periodo di conservazione dei backup determina quanto è possibile tornare indietro nel tempo con un ripristino temporizzato, essendo il ripristino basato sui backup disponibili. Il ripristino temporizzato è descritto con altri dettagli nella sezione seguente. 

## <a name="point-in-time-restore"></a>Ripristino temporizzato
Database di Azure per MySQL consente di ripristinare il server a un momento specifico e in una nuova copia del server. È possibile usare questo nuovo server per ripristinare i dati oppure configurare le applicazioni perché puntino al nuovo server.

Ad esempio, se oggi una tabella è stata accidentalmente eliminata a mezzogiorno, è possibile eseguire il ripristino a un'ora prima di mezzogiorno per recuperare la tabella e i dati mancanti dalla nuova copia del server. Il ripristino temporizzato viene eseguito a livello di server, non a livello di database.

La procedura seguente consente di ripristinare il server di esempio a un momento specifico:
1. Nel portale di Azure selezionare il server del Database di Azure per MySQL. 

2. Nella barra degli strumenti della pagina **Panoramica** del server selezionare **Ripristina**.

   :::image type="content" source="./media/howto-restore-server-portal/2-server.png" alt-text="Database di Azure per MySQL: Panoramica - Pulsante Ripristina":::

3. Compilare il modulo Ripristina con le informazioni obbligatorie:

   :::image type="content" source="./media/howto-restore-server-portal/3-restore.png" alt-text="Database di Azure per MySQL - Informazioni di ripristino":::
   - **Punto di ripristino**: selezionare il punto nel tempo per il ripristino.
   - **Server di destinazione**: specificare un nome per il nuovo server.
   - **Percorso**: non è possibile selezionare l'area. Per impostazione predefinita è uguale al server di origine.
   - **Piano tariffario**: non è possibile modificare questi parametri quando si esegue un ripristino temporizzato. È uguale al server di origine. 

4. Fare clic su **OK** per ripristinare il server a un momento specifico. 

5. Al termine del ripristino, individuare il nuovo server creato per verificare che il ripristino dei dati sia avvenuto come previsto.

Il nuovo server creato con il ripristino temporizzato ha il nome e la password di accesso dell'amministratore validi per il server esistente nel momento scelto per il ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Inoltre, al termine dell'operazione di ripristino, sono disponibili due parametri del server che vengono reimpostati sui valori predefiniti (e non vengono copiati dal server primario) dopo l'operazione di ripristino
*   time_zone: questo valore per impostare il valore predefinito del **sistema**
*   event_scheduler: il event_scheduler è impostato su **off** nel server ripristinato

È necessario copiare il valore dal server primario e impostarlo sul server ripristinato riconfigurando il [parametro Server](howto-server-parameters.md)

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Per il nuovo server, queste regole devono essere impostate separatamente. Vengono ripristinate le regole del firewall del server originale.

## <a name="geo-restore"></a>Ripristino geografico
Se il server è stato configurato per backup con ridondanza geografica, è possibile creare un nuovo server dal backup di quel server esistente. Questo nuovo server può essere creato in qualsiasi area in cui è disponibile Database di Azure per MySQL.  

1. Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale. Selezionare **Database** > **Database di Azure per MySQL**.

   :::image type="content" source="./media/howto-restore-server-portal/1_navigate-to-mysql.png" alt-text="Passare a database di Azure per MySQL.":::
 
2. Fornire la sottoscrizione, il gruppo di risorse e il nome del nuovo server. 

3. Selezionare **backup** come **origine dati**. Questa azione carica un elenco a discesa che fornisce un elenco di server in cui sono abilitati i backup con ridondanza geografica.
   
   :::image type="content" source="./media/howto-restore-server-portal/3-geo-restore.png" alt-text="Selezionare l'origine dati.":::
    
   > [!NOTE]
   > Quando un server viene creato per la prima volta, potrebbe non essere subito disponibile per il ripristino geografico. Potrebbero essere necessarie alcune ore per popolare i metadati necessari.
   >

4. Selezionare l'elenco a discesa **backup** .
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore-backup.png" alt-text="Selezionare elenco a discesa backup.":::

5. Selezionare il server di origine da cui eseguire il ripristino.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-select-backup.png" alt-text="Selezionare backup.":::

6. Il server utilizzerà i valori per il numero di **Vcore**, il **periodo di conservazione dei backup**, l'opzione di **ridondanza del backup**, la versione del **motore** e le **credenziali di amministratore**. Selezionare **Continua**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/6-accept-backup.png" alt-text="Continuare con il backup.":::

7. Compilare il resto del modulo con le proprie preferenze. È possibile selezionare qualsiasi **posizione**.

    Dopo aver selezionato il percorso, è possibile selezionare **Configura server** per aggiornare **la generazione di calcolo** (se disponibile nell'area scelta), il numero di **Vcore**, il **periodo di conservazione dei backup** e l'opzione di **ridondanza del backup**. La modifica del **piano tariffario** (Basic, Utilizzo generico o Con ottimizzazione per la memoria) o delle dimensioni della **risorsa di archiviazione** non è supportata durante il ripristino.

   :::image type="content" source="./media/howto-restore-server-portal/7-create.png" alt-text="Compila modulo."::: 

8. Selezionare **Revisione e creazione** per rivedere le selezioni effettuate. 

9. Selezionare **Crea** per effettuare il provisioning del server. Questa operazione può richiedere qualche minuto.

Il nuovo server creato con il ripristino geografico ha il nome e la password di accesso dell'amministratore validi per il server esistente al momento dell'avvio del ripristino. È possibile modificare la password dalla pagina **Panoramica** del nuovo server.

Il nuovo server creato durante un ripristino non include gli endpoint servizio di rete virtuale presenti nel server originale. Per il nuovo server, queste regole devono essere impostate separatamente. Vengono ripristinate le regole del firewall del server originale.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [backup](concepts-backup.md) del servizio
- Informazioni sulle [repliche](concepts-read-replicas.md)
- Altre informazioni sulle opzioni di [continuità aziendale](concepts-business-continuity.md)