---
title: Indirizzi IP di gestione di Azure HDInsight
description: Informazioni sugli indirizzi IP da cui è necessario consentire il traffico in ingresso, in modo da configurare correttamente i gruppi di sicurezza di rete e le route definite dall'utente per la rete virtuale con Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 89d749a04c22967198be217350ff38751336fe67
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944104"
---
# <a name="hdinsight-management-ip-addresses"></a>Indirizzi IP di gestione di HDInsight

Questo articolo elenca gli indirizzi IP usati dai servizi di gestione e integrità di Azure HDInsight. Se si usano gruppi di sicurezza di rete (gruppi) o route definite dall'utente (UDR), potrebbe essere necessario aggiungere alcuni di questi indirizzi IP all'elenco Consenti per il traffico di rete in ingresso.

## <a name="introduction"></a>Introduzione
 
> [!Important]
> Nella maggior parte dei casi, è ora possibile usare i [tag di servizio](hdinsight-service-tags.md) per i gruppi di sicurezza di rete, anziché aggiungere manualmente gli indirizzi IP. Gli indirizzi IP non verranno pubblicati per le nuove aree di Azure e avranno solo tag di servizio pubblicati. Gli indirizzi IP statici per gli indirizzi IP di gestione saranno deprecati.

Se si usano gruppi di sicurezza di rete (gruppi) o route definite dall'utente (UDR) per controllare il traffico in ingresso verso il cluster HDInsight, è necessario assicurarsi che il cluster sia in grado di comunicare con i servizi di gestione e integrità di Azure critici.  Alcuni degli indirizzi IP per questi servizi sono specifici dell'area e alcuni di essi si applicano a tutte le aree di Azure. Potrebbe anche essere necessario consentire il traffico dal servizio DNS di Azure se non si usa un DNS personalizzato.

Se sono necessari indirizzi IP per un'area non elencata qui, è possibile usare l' [API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) per trovare gli indirizzi IP per l'area geografica. Se non è possibile usare l'API, scaricare il [file JSON del tag del servizio](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) e cercare l'area desiderata.

HDInsight esegue la convalida per queste regole con la creazione e la scalabilità del cluster per evitare ulteriori errori. Se la convalida non viene superata, la creazione e la scalabilità non riescono

Nelle sezioni seguenti vengono illustrati gli indirizzi IP specifici che devono essere consentiti.

## <a name="azure-dns-service"></a>Servizio DNS di Azure

Se si usa il servizio DNS fornito da Azure, consentire l'accesso a __168.63.129.16__ sulla porta 53 sia per TCP che per UDP. Per ulteriori informazioni, vedere il documento [risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) . Se si usa il DNS personalizzato, ignorare questo passaggio.

## <a name="health-and-management-services-all-regions"></a>Servizi di integrità e gestione: tutte le aree

Consentire il traffico dagli indirizzi IP seguenti per i servizi di gestione e integrità di Azure HDInsight, che si applicano a tutte le aree di Azure:

| Indirizzo IP di origine | Destination  | Direzione |
| ---- | ----- | ----- |
| 168.61.49.99 | \*: 443 | In ingresso |
| 23.99.5.239 | \*: 443 | In ingresso |
| 168.61.48.131 | \*: 443 | In ingresso |
| 138.91.141.162 | \*: 443 | In ingresso |

## <a name="health-and-management-services-specific-regions"></a>Health and Management Services: aree specifiche

Consentire il traffico dagli indirizzi IP elencati per i servizi di gestione e integrità di Azure HDInsight nell'area specifica di Azure in cui si trovano le risorse:

> [!IMPORTANT]  
> Se l'area di Azure in uso non è inclusa nell'elenco, usare la funzionalità [tag di servizio](hdinsight-service-tags.md) per i gruppi di sicurezza di rete.

| Country | Region | Indirizzi IP di origine consentiti | Destinazione consentita | Direzione |
| ---- | ---- | ---- | ---- | ----- |
| Asia | Asia orientale | 23.102.235.122</br>52.175.38.134 | \*: 443 | In ingresso |
| &nbsp; | Asia sud-orientale | 13.76.245.160</br>13.76.136.249 | \*: 443 | In ingresso |
| Australia | Australia orientale | 104.210.84.115</br>13.75.152.195 | \*: 443 | In ingresso |
| &nbsp; | Australia sud-orientale | 13.77.2.56</br>13.77.2.94 | \*: 443 | In ingresso |
| Brasile | Brasile meridionale | 191.235.84.104</br>191.235.87.113 | \*: 443 | In ingresso |
| Canada | Canada orientale | 52.229.127.96</br>52.229.123.172 | \*: 443 | In ingresso |
| &nbsp; | Canada centrale | 52.228.37.66</br>52.228.45.222 |\*: 443 | In ingresso |
| Cina | Cina settentrionale | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*: 443 | In ingresso |
| &nbsp; | Cina orientale | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*: 443 | In ingresso |
| &nbsp; | Cina settentrionale 2 | 40.73.37.141</br>40.73.38.172 | \*: 443 | In ingresso |
| &nbsp; | Cina orientale 2 | 139.217.227.106</br>139.217.228.187 | \*: 443 | In ingresso |
| Europa | Europa settentrionale | 52.164.210.96</br>13.74.153.132 | \*: 443 | In ingresso |
| &nbsp; | Europa occidentale| 52.166.243.90</br>52.174.36.244 | \*: 443 | In ingresso |
| Francia | Francia centrale| 20.188.39.64</br>40.89.157.135 | \*: 443 | In ingresso |
| Germania | Germania centrale | 51.4.146.68</br>51.4.146.80 | \*: 443 | In ingresso |
| &nbsp; | Germania nord-orientale | 51.5.150.132</br>51.5.144.101 | \*: 443 | In ingresso |
| India | India centrale | 52.172.153.209</br>52.172.152.49 | \*: 443 | In ingresso |
| &nbsp; | India meridionale | 104.211.223.67<br/>104.211.216.210 | \*: 443 | In ingresso |
| Giappone | Giappone orientale | 13.78.125.90</br>13.78.89.60 | \*: 443 | In ingresso |
| &nbsp; | Giappone occidentale | 40.74.125.69</br>138.91.29.150 | \*: 443 | In ingresso |
| Corea del Sud | Corea centrale | 52.231.39.142</br>52.231.36.209 | \*: 443 | In ingresso |
| &nbsp; | Corea meridionale | 52.231.203.16</br>52.231.205.214 | \*: 443 | In ingresso
| Regno Unito | Regno Unito occidentale | 51.141.13.110</br>51.141.7.20 | \*: 443 | In ingresso |
| &nbsp; | Regno Unito meridionale | 51.140.47.39</br>51.140.52.16 | \*: 443 | In ingresso |
| Stati Uniti | Stati Uniti centrali | 13.89.171.122</br>13.89.171.124 | \*: 443 | In ingresso |
| &nbsp; | Stati Uniti orientali | 13.82.225.233</br>40.71.175.99 | \*: 443 | In ingresso |
| &nbsp; | Stati Uniti centro-settentrionali | 157.56.8.38</br>157.55.213.99 | \*: 443 | In ingresso |
| &nbsp; | Stati Uniti centro-occidentali | 52.161.23.15</br>52.161.10.167 | \*: 443 | In ingresso |
| &nbsp; | Stati Uniti occidentali | 13.64.254.98</br>23.101.196.19 | \*: 443 | In ingresso |
| &nbsp; | Stati Uniti occidentali 2 | 52.175.211.210</br>52.175.222.222 | \*: 443 | In ingresso |
| &nbsp; | Emirati Arabi Uniti settentrionali | 65.52.252.96</br>65.52.252.97 | \*: 443 | In ingresso |
| &nbsp; | Emirati Arabi Uniti centrali | 20.37.76.96</br>20.37.76.99 | \*: 443 | In ingresso |

Per informazioni sugli indirizzi IP da usare per Azure per enti pubblici, vedere il documento [Azure Government Intelligence + Analytics](../azure-government/compare-azure-government-global-azure.md) (Intelligence e Analisi di Azure per enti pubblici).

Per altre informazioni, vedere [controllare il traffico di rete](./control-network-traffic.md).

Se si usano le route definite dall'utente (UDR), è necessario specificare una route e consentire il traffico in uscita dalla rete virtuale agli indirizzi IP precedenti con l'hop successivo impostato su "Internet".

## <a name="next-steps"></a>Passaggi successivi

* [Creare reti virtuali per i cluster HDInsight di Azure](hdinsight-create-virtual-network.md)
* [Tag di servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight](hdinsight-service-tags.md)
