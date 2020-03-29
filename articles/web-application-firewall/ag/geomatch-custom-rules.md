---
title: Regole personalizzate di Geomatch di Azure Web Application Firewall (WAF)
description: Questo articolo è una panoramica delle regole personalizzate di corrispondenza geografica di Web Application Firewall (WAF) nel gateway applicazione di Azure.This article is an overview of Web Application Firewall (WAF) geomatch custom rules on Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: 6725e1de21dbd103850071f7511e2800c6bd7b69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961088"
---
# <a name="geomatch-custom-rules-preview"></a>Regole personalizzate di geocorrispondenza (anteprima)Geomatch custom rules (preview)

Le regole personalizzate consentono di creare regole personalizzate in base alle esigenze esatte delle applicazioni e dei criteri di sicurezza. A questo punto, è possibile limitare l'accesso alle applicazioni Web in base al paese o all'area geografica. Come per tutte le regole personalizzate, questa logica può essere abbinata ad altre regole in base alle esigenze dell'applicazione.

Per creare una regola personalizzata di filtro geografico, è sufficiente selezionare *Geo-location* come Tipo di corrispondenza, quindi selezionare il paese o i paesi che si desidera consentire/bloccare dall'applicazione. Per altre informazioni, vedere [come creare regole personalizzate in Powershell](configure-waf-custom-rules.md) e altri esempi di regole personalizzate(create-custom-waf-rules.md).

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="country-codes"></a>Codici paese

Se si utilizza l'operatore Geomatch, i selettori possono essere uno dei seguenti codici paese a due cifre. 

|Codice paese | Nome del paese |
| ----- | ----- |
| AD | Andorra |
| AE | Emirati Arabi Uniti|
| AF | Afghanistan|
| AG | Antigua e Barbuda|
| AL | Albania|
| AM | Armenia|
| AO | Angola|
| AR | Argentina|
| AS | Isole Samoa americane|
| AT | Austria|
| AU | Australia|
| AZ | Azerbaigian|
| BA | Bosnia ed Erzegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Belgio|
| BF | Burkina Faso|
| BG | Bulgaria|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brasile|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botswana|
| BY | Bielorussia|
| BZ | Belize|
| CA | Canada|
| CD | Congo, Repubblica democratica|
| CF | Repubblica centrafricana|
| CH | Svizzera|
| CI | Côte d'Ivoire (Costa d'Avorio)|
| CL | Cile|
| CM | Camerun|
| CN | Cina|
| CO | Colombia|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Cipro|
| CZ | Repubblica ceca|
| DE | Germania|
| DK | Danimarca|
| DO | Repubblica dominicana|
| DZ | Algeria|
| EC | Ecuador|
| EE | Estonia|
| EG | Egitto|
| ES | Spagna|
| ET | Etiopia|
| FI | Finlandia|
| FJ | Figi|
| FM | Micronesia, stati di|
| VF | Francia|
| GB | Regno Unito|
| GE | Georgia|
| GF | Guayana francese|
| GH | Ghana|
| GN | Guinea|
| GP | Guadalupa|
| GR | Grecia|
| GT | Guatemala|
| GY | Guiana|
| HK | RAS di Hong Kong|
| HN | Honduras|
| HR | Croazia|
| HT | Haiti|
| HU | Ungheria|
| ID | Indonesia|
| IE | Irlanda|
| IL | Israele|
| IN | India|
| IQ | Iraq|
| IR | Iran, Repubblica islamica|
| IS | Islanda|
| IT | Italia|
| JM | Giamaica|
| JO | Giordania|
| JP | Giappone|
| KE | Kenya|
| KG | Kirghizistan|
| KH | Cambogia|
| KI | Kiribati|
| KN | Saint Kitts e Nevis|
| KP | Repubblica democratica popolare di Corea|
| KR | Repubblica di Corea|
| KW | Kuwait|
| KY | Isole Cayman|
| KZ | Kazakhstan|
| LA | Repubblica democratica del Laos|
| LB | Libano|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Lituania|
| LU | Lussemburgo|
| LV | Lettonia|
| LY | Libia |
| MA | Marocco|
| MD | Moldova, Repubblica|
| MG | Madagascar|
| MK | Macedonia del Nord|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolia|
| MO | RAS di Macao|
| MQ | Martinica|
| MR | Mauritania|
| MT | Malta|
| MV | Maldive|
| MW | Malawi|
| MX | Messico|
| MY | Malaysia|
| MZ | Mozambico|
| ND | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Paesi Bassi|
| NO | Norvegia|
| NP | Nepal|
| NR | Nauru|
| NZ | Nuova Zelanda|
| OM | Oman|
| PA | Panama|
| PE | Perù|
| PH | Filippine|
| PK | Pakistan|
| PL | Polonia|
| PR | Portorico|
| PT | Portogallo|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Riunione|
| RO | Romania|
| RS | Serbia|
| RU | Federazione russa|
| LS | Ruanda|
| SA | Arabia Saudita|
| SD | Sudan|
| SE | Svezia|
| SG | Singapore|
| SI | Slovenia|
| SK | Slovacchia|
| SN | Senegal|
| SO | Somalia|
| SR | Suriname|
| SS | Sudan del Sud|
| SV | El Salvador|
| SY | Repubblica araba siriana|
| SZ | Swaziland|
| TC | Isole Turks e Caicos|
| TG | Togo|
| TH | Thailandia|
| TN | Tunisia|
| TR | Turchia|
| TT | Trinidad e Tobago|
| TW | Taiwan|
| TZ | Tanzania, Repubblica del|
| UA | Ucraina|
| UG | Uganda|
| Stati Uniti | Stati Uniti|
| UY | Uruguay|
| UZ | Uzbekistan|
| VC | Saint Vincent e Grenadine|
| VE | Venezuela|
| VG | Isole Vergini britanniche|
| VI | Isole Vergini americane|
| VN | Vietnam|
| ZA | Sud Africa|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le regole personalizzate, [creare regole personalizzate.](create-custom-waf-rules.md)
