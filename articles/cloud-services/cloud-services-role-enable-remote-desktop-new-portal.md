---
title: Usare il portale per abilitare Desktop remoto per un ruolo
description: Come configurare l'applicazione del servizio cloud di Azure per consentire le connessioni Desktop remoto
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8fa0d3c0e29c53e6fe9cb32ddf02168686be1efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743254"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Abilitare Connessione Desktop remoto per un ruolo nei servizi cloud di Azure (versione classica)

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile usare una connessione Desktop remoto per risolvere e diagnosticare i problemi dell'applicazione mentre è in esecuzione.

È possibile abilitare una connessione Desktop remoto nel ruolo durante lo sviluppo includendo i moduli di Desktop remoto nella definizione del servizio o è possibile scegliere di abilitare Desktop remoto tramite la relativa estensione. L'approccio migliore consiste nell'usare l'estensione di Desktop remoto, in quanto è possibile abilitare Desktop remoto anche dopo che l'applicazione viene distribuita senza doverla ridistribuire.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configurare Desktop remoto dal portale di Azure

Il portale di Azure usa l'approccio dell'estensione di Desktop remoto in modo da poter abilitare Desktop remoto anche dopo la distribuzione dell'applicazione. L'impostazione **Desktop remoto** per il servizio cloud consente di abilitare Desktop remoto, modificare l'account amministratore locale usato per connettersi alle macchine virtuali e il certificato usato nell'autenticazione e impostare la data di scadenza.

1. Fare clic su **Servizi cloud**, selezionare il nome del servizio cloud e infine **Desktop remoto**.

    ![immagine Mostra servizi Cloud Desktop remoto](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Scegliere se si vuole abilitare Desktop remoto per un singolo ruolo o per tutti i ruoli, quindi modificare il valore del commutatore su **Abilitato**.

3. Compilare i campi obbligatori per nome utente, password, scadenza e certificato.

    ![Servizi cloud per Desktop remoto](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Tutte le istanze del ruolo verranno riavviate la prima volta che si abilita Desktop remoto e si seleziona **OK** (segno di spunta). Per evitare un riavvio, è necessario che nel ruolo sia installato il certificato usato per crittografare la password. Per evitare un riavvio, [caricare un certificato per il servizio cloud](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) e quindi tornare alla finestra di dialogo.

4. In **Ruolo** selezionare il ruolo da aggiornare oppure selezionare **Tutti** per tutti i ruoli.

5. Al termine degli aggiornamenti della configurazione, selezionare **Salva**. Ci vorranno alcuni minuti affinché le istanze del ruolo siano pronte a ricevere le connessioni.

## <a name="remote-into-role-instances"></a>Accedere in remoto alle istanze del ruolo

Dopo aver abilitato Desktop remoto nei ruoli, è possibile avviare una connessione direttamente dal portale di Azure:

1. Fare clic su **Istanze** per aprire l'impostazione **Istanze**.
2. Selezionare un'istanza del ruolo per cui è configurato Desktop remoto.
3. Fare clic su **Connetti** per scaricare un file RDP per l'istanza del ruolo.

    ![Immagine di desktop remoto dei servizi cloud](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Fare clic su **Apri** e quindi su **Connetti** per avviare la connessione Desktop remoto.

>[!NOTE]
> Se il servizio cloud si trova dietro un gruppo di sicurezza di rete, potrebbe essere necessario creare regole che consentano il traffico sulle porte **3389** e **20000**.  Desktop remoto usa la porta **3389**.  Dato che alle istanze del servizio cloud viene applicato il bilanciamento del carico, non è possibile controllare direttamente a quale istanza connettersi.  Gli agenti *RemoteForwarder* e *RemoteAccess* gestiscono il traffico RDP e consentono al client di inviare un cookie RDP e specificare una singola istanza a cui connettersi.  Gli agenti *RemoteForwarder* e *RemoteAccess* richiedono che la porta **20000***, che potrebbe essere bloccata in presenza di un gruppo di sicurezza di rete, sia aperta.

## <a name="additional-resources"></a>Risorse aggiuntive

[Come configurare i servizi cloud](cloud-services-how-to-configure-portal.md)
