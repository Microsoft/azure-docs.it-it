---
title: Informazioni sull'utilizzo della macchina virtuale di Azure
description: Informazioni dettagliate sull'utilizzo della macchina virtuale
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: ba973bd5609dacf05eca842025d4e828d8a9f841
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550948"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Informazioni sull'utilizzo della macchina virtuale di Azure
Attraverso l'analisi dei dati di utilizzo di Azure, è possibile ottenere informazioni dettagliate sul consumo che assicurano una migliore gestione e allocazione dei costi in tutta l'organizzazione. Questo documento offre un approfondimento sui dettagli relativi al consumo di Calcolo di Azure. Per altre informazioni sull'utilizzo generale di Azure, vedere [Comprendere la fattura](../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Scaricare i dettagli di utilizzo
Per iniziare, [scaricare i dettagli di utilizzo](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). La tabella seguente fornisce la definizione e i valori di esempio relativi all'utilizzo per le macchine virtuali distribuite tramite Azure Resource Manager. Questo documento non contiene informazioni dettagliate per le macchine virtuali distribuite tramite il modello classico.


| Campo | Significato | Valori di esempio | 
|---|---|---|
| Data utilizzo | Data di utilizzo della risorsa | `11/23/2017` |
| Meter ID | Identifica il servizio di primo livello a cui appartiene questo utilizzo| `Virtual Machines`|
| Sottocategoria misuratore | ID della metrica fatturata. <br><br> Per l'utilizzo delle ore di calcolo, è disponibile un contatore per ciascuna dimensione della macchina virtuale + sistema operativo (Windows, non Windows) + area. <br><br> Per l'utilizzo del software Premium, è disponibile un contatore per ogni tipo di software. La maggior parte delle immagini software Premium ha contatori differenti per ogni dimensione core. Per altre informazioni, visita la [pagina dei prezzi di calcolo](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Nome misuratore| Specifico per ciascun servizio in Azure. Per il calcolo, è sempre "Ore di calcolo".| `Compute Hours`|
| Area misuratore| Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa.|  `JA East`|
| Unità| Identifica l'unità in base alla quale viene addebitato il servizio. Le risorse di calcolo vengono fatturate su base oraria.| `Hours`|
| Consumato| La quantità di risorsa consumata per il giorno corrente. Per il calcolo, viene addebitato un importo per ogni minuto in cui la macchina virtuale è stata eseguita per una data ora (fino a 6 cifre decimali di precisione).| `1, 0.5`|
| Percorso della risorsa  | Identifica il datacenter in cui la risorsa è in esecuzione.| `JA East`|
| Servizio utilizzato | Il servizio della piattaforma Azure che è stato utilizzato.| `Microsoft.Compute`|
| Gruppo di risorse | Il gruppo di risorse in cui la risorsa distribuita è in esecuzione. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md).|`MyRG`|
| ID istanza | Identificatore della risorsa. L'identificatore contiene il nome specificato per la risorsa al momento della creazione. Per le macchine virtuali, l'ID istanza includerà i campi SubscriptionId, ResourceGroupName e VMName (o il nome del set di scalabilità per l'utilizzo del set di scalabilità).| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>oppure<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Tag| Tag assegnato alla risorsa. Usare i tag per raggruppare i record di fatturazione. Informazioni su come contrassegnare le macchine virtuali usando l' [interfaccia](./tag-cli.md) della riga di comando o [PowerShell](./tag-portal.md) disponibile solo per gestione risorse macchine virtuali.| `{"myDepartment":"RD","myUser":"myName"}`|
| Informazioni aggiuntive | Metadati specifici del servizio. Per le macchine virtuali, è necessario specificare i dati seguenti nel campo Informazioni aggiuntive: <br><br> Image Type: l'immagine specifica che è stata eseguita. L'elenco completo delle stringhe supportate è disponibile di seguito in Tipo di immagine.<br><br> Service Type: la dimensione che è stata distribuita.<br><br> VMName: il nome della macchina virtuale. Questo campo viene popolato solo per le macchine virtuali dei set di scalabilità. Il nome della macchina virtuale per le macchine virtuali dei set di scalabilità è disponibile nella stringa ID istanza precedente.<br><br> UsageType: specifica il tipo di utilizzo che rappresenta.<br><br> ComputeHR è l'utilizzo delle ore di calcolo per la macchina virtuale sottostante, ad esempio Standard_D1_v2.<br><br> ComputeHR_SW è l'addebito per il software Premium se la macchina virtuale usa un software Premium, come Microsoft R Server. | Macchine virtuali<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Set di scalabilità di macchine virtuali<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Software Premium<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Tipo di immagine
Per alcune immagini nella raccolta di Azure, il tipo di immagine viene popolato nel campo Informazioni aggiuntive. Ciò consente agli utenti di comprendere e tenere traccia di ciò che hanno distribuito nella loro macchina virtuale. I valori che vengono popolati in questo campo in base all'immagine che è stata distribuita sono i seguenti:
- BitRock 
- FreeBSD canonico 
- Open Logic 
- Oracle 
- SLES per SAP 
- SQL Server 14 Preview in Windows Server 2012 R2 Preview 
- SUSE
- SUSE Premium
- Appliance cloud StorSimple 
- Red Hat
- Red Hat per applicazioni aziendali SAP     
- Red Hat per SAP HANA 
- Windows Client BYOL 
- Windows Server BYOL 
- Windows Server Preview 

## <a name="service-type"></a>Tipo di servizio
Il campo relativo al tipo di servizio nel campo Informazioni aggiuntive corrisponde alla dimensione esatta della macchina virtuale che è stata distribuita. Gli addebiti per le macchine virtuali di archiviazione Premium (basate su unità SSD) e per quelle di archiviazione non Premium (basate su HDD) sono gli stessi. Se si distribuisce una dimensione basata su SSD, ad esempio \_ DS2 standard \_ v2, vengono visualizzate le dimensioni non SSD ( `Standard\_D2\_v2 VM` ) nella colonna Meter Sub-Category e le dimensioni SSD ( `Standard\_DS2\_v2` ) nel campo informazioni aggiuntive.

## <a name="region-names"></a>Nomi delle aree
Il nome dell'area popolato nel campo Percorso della risorsa nei dettagli di utilizzo varia rispetto al nome dell'area usato in Azure Resource Manager. Di seguito è riepilogata l'associazione tra i valori relativi alle aree:

| **Nome area in Resource Manager** | **Percorso risorsa nei dettagli di utilizzo** |
|---|---|
| australiaeast |Australia orientale|
| australiasoutheast | Australia sud-orientale|
| brazilsouth | Brasile meridionale|
| CanadaCentral | Canada centrale|
| CanadaEast | Canada orientale|
| CentralIndia | India centrale|
| centralus | Stati Uniti centrali|
| chinaeast | Cina orientale|
| chinanorth | Cina settentrionale|
| eastasia | Asia orientale|
| eastus | Stati Uniti orientali|
| eastus2 | Stati Uniti orientali 2|
| GermanyCentral | Germania centrale|
| GermanyNortheast | Germania nord-orientale|
| japaneast | Giappone orientale|
| japanwest | Giappone occidentale|
| KoreaCentral | Corea centrale|
| KoreaSouth | Corea meridionale|
| northcentralus | Stati Uniti centro-settentrionali|
| northeurope | Europa settentrionale|
| southcentralus | Stati Uniti centro-meridionali|
| southeastasia | Asia sud-orientale|
| SouthIndia | India meridionale|
| UKNorth | Stati Uniti settentrionali|
| uksouth | Regno Unito meridionale|
| UKSouth2 | Regno Unito meridionale 2|
| ukwest | Regno Unito occidentale|
| USDoDCentral | US DoD (area centrale)|
| USDoDEast | US DoD (area orientale)|
| USGovArizona | USGov Arizona|
| usgoviowa | USGov Iowa|
| USGovTexas | USGov Texas|
| usgovvirginia | USGov Virginia|
| westcentralus | Stati Uniti centro-occidentali|
| westeurope | Europa occidentale|
| WestIndia | India occidentale|
| westus | Stati Uniti occidentali|
| westus2 | Stati Uniti occidentali 2|


## <a name="virtual-machine-usage-faq"></a>Domande frequenti sull'uso delle macchine virtuali
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Per quali risorse viene effettuato un addebito quando si distribuisce una macchina virtuale?    
Le macchine virtuali acquisiscono i costi per la macchina virtuale stessa, per eventuale software Premium in esecuzione nella macchina virtuale, per l'account di archiviazione\disco gestito associato alla macchina virtuale e per i trasferimenti della larghezza di banda di rete dalla macchina virtuale.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Come è possibile stabilire se una macchina virtuale usa Vantaggio Azure Hybrid nel file CSV relativo ai dati di utilizzo?
Se si esegue la distribuzione tramite [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/), verrà addebitata la tariffa per le macchine virtuali non Windows poiché si usa l'opzione Bring Your Own License per il cloud. Nella fattura è possibile distinguere quali macchine virtuali di Resource Manager eseguono Vantaggio Azure Hybrid, dal momento che riportano “Windows\_Server BYOL” o “Windows\_Client BYOL” nella colonna ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Come vengono differenziati i tipi di macchine virtuali Basic rispetto a quelli Standard nel file CSV relativo ai dati di utilizzo?
Sono disponibili macchine virtuali serie A di tipo Basic e Standard. Se si distribuisce una macchina virtuale Basic, in Sottocategoria misuratore è visualizzata la stringa "Basic". Se si distribuisce una macchina virtuale serie A di tipo Standard, le dimensioni della macchina virtuale vengono visualizzate come “A1 VM” poiché il valore predefinito è Standard. Per altre informazioni sulle differenze tra Basic e Standard, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Quali sono le dimensioni ExtraSmall, Small, Medium, Large ed ExtraLarge?
ExtraSmall-ExtraLarge sono i nomi legacy per Standard\_A0 – Standard\_A4. Nei record di utilizzo della macchina virtuale classica è possibile che venga usata questa convenzione se sono state distribuite queste dimensioni.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Qual è la differenza tra Area contatore e Percorso della risorsa?
Il campo Area misuratore è associato al contatore. Per alcuni servizi di Azure che usano un prezzo per tutte le aree, il campo Area contatore può essere vuoto. Tuttavia, poiché le macchine virtuali hanno prezzi dedicati in base all'area per le macchine virtuali, questo campo viene popolato. Analogamente, Percorso della risorsa per le macchine virtuali è il percorso in cui è distribuita la macchina virtuale. L'area di Azure in entrambi i campi è la stessa, anche se potrebbe esserci una convenzione di stringa diversa per il nome dell'area.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Perché il valore ImageType è vuoto nel campo Informazioni aggiuntive?
Il campo ImageType viene popolato solo per un subset di immagini. Se non è stata distribuita una delle immagini riportate in precedenza, ImageType è vuoto.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Perché VMName è vuoto in Informazioni aggiuntive?
VMName viene popolato nel campo Informazioni aggiuntive solo per le macchine virtuali in un set di scalabilità. Il campo InstanceID contiene il nome della macchina virtuale per le macchine virtuali non appartenenti a un set di scalabilità.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Che cosa significa ComputeHR nel campo UsageType in Informazioni aggiuntive?
ComputeHR indica le ore di calcolo e rappresenta l'evento di utilizzo per i costi di infrastruttura sottostanti. Se UsageType è ComputeHR\_SW, l'evento di utilizzo rappresenta l'addebito per il software Premium per la macchina virtuale.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Come si può verificare se è stato effettuato un addebito per il software Premium?
Quando si Esplora l'immagine di macchina virtuale più adatta alle proprie esigenze, assicurarsi di consultare [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). L'immagine include il piano tariffario del software. Se viene visualizzato "Free" in corrispondenza della tariffa, non è previsto alcun costo aggiuntivo per il software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Qual è la differenza tra Microsoft.ClassicCompute e Microsoft.Compute nel Servizio utilizzato?
Microsoft.ClassicCompute rappresenta le risorse classiche distribuite tramite Azure Service Manager. Se si esegue la distribuzione tramite Gestione risorse, Microsoft.Compute viene popolato nel servizio utilizzato. Sono disponibili altre informazioni sui [modelli di distribuzione di Azure](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Perché il campo InstanceID è vuoto per l'utilizzo della macchina virtuale?
Se si esegue la distribuzione tramite il modello di distribuzione classica, la stringa InstanceID non è disponibile.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Perché nei dettagli di utilizzo non pervengono i tag per le macchine virtuali?
Nel file CSV relativo ai dati di utilizzo pervengono solo i tag per le macchine virtuali di Gestione risorse. I tag delle risorse classiche non sono disponibili nei dettagli di utilizzo.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Come può la quantità utilizzata essere superiore a 24 ore al giorno?
Nel modello classico la fatturazione per le risorse è aggregata a livello del servizio cloud. Se si dispone di più di una macchina virtuale in un servizio cloud che usa lo stesso metro di fatturazione, l'utilizzo è aggregato. Per le macchine virtuali distribuite tramite Gestione risorse è previsto un addebito a livello della macchina virtuale, pertanto questa aggregazione non verrà applicata.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Perché non sono disponibili i prezzi per le dimensioni DS/FS/GS/LS nella pagina relativa ai prezzi?
Per le macchine virtuali con archiviazione Premium è prevista la stessa tariffa di quelle che non dispongono di funzionalità di archiviazione Premium. Solo i costi di archiviazione sono differenti. Visitare la [pagina dei prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) per altre informazioni.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui dettagli di utilizzo, vedere [Comprendere la fattura per Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md).