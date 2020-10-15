---
title: Usare un modello per aggiungere una macchina virtuale Windows a Azure AD DS | Microsoft Docs
description: Informazioni su come usare i modelli di Azure Resource Manager per aggiungere una macchina virtuale Windows Server nuova o esistente a un dominio gestito da Azure Active Directory Domain Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 988f009527f26a9f2be965b635d57f0bc38913c2
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960695"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Aggiungere una macchina virtuale Windows Server a un dominio gestito Azure Active Directory Domain Services usando un modello di Gestione risorse

Per automatizzare la distribuzione e la configurazione di macchine virtuali (VM) di Azure, è possibile usare un modello di Gestione risorse. Questi modelli consentono di creare distribuzioni coerenti ogni volta. Le estensioni possono essere incluse anche nei modelli per configurare automaticamente una macchina virtuale come parte della distribuzione. Un'estensione utile aggiunge le VM a un dominio, che può essere usata con i domini gestiti di Azure Active Directory Domain Services (Azure AD DS).

Questo articolo illustra come creare e aggiungere una macchina virtuale Windows Server a un dominio gestito di Azure AD DS usando i modelli Gestione risorse. Si apprenderà anche come aggiungere una macchina virtuale Windows Server esistente a un dominio Azure AD DS.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un dominio gestito di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente che fa parte del dominio gestito.

## <a name="azure-resource-manager-template-overview"></a>Panoramica del modello di Azure Resource Manager

Gestione risorse modelli consentono di definire l'infrastruttura di Azure nel codice. Le risorse necessarie, le connessioni di rete o la configurazione di macchine virtuali possono essere definite in un modello. Questi modelli creano ogni volta distribuzioni coerenti e riproducibili ed è possibile eseguire il controllo delle versioni quando si apportano modifiche. Per altre informazioni, vedere [Panoramica dei modelli di Azure Resource Manager][template-overview].

Ogni risorsa è definita in un modello usando JavaScript Object Notation (JSON). Nell'esempio JSON seguente viene usato il tipo di risorsa *Microsoft. Compute/virtualMachines/Extensions* per installare l'estensione di aggiunta al dominio Active Directory. Vengono utilizzati i parametri specificati in fase di distribuzione. Quando viene distribuita l'estensione, la macchina virtuale viene aggiunta al dominio gestito specificato.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Questa estensione della macchina virtuale può essere distribuita anche se non si crea una macchina virtuale nello stesso modello. Gli esempi in questo articolo illustrano entrambi gli approcci seguenti:

* [Creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [aggiungere una VM Windows Server esistente a un dominio gestito](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito

Se è necessaria una macchina virtuale Windows Server, è possibile crearla e configurarla usando un modello di Gestione risorse. Quando la macchina virtuale viene distribuita, viene quindi installata un'estensione per aggiungere la macchina virtuale a un dominio gestito. Se si dispone già di una VM che si vuole aggiungere a un dominio gestito, passare a un [dominio gestito per aggiungere una macchina virtuale Windows Server esistente](#join-an-existing-windows-server-vm-to-a-managed-domain).

Per creare una macchina virtuale Windows Server, aggiungerla a un dominio gestito, completare i passaggi seguenti:

1. Passare al [modello di avvio rapido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Selezionare l'opzione per la **distribuzione in Azure**.
1. Nella pagina **distribuzione personalizzata** immettere le informazioni seguenti per creare e aggiungere una macchina virtuale Windows Server al dominio gestito:

    | Impostazione                   | valore |
    |---------------------------|-------|
    | Subscription              | Selezionare la stessa sottoscrizione di Azure in cui è abilitato Azure Active Directory Domain Services. |
    | Resource group            | Scegliere il gruppo di risorse per la macchina virtuale. |
    | Posizione                  | Selezionare il percorso di per la macchina virtuale. |
    | Nome VNET esistente        | Nome della rete virtuale esistente a cui connettere la VM, ad esempio *myVnet*. |
    | Nome subnet esistente      | Nome della subnet della rete virtuale esistente, ad esempio *carichi di lavoro*. |
    | Prefisso etichetta DNS          | Immettere un nome DNS da usare per la macchina virtuale, ad esempio *MyVM*. |
    | Dimensioni macchina virtuale                   | Specificare le dimensioni della macchina virtuale, ad esempio *Standard_DS2_v2*. |
    | Dominio da aggiungere            | Nome DNS del dominio gestito, ad esempio *aaddscontoso.com*. |
    | Nome utente di dominio           | Account utente nel dominio gestito che deve essere usato per aggiungere la macchina virtuale al dominio gestito, ad esempio `contosoadmin@aaddscontoso.com` . Questo account deve far parte del dominio gestito. |
    | Password di dominio           | Password per l'account utente specificato nell'impostazione precedente. |
    | Percorso unità organizzativa facoltativo          | Unità organizzativa personalizzata in cui aggiungere la macchina virtuale. Se non si specifica un valore per questo parametro, la macchina virtuale viene aggiunta all'unità organizzativa dei *computer DC AAD* predefiniti. |
    | Nome utente amministratore VM         | Specificare un account amministratore locale da creare nella macchina virtuale. |
    | Password amministratore VM         | Specificare una password di amministratore locale per la macchina virtuale. Creare una password di amministratore locale complessa per proteggersi da attacchi di forza bruta alle password. |

1. Esaminare i termini e le condizioni, quindi selezionare la casella per **accettare i termini e le condizioni indicati in precedenza**. Quando si è pronti, selezionare **Acquista** per creare e aggiungere la macchina virtuale al dominio gestito.

> [!WARNING]
> **Gestire le password con cautela.**
> Il file dei parametri di modello richiede la password per un account utente che fa parte del dominio gestito. Non immettere manualmente i valori in questo file e lasciarlo accessibile nelle condivisioni file o in altri percorsi condivisi.

Sono necessari alcuni minuti per completare correttamente la distribuzione. Al termine, la VM Windows verrà creata e aggiunta al dominio gestito. È possibile gestire o accedere alla macchina virtuale usando gli account di dominio.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>aggiungere una VM Windows Server esistente a un dominio gestito

Se si dispone di una macchina virtuale o di un gruppo di VM esistente che si vuole aggiungere a un dominio gestito, è possibile usare un modello di Gestione risorse per distribuire semplicemente l'estensione della macchina virtuale.

Per aggiungere una macchina virtuale Windows Server esistente a un dominio gestito, attenersi alla procedura seguente:

1. Passare al [modello di avvio rapido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Selezionare l'opzione per la **distribuzione in Azure**.
1. Nella pagina **distribuzione personalizzata** immettere le informazioni seguenti per aggiungere la macchina virtuale al dominio gestito:

    | Impostazione                   | valore |
    |---------------------------|-------|
    | Subscription              | Selezionare la stessa sottoscrizione di Azure in cui è abilitato Azure Active Directory Domain Services. |
    | Resource group            | Scegliere il gruppo di risorse con la macchina virtuale esistente. |
    | Posizione                  | Selezionare il percorso della macchina virtuale esistente. |
    | Elenco VM                   | Immettere l'elenco delimitato da virgole delle macchine virtuali esistenti da aggiungere al dominio gestito, ad esempio *myVM1, myVM2*. |
    | Nome utente aggiunta al dominio     | Account utente nel dominio gestito che deve essere usato per aggiungere la macchina virtuale al dominio gestito, ad esempio `contosoadmin@aaddscontoso.com` . Questo account deve far parte del dominio gestito. |
    | Password utente aggiunta al dominio | Password per l'account utente specificato nell'impostazione precedente. |
    | Percorso unità organizzativa facoltativo          | Unità organizzativa personalizzata in cui aggiungere la macchina virtuale. Se non si specifica un valore per questo parametro, la macchina virtuale viene aggiunta all'unità organizzativa dei *computer DC AAD* predefiniti. |

1. Esaminare i termini e le condizioni, quindi selezionare la casella per **accettare i termini e le condizioni indicati in precedenza**. Quando si è pronti, selezionare **Acquista** per aggiungere la macchina virtuale al dominio gestito.

> [!WARNING]
> **Gestire le password con cautela.**
> Il file dei parametri di modello richiede la password per un account utente che fa parte del dominio gestito. Non immettere manualmente i valori in questo file e lasciarlo accessibile nelle condivisioni file o in altri percorsi condivisi.

Per completare correttamente la distribuzione, sono necessari alcuni istanti. Al termine, le VM Windows specificate vengono unite al dominio gestito e possono essere gestite o sottoposte a accesso utilizzando gli account di dominio.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato usato il portale di Azure per configurare e distribuire le risorse usando i modelli. È anche possibile distribuire le risorse con i modelli di Gestione risorse usando [Azure PowerShell][deploy-powershell] o l'interfaccia della riga di comando di [Azure][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
