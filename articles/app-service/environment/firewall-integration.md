---
title: Bloccare il traffico in uscita
description: Informazioni sull'integrazione con Firewall di Azure per proteggere il traffico in uscita da un ambiente del servizio app.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 09/24/2020
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: ec506546b52a2d137d448f07f4b7a6827c01b4d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594114"
---
# <a name="locking-down-an-app-service-environment"></a>Blocco di un ambiente del servizio app

L'ambiente del servizio app ha diverse dipendenze esterne alle quali deve poter accedere per il corretto funzionamento. L'ambiente del servizio app risiede nella Rete virtuale di Azure (VNet) del cliente. I clienti devono consentire il traffico delle dipendenze dell'ambiente del servizio app e ciò è un problema per i clienti che voglio bloccare tutti i dati in uscita dalla rete virtuale.

Per gestire un ambiente del servizio app vengono usati diversi endpoint in ingresso. Il traffico di gestione in ingresso non può essere inviato attraverso un dispositivo firewall. Gli indirizzi di origine per questo traffico sono noti e sono pubblicati nel documento [Indirizzi di gestione dell'Ambiente del servizio app](./management-addresses.md). È disponibile anche un tag di servizio denominato AppServiceManagement che è possibile usare con i gruppi di sicurezza di rete per proteggere il traffico in ingresso.

Le dipendenze in uscita dell'ambiente del servizio app sono quasi interamente definite con nomi di dominio completo, senza indirizzi statici sottostanti. L'assenza di indirizzi statici determina l'impossibilità di usare i gruppi di sicurezza di rete per bloccare il traffico in uscita da un ambiente del servizio app. Gli indirizzi cambiano con una frequenza tale, che non è possibile configurare le regole in base alla risoluzione corrente e usarla per creare gruppi di sicurezza di rete. 

La soluzione per proteggere gli indirizzi in uscita consiste nell'usare un dispositivo firewall che può controllare il traffico in uscita in base ai nomi di dominio. Firewall di Azure può limitare il traffico HTTP e HTTPS in uscita in base all'FQDN della destinazione.  

## <a name="system-architecture"></a>Architettura di sistema

Per distribuire un ambiente del servizio app con traffico in uscita che passa attraverso un dispositivo firewall è necessario modificare le route nella subnet dell'ambiente del servizio app. Le route operano a livello di IP. Una definizione poco attenta delle route può determinare una situazione in cui il traffico di risposta TCP viene forzato ad avere origine da un altro indirizzo. Quando l'indirizzo di risposta è diverso da quello a cui è stato inviato il traffico, si verifica il problema noto come routing asimmetrico e TCP non funziona.

È necessaria la presenza di route definite in modo che il traffico in ingresso verso l'ambiente del servizio app possa rispondere con la stessa modalità di ingresso. Le route devono essere definite per le richieste di gestione in ingresso e per le richieste di applicazioni in ingresso.

Il traffico da e verso un ambiente del servizio app deve rispettare le convenzioni seguenti

* Il traffico verso SQL, Archiviazione e Hub eventi di Azure non è supportato con l'uso di un dispositivo firewall. Il traffico deve essere inviato direttamente a questi servizi. A tale scopo, configurare gli endpoint di servizio per questi tre servizi. 
* È necessario definire regole della tabella di route per rispedire il traffico di gestione in ingresso dall'origine da cui proviene.
* È necessario definire regole della tabella di route per rispedire il traffico di applicazioni in ingresso dall'origine da cui proviene. 
* Tutto il resto del traffico in uscita dall'ambiente del servizio app può essere inviato al dispositivo firewall con una regola della tabella di route.

![Flusso di connessioni tra l'ambiente del servizio app e Firewall di Azure][5]

## <a name="locking-down-inbound-management-traffic"></a>Blocco del traffico di gestione in ingresso

Se alla subnet dell'ambiente del servizio app non è stato ancora assegnato un gruppo di sicurezza di rete, crearne uno. All'interno del gruppo di sicurezza di rete impostare la prima regola per consentire il traffico proveniente dal tag di servizio denominato AppServiceManagement sulle porte 454, 455. La regola per consentire l'accesso dal tag AppServiceManagement è l'unico elemento richiesto dagli indirizzi IP pubblici per gestire l'ambiente del servizio app. Gli indirizzi che si trovano dietro questo tag di servizio vengono usati solo per amministrare il servizio app di Azure. Il traffico di gestione che passa attraverso queste connessioni è crittografato e protetto con certificati di autenticazione. Il traffico tipico in questo canale include elementi quali comandi avviati dal cliente e probe di integrità. 

Gli ambienti del servizio app creati tramite il portale con una nuova subnet sono realizzati con un gruppo di sicurezza di rete contenente la regola di accesso consentito per il tag AppServiceManagement.  

L'ambiente del servizio app deve anche consentire le richieste in ingresso dal tag del servizio di bilanciamento del carico sulla porta 16001. Le richieste provenienti dal servizio di bilanciamento del carico sulla porta 16001 sono controlli keep-alive tra il servizio di bilanciamento del carico e i front-end dell'ambiente del servizio app. Se la porta 16001 è bloccata, l'integrità dell'ambiente del servizio app sarà compromessa.

## <a name="configuring-azure-firewall-with-your-ase"></a>Configurazione di Firewall di Azure con l'ambiente del servizio app 

I passaggi per bloccare i dati in uscita dall'ambiente del servizio app esistente con Firewall di Azure sono:

1. Abilitare gli endpoint di servizio per SQL, archiviazione e hub eventi nella subnet dell'ambiente del servizio app. Per abilitare gli endpoint di servizio, passare al portale di rete > subnet e selezionare Microsoft.EventHub, Microsoft.SQL e Microsoft.Storage dall'elenco a discesa Endpoint servizio. Se si dispone di endpoint di servizio abilitati per SQL Azure, devono essere configurate con gli endpoint di servizio anche le dipendenze SQL Azure delle app. 

   ![Selezionare endpoint di servizio][2]
  
1. Creare una subnet denominata AzureFirewallSubnet nella rete virtuale in cui si trova l'ambiente del servizio app. Seguire le istruzioni disponibili nella [documentazione di Firewall di Azure](../../firewall/index.yml) per creare un firewall di Azure.

1. Dall'interfaccia utente di Firewall di Azure > Regole > Raccolta regole dell'applicazione, selezionare Aggiungi raccolta regole dell'applicazione. Specificare un nome e una priorità e impostare Consenti. Nella sezione Tag FQDN specificare un nome, impostare gli indirizzi di origine su * e selezionare come tag FQDN l'ambiente del servizio app e Windows Update. 
   
   ![Aggiungere una regola dell'applicazione][1]
   
1. Dall'interfaccia utente di Firewall di Azure > Regole > Raccolta regole di rete, selezionare Aggiungi raccolta regole di rete. Specificare un nome e una priorità e impostare Consenti. Nella sezione regole, in indirizzi IP, specificare un nome, selezionare un protocollo, impostare * su **indirizzi di origine** e destinazione e impostare le porte su 123. Questa regola consente al sistema di eseguire la sincronizzazione dell'orologio tramite NTP. Creare un'altra regola nello stesso modo per la porta 12000 per agevolare la valutazione di eventuali problemi di sistema. 

   ![Aggiungere una regola di rete NTP][3]
   
1. Dall'interfaccia utente di Firewall di Azure > Regole > Raccolta regole di rete, selezionare Aggiungi raccolta regole di rete. Specificare un nome e una priorità e impostare Consenti. Nella sezione Regole sotto Tag del servizio specificare un nome, selezionare **Qualsiasi** come protocollo, impostare * in Indirizzi di origine, selezionare AzureMonitor come tag del servizio e impostare le porte su 80, 443. Questa regola consente al sistema di inviare informazioni sull'integrità e sulle metriche a Monitoraggio di Azure.

   ![Aggiungere una regola di rete NTP con tag del servizio][6]
   
1. Creare una tabella di route con gli indirizzi di gestione dagli [indirizzi di gestione dell'ambiente del servizio app]( ./management-addresses.md) con hop successivo Internet. Le voci della tabella di route sono necessarie per evitare problemi di routing asimmetrico. Aggiungere le route per le dipendenze dell'indirizzo IP indicate di seguito nelle dipendenze dell'indirizzo IP con hop successivo Internet. Aggiungere una route di appliance virtuale alla tabella di route per 0.0.0.0/0 con hop successivo corrispondente all'indirizzo IP privato di Firewall di Azure. 

   ![Creazione di una tabella di route][4]
   
1. Assegnare la tabella di route creata alla subnet dell'ambiente del servizio app.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Distribuzione di un ambiente del servizio app protetto da un firewall

La procedura per distribuire un ambiente del servizio app protetto da un firewall è uguale alla configurazione di un ambiente del servizio app esistente con Firewall di Azure, con la differenza che è necessario creare la subnet dell'ambiente del servizio app e quindi seguire la procedura precedente. Per creare un ambiente del servizio app in una subnet già esistente, è necessario usare un modello di Resource Manager, come descritto nel documento [Creare un ambiente del servizio app usando un modello di Resource Manager](./create-from-template.md).

## <a name="application-traffic"></a>Traffico delle applicazioni 

I passaggi precedenti consentiranno all'ambiente del servizio app di operare senza problemi. È comunque necessario configurare altri aspetti per tenere conto delle esigenze delle applicazioni. Esistono due problemi per le applicazioni in un ambiente del servizio app configurato con Firewall di Azure.  

- Le dipendenze delle applicazioni devono essere aggiunte a Firewall di Azure o alla tabella di route. 
- Devono essere create route per il traffico delle applicazioni per evitare problemi di routing asimmetrico.

Se le applicazioni hanno dipendenze, devono essere aggiunte a Firewall di Azure. Creare regole per le applicazioni per consentire il traffico HTTP/HTTPS e regole di rete per tutto il resto. 

Se si conosce l'intervallo di indirizzi da cui proverrà il traffico di richieste delle applicazioni, è possibile aggiungerlo alla tabella di route assegnata alla subnet dell'ambiente del servizio app. Se l'intervallo di indirizzi è grande o non specificato, è possibile usare un'appliance di rete, ad esempio il gateway applicazione, per fornire un indirizzo da aggiungere alla tabella di route. Per informazioni dettagliate sulla configurazione di un gateway applicazione con l'ambiente del servizio app con bilanciamento del carico interno, vedere [Integrazione dell'ambiente del servizio app con bilanciamento del carico interno con un gateway applicazione](./integrate-with-application-gateway.md)

Questo uso del gateway applicazione è solo un esempio di come configurare il sistema. Se si è seguito questo percorso, è necessario aggiungere una route alla tabella di route della subnet dell'ambiente del servizio app in modo che il traffico di risposta inviato al gateway applicazione passi direttamente. 

## <a name="logging"></a>Registrazione 

Firewall di Azure può inviare log ai log di Archiviazione di Azure, Hub eventi o Monitoraggio di Azure. Per integrare un'app con una destinazione supportata, passare al portale di Firewall di Azure > Log di diagnostica e abilitare i log per la destinazione desiderata. Se si esegue l'integrazione con i log di Monitoraggio di Azure, è possibile visualizzare la registrazione per tutto il traffico inviato a Firewall di Azure. Per visualizzare il traffico che viene rifiutato, aprire il portale dell'area di lavoro Log Analytics &gt; Logs e immettere una query simile alla seguente 

```kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

L'integrazione di Firewall di Azure con i log di Monitoraggio di Azure è utile all'inizio dell'utilizzo di un'applicazione quando non si è consapevoli di tutte le dipendenze dell'applicazione. Per altre informazioni sui log di Monitoraggio di Azure, vedere [Analizzare i dati di log in Monitoraggio di Azure](../../azure-monitor/logs/log-query-overview.md).
 
## <a name="dependencies"></a>Dependencies

Le informazioni seguenti sono necessarie solo se si vuole configurare un'appliance firewall diversa da Firewall di Azure. 

- Con gli endpoint di servizio devono essere configurati servizi che supportano endpoint di servizio.
- Le dipendenze degli indirizzi IP sono per il traffico non HTTP/S (traffico sia TCP che UDP)
- Gli endpoint HTTP/HTTPS con nome di dominio completo possono essere posizionati nel dispositivo firewall.
- Gli endpoint HTTP/HTTPS con caratteri jolly sono dipendenze che possono variare con l'ambiente del servizio app in base a svariati qualificatori. 
- Le dipendenze di Linux sono un problema solo se si distribuiscono app Linux nell'ambiente del servizio app. Se non è prevista la distribuzione di app Linux nell'ambiente del servizio app, non è necessario aggiungere questi indirizzi al firewall. 

#### <a name="service-endpoint-capable-dependencies"></a>Dipendenze che supportano endpoint di servizio 

| Endpoint |
|----------|
| SQL di Azure |
| Archiviazione di Azure |
| Hub eventi di Azure |

#### <a name="ip-address-dependencies"></a>Dipendenze di indirizzi IP

| Endpoint | Dettagli |
|----------| ----- |
| \*:123 | Controllo dell'orologio NTP. Il traffico viene verificato in più endpoint sulla porta 123. |
| \*:12000 | Questa porta viene usata per alcune attività di monitoraggio del sistema. Se è bloccata, la valutazione di alcuni problemi sarà più difficile, ma l'ambiente del servizio app continuerà a funzionare. |
| 40.77.24.27:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 40.77.24.27:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.90.249.229:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.90.249.229:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 104.45.230.69:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 104.45.230.69:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.82.184.151:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.82.184.151:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |

Con Firewall di Azure, tutto ciò che segue viene configurato automaticamente con i tag FQDN. 

#### <a name="fqdn-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con nome di dominio completo 

| Endpoint |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|ocsp.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |
|global-dsms.dsms.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Dipendenze HTTP/HTTPS con caratteri jolly 

| Endpoint |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Dipendenze Linux 

| Endpoint |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
|oryx-cdn.microsoft.io:443 |
| \*.cdn.mscr.io:443 |
| \*. data.mcr.microsoft.com:443 |
|mcr.microsoft.com:443 |
|\*. data.mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>Dipendenze US Gov

Per gli ambienti del servizio app nelle aree US Gov, seguire le istruzioni riportate nella sezione [Configurazione di Firewall di Azure con l'ambiente del servizio app](#configuring-azure-firewall-with-your-ase) di questo documento per configurare un'istanza di Firewall di Azure con l'ambiente del servizio app.

Se si vuole usare un dispositivo diverso da Firewall di Azure in US Gov 

* Con gli endpoint di servizio devono essere configurati servizi che supportano endpoint di servizio.
* Gli endpoint HTTP/HTTPS con nome di dominio completo possono essere posizionati nel dispositivo firewall.
* Gli endpoint HTTP/HTTPS con caratteri jolly sono dipendenze che possono variare con l'ambiente del servizio app in base a svariati qualificatori.

Linux non è disponibile nelle aree US Gov e non è pertanto elencato come configurazione facoltativa.

#### <a name="service-endpoint-capable-dependencies"></a>Dipendenze che supportano endpoint di servizio ####

| Endpoint |
|----------|
| SQL di Azure |
| Archiviazione di Azure |
| Hub eventi di Azure |

#### <a name="ip-address-dependencies"></a>Dipendenze di indirizzi IP

| Endpoint | Dettagli |
|----------| ----- |
| \*:123 | Controllo dell'orologio NTP. Il traffico viene verificato in più endpoint sulla porta 123. |
| \*:12000 | Questa porta viene usata per alcune attività di monitoraggio del sistema. Se è bloccata, la valutazione di alcuni problemi sarà più difficile, ma l'ambiente del servizio app continuerà a funzionare. |
| 40.77.24.27:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 40.77.24.27:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.90.249.229:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.90.249.229:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 104.45.230.69:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 104.45.230.69:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.82.184.151:80 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |
| 13.82.184.151:443 | Necessario per il monitoraggio e l'invio di avvisi relativi ai problemi dell'ambiente del servizio app |

#### <a name="dependencies"></a>Dependencies ####

| Endpoint |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com:80
|ocsp.digicert.com:80 |
|ocsp.verisign.com:80 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|gcs.monitoring.core.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.msocsp.com:80 |
|oneocsp.microsoft.com:80 |
|oneocsp.microsoft.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |
|global-dsms.dsms.core.usgovcloudapi.net:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
