---
title: Eseguire la migrazione di un nome DNS attivoMigrate an active DNS name
description: Informazioni su come eseguire la migrazione di un nome di dominio DNS personalizzato già assegnato a un sito live al Servizio app di Azure senza tempi di inattività.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 79bd0a19a9bd8ebd100ed80ca0206656d73ef76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672367"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Eseguire la migrazione di un nome DNS attivo al Servizio app di Azure

Questo articolo descrive come eseguire la migrazione di un nome DNS attivo al [Servizio app di Azure](../app-service/overview.md) senza tempi di inattività.

Quando si esegue la migrazione di un sito live e del relativo nome di dominio DNS al Servizio app di Azure, tale nome DNS è già usato dal traffico live. È possibile evitare tempi di inattività nella risoluzione DNS durante la migrazione associando preventivamente il nome DNS attivo al Servizio app di Azure.

Se i tempi di inattività non rappresentano un problema nella risoluzione DNS, vedere [Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura:

- [Verificare che l'app del servizio app non sia inclusa nel livello GRATUITO](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Associare preventivamente il nome del dominio

Quando si associa preventivamente un dominio personalizzato, è necessario eseguire entrambe le operazioni seguenti prima di apportare modifiche ai record DNS:

- Verificare la proprietà del dominio
- Abilitare il nome di dominio per l'app

Dopo aver eseguito la migrazione del nome DNS personalizzato dal sito precedente all'app del servizio app, non si verificheranno più tempi di inattività nella risoluzione DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Creare un record di verifica del dominio

Per verificare la proprietà del dominio, aggiungere un record TXT. Il record TXT esegue il mapping da _awverify.&lt;subdomain>_ a _&lt;appname>.azurewebsites.net_. 

Il record TXT necessario varia a seconda del record DNS di cui si vuole eseguire la migrazione. Ad esempio, vedere la tabella seguente (`@` rappresenta in genere il dominio radice):

| Esempio di record DNS | Host TXT | Valore TXT |
| - | - | - |
| \@ (radice) | _awverify_ | _&lt;nomeapp>.azurewebsites.net_ |
| www (sottodominio) | _awverify.www_ | _&lt;nomeapp>.azurewebsites.net_ |
| \* (wildcard) | _awverify.\*_ | _&lt;nomeapp>.azurewebsites.net_ |

Nella pagina dei record DNS prendere nota del tipo di record del nome DNS di cui si vuole eseguire la migrazione. Il servizio app supporta i mapping da record CNAME e A.

> [!NOTE]
> Per alcuni provider, come ad esempio CloudFlare, `awverify.*` non è un record valido. Usare invece solo `*`.

> [!NOTE]
> I record con il carattere jolly `*` non convalideranno i sottodomini con un record CNAME esistente. Potrebbe essere necessario creare in modo esplicito un record TXT per ogni sottodominio.


### <a name="enable-the-domain-for-your-app"></a>Abilitare il dominio per l'app

Nel riquadro di spostamento a sinistra della pagina dell'app nel [portale di Azure](https://portal.azure.com) selezionare **Domini personalizzati**. 

![Menu del dominio personalizzato](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Nella pagina **Domini personalizzati** selezionare l'icona **+** accanto ad **Aggiungi il nome host**.

![Aggiunta del nome host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digitare il nome di dominio completo al quale è stato aggiunto un record TXT, ad esempio `www.contoso.com`. Per un dominio con caratteri jolly (ad esempio \*. contoso.com), è possibile usare un nome DNS qualsiasi che corrisponda al dominio con caratteri jolly. 

Selezionare **Convalida**.

Viene attivato il pulsante **Aggiungi il nome host**. 

Assicurarsi che **Tipo di record del nome host** sia impostato sul tipo di record DNS di cui si vuole eseguire la migrazione.

Selezionare **Aggiungi il nome host**.

![Aggiunta del nome DNS all'app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

La visualizzazione del nuovo nome host nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Provare ad aggiornare il browser per visualizzare i dati più recenti.

![Record CNAME aggiunto](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Il dominio DNS personalizzato è ora abilitato nell'app Azure. 

## <a name="remap-the-active-dns-name"></a>Modificare il mapping del nome DNS attivo

A questo punto resta solo da modificare il mapping del record DNS attivo in modo che faccia riferimento al servizio app. Per il momento si riferisce ancora al sito precedente.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copiare l'indirizzo IP dell'app (solo record A)

Se si vuole modificare il mapping di un record CNAME, ignorare questa sezione. 

Per modificare il mapping di un record A, è necessario l'indirizzo IP esterno dell'app del servizio app, visualizzato nella pagina **Domini personalizzati**.

Chiudere la pagina **Aggiungi il nome host** selezionando **X** nell'angolo in alto a destra. 

Nella pagina **Domini personalizzati**, copiare l'indirizzo IP dell'applicazione.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aggiornare il record DNS

Tornare alla pagina dei record DNS del provider di dominio e selezionare il record DNS per il quale si vuole modificare il mapping.

Per l'esempio di dominio radice `contoso.com`, modificare il mapping del record A o CNAME come negli esempi illustrati nella tabella seguente: 

| Esempio di FQDN | Tipo di record | Host | valore |
| - | - | - | - |
| contoso.com (radice) | Una  | `@` | Indirizzo IP ricavato da [Copiare l'indirizzo IP dell'app](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;nomeapp>.azurewebsites.net_ |
| \*.contoso.com (carattere jolly) | CNAME | _\*_ | _&lt;nomeapp>.azurewebsites.net_ |

Salvare le impostazioni.

Le query DNS inizieranno a risolversi nell'app del servizio app immediatamente dopo la propagazione DNS.

## <a name="active-domain-in-azure"></a>Dominio attivo in AzureActive domain in Azure

È possibile eseguire la migrazione di un dominio personalizzato attivo in Azure, tra sottoscrizioni o all'interno della stessa sottoscrizione. Tuttavia, una migrazione senza tempi di inattività richiede l'app di origine e all'app di destinazione viene assegnato lo stesso dominio personalizzato in un determinato momento. Pertanto, è necessario assicurarsi che le due applicazioni non vengono distribuite nella stessa unità di distribuzione (internamente noto come uno spazio web). Un nome di dominio può essere assegnato a una sola app in ogni unità di distribuzione.

È possibile trovare l'unità di distribuzione per l'app esaminando `<deployment-unit>.ftp.azurewebsites.windows.net`il nome di dominio dell'URL FTP/S . Verificare che l'unità di distribuzione sia diversa tra l'app di origine e l'app di destinazione. L'unità di distribuzione di un'app è determinata dal piano di [servizio app](overview-hosting-plans.md) in cui si trova. Viene selezionato in modo casuale da Azure quando si crea il piano e non può essere modificato. Azure si assicura che due piani si trovino nella stessa unità di distribuzione quando vengono creati nello stesso gruppo di [risorse *e* nella stessa area,](app-service-plan-manage.md#create-an-app-service-plan)ma non dispone di alcuna logica per assicurarsi che i piani si trovino in unità di distribuzione diverse. L'unico modo per creare un piano in un'unità di distribuzione diversa consiste nel continuare a creare un piano in un nuovo gruppo di risorse o area fino a quando non si ottiene un'unità di distribuzione diversa.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come associare un certificato SSL personalizzato al servizio app.

> [!div class="nextstepaction"]
> [Associare un certificato SSL al servizio app di AzureBind an SSL certificate to Azure App Service](configure-ssl-bindings.md)
