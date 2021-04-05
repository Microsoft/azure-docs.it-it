---
title: Crea o Gestisci account di integrazione B2B
description: Creare, collegare e gestire gli account di integrazione per l'integrazione aziendale con le app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 51059dd1c4c5c93e155cd7a2d34c3cbaf29db6e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101705586"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Creare e gestire account di integrazione per integrazioni aziendali B2B in App per la logica di Azure

Prima di poter creare [soluzioni di integrazione aziendale e B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) tramite [App per la logica di Azure](../logic-apps/logic-apps-overview.md) è necessario creare un account di integrazione, ovvero una risorsa di Azure separata che fornisca un contenitore sicuro, scalabile e gestibile per gli artefatti di integrazione definiti e usati con i flussi di lavoro di app per la logica.

È ad esempio possibile creare, archiviare e gestire artefatti B2B, ad esempio partner commerciali, contratti, mappe, schemi, certificati e configurazioni batch. Inoltre, prima che l'app per la logica possa funzionare con questi artefatti e usare i connettori di App per la logica B2B, è necessario [collegare l'account di integrazione](#link-account) all'app per la logica. L'account di integrazione e l'app per la logica devono essere presenti nella *stessa* posizione o area.

> [!IMPORTANT]
> In base al tipo di account di integrazione selezionato, la creazione di un account di integrazione comporta costi. Per altre informazioni, vedere [modelli di prezzi e fatturazione per app](logic-apps-pricing.md#integration-accounts) per la logica e [prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica.

In questo argomento viene illustrato come eseguire queste attività:

* Creare l'account di integrazione.

  > [!TIP]
  > Per creare un account di integrazione all'interno di un [ambiente del servizio di integrazione](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vedere [creare account di integrazione in un ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

* Collegare l'account di integrazione a un'app per la logica.

* Modificare il piano tariffario per l'account di integrazione.

* Scollegare l'account di integrazione da un'app per la logica.

* Spostare l'account di integrazione in un gruppo di risorse o in una sottoscrizione di Azure differente.

* Eliminare l'account di integrazione.

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Creare un account di integrazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per questa attività, è possibile usare l'portale di Azure seguendo la procedura descritta in questa sezione, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)o l'interfaccia della riga di comando di [Azure](/cli/azure/resource#az-resource-create).

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nel menu principale di Azure scegliere **Crea una risorsa**. Nella casella di ricerca immettere "account di integrazione" come filtro e selezionare account di **integrazione**.

   ![Crea nuovo account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. In **account di integrazione** selezionare **Crea**.

   ![Scegliere "Aggiungi" per creare un account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Fornire queste informazioni sull'account di integrazione:

   ![Specificare i dettagli dell'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome** | Sì | <*nome-account-integrazione*> | Nome dell'account di integrazione, che può contenere solo lettere, numeri, trattini ( `-` ), caratteri di sottolineatura ( `_` ), parentesi ( `(` , `)` ) e punti ( `.` ). In questo esempio viene usato "Fabrikam-Integration". |
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Nome della sottoscrizione di Azure |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group-name*> | Nome del gruppo di [risorse di Azure](../azure-resource-manager/management/overview.md) da usare per organizzare le risorse correlate. Per questo esempio, creare un nuovo gruppo di risorse con il nome "FabrikamIntegration-RG". |
   | **Piano tariffario** | Sì | <*livello di prezzo*> | Il piano tariffario per l'account di integrazione, che può essere modificato in un secondo momento. Per questo esempio, selezionare **Free**. Per altre informazioni, vedere gli argomenti seguenti: <p>- [Modello di determinazione prezzi di app per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Limiti e configurazione delle app per la logica](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Prezzi di app per la logica](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Posizione** | Sì | <*Area di Azure*> | Area in cui archiviare i metadati dell'account di integrazione. Selezionare lo stesso percorso dell'app per la logica o creare le app per la logica nella stessa posizione dell'account di integrazione. Per questo esempio, usare "Stati Uniti occidentali". <p>**Nota**: per creare un account di integrazione all'interno di un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), selezionare ISE come percorso. Per altre informazioni, vedere [creare account di integrazione in un ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | No | Disattivato, acceso | Per questo esempio, lasciare l'impostazione **off** . |
   |||||

1. Al termine, selezionare **Crea**.

   Al termine della distribuzione, Azure aprirà l'account di integrazione.

   ![Azure apre l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Prima che l'app per la logica possa usare l'account di integrazione, seguire i passaggi successivi per collegare l'account di integrazione e l'app per la logica insieme.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile creare un account di integrazione usando i comandi dell'interfaccia della riga di comando di Azure in questa sezione.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Creare un account di integrazione

Usare questi comandi per creare un account di integrazione.

1. Per aggiungere l'estensione [AZ Logic Integration-account](/cli/azure/ext/logic/logic/integration-account) , usare il comando [AZ Extension Add](/cli/azure/extension#az_extension_add) :

   ```azurecli
   az extension add –-name logic
   ```

1. Per creare un gruppo di risorse o usare un gruppo di risorse esistente, eseguire il comando [AZ Group create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Per elencare gli account di integrazione per un gruppo di risorse, usare il comando [AZ Logic Integration-account list](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list) :

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Per creare un account di integrazione, eseguire il comando [AZ Logic Integration-account create](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create) :

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Il nome dell'account di integrazione può contenere solo lettere, numeri, trattini (-), caratteri di sottolineatura (_), parentesi ((,)) e punti (.).

   > [!TIP]
   > Per creare un account di integrazione all'interno di un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), selezionare ISE come percorso. Per altre informazioni, vedere [creare account di integrazione in un ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

   Per visualizzare un account di integrazione specifico, usare il comando [AZ Logic Integration-account Show](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show) :

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   È possibile modificare lo SKU o il piano tariffario usando il comando [AZ Logic Integration-account Update](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update) :

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Per ulteriori informazioni sui prezzi, vedere le risorse seguenti:

   * [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Limiti e configurazione per App per la logica](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Prezzi di app per la logica](https://azure.microsoft.com/pricing/details/logic-apps/)

Per importare un account di integrazione usando un file JSON, usare il comando [AZ Logic Integration-account import](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import) :

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Per eliminare un account di integrazione, è possibile usare il comando [AZ Logic Integration-account Delete](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete) :

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Prima che l'app per la logica possa usare l'account di integrazione, collegare l'account di integrazione e l'app per la logica insieme. La sezione successiva descrive il collegamento.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Collegare all'app per la logica

Per consentire alle app per la logica di accedere a un account di integrazione che contiene gli artefatti B2B, è innanzitutto necessario collegare l'account di integrazione all'app per la logica. L'account di integrazione e l'app per la logica devono esistere nella stessa area. Per completare questa attività, è possibile utilizzare il portale di Azure. Se si usa Visual Studio e l'app per la logica si trova in un [progetto di gruppo di risorse di Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md), è possibile [collegare l'app per la logica a un account di integrazione usando Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Nel portale di Azure trovare e aprire l'app per la logica.

1. Nel [portale di Azure](https://portal.azure.com)aprire un'app per la logica esistente o creare una nuova app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Impostazioni del flusso di lavoro**. In **account di integrazione** aprire l'elenco **selezionare un account di integrazione** . Selezionare l'account di integrazione da collegare all'app per la logica.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Per completare il collegamento, selezionare **Salva**.

   ![Screenshot che mostra dove selezionare Salva per scegliere l'account di integrazione.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Dopo che l'account di integrazione è stato collegato correttamente, Azure Visualizza un messaggio di conferma.

   ![Azure conferma l'esito positivo del collegamento](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

A questo punto, l'app per la logica può usare gli elementi nell'account di integrazione più i connettori B2B, ad esempio la convalida XML e la codifica o la decodifica dei file flat.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Modificare il piano tariffario

Per aumentare i [limiti](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) di un account di integrazione, è possibile [eseguire l'aggiornamento a un piano tariffario superiore](#upgrade-pricing-tier), se disponibile. Ad esempio, è possibile eseguire l'aggiornamento dal livello gratuito al livello Basic o standard. È anche possibile [effettuare il downgrade a un livello inferiore](#downgrade-pricing-tier), se disponibile. Per ulteriori informazioni sui prezzi, vedere gli argomenti seguenti:

* [Prezzi di app per la logica](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Aggiorna piano tariffario

Per apportare questa modifica, è possibile usare l'portale di Azure o l'interfaccia della riga di comando di Azure.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca di Azure principale immettere "account di integrazione" come filtro e selezionare **account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure Mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole spostare. Scegliere **Panoramica** dal menu dell'account di integrazione.

   ![Nel menu account di integrazione selezionare "panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Nel riquadro Panoramica selezionare Aggiorna piano **tariffario**, che elenca tutti i livelli superiori disponibili. Quando si seleziona un livello, la modifica avrà effetto immediato.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Se non è già stato fatto, [installare i prerequisiti dell'interfaccia della riga di](/cli/azure/get-started-with-azure-cli)comando di Azure.

1. Nella portale di Azure aprire l'ambiente [Azure cloud Shell](../cloud-shell/overview.md) .

   ![Aprire Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Al prompt dei comandi, immettere il [comando **AZ Resource**](/cli/azure/resource#az-resource-update)e impostare sul `skuName` livello superiore desiderato.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Se, ad esempio, si dispone del livello Basic, è possibile impostare `skuName` su `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Downgrade del piano tariffario

Per apportare questa modifica, usare l' [interfaccia](/cli/azure/get-started-with-azure-cli)della riga di comando di Azure.

1. Se non è già stato fatto, [installare i prerequisiti dell'interfaccia della riga di](/cli/azure/get-started-with-azure-cli)comando di Azure.

1. Nella portale di Azure aprire l'ambiente [Azure cloud Shell](../cloud-shell/overview.md) .

   ![Aprire Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Al prompt dei comandi, immettere il [comando **AZ Resource**](/cli/azure/resource#az-resource-update) e impostare sul `skuName` livello inferiore desiderato.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Se, ad esempio, si dispone del livello standard, è possibile impostare `skuName` su `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Scollegare da un'app per la logica

Se si vuole collegare l'app per la logica a un altro account di integrazione o non si usa più un account di integrazione con l'app per la logica, eliminare il collegamento usando Azure Resource Explorer.

1. Aprire la finestra del browser e passare a [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com). Accedere con le stesse credenziali dell'account Azure.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Nella casella di ricerca immettere il nome dell'app per la logica in modo che sia possibile trovare e selezionare l'app per la logica.

   ![Trovare e selezionare l'app per la logica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Sulla barra del titolo di Explorer selezionare **lettura/scrittura**.

   ![Attivare la modalità "Lettura/Scrittura"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Nella scheda **dati** selezionare **modifica**.

   ![Nella scheda "dati" selezionare "modifica"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Nell'editor individuare l' `integrationAccount` oggetto ed eliminare la proprietà, il cui formato è il seguente:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Ad esempio:

   ![Trova oggetto "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Nella scheda **dati** selezionare **put** per salvare le modifiche.

   ![Per salvare le modifiche, selezionare "put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Nel portale di Azure trovare e selezionare l'app per la logica. Nelle **impostazioni del flusso di lavoro** dell'app verificare che la proprietà dell' **account di integrazione** sia ora vuota.

   ![Verificare che l'account di integrazione non sia collegato](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Spostare l'account di integrazione

È possibile spostare l'account di integrazione in un altro gruppo di risorse di Azure o in una sottoscrizione di Azure. Quando si spostano le risorse, Azure crea nuovi ID di risorsa, quindi assicurarsi di usare invece i nuovi ID e aggiornare gli script o gli strumenti associati alle risorse spostate. Se si vuole modificare la sottoscrizione, è necessario specificare anche un gruppo di risorse nuovo o esistente.

Per questa attività, è possibile usare l'portale di Azure attenendosi alla procedura descritta in questa sezione o nell' [interfaccia](/cli/azure/resource#az-resource-move)della riga di comando di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca di Azure principale immettere "account di integrazione" come filtro e selezionare **account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure Mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole spostare. Scegliere **Panoramica** dal menu dell'account di integrazione.

   ![Nel menu account di integrazione selezionare "panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Accanto a **gruppo di risorse** o **nome sottoscrizione** Selezionare **Cambia**.

   ![Modificare il gruppo di risorse o la sottoscrizione](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selezionare le risorse correlate che si desidera spostare.

1. In base alla selezione, attenersi alla procedura seguente per modificare il gruppo di risorse o la sottoscrizione:

   * Gruppo di risorse: dall'elenco **gruppo di risorse** selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo di risorse**.

   * Sottoscrizione: dall'elenco **sottoscrizione** selezionare la sottoscrizione di destinazione. Dall'elenco **gruppo di risorse** selezionare il gruppo di risorse di destinazione. In alternativa, per creare un gruppo di risorse diverso, selezionare **Crea un nuovo gruppo di risorse**.

1. Per confermare che gli script o gli strumenti associati alle risorse spostate non funzioneranno finché non vengono aggiornati con i nuovi ID di risorsa, selezionare la casella di conferma e quindi fare clic su **OK**.

1. Al termine, assicurarsi di aggiornare tutti gli script con i nuovi ID risorsa per le risorse spostate.  

## <a name="delete-integration-account"></a>Eliminare l'account di integrazione

Per questa attività, è possibile usare l'portale di Azure seguendo la procedura descritta in questa sezione, l' [interfaccia](/cli/azure/resource#az-resource-delete)della riga di comando di Azure o [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca di Azure principale immettere "account di integrazione" come filtro e selezionare **account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure Mostra tutti gli account di integrazione nelle sottoscrizioni di Azure.

1. In **Account di integrazione** selezionare l'account di integrazione che si vuole eliminare. Scegliere **Panoramica** dal menu dell'account di integrazione.

   ![Nel menu account di integrazione selezionare "panoramica"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Nel riquadro Panoramica selezionare **Elimina**.

   ![Nel riquadro "panoramica" selezionare "Elimina"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Per confermare che si vuole eliminare l'account di integrazione, selezionare **Sì**.

   ![Per confermare l'eliminazione, selezionare "Sì"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare partner commerciali nell'account di integrazione](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Creare accordi tra partner nell'account di integrazione](../logic-apps/logic-apps-enterprise-integration-agreements.md)
