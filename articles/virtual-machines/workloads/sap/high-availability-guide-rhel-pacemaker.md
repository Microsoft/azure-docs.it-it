---
title: Configurazione di Pacemaker in RHEL con Azure | Microsoft Docs
description: Configurazione di Pacemaker in Red Hat Enterprise Linux in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: b111dae035e7a055628642fe7c460734199ff608
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486343"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Configurazione di Pacemaker in Red Hat Enterprise Linux in Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Leggere prima di tutto i documenti e le note SAP seguenti:

* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione di software SAP.
  * Informazioni importanti sulla capacità per le dimensioni delle macchine virtuali di Azure.
  * Software SAP e combinazioni di sistemi operativi e database supportati.
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure.
* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2002167] indica le impostazioni del sistema operativo consigliate per Red Hat Enterprise Linux
* La nota SAP [2009879] contiene le linee guida di SAP HANA per Red Hat Enterprise Linux
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Microsoft Azure per SAP in Linux (questo articolo)][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101) (Replica di sistema SAP HANA nel cluster Pacemaker)
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Criteri di supporto per il cluster RHEL a disponibilità elevata - sbd e fence_sbd](https://access.redhat.com/articles/2800691)
* Documentazione di RHEL specifica di Azure:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341) (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491) (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
  * [Considerazioni sull'adozione di RHEL 8-disponibilità elevata e cluster](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [Configurare SAP S/4HANA ASCS/ERS con il Server di accodamento autonomo 2 (ENSA2) in Pacemaker in RHEL 7.6](https://access.redhat.com/articles/3974941)
  * [RHEL per le offerte SAP in Azure](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Installazione del cluster

![Panoramica di Pacemaker in RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat non supporta il watchdog emulato da software. Red Hat non supporta SBD sulle piattaforme cloud. Per informazioni dettagliate, vedere [Criteri di supporto per il cluster RHEL a disponibilità elevata - sbd e fence_sbd](https://access.redhat.com/articles/2800691).
> L'unico meccanismo di isolamento supportato per i cluster basati sul Pacemaker di Red Hat Enterprise Linux in Azure è l'agente di isolamento di Azure.  

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]** registro. Questo passaggio non è necessario se si usano immagini RHEL abilitate per la disponibilità elevata di SAP.  

   Registrare le macchine virtuali e allegarle a un pool che contiene i repository per RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Se si connette un pool a un'immagine RHEL di Azure Marketplace PAYG, l'uso di RHEL verrà fatturato in modo efficace: una volta per l'immagine PAYG e una volta per il diritto RHEL nel pool collegato. Per attenuare questo problema, Azure offre ora immagini RHEL BYOS. Altre informazioni sono disponibili [qui](../redhat/byos.md).  

1. **[A]** abilitare RHEL per i repository SAP. Questo passaggio non è necessario se si usano immagini RHEL abilitate per la disponibilità elevata di SAP.  

   Per installare i pacchetti necessari, abilitare i repository seguenti.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Installare il componente aggiuntivo RHEL a disponibilità elevata

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Si consiglia di usare le seguenti versioni dell'agente di isolamento di Azure (o versione successiva) per i clienti per trarre vantaggio dalla riduzione del tempo di failover, in caso di errore di una risorsa o se i nodi del cluster non sono più in grado di comunicare tra loro:  
   > RHEL 7,7 o versione successiva usare la versione disponibile più recente del pacchetto degli agenti di recinzione  
   > RHEL 7.6: fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5: fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4: fence-agents-4.0.11-66.el7_4.12  
   > Per altre informazioni, vedere la pagina relativa all'[esecuzione della macchina virtuale di Azure come membro del cluster RHEL a disponibilità elevata in cui la delimitazione richiede molto tempo oppure non riesce o viene messa in timeout prima che la macchina virtuale si arresti](https://access.redhat.com/solutions/3408711).

   Controllare la versione dell'agente di isolamento di Azure. Se necessario, aggiornarlo in una versione uguale o successiva a quella indicata sopra.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Se è necessario aggiornare l'agente di isolamento di Azure e, se si usa un ruolo personalizzato, assicurarsi di aggiornare il ruolo personalizzato in modo da includere l'azione **Spegnimento**. Per informazioni, vedere [Creare un ruolo personalizzato per l'agente di isolamento](#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Configurare la risoluzione dei nomi host

   È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti.  

   >[!IMPORTANT]
   > Se si usano nomi host nella configurazione del cluster, è fondamentale avere una risoluzione dei nomi host affidabile. La comunicazione del cluster avrà esito negativo, se i nomi non sono disponibili e ciò può causare ritardi del failover del cluster.
   > Il vantaggio di usare /etc/hosts è che il cluster diventa indipendente dal DNS, che potrebbe essere un singolo punto di guasto.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Modificare la password hacluster in modo da usare la stessa password

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Aggiungere regole di firewall per Pacemaker

   Aggiungere le seguenti regole del firewall per tutte le comunicazioni del cluster tra i nodi del cluster.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Abilitare i servizi cluster di base

   Eseguire i comandi seguenti per abilitare il servizio Pacemaker e avviarlo.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Creare un cluster Pacemaker

   Eseguire i comandi seguenti per autenticare i nodi e creare il cluster. Impostare il token su 30000 per consentire la manutenzione con mantenimento della memoria. Per altre informazioni, vedere [questo articolo per Linux][virtual-machines-linux-maintenance].  
   
   Se si compila un cluster in **RHEL 7. x**, usare i comandi seguenti:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Se si compila un cluster in **RHEL 8. X**, usare i comandi seguenti:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Verificare lo stato del cluster eseguendo il comando seguente:  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** impostare i voti previsti. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Se si compila un cluster a più nodi, ovvero un cluster con più di due nodi, non impostare i voti su 2.    

1. **[1]** Consenti azioni di recinzione simultanee

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>Creare il dispositivo STONITH

Il dispositivo STONITH usa un'entità servizio per l'autorizzazione in Microsoft Azure. Per creare un'entità servizio, seguire questa procedura.

1. Passare a <https://portal.azure.com>.
1. Aprire il pannello Azure Active Directory  
   Passare a proprietà e prendere nota dell'ID directory. Si tratta dell'**ID tenant**.
1. Fare clic su Registrazioni per l'app
1. Fare clic su Nuova registrazione
1. Immettere un nome, selezionare "Account solo in questa directory dell'organizzazione" 
2. Selezionare il tipo di applicazione "Web", immettere un URL di accesso (ad esempio http:\//localhost) e fare clic su Aggiungi  
   L'URL di accesso non viene usato e può essere qualsiasi URL valido
1. Selezionare Certificati e segreti, quindi fare clic su Nuovo segreto client
1. Immettere la descrizione per una nuova chiave, selezionare "Non scade mai" e fare clic su Aggiungi
1. Rendere un nodo il valore. Viene usato come **password** per l'entità servizio
1. Selezionare Panoramica. Prendere nota dell'ID applicazione. Viene usato come nome utente (**ID di accesso** nella procedura seguente) dell'entità servizio

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Creare un ruolo personalizzato per l'agente di isolamento

L'entità servizio non ha le autorizzazioni per accedere alle risorse di Azure per impostazione predefinita. È necessario concedere all'entità servizio le autorizzazioni per avviare e arrestare (spegnimento) tutte le macchine virtuali del cluster. Se il ruolo personalizzato non è già stato creato, è possibile farlo usando [PowerShell](../../../role-based-access-control/role-assignments-powershell.md) o l'[interfaccia della riga di comando di Azure](../../../role-based-access-control/role-assignments-cli.md).

Per il file di input usare il contenuto seguente. Tale contenuto deve essere adattato alle sottoscrizioni, ovvero è necessario sostituire c276fc76-9cd4-44c9-99a7-4fd71546436e ed e91d47c4-76f3-4271-a796-21b4ecfe3624 con gli ID delle sottoscrizioni. Se si ha una sola sottoscrizione, rimuovere la seconda voce in AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Assegnare il ruolo personalizzato all'entità servizio

Assegnare all'entità servizio il ruolo personalizzato "Linux Fence Agent Role" creato nel capitolo precedente. Non usare più il ruolo Owner.

1. Passare a https://portal.azure.com.
1. Aprire il pannello Tutte le risorse
1. Selezionare la macchina virtuale del primo nodo del cluster.
1. Fare clic su Controllo di accesso (IAM)
1. Fare clic su Aggiungi assegnazione ruolo
1. Selezionare il ruolo "Linux Fence Agent Role".
1. Immettere il nome dell'applicazione creata in precedenza
1. Fare clic su Salva.

Ripetere la procedura precedente per il secondo nodo del cluster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Creare il dispositivo STONITH

Dopo aver modificato le autorizzazioni per le macchine virtuali, è possibile configurare i dispositivi STONITH nel cluster.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> L'opzione "pcmk_host_map" è richiesta SOLO nel comando, se i nomi host RHEL e i nomi dei nodi di Azure NON sono identici. Vedere la sezione in grassetto nel comando.

Per RHEL **7. X**, usare il comando seguente per configurare il dispositivo di recinzione:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

Per RHEL **8. X**, usare il comando seguente per configurare il dispositivo di recinzione:  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> Le operazioni di monitoraggio e di schermatura sono deserializzate. Di conseguenza, se è presente un'operazione di monitoraggio a esecuzione prolungata e un evento di schermatura simultanea, non si verifica alcun ritardo nel failover del cluster a causa dell'operazione di monitoraggio già in esecuzione.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Abilitare l'uso di un dispositivo STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>L'agente di isolamento di Azure ha bisogno di connettersi agli endpoint pubblici come documentato, con l'aggiunta di possibili soluzioni, in [Connettività di endpoint pubblici per macchine virtuali con ILB standard](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha]
