---
title: Connessione ai server di Azure Analysis Services | Microsoft Docs
description: Informazioni su come connettersi e ottenere dati da un server di Analysis Services in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 170cf0081e6671451ece6dc2924ae7e418f520a2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506775"
---
# <a name="connecting-to-servers"></a>Connessione ai server

Questo articolo descrive la connessione a un server usando la modellazione dei dati e le applicazioni di gestione come SQL Server Management Studio (SSMS) o Visual Studio con progetti Analysis Services o con applicazioni di creazione di report client come Microsoft Excel, Power BI Desktop o applicazioni personalizzate. Le connessioni ad Azure Analysis Services usano HTTPS.

## <a name="client-libraries"></a>Librerie client

[Ottenere le librerie client più recenti](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)

Tutte le connessioni a qualunque tipo di server richiedono le librerie client AMO, ADOMD.NET e OLEDB aggiornate per connettersi e interagire con un server di Analysis Services. Per SSMS, Visual Studio, Excel 2016 e versioni successive e Power BI, le librerie client più recenti vengono installate o aggiornate con le versioni mensili. In alcuni casi, tuttavia, è possibile che un'applicazione non abbia la versione più recente. Ad esempio, quando alcuni criteri ritardano gli aggiornamenti o quando gli aggiornamenti di Office 365 sono su Deferred Channel.

> [!NOTE]
> Le librerie client non sono in grado di connettersi a Azure Analysis Services tramite i server proxy che richiedono un nome utente e una password. 

## <a name="server-name"></a>Nome server

Quando si crea un server di Analysis Services in Azure, si specifica un nome univoco e l'area in cui il server deve essere creato. Quando si specifica il nome del server in una connessione, lo schema di denominazione del server è il seguente:

```
<protocol>://<region>/<servername>
```
 Dove protocol è la stringa **asazure**, region è l'Uri in cui è stato creato il server, ad esempio westus.asazure.windows.net, e servername è il nome del server univoco all'interno dell'area.

### <a name="get-the-server-name"></a>Ottenere il nome del server

Nel **portale di Azure** > server > **Panoramica** > **Nome server** copiare l'intero nome del server. Se anche altri utenti nell'organizzazione si connettono a questo server, è opportuno condividere il nome del server. Quando si specifica un nome di server, è necessario usare l'intero percorso.

![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Il protocollo per l'area Stati Uniti orientali 2 è **aspaaseastus2**.

## <a name="connection-string"></a>Stringa di connessione

Quando ci si connette a Azure Analysis Services usando il modello a oggetti tabulare, usare i formati seguenti per la stringa di connessione:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticazione integrata di Azure Active Directory

L'autenticazione integrata seleziona la cache delle credenziali di Azure Active Directory, se disponibile. In caso contrario, viene visualizzata la finestra di accesso di Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticazione di Azure Active Directory con nome utente e password

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticazione di Windows (sicurezza integrata)

Usare l'account di Windows su cui è in esecuzione il processo corrente.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Connettersi usando un file con estensione odc

Con le versioni precedenti di Excel, gli utenti possono connettersi a un server di Azure Analysis Services usando un file Office Data Connection, con estensione odc. Per altre informazioni, vedere [Creare un file Office Data Connection (con estensione odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Passaggi successivi

[Connettersi con Excel](analysis-services-connect-excel.md)    
[Connetti con Power BI](analysis-services-connect-pbi.md)   
[Gestire il server](analysis-services-manage.md)   

