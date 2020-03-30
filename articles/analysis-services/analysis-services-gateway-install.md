---
title: Installare il gateway dati locale per Azure Analysis Services - Documenti Microsoft
description: Informazioni su come installare e configurare un gateway dati locale per connettersi a origini dati locali da un server Di Azure Analysis Services.Learn how to install and configure an On-premises data gateway to connect to on-premises data sources from an Azure Analysis Services server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062150"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installare e configurare un gateway dati locale

Quando uno o più server Azure Analysis Services nella stessa area si connettono a origini dati locali, è necessario un gateway dati locale.  Mentre il gateway installato è lo stesso usato da altri servizi come Power BI, Power Apps e App per la logica, durante l'installazione per Azure Analysis Services, è necessario completare alcuni passaggi aggiuntivi. Questo articolo di installazione è specifico di **Azure Analysis Services.** 

Per altre informazioni sul funzionamento di Azure Analysis Services con il gateway, vedere [Connessione a origini dati locali.](analysis-services-gateway.md) Per altre informazioni sugli scenari di installazione avanzati e sul gateway in generale, vedere la documentazione sui [gateway dati locali.](/data-integration/gateway/service-gateway-onprem)

## <a name="prerequisites"></a>Prerequisiti

**Requisiti minimi:**

* .NET Framework 4.5
* Versione a 64 bit di Windows 8 / Windows Server 2012 R2 (o versione successiva)

**Consigliato:**

* 8 CPU core
* 8 GB di memoria
* Versione a 64 bit di Windows 8 / Windows Server 2012 R2 (o versione successiva)

**Considerazioni importanti:**

* Durante l'installazione, quando si registra il gateway con Azure, viene selezionata l'area predefinita per la sottoscrizione. È possibile scegliere un abbonamento e un'area geografica diversi. Se si dispone di server in più aree, è necessario installare un gateway per ogni area. 
* Il gateway non può essere installato in un controller di dominio.
* In un singolo computer può essere installato un solo gateway.
* Installare il gateway in un computer che rimane attivo e non passa alla modalità di sospensione.
* Non installare il gateway in un computer con una connessione solo wireless alla rete. È infatti possibile che si verifichi un calo delle prestazioni.
* Quando si installa il gateway, l'account utente con il quale è stato effettuato l'accesso al computer deve accedere come privilegi del servizio. Al termine dell'installazione, il servizio di gateway dati locale usa l'account NT SERVICE\PBIEgwService per accedere come un servizio. È possibile specificare un altro account durante l'installazione o in Services al termine dell'installazione. Assicurarsi che le impostazioni dei Criteri di gruppo consentano di accedere come privilegi di servizio all'account con il quale ci si è connessi al momento dell'installazione e all'account di servizio scelto per l'accesso.
* Accedere ad Azure con un account di Azure AD per lo stesso [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) della sottoscrizione in cui si sta registrando il gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione di un gateway.
* Se le origini dati si trovano in un'istanza di Rete virtuale di Azure (VNet), è necessario configurare la proprietà del server [AlwaysUseGateway](analysis-services-vnet-gateway.md).

## <a name="download"></a><a name="download"></a>Scaricare

 [Scaricare il gateway](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Installazione di

1. Eseguire l'installazione.

2. Selezionare **Gateway dati locale**.

   ![Select](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Selezionare un percorso, accettare le condizioni e quindi fare clic su **Installa**.

   ![Percorso di installazione e condizioni di licenza](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Accedere ad Azure. L'account deve essere nell'istanza di Microsoft Azure Active Directory del tenant. Questo account viene usato per l'amministratore del gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione del gateway.

   ![Accedere ad Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se si accede con un account di dominio, questo viene mappato all'account aziendale in Azure AD. L'account aziendale viene usato come amministratore del gateway.

## <a name="register"></a><a name="register"></a>Registro

Per creare una risorsa per il gateway in Azure, è necessario registrare l'istanza locale installata con il servizio cloud gateway. 

1.  Selezionare l'opzione **Consente di registrare un nuovo gateway in questo computer**.

    ![Register](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digitare un nome e la chiave di ripristino per il gateway. Per impostazione predefinita, il gateway usa l'area predefinita della sottoscrizione. Se è necessario selezionare un'area diversa, selezionare **Cambia area**.

    > [!IMPORTANT]
    > Salvare la chiave di ripristino in un luogo sicuro. La chiave di ripristino è necessaria per poter acquisire la proprietà, eseguire la migrazione o ripristinare un gateway. 

   ![Register](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Creare una risorsa per il gateway di Azure

Dopo aver installato e registrato il gateway, è necessario creare una risorsa gateway in Azure.After you've installed and registered your gateway, you need to create a gateway resource in Azure. Accedere ad Azure con lo stesso account usato durante la registrazione del gateway.

1. Nel portale di Azure fare clic su **Crea una risorsa**, cercare Gateway dati **locale**e quindi fare clic su **Crea**.

   ![Creazione di una risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. In **Crea gateway di connessione** immettere queste impostazioni:

   * **Nome**: inserire un nome per la risorsa del gateway. 

   * **Sottoscrizione**: selezionare la sottoscrizione di Azure da associare alla risorsa del gateway. 
   
     La sottoscrizione predefinita si basa sull'account di Azure usato per accedere.

   * **Gruppo di risorse**: creare un gruppo di risorse o selezionarne uno esistente.

   * **Percorso**: selezionare l'area in cui è stato registrato il gateway.

   * **Nome installazione**: se l'installazione del gateway non è già selezionata, selezionare il gateway installato nel computer e registrato. 

     Al termine dell'operazione, scegliere **Crea**.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Connettere i server alla risorsa per il gateway

1. Nella panoramica del server Azure Analysis Services fare clic su **Gateway dati locale**.

   ![Connessione del server al gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. In **Selezionare un gateway dati locale da connettere** selezionare la risorsa per il gateway e quindi fare clic su **Connetti gateway selezionato**.

   ![Connessione del server alla risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se il gateway non viene visualizzato nell'elenco, probabilmente il server non si trova nell'area specificata durante la registrazione del gateway.

    Quando la connessione tra il server e la risorsa gateway ha esito positivo, lo stato mostrerà **Connesso**.


    ![Connettere il server al successo delle risorse gateway](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

È tutto. Se è necessario aprire le porte o risolvere eventuali problemi, vedere l'articolo su [Gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire Analysis Services](analysis-services-manage.md)   
* [Ottenere dati da Azure Analysis Services](analysis-services-connect.md)   
* [Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure](analysis-services-vnet-gateway.md)
