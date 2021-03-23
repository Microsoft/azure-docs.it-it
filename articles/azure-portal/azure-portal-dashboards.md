---
title: Creare un dashboard nella portale di Azure
description: Questo articolo descrive come creare e personalizzare un dashboard nel portale di Azure.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: fa7f1813d86571b568d23d64cab5705f8a117faa
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774612"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Creare un dashboard nella portale di Azure

I dashboard sono una visualizzazione mirata e organizzata delle risorse cloud nella portale di Azure. Usare i dashboard come area di lavoro in cui è possibile monitorare le risorse e avviare rapidamente le attività per le operazioni quotidiane. Creare dashboard personalizzati basati su progetti, attività o ruoli utente, ad esempio.

Il portale di Azure fornisce un dashboard predefinito come punto di partenza. È possibile modificare il dashboard predefinito e creare e personalizzare ulteriori dashboard. Questo articolo descrive come creare un nuovo dashboard e personalizzarlo. Per informazioni sulla condivisione dei dashboard, vedere [condividere i dashboard di Azure con il controllo degli accessi in base al ruolo di Azure](azure-portal-dashboard-share-access.md).

## <a name="create-a-new-dashboard"></a>Creare un nuovo dashboard

In questo esempio si crea un nuovo dashboard privato e si assegna un nome. Per iniziare, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Scegliere **Dashboard** dal menu portale di Azure. La visualizzazione predefinita potrebbe essere già impostata sul dashboard.

    ![Aprire il dashboard](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selezionare **nuovo dashboard** , quindi **Dashboard vuoto**.

    ![Screenshot del nuovo dashboard](./media/azure-portal-dashboards/create-new-dashboard.png)

    Questa azione consente di aprire la **raccolta di sezioni**, da cui è possibile selezionare i riquadri e una griglia vuota in cui verranno organizzati i riquadri.

1. Selezionare il testo del **Dashboard** nell'etichetta del dashboard e immettere un nome che consentirà di identificare facilmente il dashboard personalizzato.

    ![Screenshot della raccolta dei riquadri e della griglia vuota](./media/azure-portal-dashboards/dashboard-name.png)

1. Nell'intestazione di pagina selezionare **fine personalizzazione** per uscire dalla modalità di modifica e quindi selezionare **Salva**.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Screenshot del processo di salvataggio del dashboard":::

Nella visualizzazione Dashboard è ora visualizzato il nuovo dashboard. Selezionare la freccia accanto al nome del dashboard per visualizzare i dashboard disponibili. L'elenco potrebbe includere i dashboard creati e condivisi da altri utenti.

## <a name="edit-a-dashboard"></a>Modificare un dashboard

A questo punto, modificare il dashboard per aggiungere, ridimensionare e disporre i riquadri che rappresentano le risorse di Azure.

### <a name="add-tiles-from-the-tile-gallery"></a>Aggiungere riquadri dalla raccolta di sezioni

Per aggiungere riquadri a un dashboard, attenersi alla procedura seguente:

1. Selezionare ![ modifica icona ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modifica** dall'intestazione di pagina.

    ![Screenshot dell'evidenziazione della modifica del dashboard](./media/azure-portal-dashboards/dashboard-edit.png)

1. Esplorare la **raccolta di riquadri** o usare il campo di ricerca per trovare il riquadro desiderato.

1. Selezionare **Aggiungi** per aggiungere il riquadro al dashboard con le dimensioni e il percorso predefiniti. In alternativa, trascinare il riquadro nella griglia e posizionarlo nel percorso desiderato. Aggiungere tutti i riquadri desiderati, ma di seguito sono riportate alcune idee:

    - Aggiungere **tutte le risorse** per visualizzare le risorse già create.

    - Se si lavora con più di un'organizzazione, aggiungere il riquadro **identità organizzazione** al dashboard per visualizzare chiaramente l'organizzazione a cui appartengono le risorse.

1. Nell'intestazione di pagina selezionare **Salva**.

### <a name="add-tiles-from-a-resource-page"></a>Aggiungere riquadri da una pagina delle risorse

Esiste un modo alternativo per aggiungere riquadri al dashboard. Molte pagine delle risorse includono un'icona a puntina da disegno sulla barra dei comandi. Se si seleziona l'icona, un riquadro che rappresenta la pagina di origine viene aggiunto al dashboard attualmente attivo. 

![Screenshot della barra dei comandi della pagina con icona del PIN](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Ridimensionare o ridisporre i riquadri

Per modificare le dimensioni di un riquadro o per ridisporre i riquadri in un dashboard, attenersi alla procedura seguente:

1. Selezionare ![ modifica icona ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modifica** dall'intestazione di pagina.

1. Selezionare il menu di scelta rapida nell'angolo superiore destro di un riquadro. Quindi, scegliere le dimensioni del riquadro. I riquadri che supportano qualsiasi dimensione includono anche un "handle" nell'angolo in basso a destra che consente di trascinare il riquadro fino alle dimensioni desiderate.

    ![Screenshot del dashboard con il menu delle dimensioni del riquadro aperto](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Selezionare un riquadro e trascinarlo in una nuova posizione sulla griglia per disporre il dashboard.

### <a name="additional-tile-configuration"></a>Configurazione aggiuntiva del riquadro

Per alcuni riquadri potrebbe essere necessaria una configurazione maggiore per visualizzare le informazioni desiderate. Ad esempio, è necessario configurare il riquadro **grafico metriche** per visualizzare una metrica da monitoraggio di Azure. È anche possibile personalizzare i dati del riquadro per sostituire le impostazioni di ora predefinite del dashboard.

Tutti i riquadri che devono essere impostati visualizzano un banner fino a quando non si Personalizza il riquadro. Per il **grafico delle metriche**, il banner viene **modificato nelle metriche**. Per personalizzare il riquadro:

1. Nell'intestazione di pagina selezionare **Salva** per uscire dalla modalità di modifica.

1. Selezionare il banner, quindi eseguire la configurazione richiesta.

    ![Screenshot del riquadro che richiede la configurazione](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Un riquadro Markdown consente di visualizzare contenuto statico personalizzato nel dashboard. Potrebbe trattarsi di istruzioni di base, un'immagine, un set di collegamenti ipertestuali o persino informazioni di contatto. Per altre informazioni sull'uso di un riquadro Markdown, vedere [usare un riquadro Markdown nei dashboard di Azure per visualizzare il contenuto personalizzato](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Personalizzare i dati del riquadro

I dati nel dashboard mostrano automaticamente le attività per le ultime 24 ore. Per visualizzare un intervallo di tempo diverso solo per questo riquadro, attenersi alla seguente procedura:

1. Selezionare **personalizzare i dati del riquadro** dal menu di scelta rapida o dal ![ filtro dell'icona del filtro ](./media/azure-portal-dashboards/dashboard-filter.png) nell'angolo superiore sinistro del riquadro.

    ![Screenshot del menu di scelta rapida del riquadro](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Selezionare la casella di controllo per **sostituire le impostazioni dell'ora del dashboard a livello di riquadro**.

    ![Screenshot della finestra di dialogo per configurare le impostazioni temporali dei riquadri](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Consente di scegliere l'intervallo di tempo da visualizzare per questo riquadro. È possibile scegliere tra 30 minuti negli ultimi 30 giorni o definire un intervallo personalizzato.

1. Scegliere la granularità temporale da visualizzare. È possibile visualizzare gli incrementi di un minuto da un mese all'altro.

1. Selezionare **Applica**.

## <a name="delete-a-tile"></a>Eliminare un riquadro

Per rimuovere un riquadro da un dashboard, attenersi alla procedura seguente:

* Selezionare il menu di scelta rapida nell'angolo superiore destro del riquadro, quindi selezionare **Rimuovi dal dashboard**. Oppure

* Selezionare ![ modifica icona ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **modifica** per attivare la modalità di personalizzazione. Passare il puntatore del mouse nell'angolo superiore destro del riquadro, quindi selezionare l' ![ icona Elimina icona ](./media/azure-portal-dashboards/dashboard-delete-icon.png) Elimina per rimuovere il riquadro dal dashboard.

   ![Screenshot che illustra come rimuovere il riquadro dal dashboard](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Clonare un dashboard

Per usare un dashboard esistente come modello per un nuovo dashboard, seguire questa procedura:

1. Assicurarsi che la visualizzazione Dashboard mostri il dashboard che si vuole copiare.

1. Nell'intestazione di pagina selezionare ![ Clone Icon ](./media/azure-portal-dashboards/dashboard-clone.png) **Clone**.

1. Una copia del dashboard denominata **clone del nome del** *Dashboard* viene aperta in modalità di modifica. Usare i passaggi precedenti di questo articolo per rinominare e personalizzare il dashboard.

## <a name="publish-and-share-a-dashboard"></a>Pubblicare e condividere un dashboard

Quando si crea un dashboard, è privato per impostazione predefinita, il che significa che l'unico utente può visualizzarlo. Per rendere disponibili i dashboard ad altri utenti, è possibile pubblicarli e condividerli. Per altre informazioni, vedere [Condividere i dashboard di Azure tramite controllo degli accessi in base al ruolo](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Aprire un dashboard condiviso

Per trovare e aprire un dashboard condiviso, seguire questa procedura:

1. Selezionare la freccia accanto al nome del dashboard.

1. Selezionare dall'elenco visualizzato dei dashboard. Se il dashboard che si vuole aprire non è elencato:

    1. Selezionare **Sfoglia tutti i dashboard**.

        ![Screenshot del menu di selezione del dashboard](./media/azure-portal-dashboards/dashboard-browse.png)

    1. Nel campo **tipo** selezionare **Dashboard condivisi**.

        ![Screenshot del menu di selezione di tutti i dashboard](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selezionare una o più sottoscrizioni. È anche possibile immettere il testo per filtrare i dashboard in base al nome.

    1. Selezionare un dashboard dall'elenco dei dashboard condivisi.

## <a name="delete-a-dashboard"></a>Eliminare un dashboard

Per eliminare definitivamente un dashboard privato o condiviso, attenersi alla procedura seguente:

1. Selezionare il dashboard che si vuole eliminare dall'elenco accanto al nome del dashboard.

1. Selezionare ![ Delete Icon ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Delete** dall'intestazione di pagina.

1. Per un dashboard privato, fare clic su **OK** nella finestra di dialogo di conferma per rimuovere il dashboard. Per un dashboard condiviso, nella finestra di dialogo di conferma selezionare la casella di controllo per confermare che il dashboard pubblicato non sarà più visualizzabile da altri. Quindi selezionare **OK**.

    ![Screenshot della conferma dell'eliminazione](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Ripristinare un dashboard eliminato

Se ci si trova nel cloud di Azure globale e si elimina un dashboard _pubblicato_ nella portale di Azure, è possibile ripristinare il dashboard entro 14 giorni dall'eliminazione. Per informazioni, vedere [ripristinare un dashboard eliminato nel portale di Azure](recover-shared-deleted-dashboard.md).

## <a name="next-steps"></a>Passaggi successivi

* [Condividere i dashboard di Azure tramite il controllo degli accessi in base al ruolo di Azure](azure-portal-dashboard-share-access.md)
* [Creare i dashboard di Azure a livello di codice](azure-portal-dashboards-create-programmatically.md)
