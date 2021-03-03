---
title: Panoramica delle macchine virtuali Linux in Azure
description: Panoramica delle macchine virtuali Linux in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4067fc4e47dce83852af971daae6cda3933d0e39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667177"
---
# <a name="linux-virtual-machines-in-azure"></a>Macchine virtuali Linux in Azure

Le macchine virtuali (VM) di Azure sono uno dei vari tipi di [risorse di calcolo scalabili e su richiesta](/azure/architecture/guide/technology-choices/compute-decision-tree) offerte da Azure. In genere, la scelta ricade su una VM se è necessario maggiore controllo dell'ambiente di calcolo rispetto a quanto offerto dalle altre soluzioni. Questo articolo fornisce informazioni sugli aspetti da tenere in considerazione prima di creare una VM e sulla relativa modalità di creazione e gestione.

Una VM di Azure offre la flessibilità della virtualizzazione senza dover acquistare e gestire l'hardware fisico su cui è in esecuzione la macchina virtuale. È comunque necessario gestire la VM eseguendo determinate attività, ovvero configurazione, applicazione di patch e installazione del software in esecuzione sulla macchina virtuale.

È possibile usare le macchine virtuali di Azure in vari modi. Ad esempio:

* **Sviluppo e test**: le VM di Azure consentono di creare in modo semplice e rapido un computer con configurazioni specifiche necessarie per scrivere il codice e testare un'applicazione.
* **Applicazioni nel cloud**: dal momento che le richieste per l'applicazione possono variare, potrebbe essere conveniente eseguirla su una VM in Azure. È possibile pagare per VM aggiuntive quando sono necessarie e arrestarle quando non sono richieste.
* **Data center avanzato**: le macchine virtuali in una rete virtuale di Azure possono essere facilmente collegate alla rete dell'organizzazione.

È possibile aumentare o ridurre il numero di VM usate dall'applicazione in base alle proprie esigenze.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Aspetti da tenere in considerazione prima della creazione di una VM
Quando si compila l'infrastruttura di un'applicazione in Azure, ci sono sempre numerose [considerazioni di progettazione](/azure/architecture/reference-architectures/n-tier/linux-vm) di cui tener conto. Prima di iniziare, è quindi importante analizzare gli aspetti seguenti di una VM:

* Nomi delle risorse dell'applicazione
* Posizione in cui sono archiviate le risorse
* Dimensioni della VM
* Numero massimo di VM che è possibile creare
* Sistema operativo in esecuzione sulla VM
* Configurazione della VM dopo l'avvio
* Risorse correlate richieste dalla VM

### <a name="locations"></a>Percorsi
Le risorse create in Azure vengono distribuite tra più [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. In genere, quando si crea una VM l'area viene chiamata **località**. Per una VM, la località specifica dove sono archiviati i dischi rigidi virtuali.

Questa tabella illustra alcuni dei metodi con cui è possibile ottenere un elenco di località disponibili.

| Metodo | Descrizione |
| --- | --- |
| Portale di Azure |Quando si crea una VM, selezionare una località nell'elenco. |
| Azure PowerShell |Usare il comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| API REST |Usare l'operazione [List locations](/rest/api/resources/subscriptions). |
| Interfaccia della riga di comando di Azure |Usare l'operazione [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Disponibilità
È stato annunciato un contratto di servizio leader del settore pari al 99,9% per una macchina virtuale a istanza singola, purché distribuita con Archiviazione Premium per tutti i dischi.  Perché la distribuzione si qualifichi per il contratto di servizio standard del 99,95% per le macchine virtuali, è comunque necessario distribuire due o più macchine virtuali che eseguono il carico di lavoro in un set di disponibilità. Un set di disponibilità assicura che le macchine virtuali vengano distribuite tra più domini di errore nei centri dati Azure e anche in host con finestre di manutenzione diverse. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

## <a name="vm-size"></a>Dimensioni macchina virtuale
Le [dimensioni](../sizes.md) della VM usata sono determinate dal carico di lavoro che si desidera eseguire. Le dimensioni scelte determinano quindi fattori quali potenza di elaborazione, memoria e capacità di archiviazione. Azure offre una vasta gamma di dimensioni per supportare molti tipi di uso.

Azure addebita un [costo orario](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) in base alla dimensione della VM e al sistema operativo. Per le ore parziali, Azure addebita il costo solo dei minuti usati. I costi di archiviazione vengono determinati e addebitati separatamente.

## <a name="vm-limits"></a>Limiti della VM
Ogni sottoscrizione di Azure ha [limiti di quota](../../azure-resource-manager/management/azure-subscription-service-limits.md) predefiniti che possono influire sulla distribuzione di molte VM per un progetto. Il limite corrente per ogni sottoscrizione è di 20 macchine virtuali per area. I limiti possono essere aumentati [creando un ticket di supporto in cui si richiede tale incremento](../../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="managed-disks"></a>Managed Disks

Il servizio Managed Disks gestisce in background le operazioni di creazione e gestione dell'account di archiviazione di Azure ed elimina la necessità di preoccuparsi dei limiti di scalabilità dell'account di archiviazione. Specificare le dimensioni del disco e il livello di prestazioni, Standard o Premium, e Azure si occuperà della creazione e della gestione del disco. Durante l'aggiunta di dischi o il ridimensionamento della macchina virtuale non è necessario preoccuparsi dello spazio di archiviazione usato. Se si creano nuove macchine virtuali, [usare l'interfaccia della riga di comando di Azure](quick-create-cli.md) o il portale di Azure per creare macchine virtuali con dischi dati e del sistema operativo gestiti. Se si dispone di macchine virtuali con dischi non gestiti, è possibile [convertire le macchine virtuali per eseguire il backup con Managed Disks](convert-unmanaged-to-managed-disks.md).

È anche possibile gestire le immagini personalizzate in un unico account di archiviazione per ogni area di Azure e usarle per creare centinaia di macchine virtuali nella stessa sottoscrizione. Per altre informazioni sui dischi gestiti, vedere [Azure Managed Disks overview](../managed-disks-overview.md) (Panoramica di Azure Managed Disks).

## <a name="distributions"></a>Distribuzioni 
Microsoft Azure supporta l'esecuzione di numerose distribuzioni comuni di Linux fornite e gestite da diversi partner.  È possibile trovare le distribuzioni disponibili in Azure Marketplace. Microsoft collabora attivamente con diverse community Linux per aggiungere altre versioni all'elenco delle [distribuzioni Linux approvate per Azure](endorsed-distros.md).

Se la distribuzione di Linux preferita non è attualmente presente nella raccolta, è possibile usare una VM "Bring your own Linux" [creando e caricando un VHD Linux in Azure](create-upload-generic.md).

Microsoft collabora con i partner per verificare che le immagini disponibili vengano aggiornate e ottimizzate per un runtime Azure.  Per altre informazioni sulle offerte dei partner di Azure, vedere i collegamenti seguenti:

* Linux in Azure - [Distribuzioni supportate](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Canonical - [Azure Marketplace - Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian - [Azure Marketplace - Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD - [Azure Marketplace - FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar - [Azure Marketplace - Flatcar Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS - [Azure Marketplace - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker - [Azure Marketplace - Immagini Docker](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>cloud-init 

Per ottenere le impostazioni cultura DevOps corrette, l'intera infrastruttura deve essere contenuta nel codice.  Quando l'intera infrastruttura è presente nel codice, può essere facilmente ricreata.  Azure funziona con gli strumenti di automazione principali, come Ansible, Chef, SaltStack e Puppet.  Azure dispone di strumenti di automazione propri:

* [Modelli di Azure](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure supporta [cloud-init](https://cloud-init.io/) nella maggior parte delle distribuzioni Linux che lo supportano.  Microsoft sta collaborando attivamente con i partner di distribuzione Linux approvati per offrire immagini abilitate per cloud-init in Azure Marketplace. Queste immagini permettono il funzionamento uniforme di distribuzioni e configurazioni di cloud-init con macchine virtuali e set di scalabilità di macchine virtuali.

* [Uso di cloud-init nelle macchine virtuali Linux di Azure](using-cloud-init.md)

## <a name="storage"></a>Archiviazione
* [Introduzione ad Archiviazione di Microsoft Azure](../../storage/common/storage-introduction.md)
* [Aggiungere un disco a una VM Linux tramite l'interfaccia della riga di comando di Azure](add-disk.md)
* [Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure](attach-disk-portal.md)

## <a name="networking"></a>Rete
* [Panoramica di Rete virtuale.](../../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../../virtual-network/public-ip-addresses.md)
* [Apertura di porte per una VM Linux in Azure](nsg-quickstart.md)
* [Creare un nome di dominio completo nel portale di Azure](../create-fqdn.md)


## <a name="data-residency"></a>Residenza dei dati

In Azure la funzionalità per abilitare l'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sud-orientale (Singapore) dell'area geografica Asia Pacifico e nell'area Brasile meridionale (stato di San Paolo) dell'area geografica Brasile. Per tutte le altre aree i dati dei clienti vengono archiviati in Geo. Per altre informazioni, visitare il [Centro protezione](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Passaggi successivi

Creare la prima VM

- [Portale](quick-create-portal.md)
- [Interfaccia della riga di comando di Azure](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
