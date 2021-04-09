---
title: Configurare gli avvisi delle metriche-portale di Azure database di Azure per MariaDB
description: Questo articolo descrive come configurare avvisi per le metriche per Database di Azure per MariaDB e accedere a tali avvisi nel portale di Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 689e830d2486420be8d551d1f88dccba8d982428
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593073"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Usare il portale di Azure per configurare avvisi per le metriche per Database di Azure per MariaDB

Questo articolo illustra come configurare gli avvisi di Database di Azure per MariaDB con il portale di Azure. È possibile ricevere un avviso in base alle metriche di monitoraggio per i servizi di Azure.

L'avviso viene attivato quando il valore di una metrica specifica supera una soglia assegnata. L'avviso viene attivato sia quando la condizione viene inizialmente soddisfatta che successivamente, quando tale condizione non è più soddisfatta.

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Azure portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Interfaccia della riga di comando di Azure](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [API REST di Monitoraggio di Azure](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Creare una regola di avviso per una metrica
1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per MariaDB da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Avvisi** come illustrato di seguito:

   ![Selezionare le regole di avviso](./media/howto-alert-metric/2-alert-rules.png)

3. Selezionare **Aggiungi avviso per la metrica** (icona +).

4. Verrà aperta la pagina **Crea regola** come illustrato di seguito. Specificare le informazioni necessarie:

   ![Form Aggiungi avviso per la metrica](./media/howto-alert-metric/4-add-rule-form.png)

5. Nella sezione **Condizione** selezionare **Aggiungi condizione**.

6. Selezionare una metrica dall'elenco dei segnali per i quali ricevere un avviso. In questo esempio selezionare "Storage percent".
   
   ![Selezionare una metrica](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Configurare la logica degli avvisi specificando i valori nei campi **Condizione** (ad esempio "Maggiore di"), **Soglia** (ad esempio l'85%), **Aggregazione temporale**, **Periodo** di tempo in cui è necessario soddisfare la regola della metrica prima che venga attivato l'avviso (ad esempio "Over the Last 30 minutes" e **Frequency**.
   
   Al termine, selezionare **Chiudi**.

   ![Selezionare la metrica 2](./media/howto-alert-metric/7-set-threshold-time.png)

8. Nella sezione **Gruppi di azioni** selezionare **Crea nuovo** per creare un nuovo gruppo di azioni per ricevere le notifiche relative all'avviso.

9. Compilare il modulo "Aggiungi gruppo di azioni" specificando nome, nome breve, sottoscrizione e gruppo di risorse.

10. Configurare un tipo di azione **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).
    
    Scegliere "Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager" per consentire a proprietari di sottoscrizioni, collaboratori e lettori di ricevere le notifiche.
   
    Facoltativamente, specificare un URI valido nel campo **Webhook** se lo si vuole chiamare quando viene attivato l'avviso.

    Al termine, fare clic su **OK**.

    ![Gruppo di azioni](./media/howto-alert-metric/10-action-group-type.png)

11. Specificare un nome per la regola di avviso, una descrizione e il livello di gravità.

    ![Gruppo di azioni 2](./media/howto-alert-metric/11-name-description-severity.png) 

12. Selezionare **Crea regola di avviso** per creare l'avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="manage-your-alerts"></a>Gestire gli avvisi
Dopo aver creato un avviso, è possibile selezionarlo ed eseguire queste azioni:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/alerts/alerts-webhooks.md).
* Leggere una [panoramica della raccolta di metriche](../azure-monitor/data-platform.md) per verificare che il servizio sia disponibile e reattivo.