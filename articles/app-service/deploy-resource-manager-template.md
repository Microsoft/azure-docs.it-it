---
title: Distribuire app con modelli
description: Informazioni dettagliate sulla creazione di modelli di Azure Resource Manager per il provisioning e la distribuzione di app del servizio app.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 0a282a412823207e5f662441158000e8c6121796
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637934"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Linee guida per la distribuzione di app Web tramite i modelli di Azure Resource Manager

Questo articolo fornisce suggerimenti per la creazione di modelli di Azure Resource Manager per la distribuzione di soluzioni del servizio app di Azure. Questi suggerimenti possono essere utili per evitare problemi comuni.

## <a name="define-dependencies"></a>Definire le dipendenze

Per definire le dipendenze per le app Web è necessario comprendere le modalità di interazione delle risorse all'interno di un'app Web. Se si specificano le dipendenze in un ordine non corretto, potrebbero verificarsi errori di distribuzione o potrebbe crearsi una race condition che blocca la distribuzione.

> [!WARNING]
> Se si include un'estensione del sito MSDeploy nel modello, è necessario impostare le risorse di configurazione come dipendenti dalla risorsa MSDeploy. Le modifiche alla configurazione causano il riavvio asincrono del sito. Impostando le risorse di configurazione come dipendenti da MSDeploy, si assicura che MSDeploy termini prima del riavvio del sito. Senza queste dipendenze il sito potrebbe essere riavviato durante il processo di distribuzione di MSDeploy. Per un modello di esempio, vedere [Modello WordPress con dipendenza di distribuzione Web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

L'immagine seguente mostra l'ordine di dipendenza per varie risorse del servizio app:

![Dipendenze delle app Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Le risorse vengono distribuite nell'ordine seguente:

**Livello 1**
* Piano di servizio app.
* Qualsiasi altra risorsa correlata come i database o gli account di archiviazione.

**Livello 2**
* App Web - dipende dal piano di servizio app.
* Istanza di Application Insights in Azure destinata alla server farm - dipende dal piano di servizio app.

**Livello 3**
* Controllo del codice sorgente - dipende dall'app Web.
* Estensione del sito MSDeploy - dipende dall'app Web.
* L'istanza di Azure Application Insights destinata all'app Web dipende dall'app Web.

**Livello 4**
* Certificato del servizio app - dipende dal controllo del codice sorgente o da MSDeploy se uno dei due è presente, altrimenti dall'app Web.
* Impostazioni di configurazione (stringhe di connessione, valori di configurazione Web, impostazioni app) - dipendono dal controllo del codice sorgente o da MSDeploy se uno dei due è presente, altrimenti dall'app Web.

**Livello 5**
* Binding dei nomi host - dipende dal certificato, se presente, altrimenti da una risorsa di livello superiore.
* Estensioni del sito - dipendono dalle impostazioni di configurazione se presenti, altrimenti da una risorsa di livello superiore.

In genere una soluzione include solo alcune di queste risorse e di questi livelli. Per i livelli mancanti, eseguire il mapping delle risorse di livello inferiore al livello superiore successivo.

L'esempio seguente mostra parte di un modello. Il valore di configurazione della stringa di connessione dipende dall'estensione MSDeploy. L'estensione MSDeploy dipende dall'app Web e dal database. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Per un esempio pronto per l'esecuzione che usa il codice precedente, vedere [Template: Build a simple Umbraco Web App](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple) (Modello: compilare un'app Web Umbraco semplice).

## <a name="find-information-about-msdeploy-errors"></a>Trovare informazioni sugli errori di MSDeploy

Se il modello di Resource Manager usa MSDeploy, i messaggi di errore relativi alla distribuzione possono essere di difficile comprensione. Per ottenere maggiori informazioni su una distribuzione non riuscita, provare questa procedura:

1. Passare alla [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) del sito.
2. Passare alla cartella D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Cercare i file appManagerStatus.xml e appManagerLog.xml. Nel primo file viene registrato lo stato, nel secondo le informazioni relative all'errore. Se l'errore non ti è chiaro, puoi includerlo quando chiedi aiuto al [forum.](https://docs.microsoft.com/answers/topics/azure-webapps.html)

## <a name="choose-a-unique-web-app-name"></a>Immettere un nome univoco per l'app Web

Il nome dell'app Web deve essere univoco a livello globale. È possibile usare una convenzione di denominazione che con ogni probabilità è univoca oppure usare la [funzione uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) per assistenza nella generazione di un nome univoco.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Distribuire il certificato dell'app Web da Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se il modello include una risorsa [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) per l'associazione TLS/SSL e il certificato è archiviato in un insieme di credenziali delle chiavi, è necessario assicurarsi che l'identità del servizio app possa accedere al certificato.

In Azure globale, l'entità servizio di servizio app include l'ID di **abfa0a7c-a6b6-4736-8310-5855508787cd**. Per fornire accesso a Key Vault all'entità servizio di servizio app, usare:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

In Azure per enti pubblici, l'entità servizio di servizio app ha l'ID **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Usare l'ID riportato nell'esempio precedente.

In Key Vault, selezionare **Certificati** e **Genera/Importa** per caricare il certificato.

![Importare il certificato](media/web-sites-rm-template-guidance/import-certificate.png)

Nel modello, specificare il nome del certificato per il `keyVaultSecretName`.

Per un modello di esempio, vedere [Distribuire un certificato di app Web dal segreto di Key Vault e usarlo per la creazione di binding SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione sulla distribuzione di app Web con un modello, vedere [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](deploy-complex-application-predictably.md).
* Per informazioni sulla sintassi e le proprietà JSON per i tipi di risorsa nei modelli, vedere [Azure Resource Manager template reference](/azure/templates/) (Informazioni di riferimento sui modelli di Azure Resource Manager).
