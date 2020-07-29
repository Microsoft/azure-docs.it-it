---
title: Usare immagini client Windows in Azure
description: Come usare i vantaggi della sottoscrizione di Visual Studio per distribuire Windows 7, Windows 8 o Windows 10 in Azure per scenari di sviluppo/test
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 26bfd9a65cd3e6d36b8190e87d382543cc7f0f60
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292063"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Usare client Windows in Azure per scenari di sviluppo/test
A condizione di disporre di una sottoscrizione appropriata di Visual Studio (in precedenza MSDN), è possibile usare Windows 7, Windows 8 o Windows 10 Enterprise (x64) in Azure per scenari di sviluppo/test. In questo articolo sono descritti i requisiti di idoneità per l'esecuzione di Windows 7, Windows 8.1, Windows 10 Enterprise in Azure e l'uso delle seguenti immagini della raccolta di Azure.

![Dettagli delle immagini sul portale di Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Per le immagini di Windows 10 Pro e Windows 10 Pro N nella raccolta di Azure, fare riferimento a [come distribuire Windows 10 in Azure con multi-tenant hosting Rights](windows-desktop-multitenant-hosting-deployment.md) 
> ![ Pro Details dell'immagine dalla portale di Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Idoneità della sottoscrizione
I sottoscrittori di Visual Studio attivi (ovvero le persone che hanno acquistato una licenza di sottoscrizione per Visual Studio) possono usare client Windows per le finalità di sviluppo e test. Il client Windows può essere usato nel proprio hardware e nelle proprie macchine virtuali di Azure in esecuzione in qualsiasi tipo di sottoscrizione di Azure. Potrebbe non essere possibile distribuire o usare il client Windows in Azure per normali finalità di produzione oppure da parte degli utenti che non possiedono una sottoscrizione a Visual Studio attiva.

Per comodità, sono state rese disponibili alcune immagini di Windows 10 nella raccolta di Azure in [eligible dev/test offers](#eligible-offers). I sottoscrittori di Visual Studio per qualsiasi tipo di offerta possono anche [preparare e creare](prepare-for-upload-vhd-image.md) un'immagine a 64 bit di Windows 7, Windows 8 o Windows 10 e quindi [caricarla in Azure](upload-generalized-managed.md). L'utilizzo rimane limitato alle attività di sviluppo e test da parte dei sottoscrittori di Visual Studio attivi.

## <a name="eligible-offers"></a>Offerte idonee
La tabella seguente descrive gli ID idonei per la distribuzione di Windows 10 tramite la raccolta di Azure. Le immagini di Windows 10 sono visibili solo per le offerte seguenti. I sottoscrittori di Visual Studio che intendono eseguire il client Windows in un altro tipo di offerta devono [preparare e creare](prepare-for-upload-vhd-image.md) un'immagine a 64 bit di Windows 7, Windows 8 o Windows 10 e quindi [caricarla in Azure](upload-generalized-managed.md).

| Nome offerta | Numero offerta | Immagini client disponibili |
|:--- |:---:|:---:|
| [Sviluppo/test con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Sottoscrittori di Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Sottoscrittori di Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Sottoscrittori di Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium con MSDN (vantaggio)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Sottoscrittori di Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Sottoscrittori di Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Controllare la sottoscrizione di Azure
Se non si conosce l'ID offerta, è possibile risalirvi mediante il portale di Azure in uno dei due modi seguenti:  

- Nella finestra *Sottoscrizioni*:

  ![Dettagli dell'ID offerta sul portale di Azure](./media/client-images/offer-id-azure-portal.png) 

- Fare clic su **Fatturazione** e quindi sull'ID sottoscrizione. L'ID offerta viene visualizzato nella finestra *Fatturazione*.

È inoltre possibile visualizzare l'ID offerta nella [scheda delle sottoscrizioni](https://account.windowsazure.com/Subscriptions) del portale degli account di Azure:

![Dettagli dell'ID offerta sul portale degli account di Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passaggi successivi
È ora possibile distribuire le VM usando [PowerShell](quick-create-powershell.md), i [modelli di Resource Manager](ps-template.md) o [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
