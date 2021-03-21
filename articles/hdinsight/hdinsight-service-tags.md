---
title: Tag di servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight
description: Usare i tag del servizio HDInsight per consentire il traffico in ingresso verso il cluster dai nodi Health and Management Services, senza aggiungere indirizzi IP al gruppi.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: d17d067b88add3006bc5c7fb10caa6b80a80a827
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931771"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Tag del servizio NSG per Azure HDInsight

I tag del servizio HDInsight di Azure per i gruppi di sicurezza di rete (gruppi) sono gruppi di indirizzi IP per i servizi di gestione e integrità. Questi gruppi consentono di ridurre al minimo la complessità per la creazione delle regole di sicurezza. I [tag di servizio](../virtual-network/network-security-groups-overview.md#service-tags) consentono il traffico in ingresso da indirizzi IP specifici senza immettere ciascuno degli [indirizzi IP di gestione](hdinsight-management-ip-addresses.md) nella gruppi.

Il servizio HDInsight gestisce questi tag del servizio. Non è possibile creare un tag di servizio personalizzato o modificare un tag esistente. Microsoft gestisce i prefissi di indirizzo che corrispondono al tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

Se si vuole usare un'area specifica e il tag di servizio non è ancora documentato in questa pagina, è possibile usare l' [API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) per trovare il tag del servizio. È anche possibile scaricare il [file JSON del tag di servizio](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) e cercare l'area desiderata.

## <a name="get-started-with-service-tags"></a>Introduzione ai tag del servizio

Sono disponibili due opzioni per l'uso dei tag di servizio nei gruppi di sicurezza di rete:

- **Usa un singolo tag del servizio HDInsight globale**: questa opzione consente di aprire la rete virtuale a tutti gli indirizzi IP usati dal servizio HDInsight per monitorare i cluster in tutte le aree. Questa opzione è il metodo più semplice, ma potrebbe non essere appropriata se si hanno requisiti di sicurezza restrittivi.

- **Usare più tag di servizio a livello** di area: questa opzione consente di aprire la rete virtuale solo per gli indirizzi IP usati da HDInsight in tale area specifica. Tuttavia, se si usano più aree, è necessario aggiungere più tag di servizio alla rete virtuale.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Usa un singolo tag del servizio HDInsight globale

Il modo più semplice per iniziare a usare i tag di servizio con il cluster HDInsight consiste nell'aggiungere il tag globale `HDInsight` a una regola NSG.

1. Dal [portale di Azure](https://portal.azure.com/)selezionare il gruppo di sicurezza di rete.

1. In **Impostazioni** selezionare **regole di sicurezza in ingresso** e quindi selezionare **+ Aggiungi**.

1. Dall'elenco a discesa **origine** selezionare **tag servizio**.

1. Dall'elenco a discesa **tag servizio di origine** selezionare **HDInsight**.

    ![Aggiungere un tag di servizio dal portale di Azure](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Questo tag contiene gli indirizzi IP dei servizi di gestione e integrità per tutte le aree in cui è disponibile HDInsight. Il tag assicurerà che il cluster sia in grado di comunicare con i servizi di integrità e gestione necessari indipendentemente da dove vengono creati.

## <a name="use-regional-hdinsight-service-tags"></a>Usare i tag di servizio HDInsight regionali

Se l'opzione Global Tag non funziona perché sono necessarie autorizzazioni più restrittive, è possibile consentire solo i tag del servizio applicabili per l'area geografica. Potrebbero essere presenti più tag del servizio, a seconda dell'area in cui viene creato il cluster.

Per informazioni sui tag di servizio da aggiungere per la propria area, vedere le sezioni seguenti dell'articolo.

### <a name="use-a-single-regional-service-tag"></a>Usa un singolo tag di servizio a livello di area

Se il cluster si trova in un'area elencata in questa tabella, è sufficiente aggiungere un singolo tag di servizio regionale a NSG.

| Country | Region | Tag di servizio |
| ---- | ---- | ---- |
| Australia | Australia orientale | HDInsight. AustraliaEast |
| &nbsp; | Australia sud-orientale | HDInsight. AustraliaSoutheast |
| &nbsp; | Australia centrale | HDInsight. AustraliaCentral |
| Cina | Cina orientale 2 | HDInsight. ChinaEast2 |
| &nbsp; | Cina settentrionale 2 | HDInsight. ChinaNorth2 |
| Stati Uniti | Stati Uniti centro-settentrionali | HDInsight. NorthCentralUS |
| &nbsp; | Stati Uniti occidentali 2 | HDInsight. WestUS2 |
| &nbsp; | Stati Uniti centro-occidentali | HDInsight. WestCentralUS |
| Canada | Canada orientale | HDInsight. CanadaEast |
| Brasile | Brasile meridionale | HDInsight. BrazilSouth |
| Corea del Sud | Corea centrale | HDInsight. KoreaCentral |
| &nbsp; | Corea meridionale | HDInsight. KoreaSouth |
| India | India centrale | HDInsight. CentralIndia |
| &nbsp; | India meridionale | HDInsight. SouthIndia |
| Giappone | Giappone occidentale | HDInsight. JapanWest |
| Francia | Francia centrale| HDInsight. FranceCentral |
| Regno Unito | Regno Unito meridionale | HDInsight. UKSouth |
| Azure Government | Stati Uniti centrali DoD | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | DOD est | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Usare più tag di servizio internazionali

Se l'area in cui è stato creato il cluster non è elencata nella tabella precedente, è necessario consentire più tag di servizio a livello di area. La necessità di usare più di uno è dovuta alle differenze nella disposizione dei provider di risorse per le diverse aree.

Le aree rimanenti sono divise in gruppi in base ai tag di servizio regionali usati.

#### <a name="group-1"></a>Gruppo 1

Se il cluster viene creato in una delle aree della tabella seguente, consentire i tag del servizio `HDInsight.WestUS` e `HDInsight.EastUS` . Inoltre, il tag di servizio regionale elencato. Le aree in questa sezione richiedono tre tag di servizio.

Ad esempio, se il cluster viene creato nell' `East US 2` area, è necessario aggiungere i tag di servizio seguenti al gruppo di sicurezza di rete:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Region | Tag di servizio |
| ---- | ---- | ---- |
| Stati Uniti | Stati Uniti orientali 2 | HDInsight. EastUS2 |
| &nbsp; | Stati Uniti centrali | HDInsight. Centralus |
| &nbsp; | NorthCentral US | HDInsight. NorthCentralUS |
| &nbsp; | Stati Uniti centro-meridionali | HDInsight. SouthCentralUS |
| &nbsp; | Stati Uniti orientali | HDInsight. Eastus |
| &nbsp; | Stati Uniti occidentali | HDInsight. Westus |
| Giappone | Giappone orientale | HDInsight. JapanEast |
| Europa | Europa settentrionale | HDInsight. NorthEurope |
| &nbsp; | Europa occidentale| HDInsight. WestEurope |
| Asia | Asia orientale | HDInsight. EastAsia |
| &nbsp; | Asia sud-orientale | HDInsight. SoutheastAsia |
| Australia | Australia orientale | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Gruppo 2

I cluster nelle aree di *Cina settentrionale* e *Cina orientale* devono consentire due tag di servizio: `HDInsight.ChinaNorth` e `HDInsight.ChinaEast` .

#### <a name="group-3"></a>Gruppo 3

I cluster nelle aree di *US gov Iowa* e *US gov Virginia* devono consentire due tag di servizio: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia` .

#### <a name="group-4"></a>Gruppo 4

I cluster nelle aree della *Germania centrale* e della *Germania nord-orientale* devono consentire due tag di servizio: `HDInsight.GermanyCentral` e `HDInsight.GermanyNortheast` .

## <a name="next-steps"></a>Passaggi successivi

- [Gruppi di sicurezza di rete: Tag del servizio](../virtual-network/network-security-groups-overview.md#security-rules)
- [Creare reti virtuali per i cluster HDInsight di Azure](hdinsight-create-virtual-network.md)