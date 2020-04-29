---
title: Offerta di immagini dei contenitori di Azure | Azure Marketplace
description: Panoramica del processo per la pubblicazione di un'offerta di contenitore in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 81908de5c2fb3960684ed6cf37952e8815d8a5d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148306"
---
# <a name="containers"></a>Contenitori

> [!IMPORTANT]
> A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte del contenitore di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Seguire le istruzioni riportate in [creare un'offerta di contenitore di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) per gestire le offerte migrate.

<table> <tr> <td>Questa sezione descrive come pubblicare un'immagine di contenitore in <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
Il tipo di offerta "contenitore" supporta immagini del contenitore Docker di cui è stato effettuato il provisioning come istanze del <a href="https://docs.microsoft.com/azure/aks/index">servizio Azure Kubernetes</a> o come <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Istanze di Azure Container</a> e che sono ospitate in un repository di <a href="https://docs.microsoft.com/azure/container-registry">Registro Azure Container</a>. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Componenti dell'offerta

Questa sezione, che descrive gli elementi della pubblicazione di un contenitore, è da intendersi come guida per chi effettua la pubblicazione in Azure Marketplace. La pubblicazione è suddivisa nelle parti principali seguenti:

- [Prerequisiti](./cpp-prerequisites.md): elenca i requisiti tecnici e commerciali necessari per la creazione o la pubblicazione dell'offerta di contenitore.
- [Creare l'offerta](./cpp-create-offer.md): elenca i passaggi necessari per creare la voce dell'offerta di un nuovo contenitore usando il portale Cloud Partner.
- [Preparare le risorse tecniche](./cpp-create-technical-assets.md): descrive come creare le risorse tecniche per una soluzione contenitore da pubblicare come offerta in Azure Marketplace.
- [Pubblicare l'offerta](./cpp-publish-offer.md): descrive come inviare l'offerta per la pubblicazione in Azure Marketplace.

## <a name="container-publishing-process"></a>Processo di pubblicazione del contenitore

Il diagramma seguente illustra ad alto livello la procedura di pubblicazione di un'offerta di macchina virtuale.
![Procedura di pubblicazione di un'offerta](./media/containers-offer-process.png)

I passaggi generali per la pubblicazione di un'offerta di contenitore sono:

1. Creazione dell'offerta: specificare le informazioni dettagliate sull'offerta. Queste informazioni includono la descrizione dell'offerta, il materiale di marketing, le informazioni di supporto e le specifiche degli asset.
2. Creazione degli asset tecnici e aziendali: creare gli asset aziendali (documenti legali e materiali di marketing) e gli asset tecnici per la soluzione associata (immagini del contenitore ospitate in un'istanza di Registro Azure Container).
3. Creazione dello SKU: creare gli SKU associati all'offerta. È necessario uno SKU per ogni immagine che si intende pubblicare.
4. Certificazione e pubblicazione dell'offerta: dopo aver completato l'offerta e le risorse tecniche, è possibile procedere all'invio dell'offerta. L'invio dell'offerta avvia il processo di pubblicazione. Durante questo processo la soluzione viene sottoposta a test, viene convalidata e certificata e quindi viene "aggiunta" in Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

Prima prendere in considerazione questi passaggi, è necessario soddisfare i [requisiti tecnici e commerciali](./cpp-prerequisites.md) per la pubblicazione di un contenitore in Microsoft Azure Marketplace.
