---
title: Archiviazione di immagini contenitore
description: Contiene informazioni dettagliate sulla modalità di archiviazione delle immagini del contenitore Docker in Registro Azure Container, tra cui sicurezza, ridondanza e capacità.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456200"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Archiviazione immagini del contenitore in Registro Azure Container

Ogni Registro Azure Container [Basic, Standard e Premium](container-registry-skus.md) beneficia delle funzionalità di archiviazione avanzate di Azure, come ad esempio la crittografia dei dati inattivi per la sicurezza dei dati di immagine e la ridondanza geografica per la protezione dei dati di immagine. Le sezioni seguenti descrivono sia le funzionalità che i limiti di archiviazione immagini in Registro Azure Container.

## <a name="encryption-at-rest"></a>Crittografia dei dati inattivi

Tutte le immagini del contenitore presenti nel registro vengono crittografate a riposo. Azure esegue automaticamente la crittografia di un'immagine prima di archiviarla e la decrittografa in tempo reale quando l'utente, un'applicazione o un servizio eseguono il pull dell'immagine.

## <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

Azure usa uno schema di archiviazione con ridondanza geografica per evitare la perdita delle immagini del contenitore. Registro Azure Container replica automaticamente le immagini del contenitore in più data center geograficamente distanti, impedendone la perdita in caso di guasto di un sistema di archiviazione regionale.

## <a name="geo-replication"></a>Replica geografica

Per gli scenari che richiedono una disponibilità ancora più elevata, è consigliabile usare la funzione di [replica geografica](container-registry-geo-replication.md) dei registri Premium. La replica geografica scongiura la perdita dell'accesso al registro in caso di guasto *totale* a livello regionale, non solo di un guasto nel sistema di archiviazione. La replica geografica offre anche altri vantaggi, come l'archiviazione immagini vicina alla rete per push/pull più rapidi negli scenari di distribuzione o sviluppo distribuiti.

## <a name="image-limits"></a>Limiti delle immagini

Nella tabella seguente vengono descritti i limiti di archiviazione e di immagine del contenitore applicati al Registro contenitori di Azure.

| Risorsa | Limite |
| -------- | :---- |
| Repository | Nessun limite |
| Immagini | Nessun limite |
| Livelli | Nessun limite |
| Tag | Nessun limite|
| Archiviazione | 5 TB |

Un numero molto elevato di repository e tag può compromettere le prestazioni del registro. Eliminare periodicamente i repository inutilizzati, tag e le immagini come parte di una routine di manutenzione del registro. Le risorse del registro eliminate, come repository, immagini e tag, *non possono* essere recuperate dopo l'eliminazione. Per altre informazioni sull'eliminazione delle risorse del registro, vedere [Eliminare le immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="storage-cost"></a>Costo di archiviazione

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Registro Azure Container][pricing].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui diversi SKU di Registro Azure Container (Basic, Standard e Premium), vedere [SKU di Registro Azure Container](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
