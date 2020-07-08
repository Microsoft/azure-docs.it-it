---
title: Come condividere Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Informazioni su come usare Azure Dev Spaces per condividere uno spazio di sviluppo nel servizio Azure Kubernetes con altri utenti del team
keywords: 'Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79474408"
---
# <a name="share-azure-dev-spaces"></a>Condividere Azure Dev Spaces

Con Azure Dev Spaces è possibile condividere lo spazio di sviluppo con altri utenti del team. Ogni sviluppatore può lavorare nel proprio spazio senza il timore di interferire con gli altri. Inoltre, lavorare insieme in uno spazio consente di testare il codice end-to-end senza dover creare oggetti fittizi o simulare le dipendenze. Per altre informazioni, vedere la guida allo [sviluppo in team](../team-development-nodejs.md).

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Impostare uno spazio di sviluppo per più sviluppatori

1. Creare uno spazio di sviluppo in Azure. Scegliere [.NET Core e Visual Studio Code](../get-started-netcore.md), [.NET Core e Visual Studio](../get-started-netcore-visualstudio.md) o [Node.js e Visual Studio Code](../get-started-nodejs.md). È necessario poter accedere come proprietario o collaboratore alla sottoscrizione di Azure selezionata.
1. Verificare che ogni membro del team disponga delle [autorizzazioni appropriate per accedere al controller di Azure Dev Spaces](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Ad esempio, è possibile configurare il **gruppo di risorse** di Azure Dev Space per [concedere l'accesso come collaboratore](/azure/active-directory/role-based-access-control-configure) per ogni membro del team. È possibile controllare il gruppo di risorse di uno spazio di sviluppo eseguendo questo comando: `azds show-context`
1. Chiedere ai membri del team di **selezionare lo spazio di lavoro** per consentire lo sviluppo all'interno dello stesso.
   * **Riga di comando o Visual Studio Code**: per visualizzare gli spazi Azure Dev Spaces a cui si ha accesso: `azds space list`. Per selezionare uno spazio di sviluppo: `azds space select`.
   * **IDE di Visual Studio**: aprire un progetto in Visual Studio, selezionare **Azure Dev Spaces** dall'elenco a discesa delle impostazioni di avvio. Selezionare un cluster esistente nella finestra di dialogo visualizzata.

     ![Elenco a discesa delle impostazioni di avvio di Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Informazioni sui team di sviluppo](../team-development-nodejs.md).
