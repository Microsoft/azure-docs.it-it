---
title: File di inclusione
description: File di inclusione
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67180580"
---
## <a name="download-the-source-code"></a>Scaricare il codice sorgente

I repository di codice sorgente di monitoraggio remoto includono il codice sorgente e i file di configurazione Docker necessari per eseguire le immagini Docker dei microservizi.

Per clonare e creare una versione locale del repository, usare l'ambiente della riga di comando per passare a una cartella adatta nel computer locale. Eseguire quindi uno dei set di comandi seguenti per clonare il repository .NET:

Per scaricare la versione più recente delle implementazioni di microservizi .NET, eseguire:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Questi comandi scaricano il codice sorgente per tutti i microservizi oltre agli script usati per eseguire i microservizi in locale. Nonostante non sia necessario per eseguire i microservizi in Docker, il codice sorgente è utile se si intende modificare in un secondo momento l'acceleratore di soluzione e testare le modifiche in locale.

## <a name="deploy-the-azure-services"></a>Distribuire i servizi di Azure

Nonostante questo articolo illustri come eseguire i microservizi in locale, questi dipendono dai servizi di Azure eseguiti nel cloud. Usare lo script seguente per distribuire i servizi di Azure. Gli esempi di script seguenti presuppongono che si usi il repository .NET in un computer Windows. Se si lavora in un altro ambiente, modificare in modo appropriato i percorsi, le estensioni di file e i separatori di percorso.

### <a name="create-new-azure-resources"></a>Creare nuove risorse di Azure

Se non sono ancora state create le risorse di Azure necessarie, seguire questa procedura:

1. Nell'ambiente della riga di comando passare alla cartella **\services\scripts\local\launch** nella copia clonata del repository.

1. Eseguire i comandi seguenti per installare lo strumento dell'interfaccia della riga di comando **pcs** e accedere all'account di Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Eseguire lo script **start.cmd**. Lo script richiede le informazioni seguenti:
   * Nome della soluzione.
   * Sottoscrizione di Azure da usare.
   * Località del data center di Azure da usare.

     Lo script crea un gruppo di risorse in Azure con il nome della soluzione. Questo gruppo di risorse contiene le risorse di Azure usate dall'acceleratore di soluzione. È possibile eliminare il gruppo di risorse nel momento in cui le risorse corrispondenti non sono più necessarie.

     Lo script aggiunge anche un set di variabili di ambiente con un prefisso **PCS** nel computer locale. Queste variabili di ambiente forniscono i dettagli per il monitoraggio remoto per essere in grado di leggere da una risorsa dell'insieme di credenziali delle chiavi di Azure.These environment variables provide the details for Remote Monitoring to be able to read from an Azure Key Vault resource. Questa risorsa dell'insieme di credenziali delle chiavi è la posizione da cui il monitoraggio remoto leggerà i valori di configurazione.

     > [!TIP]
     > Al termine dello script, le variabili di ambiente vengono salvate anche in un file denominato ** \<nome soluzione\>\\\\\<\>.env della cartella inizio .pcs**. Sarà possibile usarle per distribuzioni future di acceleratori di soluzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

1. Uscire dall'ambiente della riga di comando.

### <a name="use-existing-azure-resources"></a>Usare le risorse di Azure esistenti

Se sono già state create le risorse di Azure necessarie, creare le corrispondenti variabili di ambiente nel computer locale.
Impostare le variabili di ambiente per gli elementi seguenti:Set the environment variables for the following:
* **PCS_KEYVAULT_NAME** - Nome della risorsa dell'insieme di credenziali delle chiavi di Azure
* **PCS_AAD_APPID** - L'ID applicazione AAD
* **PCS_AAD_APPSECRET** - Il segreto dell'applicazione AAD

I valori di configurazione verranno letti da questa risorsa dell'insieme di credenziali delle chiavi di Azure.Configuration values will be read from this Azure Key Vault resource. Queste variabili di ambiente possono essere salvate nel ** \<\>\\file\\\<\>.pcs nome soluzione** della cartella inizio dalla distribuzione. Tutte le variabili di ambiente impostate nel computer locale ignorano i valori nel file **services\\scripts\\local\\.env** quando si esegue **docker-compose**.

Parte della configurazione necessaria per il microservizio **Key Vault** viene archiviata in un'istanza dell'insieme di credenziali delle chiavi creata durante la distribuzione iniziale. Le variabili corrispondenti in keyvault devono essere modificate in base alle esigenze.