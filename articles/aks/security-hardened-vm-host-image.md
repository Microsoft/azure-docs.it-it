---
title: Protezione avanzata degli host di macchine virtuali AKS
description: Informazioni sulla protezione avanzata nel sistema operativo host della macchina virtuale AKS
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b7552fc083c5ed340dc54c2a31160b0c8b4bd076
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420908"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Protezione avanzata per il sistema operativo host del nodo AKS Agent

Azure Kubernetes Service (AKS) è un servizio sicuro conforme agli standard SOC, ISO, PCI DSS e HIPAA. Questo articolo illustra la protezione avanzata applicata agli host di macchine virtuali AKS. Per altre informazioni sulla sicurezza di AKS, vedere Concetti relativi alla [sicurezza per le applicazioni e i cluster in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

> [!Note]
> Questo documento è limitato agli agenti Linux in AKS.

I cluster AKS vengono distribuiti nelle macchine virtuali host che eseguono un sistema operativo ottimizzato per la sicurezza usato per i contenitori in esecuzione su AKS. Questo sistema operativo host si basa su un'immagine **Ubuntu 16.04. LTS** con protezione avanzata e ottimizzazioni aggiuntive applicate (vedere dettagli protezione avanzata).

L'obiettivo del sistema operativo host con protezione avanzata consiste nel ridurre la superficie di attacco e l'ottimizzazione per la distribuzione dei contenitori in modo sicuro.

> [!Important]
> Il sistema operativo con protezione avanzata non è un benchmark di CIS. Sebbene ci siano sovrapposizioni con i benchmark CIS, l'obiettivo non è conforme a CIS. L'obiettivo per la protezione avanzata del sistema operativo host è quello di convergere su un livello di protezione coerente con gli standard di sicurezza degli host interni di Microsoft.

## <a name="security-hardening-features"></a>Funzionalità di protezione avanzata

* AKS fornisce un sistema operativo host con ottimizzazione per la sicurezza per impostazione predefinita. Non è possibile selezionare un sistema operativo alternativo.

* Azure applica patch giornaliere (incluse le patch di sicurezza) agli host di macchine virtuali AKS. Per alcune di queste patch è necessario riavviare il computer, mentre altre non lo sono. L'utente è responsabile della pianificazione dei riavvii dell'host della macchina virtuale AKS in base alle esigenze. Per istruzioni su come automatizzare l'applicazione di patch AKS, vedere applicazione di [patch ai nodi AKS](https://docs.microsoft.com/azure/aks/node-updates-kured).

## <a name="what-is-configured"></a>Cosa è configurato

| CIS  | Descrizione controllo|
|---|---|
| 1.1.1.1 |Verificare che il montaggio dei filesystem CRAMFS sia disabilitato|
| 1.1.1.2 |Verificare che il montaggio dei filesystem freevxfs sia disabilitato|
| 1.1.1.3 |Verificare che il montaggio dei filesystem JFFS2 sia disabilitato|
| 1.1.1.4 |Verificare che il montaggio dei filesystem HFS sia disabilitato|
| 1.1.1.5 |Verificare che il montaggio dei filesystem HFS Plus sia disabilitato|
|1.4.3 |Assicurarsi che l'autenticazione sia necessaria per la modalità utente singolo |
|1.7.1.2 |Verificare che il banner di avviso di accesso locale sia configurato correttamente |
|1.7.1.3 |Verificare che il banner di avviso di accesso remoto sia configurato correttamente |
|1.7.1.5 |Verificare che siano configurate le autorizzazioni per/etc/issue |
|1.7.1.6 |Verificare che siano configurate le autorizzazioni per/etc/issue.NET |
|2.1.5 |Verificare che--streaming-Connection-Idle-timeout non sia impostato su 0 |
|3.1.2 |Verifica dell'invio del reindirizzamento pacchetti disabilitato |
|3.2.1 |Verificare che i pacchetti indirizzati di origine non siano accettati |
|3.2.2 |Assicurarsi che i reindirizzamenti ICMP non siano accettati |
|3.2.3 |Assicurarsi che i reindirizzamenti ICMP sicuri non siano accettati |
|3.2.4 |Verificare la registrazione di pacchetti sospetti |
|3.3.1 |Verificare che gli annunci router IPv6 non siano accettati |
|3.5.1 |Verificare che DCCP sia disabilitato |
|3.5.2 |Verificare che SCTP sia disabilitato |
|3.5.3 |Verificare che RDS sia disabilitato |
|3.5.4 |Verificare che TIPC sia disabilitato |
|4.2.1.2 |Verificare che la registrazione sia configurata |
|5.1.2 |Verificare che siano configurate le autorizzazioni per/etc/crontab |
|5.2.4 |Assicurarsi che l'invio SSH X11 sia disabilitato |
|5.2.5 |Verificare che MaxAuthTries SSH sia impostato su un massimo di 4 |
|5.2.8 |Assicurarsi che l'accesso alla radice SSH sia disabilitato |
|5.2.10 |Verificare che PermitUserEnvironment SSH sia disabilitato |
|5.2.11 |Assicurarsi che vengano usati solo gli algoritmi MAX approvati |
|5.2.12 |Verificare che l'intervallo di timeout di inattività SSH sia configurato |
|5.2.13 |Verificare che LoginGraceTime SSH sia impostato su un massimo di un minuto |
|5.2.15 |Verificare che il banner di avviso SSH sia configurato |
|5.3.1 |Verificare che siano configurati i requisiti di creazione della password |
|5.4.1.1 |Verificare che la scadenza della password sia pari o inferiore a 90 giorni |
|5.4.1.4 |Verificare che il blocco di password inattivo sia pari o inferiore a 30 giorni |
|5.4.4 |Verificare che l'utente predefinito umask sia 027 o più restrittivo |
|5.6 |Verificare che l'accesso al comando su sia limitato|

## <a name="additional-notes"></a>Note aggiuntive
 
* Per ridurre ulteriormente la superficie di attacco, alcuni driver del modulo kernel non necessari sono stati disabilitati nel sistema operativo.

* Il sistema operativo con protezione avanzata viene compilato e gestito in modo specifico per AKS e non è supportato al di fuori della piattaforma AKS.

## <a name="next-steps"></a>Passaggi successivi  

Per ulteriori informazioni sulla sicurezza di AKS, vedere gli articoli seguenti: 

[Servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Considerazioni sulla sicurezza di AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Procedure consigliate di AKS](https://docs.microsoft.com/azure/aks/best-practices)
