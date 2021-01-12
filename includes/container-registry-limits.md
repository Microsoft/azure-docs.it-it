---
title: File di inclusione
description: File di inclusione
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 089b1b6f1af2f19c16866858324bde2e151e8bdb
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98053017"
---
| Risorsa | Basic | Standard | Premium |
|---|---|---|---|
| Spazio di archiviazione incluso<sup>1</sup> (GiB) | 10 | 100 | 500 |
| Limite di archiviazione (TiB) | 20| 20 | 20 |
| Dimensioni massime livello immagini (GiB) | 200 | 200 | 200 |
| Operazioni di lettura al minuto<sup>2, 3</sup> | 1\.000 | 3,000 | 10,000 |
| Operazioni di scrittura al minuto<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| Larghezza di banda in download<sup>2</sup> (Mbps) | 30 | 60 | 100 |
| Larghezza di banda in upload <sup>2</sup> (Mbps) | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| Replica geografica | N/D | N/D | [Supportato][geo-replication] |
| Zone di disponibilità | N/D | N/D | [Anteprima][zones] |
| Attendibilità del contenuto | N/D | N/D | [Supportato][content-trust] |
| Collegamento privato con endpoint privati | N/D | N/D | [Supportato][plink] |
| &bull; Endpoint privati | N/D | N/D | 10 |
| Accesso alla rete virtuale dell'endpoint servizio | N/D | N/D | [Anteprima][vnet] |
| Chiavi gestite dal cliente | N/D | N/D | [Supportato][cmk] |
| Autorizzazioni con ambito repository | N/D | N/D | [Anteprima][token]|
| &bull; token | N/D | N/D | 20.000 |
| &bull; mapping di ambito | N/D | N/D | 20.000 |
| &bull; repository per mapping di ambito | N/D | N/D | 500 |


<sup>1</sup> Spazio di archiviazione incluso nella tariffa giornaliera per ogni livello. È possibile usare uno spazio di archiviazione aggiuntivo, fino al limite di archiviazione del registro, a una velocità giornaliera aggiuntiva per GiB. Per informazioni sui costi, vedere [Prezzi di Registro Azure Container][pricing]. Se è necessario uno spazio di archiviazione oltre il limite di archiviazione del registro, contattare il supporto di Azure.

<sup>2</sup> I dati delle *operazioni di lettura*, *operazioni di scrittura* e *larghezza di banda* sono stime minime. Registro Azure Container migliora le prestazioni in base alle necessità d'uso.

<sup>3</sup>Un [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in operazioni di lettura multiple in base al numero di livelli dell'immagine e al recupero del manifesto.

<sup>4</sup>Un [push di Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in operazioni di scrittura multiple, in base al numero di livelli da inserire. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
