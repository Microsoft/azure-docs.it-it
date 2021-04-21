---
title: Gestire Azure Key Vault tramite l'interfaccia della riga di comando - Azure Key Vault | Microsoft Docs
description: Usare questo articolo per automatizzare le attività comuni nell'insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b29b668b21c375dd1202652b5093526f648c300
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749601"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Gestire Key Vault tramite l'interfaccia della riga di comando di Azure 

Questo articolo descrive come usare Azure Key Vault con l'interfaccia della riga di comando di Azure.  In particolare, fornisce informazioni sugli argomenti seguenti:

- Come creare un contenitore finalizzato, ossia un insieme di credenziali, in Azure
- Aggiunta di una chiave, un segreto o un certificato all'insieme di credenziali delle chiavi
- Registrazione di un'applicazione con Azure Active Directory
- Autorizzazione di un'applicazione all'uso di una chiave o un segreto
- Impostazione di criteri di accesso avanzati per l'insieme di credenziali delle chiavi
- Uso dei moduli di protezione hardware
- Eliminazione dell'insieme di credenziali delle chiavi e di chiavi e segreti associati
- Comandi vari dell'interfaccia della riga di comando multipiattaforma di Azure


L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Questo articolo non include istruzioni su come scrivere l'applicazione Azure usata nel passaggio che spiega come autorizzare un'applicazione all'uso di una chiave o un segreto nell'insieme di credenziali delle chiavi.
>

Per una panoramica di Azure Key Vault, vedere [Cos'è Azure Key Vault?](overview.md). Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per usare i comandi dell'interfaccia della riga di comando di Azure in questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando di Azure versione 2.0 o versioni successive. Per installare la versione più recente, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Un'applicazione che verrà configurata per usare la chiave o la password creata in questo articolo. Un'applicazione di esempio è disponibile nell'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Per istruzioni, vedere il file Readme incluso.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Risorse della Guida per l'interfaccia della riga di comando multipiattaforma di Microsoft Azure

Questo articolo presuppone che si abbia familiarità con l'interfaccia della riga di comando (Bash, terminale, prompt dei comandi).

Il parametro --help o -h consente di visualizzare la Guida per specifici comandi. In alternativa, è anche possibile usare il formato Azure help [comando] [opzioni]. In caso di dubbi sui parametri necessari per un comando, vedere la Guida. Ad esempio, tutti i comandi seguenti restituiranno le stesse informazioni:

```azurecli
az account set --help
az account set -h
```

È anche possibile vedere gli articoli seguenti per acquisire familiarità con Azure Resource Manager nell'interfaccia della riga di comando multipiattaforma di Azure:

* [Installare l'interfaccia da riga di comando di Azure](/cli/azure/install-azure-cli)
* [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Come creare un contenitore finalizzato, ossia un insieme di credenziali, in Azure

Gli insiemi di credenziali sono contenitori protetti supportati da moduli di protezione hardware. Gli insiemi di credenziali consentono di ridurre le probabilità di perdita accidentale di informazioni di sicurezza centralizzando l'archiviazione dei segreti delle applicazioni. Gli insiemi di credenziali delle chiavi controllano e registrano anche l'accesso a tutti gli elementi archiviati al loro interno. Azure Key Vault può gestire la richiesta e rinnovo dei certificati TLS (Transport Layer Security), offrendo le funzionalità necessarie per una soluzione affidabile di gestione del ciclo di vita dei certificati. Nei passaggi successivi si creerà un insieme di credenziali.

### <a name="connect-to-your-subscriptions"></a>Connettersi alle sottoscrizioni

Per accedere in modo interattivo, usare il comando seguente:

```azurecli
az login
```
Per accedere con un account aziendale, è possibile passare il nome utente e la password.

```azurecli
az login -u username@domain.com -p password
```

Se si ha più di una sottoscrizione ed è necessario specificare quale usare, digitare quanto segue per visualizzare le sottoscrizioni per il proprio account:

```azurecli
az account list
```

Specificare una sottoscrizione con il parametro subscription.

```azurecli
az account set --subscription <subscription name or ID>
```

Per ulteriori informazioni sulla configurazione dell'interfaccia della riga di comando multipiattaforma di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Creare un nuovo gruppo di risorse

Quando si usa Gestione risorse di Azure, tutte le risorse correlate vengono create in un gruppo di risorse. È possibile creare un insieme di credenziali delle chiavi in un gruppo di risorse esistente. Per usare un nuovo gruppo di risorse, è possibile crearne uno nuovo.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Il primo parametro è il nome del gruppo di risorse e il secondo è la posizione. Per ottenere un elenco di tutti i possibili tipi di posizione:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrare il provider di risorse dell'insieme di credenziali delle chiavi

 È possibile che venga visualizzato il messaggio di errore "La sottoscrizione non è registrata per l'uso dello spazio dei nomi 'Microsoft.KeyVault'" quando si prova a creare un nuovo insieme di credenziali delle chiavi. Se viene visualizzato questo messaggio, verificare che il provider di risorse di Key Vault sia registrato nella sottoscrizione. Questa operazione viene eseguita una sola volta per ogni sottoscrizione.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Usare il comando `az keyvault create` per creare un insieme di credenziali delle chiavi. Questo script ha tre parametri obbligatori: un nome del gruppo di risorse, un nome dell'insieme di credenziali delle chiavi e la località geografica.

Per creare un nuovo insieme di credenziali con il nome **ContosoKeyVault**, nel gruppo di risorse **ContosoResourceGroup**, che si trova nella località **Asia orientale**, digitare: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

L'output di questo comando mostra le proprietà dell'insieme di credenziali delle chiavi creato. Le due proprietà più importanti sono:

* **name**: nell'esempio il nome è ContosoKeyVault. Questo nome verrà usato per altri comandi di Key Vault.
* **vaultUri**: nell'esempio l'URI è https://contosokeyvault.vault.azure.net. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

L'account Azure ora è autorizzato a eseguire qualsiasi operazione su questo insieme di credenziali delle chiavi. Per ora, nessun altro è autorizzato.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Aggiunta di una chiave, un segreto o un certificato all'insieme di credenziali delle chiavi

Per usare Azure Key Vault per creare automaticamente una chiave protetta tramite software, eseguire il comando `az key create`.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Se si ha una chiave esistente in un file con estensione pem, è possibile caricarla in Azure Key Vault. È possibile scegliere di proteggere la chiave con il modulo di protezione hardware o il software. In questo esempio si importa la chiave dal file con estensione PEM e la si protegge con il software, usando la password "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

A questo punto è possibile fare riferimento alla chiave creata o caricata nell'insieme di credenziali delle chiavi di Azure, usando il relativo URI. Usare `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey` per ottenere sempre la versione corrente. Usare https://[nome-insiemedicredenzialidellechiavi].vault.azure.net/keys/[nomechiave]/[id-univoco-chiave] per ottenere questa versione specifica. Ad esempio: `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Aggiungere un segreto all'insieme di credenziali, ovvero una password denominata SQLPassword con il valore "hVFkk965BuUv" per Azure Key Vault. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Fare riferimento a questa password usando il relativo URI. Usare **https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e https://[nome-insiemedicredenzialidellechiavi].vault.azure.net/secret/[nome-segreto]/[id-univoco-segreto] per ottenere questa versione specifica. Ad esempio, **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** .

Importare un certificato nell'insieme di credenziali usando un file PEM o PFX.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Verrà ora visualizzata la chiave, il segreto o il certificato creato:

* Per visualizzare le chiavi, digitare: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Per visualizzare i segreti, digitare: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Per visualizzare i certificati, digitare: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrazione di un'applicazione con Azure Active Directory

Questo passaggio di solito viene eseguito da uno sviluppatore, su un computer separato. Anche se non è specifico di Azure Key Vault, viene incluso qui per maggiore consapevolezza. Per completare la registrazione dell'app, è necessario che l'account, l'insieme di credenziali e l'applicazione siano nella stessa directory di Azure.

Le applicazioni che usano un insieme di credenziali delle chiavi devono eseguire l'autenticazione con un token di Azure Active Directory.  Il proprietario dell'applicazione deve registrarla prima in Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:

- **ID applicazione**, detto anche ID client AAD o ID app
- **Chiave di autenticazione**, detta anche segreto condiviso 

L'applicazione deve presentare entrambi questi valori ad Azure Active Directory, per ottenere un token. Il modo in cui un'applicazione viene configurata per ottenere un token dipenderà dall'applicazione. Per l'[applicazione di esempio per l'insieme di credenziali delle chiavi](https://www.microsoft.com/download/details.aspx?id=45343), il proprietario dell'applicazione imposta questi valori nel file app.config.

Per la procedura dettagliata per registrare un'applicazione in Azure Active Directory, vedere gli articoli [Integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-register-app.md), [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md) e [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Per registrare un'applicazione in Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorizzazione di un'applicazione all'uso di una chiave o un segreto

Per autorizzare l'accesso da parte dell'applicazione alla chiave o al segreto nell'insieme di credenziali, usare il comando `az keyvault set-policy` .

Se ad esempio il nome dell'insieme di credenziali è ContosoKeyVault e l'applicazione ha un ID app 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione a decrittografare e firmare con le chiavi dell'insieme di credenziali, usare il comando seguente:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Per autorizzare la stessa applicazione per la lettura di tutti i segreti nell'insieme di credenziali, digitare il comando seguente:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Impostazione di criteri di accesso avanzati per l'insieme di credenziali delle chiavi

Usare [az keyvault update](/cli/azure/keyvault#az-keyvault-update) per abilitare i criteri avanzati per l'insieme di credenziali delle chiavi.

 Abilitazione di Key Vault per la distribuzione: consente alle macchine virtuali di recuperare i certificati archiviati come segreti dall'insieme di credenziali.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Abilitazione di Key Vault per la crittografia dei dischi: obbligatorio quando si usa l'insieme di credenziali per la crittografia dei dischi di Azure.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Abilitazione di Key Vault per la distribuzione del modello: consente a Resource Manager di recuperare i segreti dall'insieme di credenziali.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Uso dei moduli di protezione hardware

Per una maggiore sicurezza, è possibile importare o generare le chiavi da moduli di protezione hardware che rimangono sempre entro il limite del modulo di protezione hardware. I moduli di protezione hardware sono certificati per FIPS 140-2 livello 2. Se questo requisito non è applicabile, saltare questa sezione e andare a Eliminare l'insieme di credenziali chiave e le chiavi e i segreti associati.

Per creare queste chiavi HSM protette, è necessaria una sottoscrizione all'insieme di credenziali che supporti le chiavi HSM protette.

Quando si crea l'insieme di credenziali, aggiungere il parametro 'sku':

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

È possibile aggiungere a questo insieme di credenziali chiavi protette tramite software (come illustrato in precedenza) e chiavi HSM protette. Per creare una chiave HSM protetta, impostare il parametro Destination su 'HSM':

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

È possibile usare il comando seguente per importare una chiave da un file con estensione PEM nel computer. Questo comando importa la chiave nei moduli HSM nel servizio dell'insieme di credenziali delle chiavi:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Il comando successivo importa un pacchetto "Bring Your Own Key" (BYOK). Ciò consente di generare la chiave nel modulo HSM locale e di trasferirlo in moduli HSM nel servizio dell'insieme di credenziali delle chiavi, senza che la chiave esca dal limite HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Per istruzioni più dettagliate su come generare questo pacchetto BYOK, vedere [Come usare chiavi HSM protette con l'insieme di credenziali delle chiavi di Azure](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Eliminazione dell'insieme di credenziali delle chiavi e di chiavi e segreti associati

Se l'insieme di credenziali delle chiavi e le chiavi o i segreti non sono più necessari, è possibile eliminare l'insieme di credenziali delle chiavi usando il comando `az keyvault delete`:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

In alternativa, è possibile eliminare l'intero gruppo di risorse di Azure, che include l'insieme di credenziali delle chiavi e tutte le altre risorse incluse in quel gruppo:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Comandi vari dell'interfaccia della riga di comando multipiattaforma di Azure

Altri comandi che potrebbero essere utili per la gestione di Azure Key Vault.

Questo comando ottiene una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Questo comando visualizza un elenco completo di proprietà per la chiave specificata:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Questo comando ottiene una visualizzazione tabulare di tutti nomi dei segreti e tutte le proprietà selezionate:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Ecco un esempio di come rimuovere una chiave specifica:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Ecco un esempio di come rimuovere un segreto specifico:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sull'interfaccia della riga di comando di Azure per i comandi di Key Vault, vedere [Key Vault CLI reference](/cli/azure/keyvault) (Riferimento dell'interfaccia della riga di comando di Key Vault).

- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori di Azure Key Vault](developers-guide.md).

- Per informazioni su Azure Key Vault e i moduli di protezione hardware, vedere [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](../keys/hsm-protected-keys.md).
