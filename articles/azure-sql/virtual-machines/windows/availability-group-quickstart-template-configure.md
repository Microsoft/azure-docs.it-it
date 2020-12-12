---
title: Configurare il gruppo di disponibilità (modello di avvio rapido di Azure)
description: Usare i modelli di avvio rapido di Azure per creare il Cluster di failover di Windows, aggiungere a esso VM di SQL Server, creare il listener e configurare il bilanciamento del carico interno in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d7dfe010a3f4a1559454c49545af81eb14797bf1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359915"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Usare modelli di avvio rapido di Azure per configurare un gruppo di disponibilità per SQL Server in una macchina virtuale di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come usare i modelli di avvio rapido di Azure per automatizzare parzialmente la distribuzione di una configurazione di gruppo di disponibilità Always On per macchine virtuali di SQL Server in Azure. In questo processo vengono usati due modelli di avvio rapido di Azure: 

   | Modello | Descrizione |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Crea il cluster di failover di Windows e vi aggiunge le VM di SQL Server. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Crea il listener del gruppo di disponibilità e configura il bilanciamento del carico interno. Questo modello può essere usato solo se il cluster di failover Windows è stato creato con il modello **101-sql-vm-ag-setup**. |
   | &nbsp; | &nbsp; |

Altre parti della configurazione del gruppo di disponibilità devono essere eseguite manualmente, ad esempio la creazione del gruppo di disponibilità e del bilanciamento del carico interno. Questo articolo illustra la sequenza dei passaggi automatizzati e manuali.

Sebbene questo articolo usi i modelli di avvio rapido di Azure per configurare l'ambiente del gruppo di disponibilità, è anche possibile usare la [portale di Azure](availability-group-azure-portal-configure.md), [PowerShell o l'interfaccia della](availability-group-az-commandline-configure.md)riga di comando di Azure oppure [manualmente](availability-group-manually-configure-tutorial.md) . 
 

## <a name="prerequisites"></a>Prerequisiti 
Per automatizzare la configurazione di un gruppo di disponibilità Always On usando i modelli di avvio rapido sono necessari i prerequisiti seguenti: 
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un gruppo di risorse con un controller di dominio. 
- Una o più macchine virtuali appartenenti a un dominio [in Azure che eseguono SQL Server 2016 (o versione successiva) Enterprise Edition](./create-sql-vm-portal.md) presenti nello stesso set di disponibilità o nella stessa zona di disponibilità e [registrate con l'estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md).  
- Due indirizzi IP disponibili (non usati da alcuna entità): uno per il bilanciamento del carico interno e uno per il listener del gruppo di disponibilità all'interno della stessa subnet del gruppo di disponibilità. Se viene usato un bilanciamento del carico esistente, è necessario un solo indirizzo IP disponibile.  

## <a name="permissions"></a>Autorizzazioni
Per configurare il gruppo di disponibilità Always On usando i modelli di avvio rapido di Azure, sono necessarie le autorizzazioni seguenti: 

- Un account utente di dominio esistente con l'autorizzazione **Crea oggetti computer** nel dominio.  Un account amministratore di dominio ha in genere autorizzazioni sufficienti, ad esempio: account@domain.com. _Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster._
- L'account utente di dominio che controlla SQL Server. 


## <a name="create-cluster"></a>Creare cluster
Dopo aver registrato le VM SQL Server con l'estensione SQL IaaS Agent, è possibile aggiungere le VM SQL Server a *SqlVirtualMachineGroups*. Questa risorsa definisce i metadati del cluster di failover di Windows, tra cui versione, edizione, nome di dominio completo, account Active Directory per gestire sia il cluster sia l'istanza di SQL Server e l'account di archiviazione come cloud di controllo. 

L'aggiunta delle macchine virtuali di SQL Server al gruppo di risorse *SqlVirtualMachineGroups* esegue il bootstrap del servizio Cluster di failover di Windows per creare il cluster e quindi aggiunge le macchine virtuali di SQL Server a tale cluster. Questo passaggio viene automatizzato con il modello di avvio rapido **101-sql-vm-ag-setup**. È possibile procedere con l'implementazione seguendo questa procedura:

1. Andare al modello di avvio rapido [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup). Selezionare quindi **Distribuisci in Azure** per aprire il modello di avvio rapido nel portale di Azure.
1. Compilare i campi obbligatori per configurare i metadati del cluster di failover di Windows. È possibile lasciare vuoti i campi facoltativi.

   La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui si trovano le VM di SQL Server. |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server. | 
   |**Failover Cluster Name** (Nome cluster di failover) | Nome che si vuole usare per il nuovo cluster di failover di Windows. |
   | **Existing Vm List** (Elenco VM esistenti) | VM di SQL Server che si vuole includere nel gruppo di disponibilità e che faranno parte di questo nuovo cluster. Separare i valori con una virgola e uno spazio, ad esempio: *SQLVM1, SQLVM2*. |
   | **SQL Server Version** (Versione di SQL Server) | Versione di SQL Server delle VM di SQL Server, da selezionare nell'elenco a discesa. Solo le immagini di SQL Server 2016 e SQL Server 2017 sono attualmente supportate. |
   | **Existing Fully Qualified Domain Name** (Nome di dominio completo esistente) | FQDN esistente per il dominio in cui si trovano le VM di SQL Server. |
   | **Existing Domain Account** (Account di dominio esistente) | Un account utente di dominio esistente con l'autorizzazione **Crea oggetti computer** nel dominio quando viene creato il [CNO](/windows-server/failover-clustering/prestage-cluster-adds) durante la distribuzione del modello. Un account amministratore di dominio ha in genere autorizzazioni sufficienti, ad esempio: account@domain.com. *Questo account deve anche fare parte del gruppo degli amministratori locali in ogni macchina virtuale per creare il cluster.*| 
   | **Domain Account Password** (Password account di dominio) | Password dell'account utente di dominio indicato in precedenza. | 
   | **Existing Sql Service Account** (Account del servizio SQL Server esistente) | Account utente di dominio che controlla il [servizio SQL Server](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) durante la distribuzione del gruppo di disponibilità, ad esempio account@domain.com. |
   | **Sql Service Password** (Password del servizio SQL) | Password dell'account utente di dominio che controlla SQL Server. |
   | **Cloud Witness Name** (Nome cloud di controllo) | Nuovo account di archiviazione di Azure che verrà creato e usato per il cloud di controllo. È possibile modificare questo nome. |
   | **\_artifacts Location** (Posizione elementi) | Questo campo è impostato per impostazione predefinita e non deve essere modificato. |
   | **\_Artifacts Location SaS Token** (Token di firma di accesso condiviso posizione elementi) | Questo campo viene lasciato intenzionalmente vuoto. |
   | &nbsp; | &nbsp; |

1. Qualora si accettino le Condizioni, selezionare la casella di controllo **Accetto le condizioni riportate sopra**. Quindi selezionare **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a forma di campanello **Notifiche** nel banner di spostamento superiore o passare a **Gruppo di risorse** nel portale di Azure. Selezionare **Distribuzioni** in **Impostazioni** e scegliere la distribuzione **Microsoft.Template**. 

>[!NOTE]
> Le credenziali specificate durante la distribuzione del modello vengono archiviate solo per la durata della distribuzione. Al termine della distribuzione, le password vengono rimosse. Se si aggiungono altre VM di SQL Server al cluster, verrà richiesto di fornirle nuovamente. 



## <a name="validate-cluster"></a>Convalida cluster 

Affinché un cluster di failover sia supportato da Microsoft, deve superare la convalida del cluster. Connettersi alla macchina virtuale usando il metodo preferito, ad esempio Remote Desktop Protocol (RDP) e verificare che il cluster superi la convalida prima di procedere. In caso contrario, il cluster rimane in uno stato non supportato. 

È possibile convalidare il cluster usando Gestione cluster di failover (FCM) o il comando di PowerShell seguente:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>Creare un gruppo di disponibilità 
Creare manualmente il gruppo di disponibilità come di consueto, usando [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Non* creare un listener in questo momento perché non viene eseguito automaticamente dal modello di avvio rapido **101-sql-vm-aglistener-setup** nel passaggio 4. 

## <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Il listener del gruppo di disponibilità AlwaysOn richiede un'istanza interna di Azure Load Balancer. Il bilanciamento del carico interno specifica un indirizzo IP "mobile" per il listener del gruppo di disponibilità che consente la riconnessione e il failover più rapidi. Se le VM di SQL Server in un gruppo di disponibilità fanno parte dello stesso set di disponibilità, è possibile usare Load Balancer Basic. In caso contrario, è necessario usare Load Balancer Standard. 

> [!IMPORTANT]
> Il servizio di bilanciamento del carico interno deve trovarsi nella stessa rete virtuale delle istanze di VM SQL Server. 

È necessario creare solo il servizio di bilanciamento del carico interno. Nel passaggio 4 il modello di avvio rapido **101-sql-vm-aglistener-setup** gestisce il resto della configurazione, ad esempio il pool back-end, il probe di integrità e le regole di bilanciamento del carico. 

1. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. 
2. Nel gruppo di risorse selezionare **Aggiungi**.
3. Cercare **servizio di bilanciamento del carico**. Nei risultati della ricerca selezionare **Bilanciamento del carico**, pubblicato da **Microsoft**.
4. Nel pannello **Bilanciamento del carico** selezionare **Crea**.
5. Bella finestra di dialogo **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Immettere un nome di testo che rappresenti il bilanciamento del carico. Immettere ad esempio **sqlLB**. |
   | **Tipo** |**Interna**: La maggior parte delle implementazioni usa un servizio di bilanciamento del carico interno, che consente alle applicazioni all'interno della stessa rete virtuale di connettersi al gruppo di disponibilità.  </br> **Esterna**: consente alle applicazioni di connettersi al gruppo di disponibilità tramite una connessione Internet pubblica. |
   | **Rete virtuale** | Selezionare la rete virtuale in cui si trovano le istanze di SQL Server. |
   | **Subnet** | Selezionare la subnet in cui si trovano le istanze di SQL Server. |
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** | Specificare un indirizzo IP disponibile della subnet. |
   | **Sottoscrizione** |Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. In genere è la stessa sottoscrizione di tutte le risorse del gruppo di disponibilità. |
   | **Gruppo di risorse** |Selezionare il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Posizione** |Selezionare il percorso di Azure in cui si trovano le istanze di SQL Server. |
   | &nbsp; | &nbsp; |

6. Selezionare **Crea**. 


>[!IMPORTANT]
> La risorsa IP pubblico per ogni VM di SQL Server deve avere uno SKU Standard per essere compatibile con Load Balancer Standard. Per determinare lo SKU della risorsa IP pubblico della VM, andare a **Gruppo di risorse**, selezionare la risorsa **Indirizzo IP pubblico** per la VM di SQL Server e individuare il valore in **SKU**  nel pannello **Panoramica**. 

## <a name="create-listener"></a>Creare un listener 

Creare il listener del gruppo di disponibilità e configurare il servizio di bilanciamento del carico interno usando il modello di avvio rapido **101-sql-vm-aglistener-setup**. Il modello effettua il provisioning della risorsa Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Il modello di avvio rapido  **101-SQL-VM-aglistener-Setup** , tramite l'estensione SQL IaaS Agent, esegue le azioni seguenti:

- Crea una nuova risorsa IP front-end (in base al valore di indirizzo IP specificato durante la distribuzione) per il listener. 
- Configura le impostazioni di rete per il cluster e il servizio di bilanciamento del carico interno. 
- Configura il pool back-end per il servizio di bilanciamento del carico interno, il probe di integrità e le regole di bilanciamento del carico.
- Crea il listener del gruppo di disponibilità con il nome e l'indirizzo IP specificati.
 
>[!NOTE]
> È possibile usare il modello **101-sql-vm-aglistener-setup** solo se il cluster di failover Windows è stato creato con il modello **101-sql-vm-ag-setup**.
   
   
Per configurare il servizio di bilanciamento del carico interno e creare il listener del gruppo di disponibilità, seguire questa procedura:
1. Andare al modello di avvio rapido [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selezionare **Distribuisci in Azure** per avviare il modello di avvio rapido nel portale di Azure.
1. Compilare i campi obbligatori per configurare il servizio di bilanciamento del carico interno e creare il listener del gruppo di disponibilità. È possibile lasciare vuoti i campi facoltativi. 

   La tabella seguente mostra i valori necessari per il modello: 

   | **Campo** | valore |
   | --- | --- |
   |**Gruppo di risorse** | Gruppo di risorse in cui si trovano le VM di SQL Server e il gruppo di disponibilità. | 
   |**Existing Failover Cluster Name** (Nome cluster di failover esistente) | Nome del cluster a cui sono aggiunte le VM di SQL Server. |
   | **Existing Sql Availability Group** (Gruppo di disponibilità SQL esistente)| Nome del gruppo di disponibilità di cui fanno parte le VM di SQL Server |
   | **Existing Vm List** (Elenco VM esistenti) | Nomi delle VM di SQL Server che fanno parte del gruppo di disponibilità indicato in precedenza. Separare i nomi con una virgola e uno spazio, ad esempio: *SQLVM1, SQLVM2*. |
   | **Listener** | Nome DNS da assegnare al listener. Per impostazione predefinita, questo modello specifica il nome "aglistener", ma il valore può essere modificato. Il nome non deve superare i 15 caratteri. |
   | **Listener Port** (Porta listener) | Porta da usare per il listener. In genere, questa porta deve essere impostata sul valore predefinito 1433, ovvero il numero di porta specificato dal modello. Tuttavia, se la porta predefinita è stata modificata, è necessario impostare la porta del listener sul valore corrispondente. | 
   | **Listener IP** (IP listener) | Indirizzo IP che si vuole usare per il listener. Questo indirizzo verrà creato durante la distribuzione del modello, quindi specificarne uno che non è già in uso.  |
   | **Existing Subnet** (Subnet esistente) | Nome della subnet interna delle VM di SQL Server, ad esempio: *default*. È possibile determinare questo valore andando a **Gruppo di risorse**, selezionando la rete virtuale, selezionando **Subnet** nel riquadro **Impostazioni** e quindi copiando il valore in **Nome**. |
   | **Existing Internal Load Balancer** (Servizio di bilanciamento del carico interno esistente) | Nome del servizio di bilanciamento del carico interno creato nel passaggio 3. |
   | **Porta probe** | Porta probe che deve essere usata dal servizio di bilanciamento del carico interno. Il modello usa 59999 per impostazione predefinita, ma è possibile modificare questo valore. |
   | &nbsp; | &nbsp; |

1. Qualora si accettino le Condizioni, selezionare la casella di controllo **Accetto le condizioni riportate sopra**. Selezionare **Acquista** per completare la distribuzione del modello di avvio rapido. 
1. Per monitorare la distribuzione, selezionare la distribuzione dall'icona a forma di campanello **Notifiche** nel banner di spostamento superiore o passare a **Gruppo di risorse** nel portale di Azure. Selezionare **Distribuzioni** in **Impostazioni** e scegliere la distribuzione **Microsoft.Template**. 

>[!NOTE]
>Se la distribuzione si interrompe a metà, sarà necessario [rimuovere il listener appena creato](#remove-listener) manualmente tramite PowerShell prima di ridistribuire il modello di avvio rapido **101-sql-vm-aglistener-setup**. 

## <a name="remove-listener"></a>Rimuovere un listener
Se in un secondo momento è necessario rimuovere il listener del gruppo di disponibilità configurato dal modello, è necessario passare attraverso l'estensione SQL IaaS Agent. Poiché il listener viene registrato tramite l'estensione SQL IaaS Agent, è sufficiente eliminarlo tramite SQL Server Management Studio non è sufficiente. 

Il metodo migliore consiste nell'eliminarlo tramite l'estensione SQL IaaS Agent usando il frammento di codice seguente in PowerShell. Questa operazione rimuove i metadati del listener del gruppo di disponibilità dall'estensione SQL IaaS Agent. ed elimina fisicamente il listener dal gruppo di disponibilità. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Errori comuni
Questa sezione descrive alcuni problemi noti e le possibili risoluzioni. 

Il **listener del gruppo di disponibilità per il gruppo di disponibilità' \<AG-Name> ' esiste già** . il gruppo di disponibilità selezionato usato nel modello di avvio rapido di Azure per il listener del gruppo di disponibilità contiene già un listener. È fisicamente all'interno del gruppo di disponibilità o i relativi metadati rimangono nell'estensione SQL IaaS Agent. Rimuovere il listener usando [PowerShell](#remove-listener) prima di ridistribuire il modello di avvio rapido **101-sql-vm-aglistener-setup**. 

La **connessione funziona solo dalla replica primaria** Questo comportamento è probabilmente da una distribuzione del modello **101-SQL-VM-aglistener-Setup** non riuscita che ha lasciato la configurazione del servizio di bilanciamento del carico interno in uno stato incoerente. Verificare che il pool back-end elenchi il set di disponibilità e che esistano regole per il probe di integrità e per le regole di bilanciamento del carico. Se non è presente alcun elemento, la configurazione del servizio di bilanciamento del carico interno è uno stato incoerente. 

Per risolvere questo problema, rimuovere il listener usando [PowerShell](#remove-listener), eliminare il servizio di bilanciamento del carico interno tramite il portale di Azure e ricominciare dal Passaggio 3. 

**Richiesta non valida: è possibile aggiornare solo l'elenco di macchine virtuali SQL** Questo errore può verificarsi quando si distribuisce il modello **101-SQL-VM-aglistener-Setup** se il listener è stato eliminato tramite SQL Server Management Studio (SSMS), ma non è stato eliminato dall'estensione SQL IaaS Agent. L'eliminazione del listener tramite SSMS non comporta la rimozione dei metadati del listener dall'estensione SQL IaaS Agent. Il listener deve essere eliminato dal provider di risorse tramite [PowerShell](#remove-listener). 

L' **account di dominio non esiste** Questo errore può avere due cause. L'account di dominio specificato non esiste o mancano i dati del [nome dell'entità utente (UPN)](/windows/desktop/ad/naming-properties#userprincipalname). Il modello **101-sql-vm-ag-setup** prevede un account di dominio nel formato UPN, ad esempio user@domain.com, ma potrebbe mancare in alcuni account di dominio. Ciò si verifica in genere quando non viene eseguita la migrazione di un utente locale come primo account di amministratore di dominio quando il server viene alzato di livello a controller di dominio o quando un utente viene creato tramite PowerShell. 

Verificare che l'account esista. Se esiste, potrebbe trattarsi del secondo caso. Per risolvere il problema, seguire questa procedura:

1. Nel controller di dominio aprire la finestra **Utenti e computer di Active Directory** dall'opzione **Strumenti** in **Server Manager**. 
2. Andare all'account selezionando **Utenti** nel riquadro sinistro.
3. Fare clic con il pulsante destro del mouse sull'account e selezionare **Proprietà**.
4. Fare clic sulla scheda **Account**. Se la casella **Nome accesso utente** è vuota, questo è il motivo dell'errore. 

    ![Un account utente vuoto indica un UPN mancante](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. Compilare la casella **Nome accesso utente** corrispondente al nome dell'utente e quindi selezionare il dominio appropriato nell'elenco a discesa. 
6. Selezionare **Applica** per salvare le modifiche e chiudere la finestra di dialogo selezionando **OK**. 

Dopo aver apportato queste modifiche, provare a distribuire il modello di avvio rapido di Azure ancora una volta. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica delle VM di SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti sulle macchine virtuali di SQL Server](frequently-asked-questions-faq.md)
* [Guida ai prezzi per le macchine virtuali di SQL Server](pricing-guidance.md)
* [Note sulla versione delle le macchine virtuali di SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](licensing-model-azure-hybrid-benefit-ahb-change.md)