---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 14ac8ff54f5627509fe10a90bfdd0a01c07762d9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028500"
---
Prima di creare elementi, esaminiamo il concetto di ambito. Azure offre quattro livelli di gestione: gruppi di gestione, sottoscrizione, gruppo di risorse e risorsa. I [gruppi di gestione](../articles/governance/management-groups/overview.md) sono disponibili in versione di anteprima. Nella figura seguente viene illustrato un esempio di questi livelli.

![Scope](./media/resource-manager-governance-scope/scope-levels.png)

Le impostazioni di gestione possono essere applicate a qualsiasi di questi livelli di ambito. Il livello selezionato determina l'estensione con cui viene applicata l'impostazione. I livelli inferiori ereditano le impostazioni dai livelli superiori. Quando si applica un'impostazione alla sottoscrizione, tale impostazione viene applicata a tutti i gruppi di risorse e le risorse nella sottoscrizione. Quando si applica un'impostazione al gruppo di risorse, l'impostazione viene applicata al gruppo di risorse e a tutte le risorse che contiene. Tuttavia, un altro gruppo di risorse non disporrà di tale impostazione.

In genere, è consigliabile applicare le impostazioni critiche ai livelli superiori e i requisiti specifici dei progetti ai livelli inferiori. Potrebbe ad esempio essere necessario assicurarsi che tutte le risorse dell'organizzazione vengano distribuite in determinate aree geografiche. Per soddisfare questo requisito, applicare alla sottoscrizione un criterio che specifica le posizioni consentite. Quando altri utenti nell'organizzazione aggiungono nuovi gruppi di risorse e nuove risorse, vengono applicate automaticamente le posizioni consentite.