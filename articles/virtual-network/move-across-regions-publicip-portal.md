---
title: Spostare la configurazione IP pubblico di Azure in un'altra area di Azure portale di Azure
description: Usare un modello per spostare la configurazione IP pubblico di Azure da un'area di Azure a un'altra usando il portale di Azure.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 23fe515ddfdecb9ef168dd662e3fa2d91ece688f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711477"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Spostare la configurazione IP pubblico di Azure in un'altra area usando il portale di Azure

In vari scenari in cui può essere opportuno spostare le configurazioni IP pubbliche di Azure esistenti da un'area a un'altra. Ad esempio, si potrebbe voler creare un IP pubblico con la stessa configurazione e SKU per i test. Inoltre, si potrebbe spostare una configurazione IP pubblica in un'altra area come parte della pianificazione del ripristino di emergenza.

**Gli indirizzi IP pubblici di Azure sono specifici dell'area e non possono essere spostati da un'area all'altra.** Tuttavia, è possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente di un IP pubblico.  È quindi possibile spostare la risorsa in un'altra area di staging esportando l'IP pubblico in un modello, modificando i parametri in base all'area di destinazione e quindi distribuendo il modello nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere [Avvio rapido: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che l'indirizzo IP pubblico di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Gli indirizzi IP pubblici di Azure non possono essere spostati tra le aree.  È necessario associare il nuovo indirizzo IP pubblico alle risorse nell'area di destinazione.

- Per esportare una configurazione IP pubblica e distribuire un modello per creare un IP pubblico in un'altra area, è necessario un ruolo di collaboratore di rete o superiore.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamenti del carico, i gruppi di sicurezza di rete e le reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare IP pubblici nell'area di destinazione in uso. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di indirizzi IP pubblici per il processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Preparazione e spostamento
I passaggi seguenti illustrano come preparare l'IP pubblico per lo spostamento della configurazione usando un modello di Gestione risorse e spostare la configurazione IP pubblico nell'area di destinazione usando il portale di Azure.

### <a name="export-the-template-and-deploy-from-a-script"></a>Esportare il modello e distribuirlo da uno script

1. Accedere ai [Azure portal](https://portal.azure.com)  >  **gruppi di risorse**portale di Azure.
2. Individuare il gruppo di risorse che contiene l'indirizzo IP pubblico di origine e fare clic su di esso.
3. Selezionare > **Impostazioni**  >  **Esporta modello**.
4. Scegliere **Distribuisci** nel pannello **Esporta modello** .
5. Fare clic su **modello**  >  **modifica parametri** per aprire il **parameters.js** nel file nell'editor online.
8. Per modificare il parametro del nome dell'indirizzo IP pubblico, modificare la proprietà in **parametro**  >  **valore** dal nome IP pubblico di origine al nome dell'indirizzo IP pubblico di destinazione, verificare che il nome sia racchiuso tra virgolette:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Fare clic su **Salva** nell'editor.

9.  Fare **clic su modello**  >  **modifica modello** per aprire il **template.js** nel file nell'editor online.

10. Per modificare l'area di destinazione in cui verrà spostato l'IP pubblico, modificare la proprietà **location** in **Resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per un'area è il nome dell'area senza spazi, Central **Stati Uniti**centrali  =  **centralus**.

12. Se lo si desidera, è anche possibile modificare altri parametri nel modello, che sono facoltativi in base ai requisiti:

    * **SKU** : è possibile modificare lo SKU dell'indirizzo IP pubblico nella configurazione da standard a Basic o Basic a standard modificando la proprietà nome **SKU**  >  **name** nella **template.jssu** file:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Per altre informazioni sulle differenze tra gli indirizzi IP pubblici con SKU Basic e standard, vedere [creare, modificare o eliminare un indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Metodo di allocazione dell'IP pubblico** e **timeout di inattività**: è possibile modificare entrambe queste opzioni nel modello modificando la proprietà **publicIPAllocationMethod** da **dinamica** a **statica** o da **statica** a **dinamica**. Il timeout di inattività può essere modificato impostando la proprietà **idleTimeoutInMinutes** sul valore desiderato.  Il valore predefinito è **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Per ulteriori informazioni sui metodi di allocazione e i valori di timeout di inattività, vedere [Creare, modificare o eliminare un indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Fare clic su **Salva** nell'editor online.

14. Fare **BASICS**clic su  >  **sottoscrizione** di base per scegliere la sottoscrizione in cui verrà distribuito l'IP pubblico di destinazione.

15. Fare **BASICS**clic su  >  **gruppo di risorse** nozioni di base per scegliere il gruppo di risorse in cui verrà distribuito l'IP pubblico di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per l'indirizzo IP pubblico di destinazione.  Verificare che il nome non sia uguale al gruppo di risorse di origine dell'IP pubblico di origine esistente.

16. Il percorso di **base**della verifica  >  **Location** è impostato sul percorso di destinazione in cui si desidera distribuire l'indirizzo IP pubblico.

17. Verificare in **Impostazioni** che il nome corrisponda al nome immesso nell'editor di parametri precedente.

18. Selezionare la casella in **termini e condizioni**.

19. Fare clic sul pulsante **Acquista** per distribuire l'indirizzo IP pubblico di destinazione.

## <a name="discard"></a>Discard

Se si vuole rimuovere l'indirizzo IP pubblico di destinazione, eliminare il gruppo di risorse che contiene l'indirizzo IP pubblico di destinazione.  A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore della pagina Overview (panoramica).

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento dell'indirizzo IP pubblico, eliminare l'indirizzo IP pubblico o il gruppo di risorse di origine. A tale scopo, selezionare l'indirizzo IP pubblico o il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un indirizzo IP pubblico di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
