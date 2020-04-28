---
title: Configurare il gruppo di disponibilità (modello di avvio rapido di Azure)
description: Usare i modelli di avvio rapido di Azure per creare il cluster di failover Windows, aggiungere SQL Server VM al cluster, creare il listener e configurare il servizio di bilanciamento del carico interno in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74022375"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Usare i modelli di avvio rapido di Azure per configurare un gruppo di disponibilità per SQL Server in una macchina virtuale di Azure
Questo articolo descrive come usare i modelli di avvio rapido di Azure per automatizzare parzialmente la distribuzione di una configurazione del gruppo di disponibilità Always On per le macchine virtuali SQL Server in Azure. In questo processo vengono usati due modelli di avvio rapido di Azure: 

   | Modello | Descrizione |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Consente di creare il cluster di failover di Windows e di aggiungervi le macchine virtuali SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crea il listener del gruppo di disponibilità e configura il servizio di bilanciamento del carico interno. Questo modello può essere usato solo se il cluster di failover di Windows è stato creato con il modello **101-SQL-VM-AG-Setup** . |
   | &nbsp; | &nbsp; |

Altre parti della configurazione del gruppo di disponibilità devono essere eseguite manualmente, ad esempio creando il gruppo di disponibilità e creando il servizio di bilanciamento del carico interno. Questo articolo illustra la sequenza dei passaggi automatizzati e manuali.
 

## <a name="prerequisites"></a>Prerequisiti 
Per automatizzare l'installazione di un gruppo di disponibilità Always On usando i modelli di avvio rapido, è necessario che siano soddisfatti i prerequisiti seguenti: 
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un controller di dominio. 
- Una o più macchine virtuali appartenenti a un dominio [in Azure che eseguono SQL Server 2016 (o versione successiva) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) presenti nello stesso set di disponibilità o nella stessa zona di disponibilità e [registrate con il provider di risorse della macchina virtuale SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Due indirizzi IP disponibili (non usati da nessuna entità): uno per il servizio di bilanciamento del carico interno e uno per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se viene usato un servizio di bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile.  

## <a name="permissions"></a>Autorizzazioni
Per configurare il gruppo di disponibilità Always On usando i modelli di avvio rapido di Azure, sono necessarie le autorizzazioni seguenti: 

- Account utente di dominio esistente che dispone dell'autorizzazione **Crea oggetto computer** nel dominio.  Un account amministratore di dominio, ad esempio, ha in genere un'autorizzazione sufficiente account@domain.com(ad esempio:). _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- Account utente di dominio che controlla il servizio SQL Server. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Passaggio 1: creare il cluster di failover e aggiungere SQL Server macchine virtuali al cluster usando un modello di avvio rapido 
Dopo aver registrato le VM di SQL Server con il provider di risorse VM SQL, è possibile aggiungere le VM SQL Server a *SqlVirtualMachineGroups*. Questa risorsa definisce i metadati del cluster di failover di Windows. I metadati includono la versione, l'edizione, il nome di dominio completo, Active Directory account per gestire sia il cluster sia il servizio SQL Server e l'account di archiviazione come cloud di controllo. 

L'aggiunta delle macchine virtuali di SQL Server al gruppo di risorse *SqlVirtualMachineGroups* esegue il bootstrap del servizio Cluster di failover di Windows per creare il cluster e quindi aggiunge le macchine virtuali di SQL Server a tale cluster. Questo passaggio è automatizzato con il modello di avvio rapido **101-SQL-VM-AG-Setup** . È possibile implementarla usando la procedura seguente:

1. Passare al modello di avvio rapido [**101-SQL-VM-AG-Setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) . Selezionare quindi **Distribuisci in Azure** per aprire il modello di avvio rapido nella portale di Azure.
1. Compilare i campi obbligatori per configurare i metadati per il cluster di failover di Windows. È possibile lasciare vuoti i campi facoltativi.

   Nella tabella seguente vengono illustrati i valori necessari per il modello: 

   | **Campo** | valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trovano le VM di SQL Server. |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server. | 
   |**Failover Cluster Name** (Nome cluster di failover) | Nome desiderato per il nuovo cluster di failover di Windows. |
   | **Existing Vm List** (Elenco VM esistenti) | Il SQL Server le macchine virtuali che si desidera includere nel gruppo di disponibilità e far parte del nuovo cluster. Separare questi valori con una virgola e uno spazio (ad esempio: *SQLVM1, SQLVM2*). |
   | **Versione SQL Server** | La versione SQL Server delle VM SQL Server. Selezionarlo dall'elenco a discesa. Attualmente sono supportate solo le immagini SQL Server 2016 e SQL Server 2017. |
   | **Existing Fully Qualified Domain Name** (Nome di dominio completo esistente) | FQDN esistente per il dominio in cui si trovano le VM di SQL Server. |
   | **Existing Domain Account** (Account di dominio esistente) | Durante la distribuzione del modello viene creato un account utente di dominio esistente con l'autorizzazione **Crea oggetto computer** nel dominio come [oggetto nome cluster](/windows-server/failover-clustering/prestage-cluster-adds) . Un account amministratore di dominio, ad esempio, ha in genere un'autorizzazione sufficiente account@domain.com(ad esempio:). *Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster.*| 
   | **Domain Account Password** (Password account di dominio) | Password dell'account utente di dominio indicato in precedenza. | 
   | **Existing Sql Service Account** (Account del servizio SQL Server esistente) | Account utente di dominio che controlla il [servizio SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante la distribuzione del gruppo di disponibilità ( account@domain.comad esempio,). |
   | **Sql Service Password** (Password del servizio SQL) | Password dell'account utente di dominio che controlla il servizio SQL Server. |
   | **Cloud Witness Name** (Nome cloud di controllo) | Un nuovo account di archiviazione di Azure che verrà creato e usato per il server di controllo del cloud. È possibile modificare questo nome. |
   | **\_Posizione artefatti** | Questo campo è impostato per impostazione predefinita e non deve essere modificato. |
   | **\_Token SAS percorso artefatti** | Questo campo viene intenzionalmente lasciato vuoto. |
   | &nbsp; | &nbsp; |

1. Se si accettano i termini e le condizioni, selezionare la casella di controllo Accetto **le condizioni indicate sopra** . Selezionare quindi **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a forma di campana delle **notifiche** nel banner di spostamento superiore o andare al **gruppo di risorse** nella portale di Azure. Selezionare **distribuzioni** in **Impostazioni**e scegliere la distribuzione **Microsoft. template** . 

>[!NOTE]
> Le credenziali specificate durante la distribuzione del modello vengono archiviate solo per la durata della distribuzione. Al termine della distribuzione, le password vengono rimosse. Se si aggiungono altre macchine virtuali SQL Server al cluster, verrà richiesto di fornirle nuovamente. 


## <a name="step-2-manually-create-the-availability-group"></a>Passaggio 2: creare manualmente il gruppo di disponibilità 
Creare manualmente il gruppo di disponibilità come si farebbe normalmente usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Non *creare un* listener in questo momento, perché il modello di avvio rapido **101-SQL-VM-aglistener-Setup** esegue questa operazione automaticamente nel passaggio 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Passaggio 3: creare manualmente il servizio di bilanciamento del carico interno
Il listener del gruppo di disponibilità Always On richiede un'istanza interna di Azure Load Balancer. Il servizio di bilanciamento del carico interno fornisce un indirizzo IP "mobile" per il listener del gruppo di disponibilità che consente un failover e una riconnessione più veloci. Se le macchine virtuali SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare un servizio di bilanciamento del carico di base. In caso contrario, è necessario usare un servizio di bilanciamento del carico standard. 

> [!IMPORTANT]
> Il servizio di bilanciamento del carico interno deve trovarsi nella stessa rete virtuale delle istanze di VM SQL Server. 

È sufficiente creare il servizio di bilanciamento del carico interno. Nel passaggio 4, il modello di avvio rapido **101-SQL-VM-aglistener-Setup** gestisce il resto della configurazione, ad esempio il pool back-end, il probe di integrità e le regole di bilanciamento del carico. 

1. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. 
2. Nel gruppo di risorse selezionare **Aggiungi**.
3. Cercare **servizio di bilanciamento del carico**. Nei risultati della ricerca selezionare **Load Balancer**, pubblicato da **Microsoft**.
4. Nel pannello **Load Balancer** selezionare **Crea**.
5. Bella finestra di dialogo **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | valore |
   | --- | --- |
   | **Nome** |Immettere un nome di testo che rappresenti il servizio di bilanciamento del carico. Ad esempio, immettere **sqlLB**. |
   | **Type** |**Interno**: la maggior parte delle implementazioni usano un servizio di bilanciamento del carico interno, che consente alle applicazioni all'interno della stessa rete virtuale di connettersi al gruppo di disponibilità.  </br> **Esterna**: consente alle applicazioni di connettersi al gruppo di disponibilità tramite una connessione Internet pubblica. |
   | **Rete virtuale** | Selezionare la rete virtuale in cui si trovano le istanze di SQL Server. |
   | **Subnet** | Selezionare la subnet in cui si trovano le istanze di SQL Server. |
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** | Specificare un indirizzo IP disponibile della subnet. |
   | **Sottoscrizione** |Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. Si tratta in genere della stessa sottoscrizione di tutte le risorse per il gruppo di disponibilità. |
   | **Gruppo di risorse** |Selezionare il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Posizione** |Selezionare il percorso di Azure in cui si trovano le istanze di SQL Server. |
   | &nbsp; | &nbsp; |

6. Selezionare **Create** (Crea). 


>[!IMPORTANT]
> La risorsa IP pubblico per ogni macchina virtuale SQL Server deve avere uno SKU standard per essere compatibile con il servizio di bilanciamento del carico standard. Per determinare lo SKU della risorsa IP pubblico della macchina virtuale, passare a **gruppo di risorse**, selezionare la risorsa **indirizzo IP pubblico** per la macchina virtuale SQL Server e individuare il valore in **SKU** nel riquadro **Overview (panoramica** ). 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Passaggio 4: creare il listener del gruppo di disponibilità e configurare il servizio di bilanciamento del carico interno usando il modello di avvio rapido

Creare il listener del gruppo di disponibilità e configurare automaticamente il servizio di bilanciamento del carico interno usando il modello di avvio rapido **101-SQL-VM-aglistener-Setup** . Il modello effettua il provisioning della risorsa Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups/oggetto AvailabilityGroupListener. Il modello di avvio rapido**101-sql-vm-aglistener-setup**, tramite il provider di risorse di macchine virtuali SQL, esegue le azioni seguenti:

- Crea una nuova risorsa IP front-end (in base al valore di indirizzo IP specificato durante la distribuzione) per il listener. 
- Configura le impostazioni di rete per il cluster e il servizio di bilanciamento del carico interno. 
- Configura il pool back-end per il servizio di bilanciamento del carico interno, il probe di integrità e le regole di bilanciamento del carico.
- Crea il listener del gruppo di disponibilità con l'indirizzo IP e il nome specificati.
 
>[!NOTE]
> È possibile usare **101-SQL-VM-aglistener-Setup** solo se il cluster di failover di Windows è stato creato con il modello **101-SQL-VM-AG-Setup** .
   
   
Per configurare il servizio di bilanciamento del carico interno e creare il listener del gruppo di disponibilità, eseguire le operazioni seguenti:
1. Passare al modello di avvio rapido [101-SQL-VM-aglistener-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selezionare **Distribuisci in Azure** per avviare il modello di avvio rapido nel portale di Azure.
1. Compilare i campi obbligatori per configurare il servizio di bilanciamento del carico interno e creare il listener del gruppo di disponibilità. È possibile lasciare vuoti i campi facoltativi. 

   Nella tabella seguente vengono illustrati i valori necessari per il modello: 

   | **Campo** | valore |
   | --- | --- |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server e il gruppo di disponibilità. | 
   |**Existing Failover Cluster Name** (Nome cluster di failover esistente) | Nome del cluster a cui sono aggiunte le VM di SQL Server. |
   | **Existing Sql Availability Group** (Gruppo di disponibilità SQL esistente)| Nome del gruppo di disponibilità di cui fanno parte le VM di SQL Server |
   | **Existing Vm List** (Elenco VM esistenti) | Nomi delle VM di SQL Server che fanno parte del gruppo di disponibilità indicato in precedenza. Separare i nomi con una virgola e uno spazio (ad esempio: *SQLVM1, SQLVM2*). |
   | **Listener** | Nome DNS che si desidera assegnare al listener. Per impostazione predefinita, questo modello specifica il nome "aglistener", ma è possibile modificarlo. Il nome non deve superare i 15 caratteri. |
   | **Listener Port** (Porta listener) | Porta che si desidera venga utilizzata dal listener. In genere, questa porta deve essere il valore predefinito 1433. Si tratta del numero di porta specificato dal modello. Tuttavia, se la porta predefinita è stata modificata, la porta del listener utilizzerà tale valore. | 
   | **Listener IP** (IP listener) | Indirizzo IP che si desidera venga utilizzato dal listener. Questo indirizzo verrà creato durante la distribuzione del modello, pertanto è necessario specificarne uno non già in uso.  |
   | **Existing Subnet** (Subnet esistente) | Nome della subnet interna delle macchine virtuali SQL Server (ad esempio: *impostazione predefinita*). È possibile determinare questo valore passando al **gruppo di risorse**, selezionando la rete virtuale, selezionando **Subnet** nel riquadro **Impostazioni** e copiando il valore in **nome**. |
   | **Existing Internal Load Balancer** (Servizio di bilanciamento del carico interno esistente) | Nome del servizio di bilanciamento del carico interno creato nel passaggio 3. |
   | **Porta probe** | Porta probe che deve essere usata dal servizio di bilanciamento del carico interno. Per impostazione predefinita, il modello USA 59999, ma è possibile modificare questo valore. |
   | &nbsp; | &nbsp; |

1. Se si accettano i termini e le condizioni, selezionare la casella di controllo Accetto **le condizioni indicate sopra** . Selezionare **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a forma di campana delle **notifiche** nel banner di spostamento superiore o andare al **gruppo di risorse** nella portale di Azure. Selezionare **distribuzioni** in **Impostazioni**e scegliere la distribuzione **Microsoft. template** . 

>[!NOTE]
>Se la distribuzione non riesce a metà, sarà necessario rimuovere manualmente [il listener appena creato](#remove-the-availability-group-listener) usando PowerShell prima di ridistribuire il modello di avvio rapido **101-SQL-VM-aglistener-Setup** . 

## <a name="remove-the-availability-group-listener"></a>Rimuovere il listener del gruppo di disponibilità
Se in un secondo momento è necessario rimuovere il listener del gruppo di disponibilità configurato dal modello, è necessario passare attraverso il provider di risorse della macchina virtuale SQL. Poiché il listener viene registrato tramite il provider di risorse VM SQL, è sufficiente eliminarlo tramite SQL Server Management Studio non è sufficiente. 

Il metodo migliore consiste nell'eliminarlo tramite il provider di risorse VM SQL usando il frammento di codice seguente in PowerShell. In questo modo i metadati del listener del gruppo di disponibilità vengono rimossi dal provider di risorse della macchina virtuale SQL. Elimina anche fisicamente il listener dal gruppo di disponibilità. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Errori comuni
Questa sezione descrive alcuni problemi noti e le possibili risoluzioni. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Il listener del gruppo di disponibilità per il gruppo di disponibilità '\<Nome-AG>' esiste già
Il gruppo di disponibilità selezionato usato nel modello di avvio rapido di Azure per il listener del gruppo di disponibilità contiene già un listener. È fisicamente all'interno del gruppo di disponibilità o i relativi metadati rimangono all'interno del provider di risorse della macchina virtuale SQL. Rimuovere il listener usando [PowerShell](#remove-the-availability-group-listener) prima di ridistribuire il modello di avvio rapido **101-SQL-VM-aglistener-Setup** . 

### <a name="connection-only-works-from-primary-replica"></a>La connessione funziona solo dalla replica primaria
Questo comportamento è probabilmente da una distribuzione del modello **101-SQL-VM-aglistener-Setup** non riuscita che ha lasciato la configurazione del servizio di bilanciamento del carico interno in uno stato incoerente. Verificare che il pool back-end elenchi il set di disponibilità e che esistano regole per il probe di integrità e per le regole di bilanciamento del carico. Se non è presente alcun elemento, la configurazione del servizio di bilanciamento del carico interno è uno stato incoerente. 

Per risolvere questo problema, rimuovere il listener usando [PowerShell](#remove-the-availability-group-listener), eliminare il servizio di bilanciamento del carico interno tramite il portale di Azure e iniziare di nuovo al passaggio 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>Richiesta non valida - È possibile aggiornare solo l'elenco di macchine virtuali SQL
Questo errore può verificarsi quando si distribuisce il modello **101-SQL-VM-aglistener-Setup** se il listener è stato eliminato tramite SQL Server Management Studio (SSMS), ma non è stato eliminato dal provider di risorse della macchina virtuale SQL. L'eliminazione del listener tramite SSMS non comporta la rimozione dei metadati del listener dal provider di risorse della macchina virtuale SQL. Il listener deve essere eliminato dal provider di risorse tramite [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>L'account di dominio non esiste
Questo errore può avere due cause. L'account di dominio specificato non esiste o mancano i dati del [nome dell'entità utente (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) . Il modello **101-SQL-VM-AG-Setup** prevede un account di dominio nel formato UPN (ovvero, *user@domain.com*), ma alcuni account di dominio potrebbero mancare. Questo problema si verifica in genere quando un utente locale è stato migrato in modo che sia il primo account amministratore di dominio quando il server è stato promosso a controller di dominio o quando un utente è stato creato tramite PowerShell. 

Verificare che l'account esista. In caso contrario, è possibile che si verifichi la seconda situazione. Per risolverlo, eseguire le operazioni seguenti:

1. Nel controller di dominio aprire la finestra **Utenti e computer di Active Directory** dall'opzione **Strumenti** in **Server Manager**. 
2. Passare all'account selezionando **utenti** nel riquadro sinistro.
3. Fare clic con il pulsante destro del mouse sull'account e scegliere **Proprietà**.
4. Selezionare la scheda **account** . Se la casella **nome di accesso utente** è vuota, questa è la fonte dell'errore. 

    ![Un account utente vuoto indica un UPN mancante](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Immettere il **nome di accesso dell'utente** in modo che corrisponda al nome dell'utente e selezionare il dominio appropriato nell'elenco a discesa. 
6. Selezionare **Applica** per salvare le modifiche e chiudere la finestra di dialogo selezionando **OK**. 

Dopo aver apportato queste modifiche, provare a distribuire di nuovo il modello di avvio rapido di Azure. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica delle macchine virtuali SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per le macchine virtuali SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per le macchine virtuali SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per le macchine virtuali SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md)



