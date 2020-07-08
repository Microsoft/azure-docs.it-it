---
title: Monitorare applicazioni e servizi di Azure con Grafana
description: Indirizzare dati di Monitoraggio di Azure e Application Insights i modo da poterli vedere in Grafana.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77672209"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorare i servizi di Azure in Grafana
Ora è possibile monitorare i servizi e le applicazioni di Azure da [Grafana](https://grafana.com/) usando il [plug-in dell'origine dati di Monitoraggio di Azure](https://grafana.com/plugins/grafana-azure-monitor-datasource). Il plug-in raccoglie i dati sulle prestazioni dell'applicazione ottenuti da Monitoraggio di Azure, tra cui diversi log e metriche. È quindi possibile visualizzare questi dati nel dashboard di Grafana.

Usare la procedura seguente per configurare un server Grafana e creare dashboard per le metriche e i log da Monitoraggio di Azure.

## <a name="set-up-a-grafana-server"></a>Configurare un server Grafana

### <a name="set-up-grafana-locally"></a>Configurare Grafana in locale
Per configurare un server Grafana locale, [scaricare e installare Grafana nell'ambiente locale](https://grafana.com/grafana/download). Per usare l'integrazione di monitoraggio di Azure per il plug-in, installare Grafana versione 5,3 o successiva.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Configurare Grafana in Azure tramite Azure Marketplace
1. Passare ad Azure Marketplace e selezionare Grafana di Grafana Labs.

2. Compilare i nomi e i dettagli. Creare un nuovo gruppo di risorse. Tenere traccia dei valori scelti per il nome utente della VM, la password della VM e la password di amministratore del server Grafana.  

3. Scegliere la dimensione della VM e un account di archiviazione.

4. Scegliere le impostazioni di configurazione della rete.

5. Visualizzare il riepilogo e selezionare **Crea** dopo aver accettato le condizioni di utilizzo.

6. Al termine della distribuzione selezionare **Vai al gruppo di risorse**. Viene visualizzato un elenco di risorse appena create.

    ![Oggetti gruppo di risorse di Grafana](media/grafana-plugin/grafana1.png)

    Se si seleziona il gruppo di sicurezza di rete (*grafana-nsg* in questo caso), è possibile vedere che la porta 3000 viene usata per accedere al server Grafana.

7. Ottenere l'indirizzo IP pubblico del server Grafana tornando all'elenco di risorse e selezionando **Indirizzo IP pubblico**.

## <a name="sign-in-to-grafana"></a>Accedere a Grafana

1. Usando l'indirizzo IP del server, aprire la pagina di accesso in *http:// \<IP address\> : 3000* o * \<DNSName> \: 3000* nel browser. Anche se la porta predefinita è la 3000, tenere presente che potrebbe essere stata selezionata una porta diversa durante la configurazione. Compare una pagina di accesso per il server Grafana creato.

    ![Schermata di accesso di Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Accedere con il nome utente *amministratore* e la password di amministratore del server Grafana creata in precedenza. Se si usa una configurazione locale, la password predefinita sarà *admin* e verrà richiesto di cambiarla al primo accesso.

## <a name="configure-data-source-plugin"></a>Configurare il plug-in di origine dati

Una volta eseguito l'accesso, si dovrebbe vedere che il plug-di origine dati di Monitoraggio di Azure è già incluso.

![Grafana include il plug-in Monitoraggio di Azure](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Selezionare **Add data source** (Aggiungi origine dati) per aggiungere e configurare l'origine dati Monitoraggio di Azure.

2. Scegliere un nome per l'origine dati e selezionare **Monitoraggio di Azure** come tipo nell'elenco a discesa.

3. Creare un'entità servizio: Grafana usa un'entità servizio di Azure Active Directory per connettersi alle API di Monitoraggio di Azure e raccogliere i dati. È necessario creare un'entità servizio o usarne una esistente per gestire l'accesso alle risorse di Azure.
    * Per creare un'entità servizio, seguire [queste istruzioni](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Copiare e salvare l'ID tenant (ID directory), l'ID client (ID applicazione) e il segreto client (valore della chiave dell'applicazione).
    * Vedere [Assegnare l'applicazione al ruolo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) per assegnare il ruolo Lettore all'applicazione Azure Active Directory per la sottoscrizione, il gruppo di risorse o la risorsa che si vuole monitorare. 
    L'API Log Analytics richiede il [ruolo Lettore di Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), che include le autorizzazioni del ruolo Lettore e le aggiunge.

4. Fornire i dettagli della connessione alle API da usare. È possibile connettersi a tutte o ad alcune di esse. 
    * Se ci si connette a metriche e log in monitoraggio di Azure, è possibile riutilizzare le stesse credenziali selezionando gli **stessi dettagli dell'API di monitoraggio di Azure**.
    * Quando si configura il plug-in, è possibile indicare quale cloud di Azure (pubblico, Azure US Gov, Azure Germania o Azure Cina) deve essere monitorato dal plug-in.
    * Se si usa Application Insights, è possibile anche includere l'API di Application Insights e l'ID applicazione per raccogliere le metriche basate su Application Insights. Per altre informazioni, vedere [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (Ottenere la chiave API e l'ID applicazione).

        > [!NOTE]
        > Alcuni campi dell'origine dati sono denominati in modo diverso rispetto alle relative impostazioni di Azure correlate:
        > * Tenant ID (ID tenant) è l'ID Azure Directory
        > * Client ID (ID client) è l'ID applicazione di Azure Active Directory
        > * Client Secret (Segreto client) è il valore della chiave dell'applicazione di Azure Active Directory

5. Se si usa Application Insights, è possibile anche includere l'API di Application Insights e l'ID applicazione per raccogliere le metriche basate su Application Insights. Per altre informazioni, vedere [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (Ottenere la chiave API e l'ID applicazione).

6. Selezionare **Save** (Salva). Grafana testerà le credenziali per ogni API. Verrà visualizzato un messaggio simile al seguente.  
    ![Configurazione dell'origine dati Grafana approvata](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Creare un dashboard di Grafana

1. Passare alla home page di Grafana e selezionare **New Dashboard** (Nuovo dashboard).

2. Nel nuovo dashboard selezionare il **Grafico**. È possibile provare altre opzioni di creazione di grafici, ma questo articolo usa *Grafico* come esempio.

3. Un grafico vuoto viene visualizzato nel dashboard. Fare clic sul titolo del pannello e selezionare **Modifica** per immettere i dettagli dei dati che si desidera tracciare in questo grafico.
    ![Nuovo grafico di Grafana](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selezionare l'origine dati di Monitoraggio di Azure configurata.
   * Raccolta di metriche di Monitoraggio di Azure: selezionare **Monitoraggio di Azure** nell'elenco a discesa Service (Servizio). Viene visualizzato un elenco di selettori, in cui è possibile selezionare le risorse e la metrica da monitorare in questo grafico. Per raccogliere metriche da una macchina virtuale, usare lo spazio dei nomi **Microsoft.Compute/VirtualMachines**. Dopo aver selezionato le macchine virtuali e le metriche, è possibile iniziare a visualizzare i dati nel dashboard.
     ![Configurazione del grafico di Grafana per Monitoraggio di Azure](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Raccolta dei dati del log di monitoraggio di Azure: selezionare **log Analytics di Azure** nell'elenco a discesa servizio. Selezionare l'area di lavoro di cui si vuole eseguire una query e impostare il testo della query. È possibile copiare qui qualsiasi query di log già disponibile o crearne una nuova. Mentre si digita la query, IntelliSense visualizzerà e suggerirà le opzioni di completamento automatico. Selezionare il tipo di visualizzazione, **Time series (Serie temporali) ** **Table** (Tabella) ed eseguire la query.
    
     > [!NOTE]
     >
     > La query predefinita fornita con il plug-in usa due macro: "$__timeFilter() e $__interval. 
     > Queste macro consentono a Grafana di calcolare dinamicamente gli intervalli di tempo, quando si fa zoom avanti su parte di un grafico. È possibile rimuovere queste macro e usare un filtro temporale standard, ad esempio *TimeGenerated > ago(1h)*, ma in tal caso il grafico non supporterà la funzionalità di zoom avanti.
    
     ![Configurazione del grafico di Grafana per Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Di seguito è mostrato un semplice dashboard con due grafici. Quello a sinistra mostra la percentuale di CPU di due VM. Il grafico a destra mostra le transazioni in un account Archiviazione di Azure ripartite per tipo di transazione API.
    ![Esempio di Grafana con due grafici](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Facoltativo: Monitorare le metriche personalizzate nello stesso server Grafana

È anche possibile installare Telegraf e InfluxDB per raccogliere e monitorare metriche personalizzate e basate su agenti nella stessa istanza di Grafana. Esistono molti plug-in di origine dati che possono essere usati per riunire queste metriche in un dashboard.

È anche possibile riutilizzare questa configurazione per includere le metriche del server Prometheus. Usare il plug-in di origine dati di Prometheus nella raccolta di plug-in di Grafana.

Di seguito sono riportati utili articoli di riferimento su come usare Telegraf, InfluxDB, Prometheus e Docker
 - [Come monitorare le metriche di sistema con lo stack TICK in Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Una soluzione di monitoraggio per host, contenitori e servizi in contenitori Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Di seguito è riportata un'immagine di un dashboard di Grafana completo che include metriche di monitoraggio di Azure e Application Insights.
![Metriche di Grafana di esempio](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Funzionalità avanzate di Grafana

### <a name="variables"></a>variables
Alcuni valori della query possono essere selezionati tramite gli elenchi a discesa dell'interfaccia utente e aggiornati nella query. Considerare la query seguente come esempio:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

È possibile configurare una variabile che elencherà tutti i valori disponibili di **Solution** (Soluzione) e quindi aggiornerà la query in modo che la usi.
Per creare una nuova variabile, fare clic sul pulsante Settings (Impostazioni) del dashboard nell'area in alto a destra, selezionare**Variables** (Variabili) e quindi **New** (Nuova).
Nella pagina delle variabili definire l'origine dati e query da eseguire per ottenere l'elenco di valori.
![Configurare una variabile in Grafana](./media/grafana-plugin/grafana-configure-variable-dark.png)

Dopo aver creato la query, modificarla per usare i valori selezionati e i grafici risponderanno di conseguenza:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Usare le variabili in Grafana](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Creare playlist di dashboard

Una delle molte funzionalità utili di Grafana è la playlist di dashboard. È possibile creare più dashboard e aggiungerli a una playlist configurando un intervallo per ogni dashboard da visualizzare. Selezionare **Play** per vedere i dashboard scorrere in sequenza. È consigliabile visualizzarli su un monitor a parete di grandi dimensioni per fornire un tabellone di stato per il gruppo.

![Esempio di playlist di Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se è stato configurato un ambiente Grafana in Azure, è previsto un addebito quando le macchine virtuali sono in esecuzione indipendentemente dal fatto che le si usi o meno. Per evitare di dover sostenere costi aggiuntivi, eliminare il gruppo di risorse creato in questo articolo.

1. Scegliere **Gruppi di risorse** nel menu a sinistra del portale di Azure e quindi fare clic su **Grafana**.
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare **Grafana** nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica delle metriche di Monitoraggio di Azure](data-platform.md)

