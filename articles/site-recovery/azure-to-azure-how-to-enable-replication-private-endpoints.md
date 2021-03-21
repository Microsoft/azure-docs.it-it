---
title: Abilitare la replica per gli endpoint privati in Azure Site Recovery
description: Questo articolo descrive come configurare la replica per le macchine virtuali con endpoint privati da un'area di Azure a un'altra usando Site Recovery.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629828"
---
# <a name="replicate-machines-with-private-endpoints"></a>Replicare i computer con endpoint privati

Azure Site Recovery consente di usare endpoint privati di [collegamento privato di Azure](../private-link/private-endpoint-overview.md) per la replica dei computer dall'interno di una rete virtuale isolata. L'accesso dell'endpoint privato a un insieme di credenziali di ripristino è supportato in tutte le aree commerciali & governative di Azure.

Questo articolo fornisce istruzioni per eseguire i passaggi seguenti:

- Creare un insieme di credenziali di servizi di ripristino di backup di Azure per proteggere i computer.
- Abilitare un'identità gestita per l'insieme di credenziali e concedere le autorizzazioni necessarie per accedere agli account di archiviazione del cliente per replicare il traffico dall'origine ai percorsi di destinazione. L'accesso alle identità gestite per l'archiviazione è necessario quando si configura l'accesso al collegamento privato all'insieme di credenziali.
- Apportare le modifiche DNS necessarie per gli endpoint privati
- Creare e approvare endpoint privati per un insieme di credenziali all'interno di una rete virtuale
- Creare endpoint privati per gli account di archiviazione. È possibile continuare a consentire l'accesso pubblico o con firewall per l'archiviazione in base alle esigenze. La creazione di un endpoint privato per l'accesso allo spazio di archiviazione non è obbligatoria per Azure Site Recovery.
  
Di seguito è riportata un'architettura di riferimento sul modo in cui il flusso di lavoro di replica cambia con gli endpoint privati.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Architettura di riferimento per Site Recovery con endpoint privati.":::

## <a name="prerequisites-and-caveats"></a>Prerequisiti e avvertenze

- Gli endpoint privati possono essere creati solo per i nuovi insiemi di credenziali dei servizi di ripristino che non hanno elementi registrati nell'insieme di credenziali. Di conseguenza, è **necessario creare endpoint privati prima di aggiungere elementi all'insieme di** credenziali. Esaminare la struttura dei prezzi per gli [endpoint privati](https://azure.microsoft.com/pricing/details/private-link/).
- Quando viene creato un endpoint privato per un insieme di credenziali, l'insieme di credenziali è bloccato e **non è accessibile da reti diverse dalle reti con endpoint privati**.
- Azure Active Directory attualmente non supporta endpoint privati. Di conseguenza, gli indirizzi IP e i nomi di dominio completi necessari per Azure Active Directory lavorare in un'area devono essere consentiti per l'accesso in uscita dalla rete protetta. È anche possibile usare il tag del gruppo di sicurezza di rete "Azure Active Directory" e i tag del firewall di Azure per consentire l'accesso ai Azure Active Directory, come applicabile.
- **Sono necessari almeno sette indirizzi IP** nelle subnet sia dei computer di origine che dei computer di ripristino. Quando si crea un endpoint privato per l'insieme di credenziali, Site Recovery crea cinque collegamenti privati per l'accesso ai relativi microservizi. Inoltre, quando si Abilita la replica, vengono aggiunti due collegamenti privati aggiuntivi per l'associazione dell'area di origine e di destinazione.
- **È necessario un indirizzo IP aggiuntivo** sia nelle subnet di origine che in quelle di ripristino. Questo indirizzo IP è necessario solo quando è necessario usare endpoint privati che si connettono agli account di archiviazione della cache.
  Gli endpoint privati per l'archiviazione possono essere creati solo in per utilizzo generico tipo V2. Esaminare la struttura dei prezzi per il [trasferimento dei dati in GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Creazione e utilizzo di endpoint privati per Site Recovery

 Questa sezione illustra i passaggi necessari per la creazione e l'uso di endpoint privati per Azure Site Recovery all'interno delle reti virtuali.

> [!NOTE]
> Si consiglia vivamente di seguire questi passaggi nella stessa sequenza fornita. In caso contrario, è possibile che l'insieme di credenziali di cui viene eseguito il rendering non riesca a usare endpoint privati e che sia necessario riavviare il processo con un nuovo insieme di credenziali.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di servizi di ripristino è un'entità che contiene le informazioni di replica dei computer e viene usata per attivare Site Recovery operazioni. Per altre informazioni, vedere [creare un](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)insieme di credenziali di servizi di ripristino.

## <a name="enable-the-managed-identity-for-the-vault"></a>Abilitare l'identità gestita per l'insieme di credenziali.

Un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) consente all'insieme di credenziali di ottenere l'accesso agli account di archiviazione del cliente. Site Recovery necessario accedere agli account di archiviazione di origine, di destinazione e di archiviazione della cache/log a seconda del requisito dello scenario.
L'accesso alle identità gestite è essenziale quando si usa il servizio di collegamenti privati per l'insieme di credenziali.

1. Passare all'insieme di credenziali di servizi di ripristino. Selezionare **Identity** in _Settings (impostazioni_).

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Mostra la portale di Azure e la pagina servizi di ripristino.":::

1. Modificare lo **stato** _su on_ e selezionare **Salva**.

1. Viene generato un **ID oggetto** che indica che l'insieme di credenziali è ora registrato con Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Creare endpoint privati per l'insieme di credenziali di servizi di ripristino

Per abilitare il failover e il failback per le macchine virtuali di Azure, sono necessari due endpoint privati per l'insieme di credenziali. Un endpoint privato per la protezione dei computer nella rete di origine e un altro per la riprotezione dei computer di cui è stato eseguito il failover nella rete di ripristino.

Assicurarsi di creare anche una rete virtuale di ripristino nell'area di destinazione durante il processo di installazione.

Creare il primo endpoint privato per l'insieme di credenziali all'interno della rete virtuale di origine usando il centro di collegamento privato nel portale o tramite [Azure PowerShell](../private-link/create-private-endpoint-powershell.md). Creare il secondo endpoint privato per l'insieme di credenziali all'interno della rete di ripristino. Di seguito sono riportati i passaggi per creare l'endpoint privato nella rete di origine. Ripetere le stesse linee guida per creare il secondo endpoint privato.

1. Nella barra di ricerca portale di Azure cercare e selezionare "collegamento privato". Questa azione consente di eseguire il collegamento a link Center privato.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Mostra la ricerca del portale di Azure per il centro collegamenti privati.":::

1. Sulla barra di spostamento a sinistra selezionare **endpoint privati**. Nella pagina endpoint privati selezionare **\+ Aggiungi** per avviare la creazione di un endpoint privato per l'insieme di credenziali.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Mostra la creazione di un endpoint privato nel centro collegamenti privati.":::

1. Una volta nell'esperienza di creazione di un endpoint privato, è necessario specificare i dettagli per la creazione della connessione all'endpoint privato.

   1. **Nozioni** di base: inserire i dettagli di base per gli endpoint privati. L'area deve essere identica a quella dei computer di origine.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Mostra la scheda di base, i dettagli del progetto, la sottoscrizione e altri campi correlati per la creazione di un endpoint privato nel portale di Azure.":::

   1. **Risorsa**: questa scheda richiede di menzionare la risorsa della piattaforma distribuita come servizio per cui si vuole creare la connessione. Selezionare _Microsoft. RecoveryServices/Vaults_ dal **tipo di risorsa** per la sottoscrizione selezionata. Scegliere quindi il nome dell'insieme di credenziali di servizi di ripristino per **Resource** e impostare _Azure Site Recovery_ come **risorsa secondaria di destinazione**.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Mostra i campi scheda risorse, tipo di risorsa, risorsa e risorse secondarie di destinazione per il collegamento a un endpoint privato nel portale di Azure.":::

   1. **Configurazione**: in configurazione specificare la rete virtuale e la subnet in cui si vuole creare l'endpoint privato. Questa rete virtuale è la rete in cui è presente la macchina virtuale. Per abilitare l'integrazione con la zona DNS privata, selezionare **Sì**. Scegliere una zona DNS già creata o crearne una nuova. Se **si seleziona Sì** , la zona verrà collegata automaticamente alla rete virtuale di origine e verranno aggiunti i record DNS necessari per la risoluzione DNS dei nuovi indirizzi IP e i nomi di dominio completi creati per l'endpoint privato.

      Assicurarsi di scegliere di creare una nuova zona DNS per ogni nuovo endpoint privato che si connette allo stesso insieme di credenziali. Se si sceglie una zona DNS privata esistente, i record CNAME precedenti verranno sovrascritti. Vedere [linee guida per gli endpoint privati](../private-link/private-endpoint-overview.md#private-endpoint-properties) prima di continuare.

      Se l'ambiente ha un modello hub e spoke, è necessario un solo endpoint privato e una sola zona DNS privata per l'intera configurazione, perché per tutte le reti virtuali è già abilitato il peering. Per altre informazioni, vedere [integrazione di DNS con endpoint privati](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Per creare manualmente la zona DNS privata, seguire la procedura descritta in [creare zone DNS private e aggiungere manualmente i record DNS](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Mostra la scheda configurazione con i campi di integrazione di rete e DNS per la configurazione di un endpoint privato nel portale di Azure.":::

   1. **Tag**: facoltativamente, è possibile aggiungere tag per l'endpoint privato.

   1. **Verifica \+ creazione**: al termine della convalida, selezionare **Crea** per creare l'endpoint privato.

Una volta creato l'endpoint privato, all'endpoint privato vengono aggiunti cinque nomi di dominio completi. Questi collegamenti consentono ai computer nella rete virtuale di ottenere l'accesso a tutti i microservizi Site Recovery richiesti nel contesto dell'insieme di credenziali. Successivamente, quando si Abilita la replica, vengono aggiunti due nomi di dominio completi aggiuntivi allo stesso endpoint privato.

I cinque nomi di dominio sono formattati con il modello seguente:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Approva endpoint privati per Site Recovery

Se l'utente che crea l'endpoint privato è anche il proprietario dell'insieme di credenziali di servizi di ripristino, l'endpoint privato creato in precedenza viene approvato automaticamente entro pochi minuti. In caso contrario, il proprietario dell'insieme di credenziali deve approvare l'endpoint privato prima di usarlo. Per approvare o rifiutare una connessione all'endpoint privato richiesta, passare a **connessioni a endpoint privati** in "Impostazioni" nella pagina dell'insieme di credenziali di ripristino.

È possibile passare alla risorsa endpoint privato per esaminare lo stato della connessione prima di procedere.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Mostra la pagina connessioni endpoint private dell'insieme di credenziali e l'elenco delle connessioni nel portale di Azure.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Opzionale Creare endpoint privati per l'account di archiviazione della cache

È possibile usare un endpoint privato per archiviazione di Azure. La creazione di endpoint privati per l'accesso all'archiviazione è _facoltativa_ per la replica Azure Site Recovery. Quando si crea un endpoint privato per l'archiviazione, si applicano i requisiti seguenti:

- È necessario un endpoint privato per l'account di archiviazione cache/log nella rete virtuale di origine.
- È necessario un secondo endpoint privato al momento della riprotezione dei computer sottoposte a failover nella rete di ripristino. Questo endpoint privato è per il nuovo account di archiviazione creato nell'area di destinazione.

> [!NOTE]
> Se gli endpoint privati non sono abilitati per l'account di archiviazione, la protezione avrà comunque esito positivo. Tuttavia, il traffico di replica transiterà Azure Site Recovery endpoint pubblici. Per garantire che il traffico di replica fluisca attraverso collegamenti privati, è necessario abilitare l'account di archiviazione con endpoint privati.

> [!NOTE]
> L'endpoint privato per l'archiviazione può essere creato solo in un account di archiviazione **per utilizzo generico V2** . Per informazioni sui prezzi, vedere [prezzi dei BLOB di pagine standard](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Seguire le [istruzioni per la creazione di archiviazione privata](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) per creare un account di archiviazione con endpoint privato. Assicurarsi di selezionare **Sì** per l'integrazione con la zona DNS privata. Selezionare una zona DNS già creata o crearne una nuova.

## <a name="grant-required-permissions-to-the-vault"></a>Concedere le autorizzazioni necessarie all'insieme di credenziali

Se le macchine virtuali usano dischi gestiti, è necessario concedere le autorizzazioni di identità gestite solo agli account di archiviazione della cache. Se le macchine virtuali usano dischi non gestiti, è necessario concedere le autorizzazioni di identità gestite per gli account di archiviazione di origine, cache e di destinazione. In questo caso, è necessario creare l'account di archiviazione di destinazione in anticipo.

Prima di abilitare la replica delle macchine virtuali, l'identità gestita dell'insieme di credenziali deve avere le autorizzazioni seguenti per il ruolo a seconda del tipo di account di archiviazione:

- Account di archiviazione basati su Gestione risorse (tipo standard):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor)
  - [Collaboratore ai dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Account di archiviazione basati su Gestione risorse (tipo Premium):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietario dei dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Account di archiviazione classici:
  - [Collaboratore account di archiviazione classico](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

I passaggi seguenti descrivono come aggiungere un'assegnazione di ruolo agli account di archiviazione, uno alla volta:

1. Passare all'account di archiviazione e passare a **controllo di accesso (IAM)** sul lato sinistro della pagina.

1. Al **controllo di accesso (IAM)** nella casella "Aggiungi assegnazione ruolo" selezionare **Aggiungi**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Mostra la pagina controllo di accesso (IAM) in un account di archiviazione e il pulsante ' Aggiungi assegnazione ruolo ' nella portale di Azure.":::

1. Nella pagina "Aggiungi assegnazione ruolo" scegliere il ruolo dall'elenco precedente nell'elenco a discesa **ruolo** . Immettere il **nome** dell'insieme di credenziali e selezionare **Salva**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Mostra la pagina controllo di accesso (IAM) in un account di archiviazione e le opzioni per selezionare un ruolo e l'entità a cui concedere tale ruolo nella portale di Azure.":::

Oltre a queste autorizzazioni, è necessario consentire l'accesso anche a Microsoft Trusted Services. Passare a "firewall e reti virtuali" e selezionare la casella di controllo "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione" in **eccezioni**.

## <a name="protect-your-virtual-machines"></a>Proteggi le tue macchine virtuali

Una volta completate tutte le configurazioni precedenti, continuare con l'abilitazione della replica per le macchine virtuali. Tutte le operazioni di Site Recovery funzionano senza ulteriori passaggi se l'integrazione DNS è stata usata durante la creazione di endpoint privati nell'insieme di credenziali. Tuttavia, se le zone DNS vengono create e configurate manualmente, sono necessari passaggi aggiuntivi per aggiungere record DNS specifici nelle zone DNS di origine e di destinazione dopo aver abilitato la replica. Per informazioni dettagliate e procedure, vedere [creare zone DNS private e aggiungere manualmente i record DNS](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Creare zone DNS private e aggiungere i record DNS manualmente

Se non è stata selezionata l'opzione per l'integrazione con la zona DNS privata al momento della creazione dell'endpoint privato per l'insieme di credenziali, attenersi alla procedura descritta in questa sezione.

Creare una zona DNS privata per consentire all'agente di mobilità di risolvere i nomi di dominio completi del collegamento privato a indirizzi IP privati.

1. Creare una zona DNS privato

   1. Cercare "DNS privato zone" nella barra di ricerca **tutti i servizi** e selezionare "DNS privato Zones" dall'elenco a discesa.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Mostra la ricerca della &quot;zona DNS privata&quot; nella pagina nuove risorse della portale di Azure.":::

   1. Nella pagina "zone DNS privato" selezionare il pulsante **\+ Aggiungi** per avviare la creazione di una nuova zona.

   1. Nella pagina "Crea zona DNS privata" immettere i dettagli richiesti. Immettere il nome della zona DNS privata come `privatelink.siterecovery.windowsazure.com` . È possibile scegliere qualsiasi gruppo di risorse e qualsiasi sottoscrizione per crearlo.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Mostra la scheda nozioni di base della pagina Crea zona DNS privato e i dettagli del progetto correlati nel portale di Azure.":::

   1. Passare alla scheda **verifica \+ creazione** per esaminare e creare la zona DNS.

1. Collegare la zona DNS privata alla rete virtuale

   Le zone DNS private create sopra devono ora essere collegate alla rete virtuale in cui si trovano attualmente i server. È anche necessario collegare la zona DNS privata alla rete virtuale di destinazione in anticipo.

   1. Passare alla zona DNS privata creata nel passaggio precedente e passare a **collegamenti reti virtuali** sul lato sinistro della pagina. Al termine, selezionare il pulsante **\+ Aggiungi** .

   1. Immettere i dettagli necessari. I campi **sottoscrizione** e **rete virtuale** devono essere riempiti con i dettagli corrispondenti della rete virtuale in cui si trovano i server. Gli altri campi devono essere lasciati invariati.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Mostra la pagina per aggiungere un collegamento di rete virtuale con il nome del collegamento, la sottoscrizione e la rete virtuale correlata nell'portale di Azure.":::

1. Aggiungere record DNS

   Dopo aver creato le zone DNS private necessarie e gli endpoint privati, è necessario aggiungere i record DNS alle zone DNS.

   > [!NOTE]
   > Se si usa una zona DNS privata personalizzata, assicurarsi che siano state apportate voci analoghe, come descritto di seguito.

   Per questo passaggio è necessario creare voci per ogni nome di dominio completo nell'endpoint privato nella zona DNS privata.

   1. Passare alla zona DNS privata e passare alla sezione **Panoramica** sul lato sinistro della pagina. Quindi, selezionare **\+ set di record** per iniziare ad aggiungere i record.

   1. Nella pagina "Aggiungi set di record" visualizzata aggiungere una voce per ogni nome di dominio completo e IP privato come record _di_ tipo. È possibile ottenere l'elenco dei nomi di dominio completi e degli indirizzi IP dalla pagina "endpoint privato" in **Panoramica**. Come illustrato nell'esempio seguente, il primo nome di dominio completo dall'endpoint privato viene aggiunto al set di record nella zona DNS privata.

      Questi nomi di dominio completi corrispondono al modello: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Mostra la pagina per aggiungere un record di tipo DNS per il nome di dominio completo all'endpoint privato nel portale di Azure.":::

   > [!NOTE]
   > Dopo aver abilitato la replica, vengono creati due nomi di dominio più completi negli endpoint privati in entrambe le aree. Assicurarsi di aggiungere i record DNS per questi nomi di dominio completi appena creati.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati abilitati endpoint privati per la replica della macchina virtuale, vedere le altre pagine per informazioni aggiuntive e correlate:

- [Replica delle macchine virtuali di Azure in un'altra area di Azure](./azure-to-azure-how-to-enable-replication.md)
- [Esercitazione: Configurare il ripristino di emergenza per le macchine virtuali di Azure](./azure-to-azure-tutorial-enable-replication.md)