---
title: Azure Synapse Analytics crittografia
description: Articolo che illustra la crittografia in Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 71249534c6a088088213659b5a45e042229721c7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813183"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Crittografia per le aree di lavoro di Azure Synapse Analytics

In questo articolo verrà illustrato quanto segue:
* Crittografia dei dati in pausa nelle aree Synapse Analytics lavoro.
* Configurazione delle aree di lavoro synapse per abilitare la crittografia con una chiave gestita dal cliente.
* Gestione delle chiavi usate per crittografare i dati nelle aree di lavoro.

## <a name="encryption-of-data-at-rest"></a>Crittografia dei dati inattivi

Una soluzione encryption-at-rest completa garantisce che i dati non siano mai persistenti in formato non crittografato. La doppia crittografia dei dati in stato di inquieto riduce le minacce con due livelli separati di crittografia per proteggersi da compromissione di qualsiasi livello singolo. Azure Synapse Analytics un secondo livello di crittografia per i dati nell'area di lavoro con una chiave gestita dal cliente. Questa chiave è salvaguardata [nell'Azure Key Vault](../../key-vault/general/overview.md), che consente di assumere la proprietà della gestione e della rotazione delle chiavi.

Il primo livello di crittografia per i servizi di Azure è abilitato con chiavi gestite dalla piattaforma. Per impostazione predefinita, Azure Disks e i dati negli account Archiviazione di Azure vengono crittografati automaticamente in stato di inquieto. Per altre informazioni sull'uso della crittografia in Microsoft Azure, vedere Panoramica [di Crittografia di Azure.](../../security/fundamentals/encryption-overview.md)

## <a name="azure-synapse-encryption"></a>Azure Synapse crittografia

Questa sezione consente di comprendere meglio come la crittografia delle chiavi gestita dal cliente viene abilitata e applicata nelle aree di lavoro di Synapse. Questa crittografia usa chiavi esistenti o nuove chiavi generate in Azure Key Vault. Una singola chiave viene usata per crittografare tutti i dati in un'area di lavoro. Le aree di lavoro Synapse supportano le chiavi RSA con chiavi con dimensioni di byte 2048 e 3072.

> [!NOTE]
> Le aree di lavoro Synapse non supportano l'uso Elliptic-Curve chiavi di crittografia (ECC) per la crittografia.

I dati nei componenti Synapse seguenti vengono crittografati con la chiave gestita dal cliente configurata a livello di area di lavoro:
* Pool SQL
 * Pool SQL dedicati
 * Pool SQL serverless
* Pool di Apache Spark
* Azure Data Factory runtime di integrazione, pipeline e set di dati.

## <a name="workspace-encryption-configuration"></a>Configurazione della crittografia dell'area di lavoro

Le aree di lavoro possono essere configurate per abilitare la crittografia doppia con una chiave gestita dal cliente al momento della creazione dell'area di lavoro. Selezionare l'opzione "Abilita doppia crittografia tramite una chiave gestita dal cliente" nella scheda "Sicurezza" durante la creazione della nuova area di lavoro. È possibile scegliere di immettere un URI dell'identificatore di chiave o di selezionarlo da un elenco di insiemi di credenziali delle chiavi nella **stessa area dell'area** di lavoro. La Key Vault deve avere la **protezione dall'eliminazione abilitata.**

> [!IMPORTANT]
> Non è possibile modificare l'impostazione di configurazione per la crittografia doppia dopo la creazione dell'area di lavoro.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="Questo diagramma illustra l'opzione che deve essere selezionata per abilitare un'area di lavoro per la crittografia doppia con una chiave gestita dal cliente.":::

### <a name="key-access-and-workspace-activation"></a>Accesso alla chiave e attivazione dell'area di lavoro

Il Azure Synapse di crittografia con chiavi gestite dal cliente prevede che l'area di lavoro accedono alle chiavi in Azure Key Vault crittografare e decrittografare in base alle esigenze. Le chiavi vengono rese accessibili all'area di lavoro tramite criteri di accesso o Azure Key Vault [controllo degli accessi in base al ruolo.](../../key-vault/general/rbac-guide.md) Quando si concedono le autorizzazioni tramite un criterio di accesso Azure Key Vault, scegliere l'opzione ["Solo applicazione"](../../key-vault/general/security-features.md#key-vault-authentication-options) durante la creazione dei criteri (selezionare l'identità gestita dell'area di lavoro e non aggiungerla come applicazione autorizzata).

 Per poter attivare l'area di lavoro, è necessario concedere all'identità gestita dell'area di lavoro le autorizzazioni necessarie per l'insieme di credenziali delle chiavi. Questo approccio graduale all'attivazione dell'area di lavoro garantisce che i dati nell'area di lavoro siano crittografati con la chiave gestita dal cliente. Si noti che la crittografia può essere abilitata o disabilitata per i pool SQL dedicati. Per impostazione predefinita, ogni pool non è abilitato per la crittografia.

#### <a name="permissions"></a>Autorizzazioni

Per crittografare o decrittografare i dati in pausa, l'identità gestita dell'area di lavoro deve avere le autorizzazioni seguenti:
* WrapKey (per inserire una chiave in Key Vault quando si crea una nuova chiave).
* UnwrapKey (per ottenere la chiave per la decrittografia).
* Get (per leggere la parte pubblica di una chiave)

#### <a name="workspace-activation"></a>Attivazione dell'area di lavoro

Dopo aver creato l'area di lavoro (con la doppia crittografia abilitata), rimarrà in uno stato "In sospeso" fino a quando l'attivazione non riesce. L'area di lavoro deve essere attivata prima di poter usare completamente tutte le funzionalità. Ad esempio, è possibile creare un nuovo pool SQL dedicato solo al termine dell'attivazione. Concedere all'identità gestita dell'area di lavoro l'accesso all'insieme di credenziali delle chiavi e fare clic sul collegamento di attivazione nell'area portale di Azure banner. Al termine dell'attivazione, l'area di lavoro è pronta per l'uso con la garanzia che tutti i dati in essa contenuti sono protetti con la chiave gestita dal cliente. Come indicato in precedenza, l'insieme di credenziali delle chiavi deve avere la protezione della ripulitura abilitata per il successo dell'attivazione.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="Questo diagramma mostra il banner con il collegamento di attivazione per l'area di lavoro.":::


### <a name="manage-the-workspace-customer-managed-key"></a>Gestire la chiave gestita dal cliente dell'area di lavoro 

È possibile modificare la chiave gestita dal cliente usata per crittografare i dati dalla pagina **Crittografia** nel portale di Azure. Anche in questo caso è possibile scegliere una nuova chiave usando un identificatore di chiave o selezionare un insieme di credenziali delle chiavi a cui si ha accesso nella stessa area dell'area di lavoro. Se si sceglie una chiave in un insieme di credenziali delle chiavi diverso da quelli usati in precedenza, concedere all'identità gestita dell'area di lavoro le autorizzazioni "Get", "Wrap" e "Unwrap" per il nuovo insieme di credenziali delle chiavi. L'area di lavoro convaliderà l'accesso al nuovo insieme di credenziali delle chiavi e tutti i dati nell'area di lavoro verranno crittografati nuovamente con la nuova chiave.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="Questo diagramma illustra la sezione Relativa alla crittografia dell'area di lavoro nel portale di Azure.":::

>[!IMPORTANT]
>Quando si modifica la chiave di crittografia di un'area di lavoro, conservarla finché non viene sostituita nell'area di lavoro con una nuova chiave. Ciò consente la decrittografia dei dati con la chiave precedente prima di essere crittografati nuovamente con la nuova chiave.

I criteri di Azure Key Vault per la rotazione automatica e periodica delle chiavi o le azioni sulle chiavi possono comportare la creazione di nuove versioni delle chiavi. È possibile scegliere di crittografare nuovamente tutti i dati nell'area di lavoro con la versione più recente della chiave attiva. Per crittografare nuovamente, modificare la chiave nel portale di Azure in una chiave temporanea e quindi tornare alla chiave che si vuole usare per la crittografia. Ad esempio, per aggiornare la crittografia dei dati usando la versione più recente della chiave attiva Key1, modificare la chiave gestita dal cliente dell'area di lavoro in chiave temporanea Key2. Attendere il completamento della crittografia con Key2. Quindi, la chiave gestita dal cliente dell'area di lavoro di nuovo su Key1-data nell'area di lavoro verrà crittografata nuovamente con la versione più recente di Key1.

> [!NOTE]
> Azure Synapse Analytics si sta attivamente lavorando all'aggiunta di funzionalità per crittografare automaticamente i dati quando vengono create nuove versioni delle chiavi. Finché questa funzionalità non sarà disponibile, per garantire la coerenza nell'area di lavoro, forzare la nuova crittografia dei dati usando il processo descritto in precedenza.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>Sql Transparent Data Encryption con chiavi gestite dal servizio

SQL Transparent Data Encryption (TDE) è disponibile per i pool SQL dedicati nelle aree di lavoro *non* abilitate per la crittografia doppia. In questo tipo di area di lavoro viene usata una chiave gestita dal servizio per fornire la crittografia doppia per i dati nei pool SQL dedicati. La crittografia TDE con la chiave gestita dal servizio può essere abilitata o disabilitata per singoli pool SQL dedicati.

## <a name="next-steps"></a>Passaggi successivi

[Usare i criteri di Azure predefiniti per implementare la protezione della crittografia per le aree di lavoro di Synapse](../policy-reference.md)

