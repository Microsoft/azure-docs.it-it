---
title: Gestire gli asset di dati in Azure Data Catalog
description: L'articolo illustra come controllare la visibilità e la proprietà degli asset di dati registrati in Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "68736335"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gestire gli asset di dati in Azure Data Catalog
## <a name="introduction"></a>Introduzione
Azure Data Catalog è progettato per l'individuazione delle origini dati, per poter comprendere e trovare facilmente le origini dati necessarie per eseguire analisi e prendere decisioni. Le funzionalità di individuazione fanno veramente la differenza quando tutti gli utenti hanno la possibilità di trovare e comprendere la più ampia gamma di origini dati disponibili. Con questi elementi, il comportamento predefinito di Data Catalog prevede che tutte le origini dati registrate siano visibili e individuabili da tutti gli utenti del catalogo.

Data Catalog non consente di accedere ai dati stessi. L'accesso ai dati è controllato dal proprietario dell'origine dati. Data Catalog consente di trovare le origini dati e di visualizzare i metadati correlati alle origini registrate nel catalogo.

In alcune situazioni tuttavia le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. In tali scenari gli utenti possono acquisire la proprietà di asset di dati registrati nel catalogo e quindi di controllare la visibilità degli asset di cui sono proprietari.

> [!NOTE]
> Le funzionalità descritte in questo articolo sono disponibili solo nell'edizione Standard di Azure Data Catalog. L'edizione gratuita non offre funzionalità relative alla proprietà e alla limitazione della visibilità di asset di dati.
>
>

## <a name="manage-ownership-of-data-assets"></a>Gestire la proprietà degli asset di dati
Per impostazione predefinita, gli asset di dati registrati in Data Catalog sono senza proprietario. Qualsiasi utente con l'autorizzazione necessaria per accedere al catalogo può trovare e annotare questi asset. Gli utenti possono acquisire la proprietà di asset di dati senza proprietario e quindi limitarne la visibilità.

Quando un asset di dati in Data Catalog ha dei proprietari, solo gli utenti autorizzati dai proprietari possono trovare l'asset e visualizzarne i metadati e solo i proprietari possono eliminare l'asset dal catalogo.

> [!NOTE]
> In Data Catalog la proprietà interessa unicamente i metadati archiviati nel catalogo. La proprietà non conferisce autorizzazioni per l'origine dati sottostante.
>
>

### <a name="take-ownership"></a>Acquisizione della proprietà.
Gli utenti possono diventare proprietari degli asset di dati selezionando l'opzione **accetta proprietà** nel portale di Data Catalog. Non sono necessarie speciali autorizzazioni per diventare proprietario di un asset di dati senza proprietario. Qualsiasi utente può diventare proprietario di un asset di dati senza proprietario.

### <a name="add-owners-and-co-owners"></a>Aggiungere proprietari e comproprietari
Se un asset di dati ha già un proprietario, gli altri utenti non possono semplicemente diventare proprietari. Devono essere aggiunti come comproprietari da un proprietario esistente. Qualsiasi proprietario può aggiungere altri utenti o gruppi di sicurezza come comproprietari.

> [!NOTE]
> È consigliabile che ogni asset di dati con proprietari abbia almeno due proprietari.
>
>

### <a name="remove-owners"></a>Rimuovere i proprietari
Qualsiasi proprietario di asset può rimuovere i relativi comproprietari.

Un proprietario di asset che si rimuove se stesso come proprietario non può più gestire l'asset. Se il proprietario dell'asset rimuove se stesso come proprietario e non esistono altri comproprietari, l'asset viene ripristinato in uno stato non di proprietà.

## <a name="control-visibility"></a>Controllare la visibilità
La visibilità degli asset di dati è controllata dai relativi proprietari. Per limitare la visibilità come impostazione predefinita, in cui tutti gli utenti di Data Catalog possono trovare e visualizzare l'asset di dati, il proprietario dell'asset può alternare l'impostazione di visibilità tra **Tutti** e **Proprietario e questi utenti** nelle proprietà dell'asset. I proprietari possono quindi aggiungere utenti e gruppi di sicurezza specifici.

> [!NOTE]
> Quando è possibile, la proprietà dell'asset e le autorizzazioni di visibilità devono essere assegnate a gruppi di sicurezza e non a singoli utenti.
>
>

## <a name="catalog-administrators"></a>Amministratori del catalogo
Gli amministratori di Data Catalog sono implicitamente comproprietari di tutti gli asset nel catalogo. I proprietari di asset non possono rimuovere la visibilità dagli amministratori, che possono gestire la proprietà e la visibilità per tutti gli asset di dati nel catalogo.

## <a name="summary"></a>Summary
Il modello di crowdsourcing di Data Catalog per l'individuazione di asset di dati e metadati consente a tutti gli utenti del catalogo di contribuire all'individuazione. L'edizione Standard di Data Catalog è progettata per la proprietà e la gestione per poter limitare la visibilità e l'uso di asset di dati specifici.
