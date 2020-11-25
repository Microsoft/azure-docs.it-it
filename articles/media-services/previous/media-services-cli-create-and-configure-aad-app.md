---
title: Usare l'interfaccia della riga di comando di Azure per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra come usare l'interfaccia della riga di comando di Azure per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 12d6beac446f8a15a41daf0dfe8ca1783c95c775
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021113"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Usare l'interfaccia della riga di comando di Azure per creare un'app Azure AD e configurarla per l'accesso all'API Servizi multimediali

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md)

Questo argomento illustra come usare l'interfaccia della riga di comando di Azure per creare un'applicazione e un'entità servizio di Azure Active Directory (Azure AD) per accedere alle risorse di Servizi multimediali di Azure. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Per informazioni dettagliate, vedere [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Account di Servizi multimediali. Per altre informazioni, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Usare Azure Cloud Shell

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Avviare Cloud Shell dal riquadro di spostamento superiore del portale.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Per altre informazioni, vedere [Panoramica di Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Creare un'app Azure AD e configurare l'accesso all'account multimediale con l'interfaccia della riga di comando di Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Ad esempio:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

In questo esempio l'**ambito** è il percorso completo delle risorse per l'account dei servizi multimediali. Tuttavia, l'**ambito** può essere definito a qualsiasi livello.

Ad esempio, può essere definito a uno dei livelli seguenti:
 
* Il livello **sottoscrizione**.
* Il livello **gruppo di risorse**.
* Il livello **risorsa** (ad esempio, un account multimediale).

Per altre informazioni, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

Vedere anche [aggiungere o rimuovere assegnazioni di ruolo di Azure con l'interfaccia](../../role-based-access-control/role-assignments-cli.md)della riga di comando di Azure. 

## <a name="next-steps"></a>Passaggi successivi

Introduzione al [caricamento di file nell'account](media-services-portal-upload-files.md).
