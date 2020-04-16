---
title: Onboarding di computer Windows in Centro sicurezza di Azure
description: Questa guida di avvio rapido illustra come effettuare il provisioning dell'agente di Log Analytics in un computer Windows.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 2840b6636c2b511ab57a8bae8adf411f4d8a27d2
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435932"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Guida di avvio rapido: Onboarding di computer Windows in Centro sicurezza di Azure
Dopo aver caricato le sottoscrizioni di Azure è possibile abilitare il Centro sicurezza per le risorse in esecuzione all'esterno di Azure, per esempio in locale o in altri cloud, effettuando il provisioning dell'agente di Log Analytics.

Questa guida di avvio rapido illustra come installare l'agente di Log Analytics in un computer Windows.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Prima di iniziare questa guida di avvio rapido, è necessario essere nel piano tariffario Standard del Centro sicurezza. Vedere [Guida di avvio rapido per il Centro sicurezza di Azure](security-center-get-started.md) per istruzioni sull'aggiornamento. È possibile provare il livello Standard del Centro sicurezza gratuitamente. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-windows-computer"></a>Aggiunta di un nuovo computer Windows

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).
2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

   ![Panoramica del Centro sicurezza di Azure][2]

3. Scegliere **Introduzione** dal menu principale del Centro sicurezza.
4. Selezionare la scheda **Introduzione**.

   ![Introduzione][3]

5. Fare clic su **Configura** in **Aggiungi nuovi computer non Azure**. Viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare l'area di lavoro che si desidera usare.

    ![Aggiungere computer non Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Il pannello **Agente diretto** si apre con un collegamento per scaricare un agente Windows e le chiavi per l'ID dell'area di lavoro da usare nella configurazione dell'agente.

6. Selezionare il collegamento **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.

7. A destra di **ID area di lavoro** selezionare l'icona di copia e incollare l'ID nel Blocco note.

8. A destra di **Chiave primaria** selezionare l'icona di copia e incollare la chiave nel Blocco note.

## <a name="install-the-agent"></a>Installare l'agente
Ora è necessario installare il file scaricato nel computer di destinazione.

1. Copiare il file nel computer di destinazione ed eseguire l'installazione.
2. Nella pagina di **benvenuto** selezionare **Avanti**.
3. Nella pagina **Condizioni di licenza** leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina **Cartella di destinazione** modificare o mantenere la cartella di installazione predefinita e quindi scegliere **Avanti**.
5. Nella pagina **Opzioni di installazione dell'agente** scegliere di connettere l'agente ad Azure Log Analytics e quindi selezionare **Avanti**.
6. Nella pagina **Azure Log Analytics** incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note nella procedura precedente.
7. Se il computer deve fare riferimento a un'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure US Gov** nell'elenco a discesa **Cloud di Azure**. Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, scegliere **Avanzate** e specificare l'URL e il numero di porta del server proxy.
8. Scegliere **Avanti** dopo aver specificato le impostazioni di configurazione necessarie.

   ![Installare l'agente][5]

9. Nella pagina **Pronto per l'installazione** rivedere le scelte effettuate e quindi scegliere **Installa**.
10. Nella pagina **Configurazione completata** scegliere **Fine**

Al termine, l'**agente di Log Analytics** verrà visualizzato nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso.

Per altre informazioni sull'installazione e configurazione dell'agente, vedere [Connettere computer Windows al servizio Log Analytics in Azure](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Ora è possibile monitorare le macchine virtuali di Azure e i computer non di Azure in un'unica posizione. In **Calcolo** si dispone di una panoramica di tutte le VM e di tutti i computer insieme alle raccomandazioni. Ogni colonna rappresenta un set di raccomandazioni. Il colore rappresenta lo stato corrente della sicurezza della macchina virtuale o del computer per tale raccomandazione. Il Centro sicurezza segnala anche qualsiasi rilevamento per questi computer negli avvisi di sicurezza.

  ![Pannello Calcolo][6]

Esistono due tipi di icone rappresentate sul pannello **Calcolo**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computer non Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Macchina virtuale di Azure

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessario, è possibile rimuovere l'agente dal computer Windows.

Per rimuovere l'agente:

1. Aprire il **Pannello di controllo**.
2. Aprire **Programmi e funzionalità**.
3. In **Programmi e funzionalità** selezionare **Agente di Log Analytics** e fare clic su **Disinstalla**.

## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido è stato effettuato il provisioning dell'agente di Log Analytics in un computer Windows. Per altre informazioni su come usare il Centro sicurezza, continuare l'esercitazione per configurare i criteri di sicurezza e valutare la sicurezza delle risorse.

> [!div class="nextstepaction"]
> [Esercitazione: Definire e valutare i criteri di sicurezza](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
