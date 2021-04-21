---
title: 'Esercitazione: Proteggere un server Web con certificati TLS/SSL'
description: In questa esercitazione viene illustrato come usare l'interfaccia della riga di comando di Azure per proteggere una macchina virtuale di Linux che esegue il server Web NGINX con certificati SSL archiviati in Azure Key Vault.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4b2290d29d1f6fbf8acd2e2652cd47a321fe674f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813165"
---
# <a name="tutorial-use-tlsssl-certificates-to-secure-a-web-server"></a>Esercitazione: Usare certificati TLS/SSL per proteggere un server Web
Per proteggere i server Web, è possibile usare un certificato Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL), per crittografare il traffico Web. Questi certificati TLS/SSL possono essere archiviati in Azure Key Vault e consentono distribuzioni sicure dei certificati nelle macchine virtuali Linux in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un Azure Key Vault
> * Generare o caricare un certificato in Key Vault
> * Creare una macchina virtuale e installare il server Web NGINX
> * Inserire il certificato nella macchina virtuale e configurare NGINX con un'associazione TLS

Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](../../cloud-shell/overview.md), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).


## <a name="overview"></a>Panoramica
Azure Key Vault consente di proteggere chiavi crittografiche e segreti, come certificati e password. Key Vault semplifica il processo di gestione dei certificati e consente di mantenere il controllo delle chiavi che accedono a tali certificati. È possibile creare un certificato autofirmato in Key Vault o caricarne uno esistente, un certificato attendibile di cui si è già proprietari.

Invece di usare un'immagine di macchina virtuale personalizzata che include certificati incorporati, si inseriscono i certificati in una macchina virtuale in esecuzione. Questo processo assicura che, durante la distribuzione, in un server Web vengano installati i certificati più aggiornati. Se si rinnova o sostituisce un certificato, non è necessario creare anche una nuova immagine di macchina virtuale personalizzata. I certificati più recenti vengono automaticamente inseriti quando si creano macchine virtuali aggiuntive. Durante l'intero processo, i certificati non lasciano mai la piattaforma Azure e non vengono mai esposti in uno script, in una cronologia della riga di comando o in un modello.


## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault
Per poter creare un insieme di credenziali delle chiavi e i certificati, è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupSecureWeb* nella località *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Creare quindi un insieme di credenziali delle chiavi con il comando [az keyvault create](/cli/azure/keyvault) e abilitarlo all'uso quando si distribuisce una macchina virtuale. Ogni Key Vault deve avere un nome univoco in lettere minuscole. Nell'esempio seguente sostituire *\<mykeyvault>* con il nome univoco del proprio Key Vault:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generare un certificato e archiviarlo in Key Vault
Per la produzione è necessario importare un certificato valido firmato da un provider attendibile con il comando [az keyvault certificate import](/cli/azure/keyvault/certificate). Per questa esercitazione, l'esempio seguente illustra come sia possibile generare un certificato autofirmato con il comando [az keyvault certificate create](/cli/azure/keyvault/certificate) che usi i criteri dei certificati predefiniti:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Preparare un certificato per l'uso con una macchina virtuale
Per usare il certificato durante il processo di creazione della macchina virtuale, ottenere l'ID del certificato con il comando [az keyvault secret list-versions](/cli/azure/keyvault/secret). Convertire il certificato con il comando [az vm secret format](/cli/azure/vm/secret#az_vm_secret_format). L'esempio seguente assegna l'output di questi comandi a delle variabili per semplificarne l'uso nei passaggi successivi:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Creare una configurazione cloud-init per proteggere NGINX
[Cloud-init](https://cloudinit.readthedocs.io) è un approccio diffuso per personalizzare una macchina virtuale Linux al primo avvio. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Quando cloud-init viene eseguito durante il processo di avvio iniziale non vi sono altri passaggi o agenti necessari per applicare la configurazione.

Quando si crea una macchina virtuale, certificati e chiavi vengono archiviati nella directory */var/lib/waagent/* protetta. Per automatizzare l'aggiunta del certificato alla macchina virtuale e la configurazione del server Web, usare cloud-init. In questo esempio viene installato e configurato il server Web NGINX. È possibile usare lo stesso processo per installare e configurare Apache. 

Creare un file denominato *cloud-init-web-server.txt* e incollare la configurazione seguente:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Creare una macchina virtuale sicura
Creare quindi una macchina virtuale con il comando [az vm create](/cli/azure/vm). I dati del certificato sono inseriti da Key Vault con il parametro `--secrets`. Passare la configurazione cloud-init con il parametro `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Per creare la macchina virtuale, installare i pacchetti e avviare l'applicazione sono necessari alcuni minuti. Dopo aver creato la macchina virtuale, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Questo indirizzo viene usato per accedere al sito in un Web browser.

Per consentire al traffico Web protetto di raggiungere la macchina virtuale, aprire la porta 443 da Internet con il comando [az vm open-port](/cli/azure/vm):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testare l'applicazione Web protetta
È ora possibile aprire un Web browser e immettere *https:\/\/\<publicIpAddress>* nella barra degli indirizzi. Fornire il proprio indirizzo IP pubblico dal processo di creazione della macchina virtuale. Se è stato usato un certificato autofirmato, accettare l'avviso di sicurezza:

![Accettare l'avviso di sicurezza del Web browser](./media/tutorial-secure-web-server/browser-warning.png)

Il sito NGINX protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Visualizzare il sito protetto NGINX in esecuzione](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è protetto un server Web NGINX con un certificato TLS/SSL archiviato in Azure Key Vault. Si è appreso come:

> [!div class="checklist"]
> * Creare un Azure Key Vault
> * Generare o caricare un certificato in Key Vault
> * Creare una macchina virtuale e installare il server Web NGINX
> * Inserire il certificato nella macchina virtuale e configurare NGINX con un'associazione TLS

Seguire questo collegamento per vedere esempi di script predefiniti delle macchine virtuali.

> [!div class="nextstepaction"]
> [Esempi di script delle macchine virtuali Linux](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)