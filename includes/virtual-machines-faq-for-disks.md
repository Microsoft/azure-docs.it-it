---
title: includere il file
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/31/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e1cf3905a34fdced878526cfcc55e6dd0a1a369f
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82595298"
---
Questo articolo risponde alle domande frequenti su Managed Disks e i dischi SSD Premium di Azure.

## <a name="managed-disks"></a>Managed Disks

**Che cos'è Azure Managed Disks?**

Managed Disks è una funzionalità che semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure, gestendo l'account di archiviazione per conto dell'utente. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Se si crea un disco gestito Standard da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco gestito Standard creato da un disco rigido virtuale da 80 GB viene considerato come il disco Standard immediatamente successivo in termini di dimensioni, ovvero un disco S10. Il costo addebitato corrisponde al prezzo del disco S10. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Sono previsti costi di transazione per i dischi gestiti Standard?**

Sì. Sì, ogni transazione prevede un addebito. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Per un disco gestito Standard, si riceverà un addebito per le dimensioni effettive dei dati su disco o per la capacità con provisioning del disco?**

L'addebito viene effettuato in base alla capacità con provisioning del disco. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**In che modo il prezzo della versione Premium dei dischi gestiti è diverso da quello dei dischi non gestiti?**

Il prezzo della versione Premium dei dischi gestiti è uguale a quello della versione Premium dei dischi non gestiti.

**È possibile modificare il tipo di account di archiviazione (Standard o Premium) dei dischi gestiti?**

Sì. È possibile modificare il tipo di account di archiviazione dei dischi gestiti tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito con una sottoscrizione diversa?**

Sì.

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito in un'area geografica diversa?**

No.

**Ci sono limitazioni di scalabilità per i clienti che usano dischi gestiti?**

Managed Disks elimina i limiti legati agli account di archiviazione. Tuttavia, il limite massimo è di 50.000 dischi gestiti per area e per tipo di disco per una sottoscrizione.

**Le macchine virtuali in un set di disponibilità possono essere composte da una combinazione di dischi gestiti e non gestiti?**

No. No, i dischi nelle macchine virtuali in un set di disponibilità devono essere o tutti gestiti o tutti non gestiti. Quando si crea un set di disponibilità, è possibile scegliere il tipo di dischi da usare.

**Managed Disks è l'opzione predefinita nel portale di Azure?**

Sì.

**È possibile creare un disco vuoto gestito?**

Sì. È possibile creare un disco vuoto. Un disco gestito può essere creato indipendentemente da una macchina virtuale, ad esempio non collegandolo a una macchina virtuale.

**Qual è il numero di domini di errore supportati per un set di disponibilità con Managed Disks?**

Il numero di domini di errore supportato per i set di disponibilità con Managed Disks è di 2 o 3, a seconda dell'area in cui si trovano.

**Come viene configurato l'account di archiviazione Standard per l'impostazione della diagnostica?**

Si imposta un account di archiviazione privato per la diagnostica della macchina virtuale.

**Che tipo di supporto per il controllo degli accessi in base al ruolo è disponibile per Managed Disks?**

Managed Disks supporta tre ruoli predefiniti principali:

* Proprietario: può gestire tutto, compresi gli accessi
* Collaboratore: può gestire tutto ad eccezione degli accessi
* Lettore: può visualizzare tutto, ma non apportare modifiche

**È possibile copiare o esportare un disco gestito in un account di archiviazione privato?**

È possibile generare un URI di firma di accesso condiviso (SAS) in sola lettura per il disco gestito e usarlo per copiare i contenuti in un account di archiviazione privato o in un archivio locale. È possibile usare l'URI SAS usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**È possibile creare una copia del proprio disco gestito?**

I clienti possono eseguire uno snapshot dei relativi dischi gestiti e usarlo per creare un altro disco gestito.

**I dischi non gestiti sono ancora supportati?**

Sì, sono supportati sia i dischi non gestiti che quelli gestiti. È consigliabile usare i dischi gestiti per i nuovi carichi di lavoro ed eseguire la migrazione dei carichi di lavoro correnti a dischi gestiti.

**È possibile condividere il percorso dei dischi gestiti e non gestiti nella stessa macchina virtuale?**

No.

**Se si crea un disco da 128 GB e si aumentano le dimensioni a 130 gibibyte (GiB), verranno addebitate le dimensioni del disco successive (256 GiB)?**

Sì.

**È possibile creare dischi gestiti per l'archiviazione con ridondanza locale, l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza della zona?**

Attualmente Azure Managed Disks supporta solo dischi gestiti per l'archiviazione con ridondanza locale.

**È possibile ridurre/ridimensionare i dischi gestiti?**

No. Questa funzionalità non è attualmente supportata.

**È possibile interrompe un lease sul disco?**

No. Questa funzionalità non è supportata attualmente in quanto è presente un lease per impedire l'eliminazione accidentale quando il disco è in uso.

**È possibile modificare la proprietà del nome del computer quando si usa un disco del sistema operativo specializzato (non preparato con Utilità preparazione sistema o generalizzato) per il provisioning di una VM?**

No. Non è possibile aggiornare la proprietà del nome del computer. La nuova VM eredita la proprietà dalla VM padre usata per creare il disco del sistema operativo. 

**Dove si possono trovare modelli di Azure Resource Manager di esempio per creare macchine virtuali con dischi gestiti?**
* [Elenco di modelli che usano Managed Disks](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Quando si crea un disco da un BLOB, è presente una relazione continua con il BLOB di origine?**

No, quando viene creato, il nuovo disco è una copia completamente autonoma del BLOB in quel determinato momento e non esiste alcuna connessione tra i due. Dopo aver creato il disco, è possibile eliminare il BLOB di origine senza modificare in alcun modo il disco appena creato.

**È possibile rinominare un disco gestito o non gestito dopo che è stato creato?**

Non è possibile rinominare i dischi gestiti. È tuttavia possibile rinominare un disco non gestito purché non sia collegato a una macchina virtuale o a un disco rigido virtuale.

**È possibile usare il partizionamento GPT in un disco di Azure?**

Le immagini di generazione 1 possono usare il partizionamento GPT solo sui dischi dati, non sui dischi del sistema operativo. I dischi del sistema operativo devono usare lo stile di partizione MBR.

Le [Immagini di generazione 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) possono usare il partizionamento GPT sul disco del sistema operativo e sui dischi dati.

**Quali tipi di dischi supportano gli snapshot?**

Snapshot di supporto SSD Premium, SSD standard e HDD standard. Per questi tre tipi di dischi, gli snapshot sono supportati per tutte le dimensioni dei dischi (inclusi i dischi con dimensioni fino a 32 TiB). I dischi Ultra non supportano gli snapshot.

**Che cosa sono le prenotazioni dischi di Azure?**
La prenotazione del disco è la possibilità di acquistare un anno di spazio di archiviazione su disco in anticipo, riducendo i costi totali. Per informazioni dettagliate sulle prenotazioni dischi di Azure, vedere l'articolo sull'argomento: [informazioni sul modo in cui viene applicato lo sconto per la prenotazione al disco di Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Quali sono le opzioni offerte dalla prenotazione dischi di Azure?**    
Prenotazione dischi di Azure offre la possibilità di acquistare SSD Premium negli SKU specificati da P30 (1 TiB) fino a P80 (32 TiB) per un periodo di validità di un anno. Non esiste alcuna limitazione sulla quantità minima di dischi necessaria per acquistare una prenotazione su disco. Inoltre, è possibile scegliere di pagare con un singolo pagamento iniziale o pagamenti mensili. Non è stato applicato alcun costo transazionale aggiuntivo per SSD Premium Managed Disks.    

Le prenotazioni vengono effettuate sotto forma di dischi, non di capacità. In altre parole, quando si riserva un disco P80 (32 TiB), si ottiene un singolo disco P80, non è quindi possibile dividere la prenotazione specifica in due dischi P70 (16 TiB) più piccoli. Naturalmente, è possibile riservare il numero di dischi desiderato, inclusi due dischi P70 (16 TiB) distinti.

**Come viene applicata la prenotazione dischi di Azure?**    
La prenotazione dischi segue un modello simile alle istanze di macchina virtuale riservate (VM). La differenza consiste nel fatto che non è possibile applicare una prenotazione disco a SKU diversi, mentre un'istanza di macchina virtuale può. Per altre informazioni sulle istanze di VM, vedere [risparmiare sui costi con le istanze di VM riservate di Azure](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) .     

**È possibile usare la risorsa di archiviazione dei dati acquistata tramite prenotazione dischi di Azure in più aree?**    
La prenotazione dei dischi di Azure viene acquistata per un'area e uno SKU specifici, ad esempio P30 in Stati Uniti orientali 2, e pertanto non può essere usata all'esterno di questi costrutti. È sempre possibile acquistare una prenotazione aggiuntiva di dischi di Azure per le esigenze di archiviazione su disco in altre aree o SKU.    

**Cosa accade quando la prenotazione di dischi di Azure scade?**    
Si riceveranno le notifiche tramite posta elettronica 30 giorni prima della scadenza e nuovamente alla data di scadenza. Al termine della prenotazione, i dischi distribuiti continueranno a essere eseguiti e verranno fatturati con le [tariffe con pagamento in base](https://azure.microsoft.com/pricing/details/managed-disks/)al consumo più recenti.

### <a name="azure-shared-disks"></a>Dischi condivisi di Azure

**La funzionalità dischi condivisi è supportata per i dischi non gestiti o i BLOB di pagine?**

No, è supportato solo per i dischi gestiti da unità SSD Premium.

**Quali aree supportano i dischi condivisi?**

Attualmente solo negli Stati Uniti centro-occidentali.

**I dischi condivisi possono essere usati come disco del sistema operativo?**

No, i dischi condivisi sono supportati solo per i dischi dati.

**Quali dimensioni dei dischi supportano i dischi condivisi?**

Solo le unità SSD Premium P15 o versioni successive supportano dischi condivisi.

**Se ho un'unità SSD Premium esistente, posso abilitarvi I dischi condivisi?**

Tutti i dischi gestiti creati con API versione 2019-07-01 o successiva possono abilitare i dischi condivisi. A tale scopo, è necessario smontare il disco da tutte le macchine virtuali a cui è collegato. Modificare quindi la `maxShares` proprietà sul disco.

**Se non si vuole più usare un disco in modalità condivisa, come si disabilita?**

Smontare il disco da tutte le macchine virtuali a cui è collegato. Modificare quindi la proprietà maxShare del disco su 1.

**È possibile ridimensionare un disco condiviso?**

Sì.

**È possibile abilitare l'acceleratore di scrittura su un disco in cui sono abilitati anche dischi condivisi?**

No.

**È possibile abilitare la memorizzazione nella cache dell'host per un disco in cui è abilitato il disco condiviso?**

L'unica opzione supportata per la memorizzazione nella cache dell'host è' none '.

## <a name="ultra-disks"></a>Dischi Ultra

**Per cosa è necessario impostare la velocità effettiva del disco Ultra?**
Se non si è certi di cosa impostare la velocità effettiva del disco, è consigliabile iniziare supponendo una dimensione di i/o di 16 KiB e regolare le prestazioni da tale posizione durante il monitoraggio dell'applicazione. La formula è: velocità effettiva in MBps = # di IOPS * 16/1000.

**Il disco è stato configurato per 40000 IOPS, ma vengono visualizzati solo 12800 IOPS, perché le prestazioni del disco non vengono visualizzati?**
Oltre alla limitazione del disco, viene applicata una limitazione di i/o a livello di macchina virtuale. Assicurarsi che le dimensioni della macchina virtuale in uso siano in grado di supportare i livelli configurati sui dischi. Per informazioni dettagliate sui limiti di i/o imposti dalla VM, vedere [dimensioni per le macchine virtuali Windows in Azure](../articles/virtual-machines/windows/sizes.md).

**È possibile usare I livelli di memorizzazione nella cache con un disco Ultra?**
No, i dischi Ultra non supportano i diversi metodi di Caching supportati in altri tipi di dischi. Impostare la memorizzazione nella cache del disco su nessuno.

**È possibile aggiungere un disco ultra alla macchina virtuale esistente?**
Probabilmente, la macchina virtuale deve trovarsi in una coppia area e zona di disponibilità che supporta dischi Ultra. Per informazioni dettagliate, vedere [Getting Started with ultra disks](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) .

**È possibile usare un disco Ultra come disco del sistema operativo per la macchina virtuale?**
No, i dischi Ultra sono supportati solo come dischi dati e sono supportati solo come dischi nativi 4K.

**È possibile convertire un disco esistente in un disco Ultra?**
No, ma è possibile eseguire la migrazione dei dati da un disco esistente a un disco Ultra. Per eseguire la migrazione di un disco esistente a un disco Ultra, alleghi entrambi i dischi alla stessa VM e copia i dati del disco da un disco all'altro o sfrutta una soluzione di terze parti per la migrazione dei dati.

**È possibile creare snapshot per i dischi ultra?**
No, gli snapshot non sono ancora disponibili.

**Backup di Azure è disponibile per i dischi ultra?**
No, il supporto di backup di Azure non è ancora disponibile.

**È possibile aggiungere un disco Ultra a una macchina virtuale in esecuzione in un set di disponibilità?**
No, questa operazione non è ancora supportata.

**È possibile abilitare Azure Site Recovery per le macchine virtuali con dischi ultra?**
No, Azure Site Recovery non è ancora supportata per i dischi Ultra.

## <a name="uploading-to-a-managed-disk"></a>Caricamento in un disco gestito

**È possibile caricare i dati in un disco gestito esistente?**

No, il caricamento può essere usato solo durante la creazione di un nuovo disco vuoto con lo stato **ReadyToUpload** .

**Ricerca per categorie caricare in un disco gestito?**

Creare un disco gestito con la proprietà [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) di [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) impostata su "upload", quindi è possibile caricarvi dati.

**È possibile allegare un disco a una macchina virtuale mentre è in stato di caricamento?**

No.

**È possibile creare uno snapshot di un disco modificato in uno stato di caricamento?**

No.

## <a name="standard-ssd-disks"></a>Dischi SSD Standard

**Cosa sono i dischi SSD Standard di Azure?**
I dischi SSD standard sono dischi standard supportati da unità SSD, ottimizzati come spazio di archiviazione conveniente per carichi di lavoro che richiedono prestazioni coerenti ai livelli di operazioni di I/O al secondo inferiori.

<a id="standard-ssds-azure-regions"></a>**Quali sono le aree attualmente supportate per i dischi SSD Standard?**
Tutte le aree di Azure supportano ora i dischi SSD Standard.

**Backup di Azure è disponibile quando si usano unità SSD Standard?**
Sì, Backup di Azure è ora disponibile.

**Come si creano i dischi SSD Standard?**
È possibile creare dischi SDD Standard usando Azure Resource Manager modelli, SDK, PowerShell o l'interfaccia della riga di comando. Di seguito sono elencati i parametri necessari nel modello di Resource Manager per creare dischi SSD Standard:

* *apiVersion* per Microsoft.Computer deve essere impostato su `2018-04-01` (o versioni successive)
* Specificare *managedDisk.storageAccountType* come `StandardSSD_LRS`

L'esempio seguente mostra la sezione *properties.storageProfile.osDisk* per una macchina virtuale che usa dischi SSD Standard:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Per un esempio di modello completo di come creare un disco SSD standard con un modello, vedere [Create a VM from a Windows Image with Standard SSD Data Disks](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/) (Creare una macchina virtuale da un'immagine Windows con dischi dati SSD standard).

**È possibile convertire i dischi esistenti in unità SSD Standard?**
Sì, Per le linee guida generali per la conversione di Azure Managed Disks fare riferimento a [Convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Usare anche il valore seguente per aggiornare il tipo di disco in SSD Standard.
-AccountType StandardSSD_LRS

**Quali sono i vantaggi offerti dall'uso dei dischi SSD Standard rispetto ai dischi HDD?**
I dischi SDD Standard offrono latenza, coerenza, disponibilità e affidabilità migliori rispetto ai dischi HDD. Per questo motivo, i carichi di lavoro delle applicazioni vengono eseguiti in modo molto più efficiente su unità SSD Standard. Si noti che i dischi SSD Premium rappresentano la soluzione consigliata per la maggior parte dei carichi di lavoro di produzione con I/O intensivo.

**Si possono usare dischi SSD Standard come dischi non gestiti?**
No, i dischi SSD Standard sono disponibili solo come dischi gestiti.

**I dischi SSD Standard supportano "contratti di servizio per macchine virtuali a istanza singola"?**
No, i dischi SSD Standard non supportano contratti di servizio per macchine virtuali a istanza singola. Per i contratti di servizio per macchine virtuali a istanza singola, usare dischi SSD Premium.

## <a name="migrate-to-managed-disks"></a>Eseguire la migrazione a Managed Disks

**Si verificano effetti della migrazione sulle prestazioni del Managed Disks?**

La migrazione comporta lo spostamento del disco da una posizione di archiviazione a un'altra. Questa operazione viene orchestrata tramite una copia in background dei dati, il cui completamento può richiedere diverse ore, in genere inferiore a 24 ore, in base alla quantità di dati nei dischi. Durante questo periodo, l'applicazione può riscontrare una latenza di lettura maggiore del solito poiché alcune operazioni di lettura possono essere reindirizzate alla posizione originale e possono quindi richiedere più tempo. Non è previsto alcun impatto sulla latenza di scrittura durante questo periodo.  

**Quali modifiche sono necessarie in una configurazione del servizio Backup di Azure preesistente prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**I backup di macchine virtuali creati tramite il servizio Backup di Azure prima della migrazione continueranno a funzionare correttamente?**

Sì, i backup continueranno a funzionare senza problemi.

**Quali modifiche sono necessarie in una configurazione preesistente di Crittografia dischi di Azure prima/dopo la migrazione a Managed Disks?**

Non sono necessarie modifiche.

**La migrazione automatica di un set di scalabilità di macchine virtuali esistente da dischi non gestiti a Managed Disks supportata?**

No. È possibile creare un nuovo set di scalabilità di macchine virtuali con Managed Disks usando l'immagine dal vecchio set di scalabilità con dischi non gestiti.

**È possibile creare un disco gestito da uno snapshot di BLOB di pagine eseguito prima della migrazione a Managed Disks?**

No. È possibile esportare uno snapshot di BLOB di pagine come BLOB di pagine e quindi creare un disco gestito dal BLOB di pagine esportato.

**È possibile eseguire il failover su macchine virtuali locali protette da Azure Site Recovery in una macchina virtuale con Managed Disks?**

Sì, è possibile scegliere di eseguire il failover su una macchina virtuale con Managed Disks.

**La migrazione su macchine virtuali di Azure protette da Azure Site Recovery tramite la replica da Azure ad Azure ha qualche ripercussione?**

No. È disponibile Azure Site Recovery la protezione da Azure ad Azure per le macchine virtuali con Managed Disks.

**È possibile eseguire la migrazione di macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks e crittografia del servizio di archiviazione

**La crittografia del servizio di archiviazione è abilitata per impostazione predefinita quando si crea un nuovo disco gestito?**

Sì.

**Il volume di avvio è crittografato per impostazione predefinita in un disco gestito?**

Sì. Per impostazione predefinita, tutti i dischi gestiti sono crittografati, incluso il disco del sistema operativo.

**chi gestisce le chiavi di crittografia?**

Le chiavi di crittografia sono gestite da Microsoft.

**È possibile disabilitare la crittografia del servizio di archiviazione per i dischi gestiti?**

No.

**La crittografia del servizio di archiviazione è disponibile solo in aree specifiche?**

No. È disponibile in tutte le aree in cui è disponibile Managed Disks. Managed Disks è disponibile in tutte le aree pubbliche e in Germania. È disponibile anche in Cina, ma solo per le chiavi gestite da Microsoft e non per quelle gestite dal cliente.

**Come si può determinare se un disco gestito è crittografato?**

Si può scoprire quando è stato creato il disco gestito tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Se è stato creato dopo il 9 giugno 2017, il disco è crittografato.

**Come è possibile crittografare i dischi esistenti creati prima del 10 giugno 2017?**

A partire dal 10 giugno 2017, i nuovi dati scritti nei dischi gestiti esistenti vengono crittografati automaticamente. È anche prevista l'implementazione della crittografia dei dati esistenti, che verrà eseguita in modo asincrono in background. Se è necessario crittografare i dati esistenti adesso, creare una copia del disco. I nuovi dischi verranno crittografati.

* [Copiare i dischi gestiti tramite l'interfaccia della riga di comando di Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copiare i dischi gestiti tramite PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Le immagini e gli snapshot gestiti vengono crittografati?**

Sì. Tutte le immagini e gli snapshot gestiti creati dopo il 9 giugno 2017 vengono crittografati automaticamente. 

**È possibile convertire macchine virtuali con dischi non gestiti ubicati in account di archiviazione che sono o sono stati crittografati in precedenza in VM con dischi gestiti?**

Sì

**Un disco rigido virtuale esportato da un disco gestito o uno snapshot verrà crittografato?**

No. Se però si esporta un disco rigido virtuale da un disco gestito o uno snapshot crittografato a un account di archiviazione crittografato, verrà crittografato. 

## <a name="premium-disks-managed-and-unmanaged"></a>Dischi Premium, gestiti e non gestiti

**Se una macchina virtuale usa una serie di dimensioni che supporta i dischi SSD Premium, ad esempio DSv2, è possibile collegare dischi dati sia Premium che Standard?** 

Sì.

**È possibile collegare dischi dati sia Premium che Standard a una serie di dimensioni che non supporta dischi SSD Premium, come le serie D, Dv2, G o F?**

No. È possibile collegare solo dischi dati Standard alle macchine virtuali che non usano una serie di dimensioni con supporto di dischi SSD Premium.

**Se si crea un disco dati Premium da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco dati Premium creato da un disco rigido virtuale da 80 GB viene considerato come il disco Premium immediatamente successivo in termini di dimensioni, ovvero un disco P10. Il costo addebitato corrisponde al prezzo del disco P10.

**Sono previsti costi per transazione per usare i dischi SSD Premium?**

È previsto un costo fisso per ogni dimensione di disco con provisioning di un certo numero di IOPS e di una certa velocità effettiva. Gli altri costi sono la larghezza di banda in uscita e la capacità di snapshot, se applicabile. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage).

**Quali sono i limiti per IOPS e velocità effettiva che è possibile ottenere dalla cache del disco?**

I limiti combinati per la cache e l'unità SSD locale per la serie DS sono 4.000 operazioni di I/O al secondo per core e 33 MiB al secondo per core. La serie GS offre 5,000 operazioni di I/O al secondo per core e 50 MiB al secondo per core.

**Sono supportate le unità SSD locali per le macchine virtuali di Managed Disks?**

L'unità SSD locale è un archivio temporaneo che è incluso in una macchina virtuale di Managed Disks. Questa archiviazione temporanea non comporta costi aggiuntivi. Si consiglia di non usare questa unità SSD locale per archiviare dati applicazione, perché non vengono salvati in modo permanente nell'archiviazione BLOB di Azure.

**L'uso di TRIM su dischi Premium ha ripercussioni?**

L'uso di TRIM su dischi Azure Premium o Standard non ha alcun impatto negativo.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Dimensioni dei nuovi dischi, gestiti e non gestiti

**Quali aree supportano la funzionalità di espansione per le dimensioni dei dischi SSD Premium applicabili?**

La funzionalità di espansione è attualmente supportata in tutte le aree del cloud pubblico di Azure e il supporto per i cloud sovrani sarà presto disponibile. 

**Quali aree sono le dimensioni del disco gestito 4/8/16 GiB (P1/P2/P3, E1/E2/E3) supportate in?**

Queste nuove dimensioni dei dischi sono attualmente supportate in tutte le aree del cloud pubblico di Azure, con supporto per i cloud sovrani presto disponibili. 

**Le dimensioni dei dischi P1/P2/P3 sono supportate per i dischi non gestiti o i BLOB di pagine?**

No, è supportato solo nei dischi gestiti da unità SSD Premium. 

**Le dimensioni dei dischi E1/E2/E3 sono supportate per i dischi non gestiti o i BLOB di pagine?**

No, non è possibile usare dischi gestiti SSD standard di qualsiasi dimensione con dischi non gestiti o BLOB di pagine.

**Qual è la dimensioni massima supportata per i dischi gestiti e il sistema operativo?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta un disco con dimensioni massime pari a 2 TiB. La dimensione massima supportata da Azure per un disco di sistema operativo è a 2 TiB. Azure supporta fino a 32 TiB per i dischi dati gestiti.

**Quali sono le dimensioni massime del disco non gestito supportate per il sistema operativo e i dischi dati?**

Il tipo di partizione supportata da Azure per un disco del sistema operativo è MBR (Master Boot Record). Il formato MBR supporta un disco con dimensioni massime pari a 2 TiB. La dimensione massima supportata da Azure per un disco non gestito del sistema operativo è di 2 TiB. Azure supporta fino a 4 TiB per i dischi non gestiti di dati.

**Quali sono le dimensioni massime supportate per un BLOB di pagine?**

Le dimensioni massime supportate da Azure per un BLOB di pagine sono di 8 TiB (8.191 GiB). Le dimensioni massime per un BLOB di pagine collegato a una macchina virtuale come dischi dati o dischi del sistema operativo sono di 4 TiB (4.095 GiB).

**È necessario usare una nuova versione degli strumenti di Azure per creare, collegare, ridimensionare e caricare dischi più grandi di 1 TiB?**

Non è necessario aggiornare gli strumenti di Azure esistenti per creare, collegare o ridimensionare i dischi di dimensioni superiori a 1 TiB. Per caricare il file VHD dall'ambiente locale direttamente in Azure come BLOB di pagine o disco non gestito, è necessario usare i set di strumenti più recenti, elencati di seguito: Sono supportati solo i caricamenti del disco rigido virtuale fino a 8 TiB.

|Strumenti di Azure      | Versioni supportate                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Numero di versione 4.1.0: versione di giugno 2017 o successiva|
|Interfaccia della riga di comando di Azure v1     | Numero di versione 0.10.13: versione di maggio 2017 o successiva|
|Interfaccia della riga di comando di Azure versione 2     | Numero di versione 2.0.12: versione di giugno 2017 o successiva|
|AzCopy              | Numero di versione 6.1.0: versione di giugno 2017 o successiva|

**Le dimensioni del disco P4 e P6 sono supportate per i dischi gestiti o i BLOB di pagine?**

Le dimensioni dei dischi P4 (32 GiB) e P6 (64 GiB) non sono supportate come livelli di disco predefinito per i dischi non gestiti e i BLOB di pagine. È necessario [impostare il livello di Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) in modo esplicito su P4 e P6 per eseguire il mapping su questi livelli. Se si distribuisce un disco non gestito o un BLOB di pagine con dimensioni del disco o lunghezza del contenuto inferiori a 32 GiB o tra 32 GiB a 64 GiB senza aver impostato il livello di blob, P10 continuerà con 500 IOPS e 100 MiB/secondo e persisterà il piano tariffario mappato.

**Come viene fatturato un disco gestito Premium di dimensioni inferiori a 64 GiB creato prima dell'abilitazione dei dischi più piccoli (intorno al 15 giugno 2017)?**

I dischi Premium esistenti di dimensioni inferiori a 64 GiB continuano a essere fatturati in base al piano tariffario P10.

**Secondo quali modalità è possibile modificare il piano tariffario dei dischi Premium di dimensioni inferiori a 64 GiB da P10 a P4 o P6?**

È possibile creare uno snapshot dei dischi di piccole dimensioni e quindi creare un disco per passare automaticamente al piano tariffario a P4 o P6 in base alla dimensione del disco di cui viene effettuato il provisioning.

**È possibile ridimensionare Managed Disks esistenti da dimensioni inferiori a 4 TB (TiB) a nuove dimensioni del disco appena introdotte fino a 32 TiB?**

Sì.

**Quali sono le dimensioni massime dei dischi supportate da backup di Azure e Azure Site Recovery servizio?**

Le dimensioni massime del disco supportate da backup di Azure sono 32 TiB (4 TiB per i dischi crittografati). La dimensione massima del disco supportata da Azure Site Recovery è 8 TiB. Il supporto per i dischi di dimensioni maggiori fino a 32 TiB non è ancora disponibile in Azure Site Recovery.

**Quali sono le dimensioni di macchina virtuale consigliate per le dimensioni dei dischi più grandi (>4 TiB) per i dischi SDD Standard e HDD Standard per ottenere una larghezza di banda e un numero di unità ottimizzate**

Per ottenere la velocità effettiva del disco di SDD Standard e HDD Standard grandi dimensioni dei dischi (>4 TiB) oltre 500 IOPS e 60 MiB/s, per ottimizzare le prestazioni, è consigliabile distribuire una nuova macchina virtuale da una delle dimensioni di macchina virtuale seguenti: serie B, serie DSv2, serie Dsv3, serie ESv3, serie FS, serie Fsv2, serie M, serie GS, NCv2, serie NCv3 o VM serie LS. Il montaggio di dischi di grandi dimensioni in macchine virtuali o macchine virtuali esistenti che non usano le dimensioni consigliate precedente può comportare prestazioni ridotte.

**Come è possibile aggiornare i dischi (>4 TiB) che sono stati distribuiti durante l'anteprima di dimensioni dei dischi più grandi per ottenere i valori di IOPS più elevati & larghezza di banda in GA?**

È possibile arrestare e avviare la macchina virtuale a cui è collegato il disco o, scollegare e ricollegare il disco. Gli obiettivi di prestazioni di dimensioni dei dischi più grandi sono stati aumentati sia per le unità SSD Premium sia per le SSD standard in GA.

**Quali aree sono le dimensioni dei dischi gestiti di 8 TiB, 16 TiB e 32 TiB supportati in?**

Gli SKU di dischi da 8 TiB, 16 TiB e 32 TiB sono supportati in tutte le aree in Azure globale, Microsoft Azure per enti pubblici e Azure Cina 21Vianet.

**È supportata l'abilitazione della memorizzazione nella cache dell'host su tutte le dimensioni dei dischi?**

Supporta la memorizzazione nella cache dell'host di ReadOnly e la lettura/scrittura su disco con dimensioni inferiori a 4 TiB. Per le dimensioni dei dischi superiori a 4 TiB, non è supportata l'impostazione di un'opzione di memorizzazione nella cache diversa da nessuna. È consigliabile sfruttare la memorizzazione nella cache per una dimensione disco inferiore in merito alla quale sarà possibile osservare l'incremento delle prestazioni con i dati memorizzati nella cache per la macchina virtuale.

## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?

Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto. È possibile pubblicare una domanda nei commenti alla fine di questo articolo. Per interagire con il team di Archiviazione di Azure e altri membri della community in merito a questo articolo, usare il [forum MSDN di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Per richiedere funzionalità, inviare richieste e idee al [forum dei commenti su Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage).
