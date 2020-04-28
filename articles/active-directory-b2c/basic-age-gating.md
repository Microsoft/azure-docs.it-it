---
title: Abilitare il controllo dell'accesso in base all'età in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come identificare i minori che utilizzano l'applicazione.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56cbeb8e8fe21f4b39c2f5c6af43e83ae330e5d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189974"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Abilitare il controllo dell'accesso in base all'età in Azure Active Directory B2C

>[!IMPORTANT]
>Questa funzionalità è disponibile in anteprima pubblica. Non usarla per le applicazioni di produzione.
>

Il controllo Age in Azure Active Directory B2C (Azure AD B2C) consente di identificare i minorenni che vogliono usare l'applicazione. È possibile scegliere di impedire ai minori di accedere all'applicazione. In alternativa, gli utenti possono tornare all'applicazione e identificare la propria fascia di età e il relativo stato di consenso dei genitori. Con Azure AD B2C è possibile bloccare i minori privi del consenso dei genitori. Azure AD B2C può inoltre essere configurato in modo da consentire all'applicazione di decidere come gestire l'accesso dei minori.

Dopo aver abilitato il controllo Age nel [flusso utente](user-flow-overview.md), gli utenti vengono invitati quando sono nati e in quale paese/area geografica vivono. Se un utente esegue l'accesso senza avere precedentemente immesso queste informazioni, dovrà farlo al successivo accesso. Le regole vengono applicate ogni volta che un utente esegue l'accesso.

Azure AD B2C usa le informazioni immesse dall'utente per stabilire se si tratta di un minore. Il campo **ageGroup** viene aggiornato di conseguenza nel rispettivo account. Il valore può essere `null`, `Undefined`, `Minor`, `Adult` e `NotAdult`.  I campi **ageGroup** e **consentProvidedForMinor** vengono quindi usati per calcolare il valore di **legalAgeGroupClassification**.

Il controllo dell'accesso in base all'età prevede due valori: l'età in cui un utente non è più considerato un minore e l'età in cui un minore deve avere il consenso dei genitori. La tabella seguente elenca le regole per l'età che vengono usate per identificare un minore e un minore che necessita del consenso.

| Paese/Area geografica | Nome del paese/area geografica | Minore età per cui richiedere il consenso | Minore età |
| -------------- | ------------------- | ----------------- | --------- |
| Impostazione predefinita | nessuno | nessuno | 18 |
| AE | Emirati Arabi Uniti | nessuno | 21 |
| AT | Austria | 14 | 18 |
| BE | Belgio | 14 | 18 |
| BG | Bulgaria | 16 | 18 |
| BH | Bahrain | nessuno | 21 |
| CM | Camerun | nessuno | 21 |
| CY | Cipro | 16 | 18 |
| CZ | Repubblica ceca | 16 | 18 |
| DE | Germania | 16 | 18 |
| DK | Danimarca | 16 | 18 |
| EE | Estonia | 16 | 18 |
| EG | Egitto | nessuno | 21 |
| ES | Spagna | 13 | 18 |
| VF | Francia | 16 | 18 |
| GB | Regno Unito | 13 | 18 |
| GR | Grecia | 16 | 18 |
| HR | Croazia | 16 | 18 |
| HU | Ungheria | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| IT | Italia | 16 | 18 |
| KR | Repubblica di Corea | 14 | 18 |
| LT | Lituania | 16 | 18 |
| LU | Lussemburgo | 16 | 18 |
| LV | Lettonia | 16 | 18 |
| MT | Malta | 16 | 18 |
| N/D | Namibia | nessuno | 21 |
| NL | Paesi Bassi | 16 | 18 |
| PL | Polonia | 13 | 18 |
| PT | Portogallo | 16 | 18 |
| RO | Romania | 16 | 18 |
| SE | Svezia | 13 | 18 |
| SG | Singapore | nessuno | 21 |
| SI | Slovenia | 16 | 18 |
| SK | Slovacchia | 16 | 18 |
| TD | Ciad | nessuno | 21 |
| TH | Thailandia | nessuno | 20 |
| TW | Taiwan | nessuno | 20 |
| Stati Uniti | Stati Uniti | 13 | 18 |

## <a name="age-gating-options"></a>Opzioni di controllo dell'accesso in base all'età

### <a name="allowing-minors-without-parental-consent"></a>Consenso ai minori senza il consenso dei genitori

Per i flussi utente che consentono la registrazione, l'accesso o entrambi, è possibile scegliere di consentire l'accesso all'applicazione ai minori senza consenso. Ai minori senza il consenso dei genitori viene concesso di accedere o registrarsi come di consueto e Azure AD B2C emette un token ID con l'attestazione **legalAgeGroupClassification**. Questa attestazione definisce l'esperienza degli utenti, ad esempio la raccolta del consenso dei genitori e l'aggiornamento del campo **consentProvidedForMinor**.

### <a name="blocking-minors-without-parental-consent"></a>Blocco dei minori senza il consenso dei genitori

Per i flussi utente che consentono la registrazione, l'accesso o entrambi, è possibile scegliere di bloccare l'accesso all'applicazione ai minori senza consenso. Per la gestione degli utenti bloccati in Azure AD B2C sono disponibili le opzioni seguenti:

- Restituisci un messaggio JSON all'applicazione: questa opzione invia una risposta all'applicazione rispetto alla quale è stato bloccato un minore.
- Mostra una pagina di errore: l'utente visualizza una pagina che lo informa dell'impossibilità di accedere all'applicazione.

## <a name="set-up-your-tenant-for-age-gating"></a>Configurare il tenant per il controllo dell'accesso in base all'età

Per usare il controllo dell'accesso in base all'età in un flusso utente, è necessario configurare il tenant in modo tale da supportare proprietà aggiuntive.

1. Assicurarsi di usare la directory che contiene il tenant Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto. Selezionare la directory contenente il tenant.
2. Selezionare **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
3. Selezionare **Proprietà** per il tenant nel menu a sinistra.
2. Nella sezione **Controllo dell'accesso in base all'età** fare clic su **Configura**.
3. Attendere il completamento dell'operazione e il tenant verrà configurato per il controllo dell'accesso in base all'età.

## <a name="enable-age-gating-in-your-user-flow"></a>Abilitare il controllo dell'accesso in base all'età nel flusso utente

Dopo che il tenant è stato configurato per l'uso del controllo dell'accesso in base all'età, è possibile usare questa funzionalità nei [flussi utente](user-flow-versions.md) in cui è abilitata. Abilitare il controllo dell'accesso in base all'età tramite i passaggi seguenti:

1. Creare un flusso utente con il controllo dell'accesso in base all'età abilitato.
2. Dopo aver creato il flusso utente, selezionare **Proprietà** nel menu.
3. Nella sezione **Controllo dell'accesso in base all'età** selezionare **Abilitato**.
4. È quindi possibile decidere in che modo gestire gli utenti che si identificano come minori. Per la **registrazione o l'accesso** selezionare `Allow minors to access your application` o `Block minors from accessing your application`. Se è selezionato il blocco dei minori, selezionare `Send a JSON back to the application` o `Show an error message`.




