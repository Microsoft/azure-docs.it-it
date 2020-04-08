---
title: Creazione di ILB ASE v1
description: Creare un ambiente del servizio app con un servizio di bilanciamento del carico interno .ILB. Questo documento viene fornito solo per i clienti che utilizzano l'app ase versione 21 legacy.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: f05780610a2a6033b069721b143aca5e5efa6c35
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804521"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Come creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando modelli di Azure Resource Manager

> [!NOTE] 
> Questo articolo riguarda l'ambiente del servizio app v1. È disponibile una versione più recente dell'ambiente del servizio app più facile da usare ed eseguita in un'infrastruttura più potente. Per altre informazioni sulla nuova versione, iniziare con [l'introduzione all'ambiente del servizio app](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica
È possibile creare ambienti del servizio app con un indirizzo interno di rete virtuale anziché un indirizzo VIP pubblico.  L'indirizzo interno viene messo a disposizione da un componente di Azure denominato servizio di bilanciamento del carico interno (ILB).  È possibile creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando il portale di Azure.  L'ambiente può anche essere creato con l'automazione, usando i modelli di Azure Resource Manager.  Questo articolo illustra i passaggi e la sintassi necessari per creare un ambiente del servizio app con servizio di bilanciamento del carico interno con i modelli di Azure Resource Manager.

L'automazione della creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno prevede tre passaggi:

1. Viene prima creato l'ambiente del servizio app di base in una rete virtuale usando un indirizzo di servizio di bilanciamento del carico interno anziché un indirizzo VIP pubblico.  Nell'ambito di questo passaggio viene assegnato un nome di dominio radice all'ambiente del servizio app con servizio di bilanciamento del carico interno.
2. Una volta creato l'ASE ILB, viene caricato un certificato TLS/SSL.  
3. Il certificato TLS/SSL caricato viene assegnato in modo esplicito all'ASE ILB come certificato TLS/SSL "predefinito".  Questo certificato TLS/SSL verrà utilizzato per il traffico TLS verso le app nell'app del bilanciamento del servizio app `https://someapp.mycustomrootcomain.com`ilB quando le app vengono indirizzate usando il dominio radice comune assegnato all'app (ad esempio)

## <a name="creating-the-base-ilb-ase"></a>Creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno
Un modello di Azure Resource Manager di esempio e il file dei parametri associati sono disponibili in GitHub [qui][quickstartilbasecreate].

La maggior parte dei parametri del file *azuredeploy.parameters.json* è comune alla creazione sia dell'ambiente del servizio app con servizio di bilanciamento del carico interno che dell'ambiente del servizio app associato a un indirizzo VIP pubblico.  L'elenco seguente indica parametri importanti o specifici per la creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno:

* *internalLoadBalancingMode:* nella maggior parte dei casi impostare questo valore su 3, ovvero sia il traffico HTTP/HTTPS sulle porte 80/443, sia le porte del canale di controllo/dati ascoltate dal servizio FTP nell'ambiente del servizio all'utente, verranno associate a un indirizzo interno della rete virtuale allocata ilB.  Se questa proprietà viene invece impostata su 2, solo le porte correlate al servizio FTP, ovvero i canali di controllo e i canali dei dati, saranno associate a un indirizzo del servizio di bilanciamento del carico interno, mentre il traffico HTTP/HTTPS rimarrà associato all'indirizzo VIP pubblico.
* *dnsSuffix*: questo parametro indica il dominio radice predefinito che verrà assegnato all'ambiente del servizio app.  Nella variante pubblica del servizio app di Azure, il dominio radice predefinito per tutte le app Web è *azurewebsites.net*.  Poiché tuttavia un ambiente del servizio app con servizio di bilanciamento del carico interno è interno alla rete virtuale di un cliente, non ha senso usare il dominio radice predefinito del servizio pubblico.  Un ambiente del servizio app con servizio di bilanciamento del carico interno deve invece avere un dominio radice predefinito appropriato per la rete virtuale interna dell'azienda.  Un'ipotetica azienda Contoso Corporation potrebbe ad esempio usare un dominio radice predefinito *internal-contoso.com* per le app che devono essere risolvibili e accessibili solo nella rete virtuale di Contoso. 
* *ipSslAddressCount*: questo parametro viene automaticamente impostato sul valore predefinito di 0 nel file *azuredeploy.json* perché gli ambienti del servizio app con servizio di bilanciamento del carico interno hanno un solo indirizzo del servizio di bilanciamento del carico interno.  Non sono disponibili indirizzi IP SSL espliciti per un ambiente del servizio app con servizio di bilanciamento del carico interno, quindi il pool di indirizzi IP SSL per un ambiente del servizio app con servizio di bilanciamento del carico interno deve essere impostato su zero, in caso contrario si verificherà un errore di provisioning. 

Dopo che il file *azuredeploy.parameters.json* è stato compilato per un ambiente del servizio app con servizio di bilanciamento del carico interno, l'ambiente potrà essere quindi creato con il seguente frammento di codice Powershell.  Modificare i parametri PATH dei file per fare in modo che corrispondano al percorso dei file dei modelli di Azure Resource Manager nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo aver inviato il modello di Azure Resource Manager, la creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno richiederà alcune ore.  Al termine della creazione, l'ambiente del servizio app con servizio di bilanciamento del carico interno verrà visualizzato nel portale nell'elenco di ambienti del servizio app per la sottoscrizione che ha attivato la distribuzione.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Caricamento e configurazione del certificato TLS/SSL "Predefinito"
Una volta creato l'ASE ILB, un certificato TLS/SSL deve essere associato all'app come certificato TLS/SSL "predefinito" per stabilire connessioni TLS/SSL alle app.  Continuando con l'ipotetico esempio contoso Corporation, se il suffisso DNS predefinito *https://some-random-app.internal-contoso.com* dell'ambiente del servizio app è *internal-contoso.com*, una connessione a richiede un certificato TLS/SSL valido per*internal-contoso.com*. 

Esistono diversi modi per ottenere un certificato TLS/SSL valido, incluse le CA interne, l'acquisto di un certificato da un'autorità di certificazione esterna e l'utilizzo di un certificato autofirmato.  Indipendentemente dall'origine del certificato TLS/SSL, i seguenti attributi del certificato devono essere configurati correttamente:

* *Subject*: questo attributo deve essere impostato su **.your-root-domain-here.com*.
* *Nome alternativo soggetto*: questo attributo deve includere sia **.your-root-domain-here.com* che **.scm.your-root-domain-here.com*.  Il motivo della seconda voce è che le connessioni TLS al sito SCM/Kudu associato a ogni app verranno effettuate utilizzando un indirizzo nel formato *your-app-name.scm.your-root-domain-here.com*.

Con un certificato TLS/SSL valido in mano, sono necessari due passaggi preparatori aggiuntivi.  Il certificato TLS/SSL deve essere convertito/salvato come file con estensione pfx.  Il file con estensione pfx deve includere tutti i certificati intermedi e radice ed essere protetto con una password.

Il file con estensione pfx risultante deve quindi essere convertito in una stringa base64 perché il certificato TLS/SSL verrà caricato usando un modello di Azure Resource Manager.Then the resultant .pfx file needs to be converted into a base64 string because the TLS/SSL certificate will be uploaded using an Azure Resource Manager template.  Poiché i modelli di Azure Resource Manager sono file di testo, il file con estensione pfx deve essere convertito in una stringa con codifica Base64 per essere incluso come parametro del modello.

Il frammento di codice Powershell riportato di seguito illustra un esempio di generazione di un certificato autofirmato, esportazione del certificato come file con estensione pfx, conversione del file con estensione pfx in una stringa con codifica Base64 e quindi salvataggio della stringa con codifica Base64 in un file separato.  Il codice Powershell per la codifica Base64 è stato adattato dal [blog di script di Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Dopo che il certificato TLS/SSL è stato generato correttamente e convertito in una stringa codificata in base64, è possibile usare il modello di Azure Resource Manager di Azure in GitHub per [la configurazione del certificato TLS/SSL predefinito.][configuringDefaultSSLCertificate]

I parametri del file *azuredeploy.parameters.json* sono elencati di seguito:

* *appServiceEnvironmentName*: nome dell'ambiente del servizio appE ILB in fase di configurazione.
* *existingAseLocation*: stringa di testo contenente l'area di Azure in cui è stato distribuito l'ambiente del servizio app ILB.existingAseLocation : Text string containing the Azure region where the ILB ASE was deployed.  Ad esempio "Stati Uniti centro-meridionali".
* *pfxBlobString*: rappresentazione di stringa codificata based64 del file con estensione pfx.  Usando il frammento di codice indicato in precedenza, la stringa contenuta in "exportedcert.pfx.b64" dovrà essere copiata e incollata come valore dell'attributo *pfxBlobString* .
* *password*: la password utilizzata per proteggere il file .pfx.
* *certificateThumbprint*: Identificazione personale del certificato.  Se si recupera questo valore da Powershell, ad esempio *$certificate.Thumbprint* nel frammento di codice precedente, è possibile usare il valore così com'è.  Se tuttavia si copia il valore dalla finestra di dialogo del certificato di Windows, rimuovere gli spazi estranei.  Il *certificatoThumbprint* dovrebbe essere simile a: AF3143EB61D43F6727842115BB7F17BBECAE
* *certificateName*: Un identificatore di stringa descrittivo di propria scelta utilizzato per identificare il certificato.  Il nome viene usato come parte dell'identificatore univoco di Azure Resource Manager per l'entità *Microsoft.Web/certificates* che rappresenta il certificato TLS/SSL.  Il nome **deve** terminare con il suffisso seguente: \_yourASENameHere_InternalLoadBalancingASE.  Questo suffisso viene usato dal portale per indicare che il certificato è usato per la protezione di un ambiente del servizio app abilitato al bilanciamento del carico interno.

Un esempio abbreviato di *azuredeploy.parameters.json* è illustrato di seguito:

    {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Dopo aver compilato il file *azuredeploy.parameters.json,* è possibile configurare il certificato TLS/SSL predefinito usando il frammento di codice di Powershell seguente.  Modificare i parametri PATH dei file per fare in modo che corrispondano al percorso dei file dei modelli di Azure Resource Manager nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo l'invio del modello di Azure Resource Manager, ci vorranno circa 40 minuti per ogni front-end dell'ambiente del servizio app per apportare la modifica.  Per un ambiente del servizio app di dimensioni predefinite che usa due front-end, ad esempio, l'operazione richiederà all'incirca un'ora e venti minuti.  Durante l'esecuzione del modello, l'ambiente del servizio app non potrà essere ridimensionato.  

Una volta completato il modello, è possibile accedere alle app nell'ambiente del servizio app ILB tramite HTTPS e le connessioni verranno protette utilizzando il certificato TLS/SSL predefinito.  Il certificato TLS/SSL predefinito verrà usato quando le app nell'ase ILB vengono indirizzate utilizzando una combinazione del nome dell'applicazione più il nome host predefinito.  Ad *https://mycustomapp.internal-contoso.com* esempio, si utilizzerebbe il certificato TLS/SSL predefinito per *.internal-contoso.com*.

Tuttavia, proprio come le app in esecuzione nel servizio multi-tenant pubblico, gli sviluppatori possono anche configurare nomi host personalizzati per le singole app e quindi configurare binding di certificati SNI TLS/SSL univoci per le singole app.  

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

