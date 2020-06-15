---
title: Creare un account nel portale di Azure
description: Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6cccef176e3e5ba0f4774a5897f082c4847a4005
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800245"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creare un account Batch nel portale di Azure

Informazioni su come creare un account Azure Batch nel [portale di Azure][azure_portal] e scegliere le proprietà dell'account adatte allo scenario di calcolo. Informazioni su dove trovare le proprietà di account importanti come le chiavi di accesso e gli URL dell'account.

Per informazioni sugli account e gli scenari Batch, vedere [Flusso di lavoro del servizio Batch e risorse](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Creare un account Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Accedere al [portale di Azure][azure_portal].

1. Selezionare **Crea una risorsa** > **Calcolo** > **Servizio Batch**.

    ![Batch in Marketplace][marketplace_portal]

1. Immettere le impostazioni di **Nuovo account Batch**. Vedere i dettagli seguenti.

    ![Creare un account Batch][account_portal]

    a. **Sottoscrizione** sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

    b. **Gruppo di risorse**: selezionare un gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

    c. **Account name** (Nome dell'account): il nome scelto deve essere univoco all'interno dell'area di Azure in cui viene creato l'account. Vedere **Località** di seguito. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.

    d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione**: un account di Archiviazione di Azure facoltativo da associare all'account Batch. Per prestazioni ottimali, è consigliabile configurare un account di archiviazione per utilizzo generico v2. Per tutte le opzioni dell'account di archiviazione in Batch, vedere [Panoramica delle funzionalità di Batch](accounts.md#azure-storage-accounts). Nel portale selezionare un account di archiviazione esistente o crearne uno nuovo.

      ![Creare un account di archiviazione][storage_account]

    f. **Modalità di allocazione del pool**: Nella scheda di impostazioni **Avanzate** è possibile specificare la modalità di allocazione del pool come **Servizio Batch** o come **Sottoscrizione utente**. per la maggior parte degli scenari accettare il valore predefinito **Servizio Batch**.

      ![Modalità di allocazione del pool][pool_allocation]

1. Selezionare **Crea** per creare l'account.

## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch

Dopo aver creato l'account, selezionarlo per accedere alle impostazioni e proprietà associate. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu a sinistra.

> [!NOTE]
> Il nome dell'account Batch è il relativo ID e non può essere modificato. Per modificare il nome di un account Batch, è necessario eliminare l'account e crearne uno nuovo con il nome desiderato.

![Pagina Account Batch nel portale di Azure][account_blade]

* **Nome, URL e chiavi dell'account Batch**: quando si sviluppa un'applicazione con le [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), sono necessari un URL e una chiave dell'account per accedere alle risorse di Batch. Batch supporta anche l'autenticazione di Azure Active Directory.

    Per visualizzare le informazioni di accesso dell'account Batch, selezionare **Chiavi**.

    ![Chiavi dell'account Batch nel portale di Azure][account_keys]

* Per visualizzare il nome e le chiavi dell'account di archiviazione associato all'account Batch, selezionare **Account di archiviazione**.

* Per visualizzare le quote di risorse applicabili all'account Batch, selezionare **Quote**. Per dettagli, vedere [Quote e limiti del servizio Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Configurazione aggiuntiva per la modalità Sottoscrizione utente

Se si sceglie di creare un account Batch in modalità Sottoscrizione utente, prima di creare l'account eseguire questi passaggi aggiuntivi.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Consentire ad Azure Batch di accedere alla sottoscrizione (operazione occasionale)

Quando si crea il primo account Batch in modalità Sottoscrizione utente, è necessario registrare la sottoscrizione in Batch. Se questa operazione è stata fatta precedentemente, passare alla sezione successiva.

1. Accedere al [portale di Azure][azure_portal].

1. Selezionare **Altri servizi** > **Sottoscrizioni** e quindi fare clic sulla sottoscrizione che si intende usare per l'account Batch.

1. Nella pagina **Sottoscrizione** selezionare **Provider di risorse** e cercare **Microsoft.Batch**. Verificare che il provider di risorse **Microsoft.Batch** sia registrato nella sottoscrizione. Se non è registrato, selezionare il collegamento **Registra**.

    ![Registrare il provider Microsoft.Batch][register_provider]

1. Nella pagina **Sottoscrizione** selezionare**Controllo di accesso (IAM)**  > **Assegnazioni di ruolo** > **Aggiungi assegnazione di ruolo**.

    ![Controllo di accesso alla sottoscrizione][subscription_access]

1. Nella pagina **Aggiungi assegnazione di ruolo** selezionare il ruolo **Collaboratore** e cercare l'API Batch. Cercare ognuna di queste stringhe fino a trovare l'API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. I tenant di Azure AD più recenti potrebbero usare questo nome.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** è l'ID dell'API Batch.

1. Dopo aver trovato l'API Batch, selezionarla e fare clic su **Salva**.

    ![Aggiungere le autorizzazioni di Batch][add_permission]

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

In modalità di sottoscrizione utente, è necessario un insieme di credenziali delle chiavi di Azure che appartiene allo stesso gruppo di risorse come l'account Batch da creare. Assicurarsi che il gruppo di risorse sia in un'area in cui Batch è [disponibile](https://azure.microsoft.com/regions/services/) e che supporta la sottoscrizione.

1. Nel [portale di Azure][azure_portal] selezionare **Nuovo** > **Sicurezza** > **Key Vault**.

1. Nella pagina **Crea insieme di credenziali delle chiavi** immettere un nome per l'insieme di credenziali delle chiavi e creare un gruppo di risorse nell'area desiderata per l'account Batch. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi selezionare **Crea**.

Quando si crea l'account Batch in modalità di sottoscrizione utente, usare il gruppo di risorse per l'insieme di credenziali delle chiavi. Scegliere **Sottoscrizione utente** come modalità di allocazione pool, selezionare l'insieme di credenziali delle chiavi e quindi fare clic sulla casella per consentire ad Azure Batch di accedere all'insieme di credenziali delle chiavi. 

Per consentire l'accesso all'insieme di credenziali delle chiavi manualmente, passare alla sezione **Criteri di accesso** di Key Vault, selezionare **Aggiungi criteri di accesso** e cercare **Microsoft Azure Batch**. Dopo averla selezionata, sarà necessario configurare le **Autorizzazioni segrete** usando il menu a discesa. In Azure Batch deve essere specificato un numero minimo di autorizzazioni **Get**, **List**, **Set**e **Delete**.

![Autorizzazioni segrete per Azure Batch](./media/batch-account-create-portal/secret-permissions.png)


> [!NOTE]
> Assicurarsi che le caselle di controllo **Macchine virtuali di Azure per la distribuzione di** e **Azure Resource Manager per la distribuzione di modelli** siano selezionate in **Criteri di accesso** per la risorsa **Key Vault** collegata.
> 
> ![Criteri di accesso obbligatori di Key Vault](./media/batch-account-create-portal/key-vault-access-policy.png) Non obbligatorio quando si crea un account Batch nel portale di Azure. L'opzione è selezionata per impostazione predefinita.



### <a name="configure-subscription-quotas"></a>Configurare le quote di sottoscrizione

Le quote di core non vengono configurate per impostazione predefinita negli account Batch delle sottoscrizioni utente. Devono essere impostate manualmente perché le quote di core di Batch standard non si applicano agli account in modalità sottoscrizione utente.

1. Nel [portale di Azure][azure_portal] selezionare l'account Batch in modalità sottoscrizione utente per visualizzare le relative impostazioni e proprietà.

1. Scegliere **Quote** dal menu a sinistra per visualizzare e configurare le quote di core associate all'account Batch.

Per altre informazioni sulle quote di core in modalità sottoscrizione utente, vedere [Quote e limiti del servizio Batch](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione dell'account Batch

Oltre a usare il portale di Azure, è possibile creare e gestire gli account Batch con alcuni strumenti, tra cui:

* [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
* [Interfaccia della riga di comando di Azure](batch-cli-get-started.md)
* [.NET per la gestione di Batch](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
* Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](quick-run-dotnet.md) o con [Python](quick-run-python.md). Queste guide introduttive illustrano un'applicazione di esempio che usa il servizio Batch per eseguire un carico di lavoro su più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
