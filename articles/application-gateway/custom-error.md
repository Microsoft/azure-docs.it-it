---
title: Creare pagine di errore personalizzate del gateway applicazione di Azure
description: Questo articolo illustra come creare pagine di errore personalizzate del gateway applicazione. Con una pagina di errore personalizzata è possibile usare il layout e il marchio aziendali.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff3e9db4dcfe0bedc348323dbbddd1e66124fc5d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360158"
---
# <a name="create-application-gateway-custom-error-pages"></a>Creare pagine di errore personalizzate del gateway applicazione

Il gateway applicazione consente di creare pagine di errore personalizzate da visualizzare al posto delle pagine di errore predefinite. Con una pagina di errore personalizzata è possibile usare il layout e il marchio aziendali.

È possibile, ad esempio, definire la propria pagina di manutenzione se l'applicazione Web non è raggiungibile. In alternativa, è possibile creare una pagina di accesso non autorizzato se viene inviata una richiesta dannosa a un'applicazione Web.

Le pagine di errore personalizzate sono supportate per i due scenari seguenti:

- **Pagina Manutenzione**: questa pagina di errore personalizzata viene inviata al posto di una pagina 502 - Gateway non valido. Viene visualizzata quando il gateway applicazione non dispone di back-end a cui indirizzare il traffico. Quando, ad esempio, è pianificata la manutenzione oppure quando un problema imprevisto influisce sull'accesso al pool back-end.
- **Pagina Accesso non autorizzato**: questa pagina di errore personalizzata viene inviata al posto di una pagina 403 - Accesso non autorizzato. Viene visualizzata quando il WAF del gateway applicazione rileva traffico dannoso e lo blocca.

Se un errore proviene dai server back-end, viene reinviato senza modifiche al chiamante. Non viene visualizzata una pagina di errore personalizzata. Il gateway applicazione può visualizzare una pagina di errore personalizzata quando una richiesta non riesce a raggiungere il back-end.

Le pagine di errore personalizzate possono essere definite a livello globale e a livello di listener:

- **Livello globale**: la pagina di errore è applicabile al traffico di tutte le applicazioni Web distribuite nel gateway applicazione specifico.
- **Livello di listener**: la pagina di errore è applicabile al traffico ricevuto nel listener specifico.
- **Entrambi i livelli**: la pagina di errore personalizzata definita a livello di listener sostituisce quella impostata a livello globale.

Per creare una pagina di errore personalizzata è necessario disporre di:

- un codice di stato risposta HTTP;
- il percorso corrispondente per la pagina di errore; 
- un BLOB del servizio di archiviazione di Azure accessibile pubblicamente per il percorso;
- un tipo di estensione *.htm o *.HTML. 

Le dimensioni della pagina di errore devono essere minori di 1 MB. Se nella pagina di errore sono presenti immagini collegate, queste devono essere un URL assoluto accessibile pubblicamente o un'immagine con codifica Base64 inline nella pagina di errore personalizzata. I collegamenti relativi con immagini nello stesso percorso BLOB non sono attualmente supportati. 

Dopo che l'utente ha specificato una pagina di errore, il gateway applicazione la scarica dal percorso BLOB del servizio di archiviazione e la salva nella cache locale del gateway applicazione. La pagina di errore viene quindi distribuita direttamente dal gateway applicazione. Per modificare una pagina di errore personalizzata esistente, è necessario puntare a un percorso BLOB diverso nella configurazione del gateway applicazione. Il gateway applicazione non verifica periodicamente il percorso BLOB per recuperare le nuove versioni.

## <a name="portal-configuration"></a>Configurazione del portale

1. Passare a Gateway applicazione nel portale e scegliere un gateway applicazione.

    ![Screenshot che mostra la pagina Panoramica per un gateway applicazione.](media/custom-error/ag-overview.png)
2. Fare clic su **Listener** e passare a un listener specifico in cui si desidera specificare una pagina di errore.

    ![Listener dei gateway applicazione](media/custom-error/ag-listener.png)
3. Configurare una pagina di errore personalizzata per un errore WAF 403 o una pagina di manutenzione 502 a livello di listener.

    > [!NOTE]
    > La creazione di pagine di errore personalizzate a livello globale dal portale di Azure non è attualmente supportata.

4. Specificare un URL BLOB accessibile pubblicamente per un determinato codice di stato di errore e fare clic su **Salva**. Il gateway applicazione è ora configurato con la pagina di errore personalizzata.

   ![Codici di errore del gateway applicazione](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Configurazione di Azure PowerShell

È anche possibile usare Azure PowerShell per configurare una pagina di errore personalizzata. Una pagina di errore personalizzata globale di esempio:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

O una pagina di errore a livello di listener:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Per altre informazioni, vedere [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) e [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla diagnostica del gateway applicazione, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).
