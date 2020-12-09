---
title: Note sulla versione per il Centro sicurezza di Azure
description: Descrizione delle novità e delle modifiche apportate al Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2020
ms.author: memildin
ms.openlocfilehash: 0dbd208cea64a3b2dc22f7603f654127e5b46294
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511764"
---
# <a name="whats-new-in-azure-security-center"></a>Novità del Centro sicurezza di Azure

Il Centro sicurezza è in fase di sviluppo attivo e riceve regolarmente miglioramenti. Per stare al passo con gli sviluppi più recenti, questa pagina fornisce informazioni su funzionalità nuove e deprecate, oltre che sulle correzioni dei bug.

Questa pagina viene aggiornata regolarmente, quindi è consigliabile consultarla spesso. 

Per informazioni sulle modifiche *pianificate* che saranno presto disponibili nel Centro sicurezza, vedere [Modifiche importanti che interesseranno il Centro sicurezza di Azure](upcoming-changes.md). 

> [!TIP]
> Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarle in [Archive for What's new in Azure Security Center](release-notes-archive.md) (Archivio per le novità nel Centro sicurezza di Azure).


## <a name="december-2020"></a>Dicembre 2020

Gli aggiornamenti di dicembre includono:

- [Azure Defender per server SQL nei computer è disponibile a livello generale](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Il supporto di Azure Defender per SQL per i pool SQL dedicati di Azure Synapse Analytics è disponibile a livello generale](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)

### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender per server SQL nei computer è disponibile a livello generale

Il Centro sicurezza di Azure prevede due piani di Azure Defender per SQL Server:

- **Azure Defender per i server di database SQL di Azure**: protegge i sistemi SQL Server nativi di Azure 
- **Azure Defender per i server SQL nei computer**: estende le stesse protezioni ai server SQL in ambienti ibridi, multicloud e locali

Con questo annuncio, **Azure Defender per SQL** ora protegge i database e i relativi dati ovunque siano collocati.

Azure Defender per SQL include funzionalità di valutazione delle vulnerabilità. Lo strumento di valutazione delle vulnerabilità offre le seguenti funzionalità avanzate:

- **Configurazione di base** (novità) per affinare in modo intelligente i risultati delle analisi di vulnerabilità limitandoli a quelli che potrebbero rappresentare problemi concreti per la sicurezza. Dopo aver stabilito lo stato di sicurezza di base, lo strumento di valutazione delle vulnerabilità segnala solo le deviazioni da tale stato. I risultati corrispondenti alla base non vengono considerati problemi nelle analisi successive. In questo modo gli analisti possono dedicare la loro attenzione alle questioni importanti.
- **Informazioni di benchmark dettagliate** per aiutare a *interpretare* i risultati individuati e al motivo per cui riguardano le risorse.
- **Script di correzione** per mitigare i rischi identificati.

Vedere altre informazioni su [Azure Defender per SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Il supporto di Azure Defender per SQL per i pool SQL dedicati di Azure Synapse Analytics è disponibile a livello generale

Azure Synapse Analytics (in precedenza SQL Data Warehouse) è un servizio di analisi che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. I pool SQL dedicati rappresentano le funzionalità di data warehousing aziendali di Azure Synapse. Per altre informazioni, vedere [Che cos'è Azure Synapse Analytics (in precedenza SQL Data Warehouse)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Azure Defender per SQL protegge i pool SQL dedicati con:

- **Advanced Threat Protection** per rilevare minacce e attacchi 
- **Funzionalità di valutazione delle vulnerabilità** per identificare e correggere le configurazioni di sicurezza errate

Il supporto di Azure Defender per SQL per i pool SQL di Azure Synapse Analytics viene aggiunto automaticamente al bundle di database SQL di Azure nel Centro sicurezza di Azure. Nella pagina dell'area di lavoro di Synapse nel portale di Azure sarà disponibile una nuova scheda "Azure Defender per SQL".

Vedere altre informazioni su [Azure Defender per SQL](defender-for-sql-introduction.md).

## <a name="november-2020"></a>Novembre 2020

Gli aggiornamenti del mese di novembre includono quanto segue:

- [Aggiunte 29 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Aggiunta di NIST SP 800 171 R2 al dashboard di conformità alle normative del Centro sicurezza](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [L'elenco di raccomandazioni ora include i filtri](#recommendations-list-now-includes-filters)
- [Esperienza di provisioning automatico migliorata e ampliata](#auto-provisioning-experience-improved-and-expanded)
- [Punteggio di sicurezza ora disponibile nell'esportazione continua (anteprima)](#secure-score-is-now-available-in-continuous-export-preview)
- [Aggiunta di raccomandazioni secondarie alla raccomandazione "Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali"](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [La pagina di gestione dei criteri nel portale di Azure ora mostra lo stato delle assegnazioni di criteri predefiniti](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Aggiunte 29 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark

Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md).

Le seguenti 29 nuove raccomandazioni di anteprima verranno aggiunte al Centro sicurezza per aumentare la copertura del benchmark.

Le raccomandazioni in anteprima non contrassegnano una risorsa come non integra e non sono incluse nei calcoli del punteggio di sicurezza. Correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima. Per altre informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

| Controllo di sicurezza                     | Nuove raccomandazioni                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Crittografa i dati in transito              | - Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL<br>- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL<br>- TLS deve essere aggiornato all'ultima versione per l'app per le API<br>- TLS deve essere aggiornato all'ultima versione per l'app per le funzioni<br>- TLS deve essere aggiornato all'ultima versione per l'app Web<br>- L'app per le API deve usare FTPS<br>- L'app per le funzioni deve usare FTPS<br>- L'app Web deve usare FTPS                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gestire l'accesso e le autorizzazioni        | - Le app Web devono richiedere un certificato SSL per tutte le richieste in ingresso<br>- Nell'app per le API è necessario usare un'identità gestita<br>- Nell'app per le funzioni è necessario usare un'identità gestita<br>- Nell'app Web è necessario usare un'identità gestita                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Limita l'accesso non autorizzato alla rete | - L'endpoint privato deve essere abilitato per i server PostgreSQL<br>- L'endpoint privato deve essere abilitato per i server MariaDB<br>- L'endpoint privato deve essere abilitato per i server MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Abilita il controllo e la registrazione          | - È necessario abilitare i log di diagnostica in Servizi app                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementa le procedure consigliate per la sicurezza    | - La soluzione Backup di Azure deve essere abilitata per le macchine virtuali<br>- Il backup con ridondanza geografica deve essere abilitato per Database di Azure per MariaDB<br>- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL<br>- Il backup con ridondanza geografica deve essere abilitato per Database di Azure per PostgreSQL<br>- PHP deve essere aggiornato all'ultima versione per l'app per le API<br>- PHP deve essere aggiornato all'ultima versione per l'app Web<br>- Java deve essere aggiornato all'ultima versione per l'app per le API<br>- Java deve essere aggiornato all'ultima versione per l'app per le funzioni<br>- Java deve essere aggiornato all'ultima versione per l'app Web<br>- Python deve essere aggiornato all'ultima versione per l'app per le API<br>- Python deve essere aggiornato all'ultima versione per l'app per le funzioni<br>- Python deve essere aggiornato all'ultima versione per l'app Web<br>- La conservazione dei controlli per i server SQL deve essere impostata su almeno 90 giorni |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Collegamenti correlati:

- [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Altre informazioni sulle app per le API di Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Altre informazioni sulle app per le funzioni di Azure](../azure-functions/functions-overview.md)
- [Altre informazioni sulle app Web di Azure](../app-service/overview.md)
- [Altre informazioni su Database di Azure per MariaDB](../mariadb/overview.md)
- [Altre informazioni su Database di Azure per MySQL](../mysql/overview.md)
- [Altre informazioni su Database di Azure per PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Aggiunta di NIST SP 800 171 R2 al dashboard di conformità alle normative del Centro sicurezza

Lo standard NIST SP 800-171 R2 è ora disponibile come iniziativa predefinita per l'uso nel dashboard di conformità alle normative del Centro sicurezza di Azure. I mapping per i controlli sono descritti in [Dettagli dell'iniziativa predefinita di conformità alle normative per NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Per applicare lo standard alle sottoscrizioni e monitorare continuamente lo stato di conformità, attenersi alle istruzioni riportate in [Personalizzazione del set di standard nel dashboard di conformità alle normative](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard NIST SP 800 171 R2 nel dashboard di conformità alle normative del Centro sicurezza":::

Per altre informazioni su questo standard di conformità, vedere [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>L'elenco di raccomandazioni ora include i filtri

È ora possibile filtrare l'elenco di raccomandazioni di sicurezza in base a una serie di criteri. Nell'esempio seguente l'elenco di raccomandazioni è stato filtrato per mostrare le raccomandazioni che:

- Sono **disponibili a livello generale** (ovvero non in anteprima)
- Riguardano gli **account di archiviazione**
- Supportano la **correzione rapida**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtri per l'elenco di raccomandazioni":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Esperienza di provisioning automatico migliorata e ampliata

La funzionalità di provisioning automatico consente di ridurre il sovraccarico di gestione installando le estensioni necessarie nelle VM di Azure nuove ed esistenti in modo che possano trarre vantaggio dalle protezioni del Centro sicurezza. 

Con la crescita del Centro sicurezza di Azure, sono state sviluppate più estensioni ed è ora possibile monitorare un elenco più lungo di tipi di risorsa. Gli strumenti di provisioning automatico sono stati ampliati per supportare estensioni e tipi di risorsa aggiuntivi sfruttando le funzionalità di Criteri di Azure.

È ora possibile configurare il provisioning automatico di:

- Agente di Log Analytics
- (Novità) Componente aggiuntivo Criteri di Azure per Kubernetes
- (Novità) Microsoft Dependency Agent

Per altre informazioni, vedere [Provisioning automatico di agenti ed estensioni del Centro sicurezza di Azure](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Punteggio di sicurezza ora disponibile nell'esportazione continua (anteprima)

Con l'esportazione continua del punteggio di sicurezza, è possibile trasmettere le modifiche del punteggio in tempo reale a Hub eventi di Azure o a un'area di lavoro Log Analytics. Usare questa funzionalità per:

- Tenere traccia del punteggio di sicurezza nel corso del tempo con report dinamici
- Esportare i dati sul punteggio di sicurezza in Azure Sentinel (o qualsiasi altro strumento SIEM)
- Integrare questi dati con qualsiasi processo che potrebbe già essere in uso per monitorare il punteggio di sicurezza nell'organizzazione

Per altre informazioni, vedere [Esportazione continua dei dati del Centro sicurezza](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>Aggiunta di raccomandazioni secondarie alla raccomandazione "Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali"

La raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali** è stata ottimizzata. La nuova versione include raccomandazioni secondarie per ogni aggiornamento mancante e include i miglioramenti seguenti:

- Un'esperienza riprogettata nelle pagine del Centro sicurezza di Azure del portale di Azure. La pagina di dettagli della raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali** include l'elenco di risultati, come illustrato di seguito. Quando si seleziona un singolo risultato, viene visualizzato il riquadro dei dettagli con un collegamento alle informazioni sulla correzione e un elenco delle risorse interessate.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Apertura di una delle raccomandazioni secondarie nell'esperienza del portale per la raccomandazione aggiornata":::

- Dati della raccomandazione arricchiti da Azure Resource Graph. Azure Resource Graph è un servizio di Azure progettato per offrire un'esplorazione efficiente delle risorse. È possibile usare Azure Resource Graph per eseguire query su larga scala su un determinato set di sottoscrizioni, in modo da regolamentare efficacemente l'ambiente. 

    Per il Centro sicurezza di Azure, è possibile usare Azure Resource Graph e il [linguaggio di query Kusto](/azure/data-explorer/kusto/query/) per eseguire query su un'ampia gamma di dati relativi alla postura di sicurezza.

    In precedenza, se si eseguivano query su questa raccomandazione in Azure Resource Graph, le uniche informazioni disponibili erano che era necessario apportare la correzione raccomandata in un computer. La query seguente della versione ottimizzata restituirà tutti gli aggiornamenti di sistema mancanti raggruppati in base al computer.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>La pagina di gestione dei criteri nel portale di Azure ora mostra lo stato delle assegnazioni di criteri predefiniti

È ora possibile verificare se alle sottoscrizioni sono assegnati o meno i criteri predefiniti del Centro sicurezza nella relativa pagina **criteri di sicurezza** del portale di Azure.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="La pagina di gestione dei criteri del Centro sicurezza di Azure mostra le assegnazioni di criteri predefiniti":::

## <a name="october-2020"></a>Ottobre 2020

Gli aggiornamenti del mese di ottobre includono quanto segue:
- [Valutazione delle vulnerabilità per computer locali e multicloud (anteprima)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Aggiunta una raccomandazione per Firewall di Azure (anteprima)](#azure-firewall-recommendation-added-preview)
- [Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes - Aggiornata la raccomandazione con una correzione rapida](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Il dashboard Conformità con le normative include ora un'opzione per rimuovere gli standard](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Rimossa la tabella Microsoft.Security/securityStatuses da Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Valutazione delle vulnerabilità per computer locali e multicloud (anteprima)

Lo strumento di analisi integrato per la valutazione delle vulnerabilità di [Azure Defender per i server](defender-for-servers-introduction.md) (con tecnologia Qualys) ora analizza i server con abilitazione di Azure Arc.

Quando Azure Arc viene abilitato in computer non di Azure, il Centro sicurezza offre la possibilità di distribuirvi lo strumento integrato di analisi delle vulnerabilità, manualmente e su larga scala.

Con questo aggiornamento, è possibile sfruttare la potenza di **Azure Defender per i server** per consolidare il programma di gestione delle vulnerabilità in tutti gli asset di Azure e non di Azure.

Funzionalità principali:

- Monitoraggio dello stato di provisioning dello strumento di analisi per la valutazione delle vulnerabilità nei computer Azure Arc
- Provisioning dell'agente di valutazione delle vulnerabilità nei computer Azure Arc Windows e Linux non protetti (manualmente e su larga scala)
- Ricezione e analisi delle vulnerabilità rilevate dagli agenti distribuiti (manualmente e su larga scala)
- Esperienza unificata per macchine virtuali di Azure e computer Azure Arc

[Altre informazioni sulla distribuzione dello strumento di analisi delle vulnerabilità integrato nei computer ibridi](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Altre informazioni sui server con abilitazione di Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Aggiunta una raccomandazione per Firewall di Azure (anteprima)

È stata aggiunta una nuova raccomandazione per proteggere tutte le reti virtuali con Firewall di Azure.

La raccomandazione **È consigliabile che le reti virtuali siano protette da Firewall di Azure** consiglia di limitare l'accesso alle reti virtuali ed evitare potenziali minacce tramite Firewall di Azure.

Altre informazioni su [Firewall di Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes - Aggiornata la raccomandazione con una correzione rapida

La raccomandazione **Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes** include ora un'opzione per la correzione rapida.

Per altri dettagli su questa raccomandazione e su tutte le altre raccomandazioni del Centro sicurezza, vedere [Raccomandazioni sulla sicurezza: una guida di riferimento](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="La raccomandazione Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes con l'opzione per la correzione rapida":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Il dashboard Conformità con le normative include ora un'opzione per rimuovere gli standard

Il dashboard Conformità con le normative del Centro sicurezza fornisce informazioni dettagliate sul comportamento di conformità in base a come vengono soddisfatti specifici controlli e requisiti.

Il dashboard include un set predefinito di standard normativi. Se uno o più di questi standard non sono pertinenti per la propria organizzazione, è ora possibile rimuoverli facilmente dall'interfaccia utente per una sottoscrizione. Gli standard possono essere rimossi solo a livello della *sottoscrizione*, non nell'ambito del gruppo di gestione.

Per altre informazioni, vedere [Rimozione di uno standard dal dashboard](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Rimossa la tabella Microsoft.Security/securityStatuses da Azure Resource Graph (ARG)

Azure Resource Graph è un servizio di Azure progettato per offrire un'esplorazione efficiente delle risorse con la possibilità di eseguire query su larga scala in un determinato set di sottoscrizioni in modo da poter controllare l'ambiente efficacemente. 

Per il Centro sicurezza di Azure, è possibile usare Azure Resource Graph e il [linguaggio di query Kusto](/azure/data-explorer/kusto/query/) per eseguire query su un'ampia gamma di dati relativi alla postura di sicurezza. Esempio:

- L'inventario degli asset utilizza Azure Resource Graph
- È stata documentata una query di esempio di Azure Resource Graph che illustra come [identificare gli account senza l'opzione Multi-Factor Authentication (MFA) abilitata](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Azure Resource Graph include tabelle di dati da usare nelle query.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer e le tabelle disponibili":::

> [!TIP]
> La documentazione di Azure Resource Graph descrive tutte le tabelle disponibili nella sezione [Tabella di Azure Resource Graph e riferimenti sui tipi di risorsa](../governance/resource-graph/reference/supported-tables-resources.md).

Con questo aggiornamento, la tabella **Microsoft.Security/securityStatuses** è stata rimossa. L'API securityStatuses è ancora disponibile.

La sostituzione dei dati può essere eseguita tramite la tabella Microsoft.Security/Assessments.

La principale differenza tra Microsoft.Security/securityStatuses e Microsoft.Security/Assessments è che mentre la prima mostra l'aggregazione delle valutazioni, la seconda contiene un singolo record per ognuna.

Ad esempio, Microsoft.Security/securityStatuses restituisce un risultato con una matrice di due policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Mentre Microsoft.Security/Assessments contiene un record per ognuna di queste valutazioni dei criteri, come segue:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Esempio di conversione di una query di Azure Resource Graph esistente usando securityStatuses per usare la tabella Assessments:**

Query che fa riferimento a SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Query di sostituzione per la tabella Assessments:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Per altre informazioni, vedere i collegamenti seguenti:
- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Linguaggio di query Kusto (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Settembre 2020

Gli aggiornamenti del mese di settembre includono quanto segue:
- [Nuovo aspetto per il Centro sicurezza](#security-center-gets-a-new-look)
- [Rilascio di Azure Defender](#azure-defender-released)
- [Disponibilità generale di Azure Defender per Key Vault](#azure-defender-for-key-vault-is-generally-available)
- [Disponibilità generale della protezione di Azure Defender per Archiviazione per File e ADLS Gen2](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Disponibilità generale degli strumenti di inventario delle risorse](#asset-inventory-tools-are-now-generally-available)
- [Possibilità di disabilitare un risultato specifico della vulnerabilità per le analisi dei registri contenitori e delle macchine virtuali](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Esentare una risorsa da una raccomandazione](#exempt-a-resource-from-a-recommendation)
- [Esperienza per più cloud grazie ai connettori AWS e GCP nel Centro sicurezza](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Aggregazione di raccomandazioni sulla protezione dei carichi di lavoro Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Possibilità di esportazione continua dei risultati delle valutazioni delle vulnerabilità](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Possibilità di impedire errori di configurazione della sicurezza tramite l'applicazione di raccomandazioni durante la creazione di nuove risorse](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Miglioramento alle raccomandazioni per i gruppi di sicurezza di rete](#network-security-group-recommendations-improved)
- [Deprecazione della raccomandazione in anteprima "I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes" del servizio Azure Kubernetes](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Miglioramento delle notifiche tramite posta elettronica dal Centro sicurezza di Azure](#email-notifications-from-azure-security-center-improved)
- [Raccomandazioni in anteprima non incluse nel punteggio di sicurezza](#secure-score-doesnt-include-preview-recommendations)
- [Indicatore di gravità e intervallo di aggiornamento ora inclusi nelle raccomandazioni](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Nuovo aspetto per il Centro sicurezza

È stata rilasciata un'interfaccia utente aggiornata per le pagine del portale del Centro sicurezza. Le nuove pagine includono una nuova pagina di panoramica, oltre a dashboard per il punteggio di sicurezza, l'inventario delle risorse e Azure Defender.

La pagina di panoramica riprogettata include ora un riquadro per l'accesso al punteggio di sicurezza, all'inventario delle risorse e ai dashboard di Azure Defender. Include anche un riquadro di collegamento al dashboard di conformità alle normative.

Altre informazioni sulla [pagina di panoramica](overview-page.md).


### <a name="azure-defender-released"></a>Rilascio di Azure Defender

**Azure Defender** è la soluzione CWPP (Cloud Workload Protection Platform) integrata nel Centro sicurezza per offrire una protezione avanzata e intelligente dei carichi di lavoro di Azure e ibridi. Sostituisce l'opzione Standard del piano tariffario del Centro sicurezza. 

Quando si abilita Azure Defender dall'area **Prezzi e impostazioni** del Centro sicurezza di Azure, vengono abilitati contemporaneamente tutti i piani seguenti di Defender e vengono fornite difese complete per i livelli di calcolo, dati e servizio dell'ambiente:

- [Azure Defender per server](defender-for-servers-introduction.md)
- [Azure Defender per il servizio app](defender-for-app-service-introduction.md)
- [Azure Defender per Archiviazione](defender-for-storage-introduction.md)
- [Azure Defender per SQL](defender-for-sql-introduction.md)
- [Azure Defender per Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender per registri contenitori](defender-for-container-registries-introduction.md)

Ogni piano è illustrato separatamente nella documentazione del Centro sicurezza.

Grazie al dashboard dedicato, Azure Defender offre avvisi di sicurezza e protezione avanzata dalle minacce per macchine virtuali, database SQL, contenitori, applicazioni Web, rete e altro ancora.

[Altre informazioni su Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Disponibilità generale di Azure Defender per Key Vault

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

**Azure Defender per Key Vault** fornisce la protezione avanzata dalle minacce nativa di Azure per Azure Key Vault, offrendo un livello aggiuntivo di intelligence sulla sicurezza. Azure Defender per Key Vault protegge quindi molte delle risorse che dipendono dagli account di Key Vault.

Il piano facoltativo è ora disponibile a livello generale. Questa funzionalità era disponibile in anteprima come "Advanced Threat Protection per Azure Key Vault".

Le pagine di Key Vault nel portale di Azure includono ora inoltre una pagina **Sicurezza** dedicata per le raccomandazioni e gli avvisi del **Centro sicurezza**.

Per altre informazioni, vedere [Azure Defender per Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Disponibilità generale della protezione di Azure Defender per Archiviazione per File e ADLS Gen2 

**Azure Defender per Archiviazione** rileva le attività potenzialmente dannose negli account di archiviazione di Azure. I dati possono essere protetti indipendentemente dal fatto che siano archiviati come contenitori BLOB, condivisioni file o data lake.

È ora disponibile a livello generale il supporto per [File di Azure](../storage/files/storage-files-introduction.md) e [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

A partire dal 1° ottobre 2020 verranno applicati addebiti per la protezione delle risorse in questi servizi.

Per altre informazioni, vedere [Azure Defender per Archiviazione](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Disponibilità generale degli strumenti di inventario delle risorse

La pagina di inventario delle risorse del Centro sicurezza di Azure offre una pagina singola per visualizzare il comportamento di sicurezza delle risorse connesse al Centro sicurezza.

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza. Fornisce quindi raccomandazioni su come correggere tali vulnerabilità.

Le risorse a cui sono associate raccomandazioni in attesa verranno visualizzate nell'inventario.

Per altre informazioni, vedere [Esplorare e gestire le risorse con gli strumenti di inventario e gestione delle risorse](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Possibilità di disabilitare un risultato specifico della vulnerabilità per le analisi dei registri contenitori e delle macchine virtuali

Azure Defender include rilevatori di vulnerabilità per analizzare le immagini nel Registro Azure Container e nelle macchine virtuali.

Se l'organizzazione deve ignorare un risultato invece di correggerlo, è possibile disabilitarlo facoltativamente. I risultati disabilitati non influiscono sul punteggio di sicurezza e non generano elementi non significativi.

Quando un risultato corrisponde ai criteri definiti nelle regole di disabilitazione, non verrà visualizzato nell'elenco di risultati.

Questa opzione è disponibile nella pagina dei dettagli delle raccomandazioni per:

- **È consigliabile correggere le vulnerabilità nelle immagini del Registro Azure Container**
- **È consigliabile correggere le vulnerabilità nelle macchine virtuali**

Per altre informazioni, vedere [Disabilitare risultati specifici per le immagini del contenitore](defender-for-container-registries-usage.md#disable-specific-findings-preview) e [Disabilitare risultati specifici per le macchine virtuali](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Esentare una risorsa da una raccomandazione

Una risorsa verrà occasionalmente elencata come non integra rispetto a una raccomandazione specifica, con una conseguente riduzione del punteggio di sicurezza, anche se si ritiene che si tratti di un errore. È possibile che sia stata corretta da un processo non rilevato dal Centro sicurezza o che l'organizzazione abbia deciso di accettare il rischio per tale risorsa specifica. 

In questi casi è possibile creare una regola di esenzione e assicurare che la risorsa non sia elencata in futuro tra le risorse non integre. Queste regole possono includere giustificazioni documentate, come illustrato di seguito.

Per altre informazioni, vedere [Esentare una risorsa dalle raccomandazioni e dal punteggio di sicurezza](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Esperienza per più cloud grazie ai connettori AWS e GCP nel Centro sicurezza

I carichi di lavoro cloud si estendono in genere su più piattaforme cloud, quindi anche i servizi di sicurezza cloud devono adottare lo stesso approccio.

Il Centro sicurezza di Azure protegge ora i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

L'onboarding degli account di AWS e GCP nel Centro sicurezza consente di integrare AWS Security Hub, GCP Security Command e il Centro sicurezza di Azure. 

Per altre informazioni, vedere [Connettere gli account di AWS al Centro sicurezza di Azure](quickstart-onboard-aws.md) e [Connettere gli account di GCP al Centro sicurezza di Azure](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Aggregazione di raccomandazioni sulla protezione dei carichi di lavoro Kubernetes

Per assicurare che i carichi di lavoro di Kubernetes siano protetti per impostazione predefinita, il Centro sicurezza aggiunge raccomandazioni per la protezione avanzata a livello di Kubernetes, incluse opzioni di applicazione con il controllo ammissione di Kubernetes.

Dopo l'installazione del componente aggiuntivo di Criteri di Azure per Kubernetes nel cluster del servizio Azure Kubernetes, ogni richiesta al server API Kubernetes sarà monitorata rispetto al set predefinito di procedure consigliate prima del salvataggio permanente nel cluster. È quindi possibile configurare l'imposizione delle procedure consigliate e renderle obbligatorie per i carichi di lavoro futuri.

È ad esempio possibile imporre che i contenitori con privilegi non debbano essere creati ed eventuali richieste future di creazione di tali contenitori verranno bloccate.

Per altre informazioni, vedere [Procedure consigliate per la protezione dei carichi di lavoro con il controllo ammissione di Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Possibilità di esportazione continua dei risultati delle valutazioni delle vulnerabilità

È possibile usare l'esportazione continua per eseguire lo streaming di avvisi e raccomandazioni in tempo reale in Hub eventi di Azure, aree di lavoro Log Analytics o Monitoraggio di Azure. È quindi possibile integrare questi dati con soluzioni di informazioni di sicurezza e gestione degli eventi, tra cui Azure Sentinel, Power BI, Esplora dati di Azure e altre ancora.

Gli strumenti integrati di valutazione delle vulnerabilità del Centro sicurezza restituiscono risultati sulle risorse sotto forma di raccomandazioni di utilità pratica con una raccomandazione "padre", ad esempio "È consigliabile correggere le vulnerabilità nelle macchine virtuali". 

I risultati di sicurezza sono ora disponibili per l'esportazione tramite l'esportazione continua quando si selezionano le raccomandazioni e si abilita l'opzione **Includi i risultati per la sicurezza**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Interruttore Includi i risultati per la sicurezza nella configurazione dell'esportazione continua" :::

Pagine correlate:

- [Soluzione integrata di valutazione delle vulnerabilità del Centro sicurezza per macchine virtuali di Azure](deploy-vulnerability-assessment-vm.md)
- [Soluzione integrata di valutazione delle vulnerabilità del Centro sicurezza per immagini del Registro Azure Container](defender-for-container-registries-usage.md)
- [Esportazione continua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Possibilità di impedire errori di configurazione della sicurezza tramite l'applicazione di raccomandazioni durante la creazione di nuove risorse

Gli errori di configurazione della sicurezza sono una delle cause principali degli eventi imprevisti di sicurezza. Il Centro sicurezza consente ora di contribuire a *evitare* gli errori di configurazione nelle nuove risorse per quanto riguarda raccomandazioni specifiche. 

Questa funzionalità può contribuire alla protezione dei carichi di lavoro e alla stabilizzazione del punteggio di sicurezza.

È possibile imporre una configurazione di sicurezza, basata su una raccomandazione specifica, in due modi:

- L'effetto **Deny** di Criteri di Azure consente di interrompere la creazione di risorse non integre

- L'opzione **Imponi** consente di sfruttare i vantaggi dell'effetto **DeployIfNotExist** di Criteri di Azure e di correggere automaticamente eventuali risorse non conformi al momento della creazione
 
Questa opzione è disponibile per raccomandazioni di sicurezza selezionate e si trova nella parte superiore della pagina dei dettagli delle risorse.

Per altre informazioni, vedere [Impedire gli errori di configurazione con le raccomandazioni di tipo Imponi/Nega](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Miglioramento alle raccomandazioni per i gruppi di sicurezza di rete

Le raccomandazioni di sicurezza seguenti correlate ai gruppi di sicurezza di rete sono state migliorate per ridurre alcune istanze di falsi positivi.

- È consigliabile limitare tutte le porte di rete nei gruppi di sicurezza di rete associati alla VM
- È consigliabile chiudere le porte di gestione nelle macchine virtuali
- Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete
- Le subnet devono essere associate a un gruppo di sicurezza di rete


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Deprecazione della raccomandazione in anteprima "I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes" del servizio Azure Kubernetes

La raccomandazione in anteprima "I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes" verrà deprecata come illustrato nella documentazione del [servizio Azure Kubernetes](../aks/use-pod-security-policies.md).

La funzionalità Criteri di sicurezza dei pod (anteprima) è configurata per la deprecazione e non sarà più disponibile dopo il 15 ottobre 2020. Verrà sostituita da Criteri di Azure per il servizio Azure Kubernetes.

Dopo la deprecazione di Criteri di sicurezza dei pod (anteprima), sarà necessario disabilitare la funzionalità in eventuali cluster esistenti che usano la funzionalità deprecata per eseguire aggiornamenti futuri del cluster e mantenere il supporto tecnico di Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Miglioramento delle notifiche tramite posta elettronica dal Centro sicurezza di Azure

Le aree seguenti dei messaggi di posta elettronica correlati agli avvisi di sicurezza sono state migliorate: 

- Aggiunta della possibilità di inviare notifiche tramite posta elettronica sugli avvisi per tutti i livelli di gravità
- Aggiunta la possibilità di inviare notifiche agli utenti con ruoli di Azure diversi nella sottoscrizione
- Notifiche proattive per i proprietari delle sottoscrizioni per impostazione predefinita in caso di avvisi a gravità alta, con probabilità elevata di essere violazioni effettive
- Rimozione del campo relativo al numero di telefono dalla pagina di configurazione delle notifiche tramite posta elettronica

Per altre informazioni, vedere [Configurare le notifiche tramite posta elettronica per gli avvisi di sicurezza](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Raccomandazioni in anteprima non incluse nel punteggio di sicurezza 

Il Centro sicurezza valuta continuamente risorse, sottoscrizioni e organizzazione per rilevare problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato.

Quando vengono individuate nuove minacce, vengono resi disponibili nuovi consigli sulla sicurezza nel Centro sicurezza tramite nuove raccomandazioni. Per evitare modifiche inaspettate al punteggio di sicurezza e per offrire un periodo di tolleranza in cui è possibile esplorare le nuove raccomandazioni prima che influiscano sui punteggi, le raccomandazioni contrassegnate come **Anteprima** non sono più incluse nei calcoli del punteggio di sicurezza. È comunque necessario correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima.

Le raccomandazioni di tipo **Anteprima** non contrassegnano inoltre una risorsa come "Non integra".

Esempio di una raccomandazione in anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Raccomandazione con il flag di anteprima":::

[Altre informazioni sul punteggio di sicurezza](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Indicatore di gravità e intervallo di aggiornamento ora inclusi nelle raccomandazioni

La pagina dei dettagli per le raccomandazioni include ora un indicatore dell'intervallo di aggiornamento, se rilevante, e un'indicazione chiara della gravità della raccomandazione.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Pagina delle raccomandazioni che mostra l'aggiornamento e la gravità":::



## <a name="august-2020"></a>Agosto 2020

Gli aggiornamenti del mese di agosto includono quanto segue:

- [Inventario delle risorse: nuova visualizzazione potente del comportamento di sicurezza delle risorse](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Aggiunta di supporto per le impostazioni predefinite per la sicurezza di Azure Active Directory per l'autenticazione a più fattori](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Aggiunta di una raccomandazione per le entità servizio](#service-principals-recommendation-added)
- [Valutazione delle vulnerabilità nelle VM: consolidamento di raccomandazioni e criteri](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Aggiunta di nuovi criteri di sicurezza del servizio Azure Kubernetes all'iniziativa ASC_default per l'uso esclusivo da parte dei clienti dell'anteprima privata](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventario delle risorse: nuova visualizzazione potente del comportamento di sicurezza delle risorse

L'inventario delle risorse del Centro sicurezza, attualmente disponibile in anteprima, offre una modalità per visualizzare il comportamento di sicurezza delle risorse connesse al Centro sicurezza.

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza. Fornisce quindi raccomandazioni su come correggere tali vulnerabilità. Le risorse a cui sono associate raccomandazioni in attesa verranno visualizzate nell'inventario.

È possibile usare la visualizzazione e i rispettivi filtri per esplorare i dati del comportamento di sicurezza ed eseguire altre azioni in base ai risultati.

Altre informazioni sull'[inventario delle risorse](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Aggiunta di supporto per le impostazioni predefinite per la sicurezza di Azure Active Directory per l'autenticazione a più fattori

Il Centro sicurezza ha aggiunto il supporto completo per le [impostazioni predefinite per la sicurezza](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), ovvero le protezione gratuite di Microsoft per la sicurezza delle identità.

Le impostazioni predefinite per la sicurezza offrono impostazioni preconfigurate per la sicurezza delle identità per proteggere l'organizzazione da attacchi comuni correlati alle identità. Le impostazioni predefinite per la sicurezza proteggono già più di 5 milioni di tenant complessivamente. 50.000 tenant sono protetti anche dal Centro sicurezza.

Il Centro sicurezza fornisce ora una raccomandazione sulla sicurezza ogni volta che identifica una sottoscrizione di Azure in cui non sono abilitate le impostazioni predefinite per la sicurezza. Fino a oggi il Centro sicurezza ha consigliato l'abilitazione dell'autenticazione a più fattori con l'accesso condizionale, incluso nella licenza di Azure Active Directory (AD) Premium. Per i clienti che usano Azure AD Gratuito è ora consigliabile abilitare le impostazioni predefinite per la sicurezza. 

L'obiettivo consiste nell'invitare più clienti a proteggere gli ambienti cloud tramite autenticazione a più fattori e attenuare uno dei rischi più elevati e con impatto più significativo sul [punteggio di sicurezza](secure-score-security-controls.md).

Altre informazioni sulle [impostazioni predefinite per la sicurezza](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Aggiunta di una raccomandazione per le entità servizio

È stata aggiunta una nuova raccomandazione per consigliare ai clienti del Centro sicurezza che usano i certificati di gestione per gestire le sottoscrizioni di passare alle entità servizio.

La raccomandazione **Per proteggere le sottoscrizioni è consigliabile usare le entità servizio invece dei certificati di gestione** consiglia di usare le entità servizio o Azure Resource Manager per migliorare la sicurezza della gestione delle sottoscrizioni. 

Altre informazioni su [Oggetti applicazione ed entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Valutazione delle vulnerabilità nelle VM: consolidamento di raccomandazioni e criteri

Il Centro sicurezza esamina le VM per rilevare se eseguono una soluzione di valutazione delle vulnerabilità. Se non viene trovata alcuna soluzione di valutazione delle vulnerabilità, il Centro sicurezza fornisce una raccomandazione per semplificare la distribuzione.

Quando vengono trovate vulnerabilità, il Centro sicurezza fornisce una raccomandazione che riepiloga i risultati per consentire l'indagine e la correzione, se necessario.

Per assicurare un'esperienza coerente per tutti gli utenti, indipendentemente dal tipo di rilevatore usato, quattro raccomandazioni sono state combinate nelle due raccomandazioni seguenti:

|Raccomandazione unificata|Descrizione delle modifiche|
|----|:----|
|**È consigliabile abilitare una soluzione di valutazione della vulnerabilità nelle macchine virtuali**|Sostituisce le due raccomandazioni seguenti:<br> **•** Abilitare la soluzione di valutazione delle vulnerabilità predefinita nelle macchine virtuali (con tecnologia Qualys) - Ora deprecata e inclusa nel livello Standard<br> **•** È consigliabile installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali - Ora deprecata (livelli Standard e Gratuito)|
|**È consigliabile correggere le vulnerabilità nelle macchine virtuali**|Sostituisce le due raccomandazioni seguenti:<br>**•** Correggere le vulnerabilità rilevate nelle macchine virtuali (con tecnologia Qualys) - Ora deprecata<br>**•** È consigliabile correggere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità - Ora deprecata|
|||

Sarà ora possibile usare la stessa raccomandazione per distribuire l'estensione di valutazione delle vulnerabilità del Centro sicurezza o una soluzione con licenza privata ("BYOL") da un partner quale Qualys o Rapid7.

Quando le vulnerabilità vengono trovate e segnalate al Centro sicurezza, una singola raccomandazione informerà gli utenti in merito alla disponibilità dei risultati, indipendentemente dalla soluzione di valutazione delle vulnerabilità che le ha individuate.

#### <a name="updating-dependencies"></a>Aggiornamento delle dipendenze

Se sono presenti script, query o automazioni che fanno riferimento a raccomandazioni o chiavi/nomi di criteri precedenti, usare la tabella seguente per aggiornare i riferimenti:

##### <a name="before-august-2020"></a>Prima del mese di agosto 2020

|Recommendation|Ambito|
|----|:----|
|**Abilitare la soluzione di valutazione delle vulnerabilità predefinita nelle macchine virtuali (con tecnologia Qualys)**<br>Chiave: 550e890b-e652-4d22-8274-60b3bdb24c63|Predefinito|
|**Correggere le vulnerabilità rilevate nelle macchine virtuali (con tecnologia Qualys)**<br>Chiave: 1195afff-c881-495e-9bc5-1486211ae03f|Predefinito|
|**È consigliabile installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali**<br>Chiave: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità**<br>Chiave: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Policy|Ambito|
|----|:----|
|**La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali**<br>ID criterio: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Predefinito|
|**È consigliabile correggere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità**<br>ID criterio: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Dal mese di agosto 2020

|Recommendation|Ambito|
|----|:----|
|**È consigliabile abilitare una soluzione di valutazione della vulnerabilità nelle macchine virtuali**<br>Chiave: ffff0522-1e88-47fc-8382-2a80ba848f5d|Predefinito + BYOL|
|**È consigliabile correggere le vulnerabilità nelle macchine virtuali**<br>Chiave: 1195afff-c881-495e-9bc5-1486211ae03f|Predefinito + BYOL|
||||

|Policy|Ambito|
|----|:----|
|[**La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID criterio: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Predefinito + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Aggiunta di nuovi criteri di sicurezza del servizio Azure Kubernetes all'iniziativa ASC_default per l'uso esclusivo da parte dei clienti dell'anteprima privata

Per assicurare che i carichi di lavoro di Kubernetes siano protetti per impostazione predefinita, il Centro sicurezza aggiunge criteri e raccomandazioni per la protezione avanzata a livello di Kubernetes, incluse opzioni di imposizione con il controllo ammissione di Kubernetes.

La fase iniziale di questo progetto include un'anteprima privata e l'aggiunta di nuovi criteri, disabilitati per impostazione predefinita, all'iniziativa ASC_default.

È possibile ignorare questi criteri, senza impatto sull'ambiente. Se si vogliono abilitare i criteri, iscriversi all'anteprima in https://aka.ms/SecurityPrP e selezionare una delle opzioni seguenti:

1. **Anteprima singola**: per partecipare solo a questa anteprima privata. Indicare esplicitamente "Analisi continua del Centro sicurezza di Azure" come anteprima a cui si vuole partecipare.
1. **Programma in corso**: per partecipare a questa anteprima privata e alle anteprime private future. Sarà necessario completare un profilo e accettare un contratto sulla privacy.


## <a name="july-2020"></a>Luglio 2020

Gli aggiornamenti del mese di luglio includono quanto segue:
- [Disponibilità della valutazione delle vulnerabilità per macchine virtuali per immagini non del marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Espansione della protezione dalle minacce per Archiviazione di Azure in modo da includere File di Azure e Azure Data Lake Storage Gen2 (anteprima)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Otto nuove raccomandazioni per abilitare le funzionalità di protezione dalle minacce](#eight-new-recommendations-to-enable-threat-protection-features)
- [Miglioramenti alla sicurezza dei contenitori: analisi più rapida dei registri e documentazione aggiornata](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Aggiornamento dei controlli applicazioni adattivi con una nuova raccomandazione e supporto per caratteri jolly nelle regole di percorso](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sei criteri per la funzionalità deprecata Sicurezza dei dati avanzata SQL](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Disponibilità della valutazione delle vulnerabilità per macchine virtuali per immagini non del marketplace

Durante la distribuzione di una soluzione di valutazione delle vulnerabilità, il Centro sicurezza eseguiva in precedenza un controllo di convalida prima della distribuzione. Il controllo consentiva di confermare la disponibilità di uno SKU del marketplace della macchina virtuale di destinazione. 

A partire da questo aggiornamento, il controllo è stato rimosso ed è ora possibile distribuire strumenti di valutazione delle vulnerabilità in computer Windows e Linux "personalizzati". Le immagini personalizzate sono immagini modificate a partire da impostazioni predefinite del marketplace.

Anche se è ora possibile distribuire l'estensione di valutazione delle vulnerabilità integrata (con tecnologia Qualys) in molti altri computer, il supporto è disponibile solo se si usa un sistema operativo elencato in [Distribuire il rilevatore di vulnerabilità integrato nelle macchine virtuali di livello Standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

Altre informazioni sul [rilevatore di vulnerabilità integrato per macchine virtuali (richiede Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Per altre informazioni sull'uso di una soluzione personalizzata di valutazione delle vulnerabilità con licenza privata di Qualys o Rapid7, vedere [Distribuzione di una soluzione di analisi delle vulnerabilità di partner](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Espansione della protezione dalle minacce per Archiviazione di Azure in modo da includere File di Azure e Azure Data Lake Storage Gen2 (anteprima)

Threat Protection per Archiviazione di Azure rileva le attività potenzialmente dannose negli account di archiviazione di Azure. Il Centro sicurezza mostra avvisi quando rileva tentativi di accesso o di exploit degli account di archiviazione. 

I dati possono essere protetti indipendentemente dal fatto che siano archiviati come contenitori BLOB, condivisioni file o data lake.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Otto nuove raccomandazioni per abilitare le funzionalità di protezione dalle minacce

Sono state aggiunte otto nuove raccomandazioni per offrire un modo semplice per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza di Azure per i tipi di risorse seguenti: macchine virtuali, piani di servizio app, server di database SQL di Azure, Server SQL nei computer, account di archiviazione di Azure, cluster del servizio Azure Kubernetes, registri del Registro Azure Container e insiemi di credenziali di Azure Key Vault.

Di seguito sono elencate le nuove raccomandazioni:

- **La soluzione Sicurezza dei dati avanzata deve essere abilitata nei server del database SQL di Azure**
- **La soluzione Sicurezza dei dati avanzata deve essere abilitata in SQL Server in macchine virtuali**
- **La soluzione Advanced Threat Protection deve essere abilitata nei piani di servizio app di Azure**
- **La soluzione Advanced Threat Protection deve essere abilitata nei registri in Registro Azure Container**
- **La soluzione Advanced Threat Protection deve essere abilitata negli insiemi di credenziali in Azure Key Vault**
- **La soluzione Advanced Threat Protection deve essere abilitata nei cluster del servizio Azure Kubernetes**
- **La soluzione Advanced Threat Protection deve essere abilitata negli account di archiviazione di Azure**
- **È consigliabile abilitare Advanced Threat Protection nelle macchine virtuali**

Queste nuove raccomandazioni appartengono al controllo di sicurezza **Abilita Azure Defender**.

Le raccomandazioni includono anche la capacità di correzione rapida. 

> [!IMPORTANT]
> La correzione in base a una di queste raccomandazioni comporterà addebiti per la protezione delle risorse rilevanti. L'applicazione degli addebiti inizierà immediatamente se sono presenti risorse correlate nella sottoscrizione corrente oppure inizierà in futuro se si aggiungono risorse in un momento successivo.
> 
> Se ad esempio non sono presenti cluster del servizio Azure Kubernetes nella sottoscrizione e si abilita la protezione dalle minacce, non verranno applicati addebiti. Se in futuro si aggiunge un cluster nella stessa sottoscrizione, il cluster verrà protetto automaticamente e l'applicazione degli addebiti avrà inizio in tale momento.

Per altre informazioni su ogni raccomandazione, vedere la [pagina di riferimento delle raccomandazioni sulla sicurezza](recommendations-reference.md).

Altre informazioni sulla [protezione dalle minacce nel Centro sicurezza di Azure](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Miglioramenti alla sicurezza dei contenitori: analisi più rapida dei registri e documentazione aggiornata

Come parte degli investimenti continui nel dominio della sicurezza dei contenitori, è ora disponibile nel Centro sicurezza un miglioramento significativo delle prestazioni per le analisi dinamiche di immagini dei contenitori archiviate nel Registro Azure Container. Le analisi vengono ora in genere completate in due minuti circa. In alcuni casi l'analisi potrebbe richiedere fino a 15 minuti.

Per migliorare la chiarezza e le indicazioni correlate alle funzionalità di sicurezza dei contenitori del Centro sicurezza di Azure, sono stati anche apportati aggiornamenti alle pagine della documentazione sulla sicurezza. 

Per altre informazioni sulla sicurezza dei contenitori del Centro sicurezza, vedere gli articoli seguenti:

- [Panoramica delle funzionalità di sicurezza dei contenitori del Centro sicurezza](container-security.md)
- [Dettagli dell'integrazione con il Registro Azure Container](defender-for-container-registries-introduction.md)
- [Dettagli dell'integrazione con il servizio Azure Kubernetes](defender-for-kubernetes-introduction.md)
- [Come analizzare i registri e applicare la protezione avanzata agli host Docker](container-security.md)
- [Avvisi di sicurezza dalle funzionalità di protezione dalle minacce per cluster del servizio Azure Kubernetes](alerts-reference.md#alerts-akscluster)
- [Avvisi di sicurezza dalle funzionalità di protezione dalle minacce per host del servizio Azure Kubernetes](alerts-reference.md#alerts-containerhost)
- [Raccomandazioni sulla sicurezza per i contenitori](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Aggiornamento dei controlli applicazioni adattivi con una nuova raccomandazione e supporto per caratteri jolly nelle regole di percorso

Sono stati apportati due aggiornamenti significativi alla funzionalità Controlli applicazioni adattivi:

* Una nuova raccomandazione identifica un comportamento potenzialmente legittimo che non era consentito in precedenza. La nuova raccomandazione, **Le regole dell'elenco Consentiti dei criteri dei controlli applicazioni adattivi devono essere aggiornate**, richiede l'aggiunta di nuove regole al criterio esistente per ridurre il numero di falsi positivi negli avvisi di violazione dei controlli applicazioni adattivi.

* Le regole di percorso supportano ora i caratteri jolly. A partire da questo aggiornamento, è possibile configurare le regole di percorso consentite usando i caratteri jolly. Sono supportati due scenari:

    * Uso di un carattere jolly alla fine del percorso per consentire tutti i file eseguibili entro tale cartella e nelle sottocartelle

    * Uso di un carattere jolly al centro del percorso per consentire un nome di file eseguibile noto con un nome di cartella modificabile, ad esempio cartelle personali dell'utente con un file eseguibile noto, nomi di cartella generati automaticamente e così via


[Altre informazioni sull'applicazione di controlli applicazioni adattivi](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sei criteri per la funzionalità deprecata Sicurezza dei dati avanzata SQL

Verranno deprecati sei criteri correlati alla sicurezza dei dati avanzata per i computer SQL:

- I tipi di protezione di Advanced Threat Protection devono essere impostati su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata dell'istanza gestita di SQL
- È necessario impostare i tipi di protezione di Advanced Threat Protection su "Tutti" nelle impostazioni di Sicurezza dei dati avanzata di SQL Server
- Le impostazioni avanzate di sicurezza dei dati per l'istanza gestita di SQL devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata dell'istanza gestita di SQL
- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata del server SQL

Altre informazioni sui [criteri predefiniti](./policy-reference.md).
