---
title: Usare la funzionalità diagnostica connettività nel runtime di integrazione SSIS
description: Risolvere i problemi di connessione nel runtime di integrazione SSIS utilizzando la funzionalità di connettività di diagnostica.
ms.service: data-factory
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
ms.date: 06/07/2020
ms.openlocfilehash: 1fb9a0c77a7cdc286c7c206d6eb33e43917ee719
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361860"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Usare la funzionalità diagnostica connettività nel runtime di integrazione SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si potrebbero riscontrare problemi di connettività durante l'esecuzione di pacchetti di SQL Server Integration Services (SSIS) nel runtime di integrazione SSIS. Questi problemi si verificano soprattutto se il runtime di integrazione SSIS viene aggiunto alla rete virtuale di Azure.

Risolvere i problemi di connettività usando la funzionalità *diagnostica connettività* per testare le connessioni. La funzionalità si trova nella pagina Monitoraggio di runtime di integrazione SSIS del portale di Azure Data Factory.

 ![Pagina monitoraggio-diagnostica connettività](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Monitoraggio pagina-Test connessione](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

Usare le sezioni seguenti per ottenere informazioni sugli errori più comuni che si verificano durante il test delle connessioni. Ogni sezione descrive:

- Codice di errore
- Messaggio di errore
- Possibili cause dell'errore
- Soluzioni consigliate

## <a name="error-code-invalidinput"></a>Codice di errore: InvalidInput

- **Messaggio di errore**: "verificare che l'input sia corretto".
- **Possibili cause**: l'input non è corretto.
- **Consiglio**: controllare l'input.

## <a name="error-code-firewallornetworkissue"></a>Codice di errore: FirewallOrNetworkIssue

- **Messaggio di errore**: "verificare che la porta sia aperta nel firewall/server/NSG e che la rete sia stabile".
- **Possibili cause:**
  - Il server non apre la porta.
  - Al gruppo di sicurezza di rete viene negato il traffico in uscita sulla porta.
  - Il firewall di appliance virtuale di Azure/sito locale non apre la porta.
- **Raccomandazioni**
  - Aprire la porta sul server.
  - Aggiornare il gruppo di sicurezza di rete per consentire il traffico in uscita sulla porta.
  - Aprire la porta nel firewall dell'appliance virtuale di Azure o nel firewall locale.

## <a name="error-code-misconfigureddnssettings"></a>Codice di errore: MisconfiguredDnsSettings

- **Messaggio di errore**: "Se si usa il proprio server DNS nel VNet aggiunto dal Azure-SSIS IR, verificare che sia in grado di risolvere il nome host."
- **Possibili cause:**
  -  Si è verificato un problema con il DNS personalizzato.
  -  Non si usa un nome di dominio completo (FQDN) per il nome host privato.
- **Raccomandazioni**
  -  Correggere il problema DNS personalizzato per assicurarsi che sia in grado di risolvere il nome host.
  -  Usare il nome di dominio completo. Azure-SSIS IR non aggiungerà automaticamente il proprio suffisso DNS. Utilizzare, ad esempio, **<your_private_server>. contoso.com** anziché **<your_private_server>**.

## <a name="error-code-servernotallowremoteconnection"></a>Codice di errore: ServerNotAllowRemoteConnection

- **Messaggio di errore**: "verificare che il server consenta le connessioni TCP remote tramite questa porta".
- **Possibili cause:**
  -  Il firewall del server non consente le connessioni TCP remote.
  -  Il server non è online.
- **Raccomandazioni**
  -  Consente le connessioni TCP remote nel firewall del server.
  -  Avviare il server.
   
## <a name="error-code-misconfigurednsgsettings"></a>Codice di errore: MisconfiguredNsgSettings

- **Messaggio di errore**: "verificare che il NSG della VNet consenta il traffico in uscita attraverso questa porta. Se si usa Azure ExpressRoute e o una UDR, verificare che la porta sia aperta nel firewall/server. "
- **Possibili cause:**
  -  Al gruppo di sicurezza di rete viene negato il traffico in uscita sulla porta.
  -  Il firewall di appliance virtuale di Azure/sito locale non apre la porta.
- **Consiglio:**
  -  Aggiornare il gruppo di sicurezza di rete per consentire il traffico in uscita sulla porta.
  -  Aprire la porta nel firewall dell'appliance virtuale di Azure o nel firewall locale.

## <a name="error-code-genericissues"></a>Codice di errore: GenericIssues

- **Messaggio di errore**: "test della connessione non riuscito a causa di problemi generici."
- **Causa potenziale**: la connessione di test ha rilevato un problema temporaneo generale.
- **Raccomandazione**: ripetere la connessione di test in un secondo momento. Se il nuovo tentativo non è utile, contattare il team di supporto di Azure Data Factory.

## <a name="error-code-pspingexecutiontimeout"></a>Codice di errore: PSPingExecutionTimeout

- **Messaggio di errore**: "verificare il timeout della connessione. riprovare più tardi".
- **Possibili cause**: timeout della connettività di test.
- **Raccomandazione**: ripetere la connessione di test in un secondo momento. Se il nuovo tentativo non è utile, contattare il team di supporto di Azure Data Factory.

## <a name="error-code-networkinstable"></a>Codice di errore: NetworkInstable

- **Messaggio di errore**: "la connessione di test ha avuto esito positivo a causa di un'instabilità della rete."
- **Causa potenziale**: problema di rete temporaneo.
- **Consiglio**: controllare se la rete del server o del firewall è stabile.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un progetto SSIS in Azure con SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [Eseguire pacchetti SSIS in Azure con SSMS](/sql/integration-services/ssis-quickstart-run-ssms)
- [Pianificare pacchetti SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)