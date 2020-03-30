---
title: Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure
description: Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: a8cb0ea9fb3c6e8388271c4274baf4ecc7282cda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247232"
---
# <a name="azure-key-vault-developers-guide"></a>Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure

Key Vault consente di accedere in modo sicuro ai dati sensibili dall'interno delle applicazioni:

- Le chiavi e i segreti vengano protetti senza dover scrivere manualmente il codice e possano essere usati dalle applicazioni.
- I clienti possano avere e gestire autonomamente le rispettive chiavi in modo che lo sviluppatore possa concentrarsi su altre attività, ad esempio fornire le principali funzionalità del software. In questo modo, le applicazioni non saranno proprietarie della responsabilità o della responsabilità potenziale per le chiavi e i segreti tenant dei clienti.
- L'applicazione possa usare le chiavi per la firma e la crittografia, ma tenga la gestione della chiave esterna all'applicazione in modo che la soluzione sia adatta a essere un'app geograficamente distribuita.
- Gestire i certificati dell'insieme di credenziali delle chiavi. Per i dettagli, vedere l'articolo relativo alle [informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md).

Per altre informazioni generali sull'insieme di credenziali delle chiavi di Azure, vedere l'articolo [Cos'è l'insieme di credenziali chiave di Azure?](key-vault-overview.md)

## <a name="public-previews"></a>Anteprime pubbliche

Periodicamente, viene rilasciata un'anteprima pubblica di una nuova funzionalità di Key Vault. Provare le nuove funzionalità e comunicare il feedback all'indirizzo e-mail azurekeyvault@microsoft.com, dedicato ai commenti e ai suggerimenti.

## <a name="creating-and-managing-key-vaults"></a>Creazione e gestione di insiemi di credenziali delle chiavi

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. Le identità gestite per le risorse di Azure consentono di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice. 

Per altre informazioni sulle identità gestite per le risorse di Azure, vedere [la panoramica delle identità gestite](../active-directory/managed-identities-azure-resources/overview.md). Per altre informazioni sull'uso di AAD, vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Prima di usare chiavi, segreti o certificati nell'insieme di credenziali delle chiavi, l'insieme deve essere creato e gestito tramite l'interfaccia della riga di comando, PowerShell, modelli di Resource Manager o REST, come descritto negli articoli seguenti:

- [Creare e gestire insiemi di credenziali delle chiavi tramite l'interfaccia della riga di comando](quick-create-cli.md)
- [Creare e gestire insiemi di credenziali delle chiavi tramite PowerShell](quick-create-powershell.md)
- [Creare e gestire gli insiemi di credenziali delle chiavi con la porta di AzureCreate and manage Key Vaults with the Azure port](quick-create-portal.md)
- [Crea e gestisci gli insiemi di credenziali delle chiavi con Python](quick-create-python.md)
- [Creare e gestire gli insiemi di credenziali delle chiavi con Java](quick-create-java.md)
- [Creare e gestire gli insiemi di credenziali delle chiavi con Node.js](quick-create-node.md)
- [Creare e gestire gli insiemi di credenziali delle chiavi con .NET (v4 SDK)](quick-create-net.md)
- [Creare un insieme di credenziali delle chiavi e aggiungere un segreto tramite un modello di Azure Resource Manager](quick-create-template.md)
- [Creare e gestire Key Vault con REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codifica con l'insieme di credenziali delle chiavi

Il sistema di gestione di Key Vault per i programmatori è costituito da diverse interfacce. Questa sezione contiene collegamenti a tutti i linguaggi, nonché alcuni esempi di codice. 

### <a name="supported-programming-and-scripting-languages"></a>Linguaggi di programmazione e di script supportati

#### <a name="rest"></a>REST

Tutte le risorse di Key Vault (insiemi di credenziali, chiavi, segreti e così via) sono accessibili tramite l'interfaccia REST. 

[Informazioni di riferimento sull'API REST di Key Vault](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Informazioni di riferimento sulle API .NET per Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Per altre informazioni sulla versione 2.x di .NET SDK, vedere le [note sulla versione](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK per Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

In Node.js l'API di gestione e l'API degli oggetti di Key Vault sono separate. L'articolo di panoramica seguente consente di accedere a entrambe. 

[Moduli di Azure Key Vault per Node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Librerie di Azure Key Vault per Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[Interfaccia della riga di comando di Azure per Key Vault](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell per Key Vault](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Esempi di codice

Per esempi completi che usano l'insieme di credenziali delle chiavi con le applicazioni, vedere:

- [Esempi di codice di Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault): esempi di codice per Azure Key Vault. 
- [Usare Azure Key Vault da un'applicazione Web](quick-create-net.md): esercitazione sull'uso di Azure Key Vault da un'applicazione Web in Azure. 

## <a name="how-tos"></a>Procedure

Gli articoli e gli scenari seguenti offrono indicazioni specifiche su come usare l'insieme di credenziali delle chiavi di Azure:

- [Modificare l'ID tenant dell'insieme di credenziali delle chiavi dopo lo spostamento della sottoscrizione](key-vault-subscription-move-fix.md): quando si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non sono accessibili dalle entità (utenti e applicazioni) nel tenant B. Per risolvere questo problema, seguire questa guida.
- [Accesso a un insieme di credenziali delle chiavi protetto da firewall](key-vault-access-behind-firewall.md): per accedere a un insieme di credenziali delle chiavi, l'applicazione client dell'insieme di credenziali delle chiavi deve poter accedere a più endpoint per varie funzionalità.
- [Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure](key-vault-hsm-protected-keys.md) : questo articolo consente di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure.
- [Passare valori protetti come password durante la distribuzione](../azure-resource-manager/templates/key-vault-parameter.md) : se è necessario passare come parametro durante la distribuzione un valore protetto, ad esempio una password, è possibile archiviare tale valore come chiave privata in un insieme di credenziali delle chiavi di Azure e fare riferimento al valore in altri modelli di Resource Manager.
- [Extensible Key Management tramite l'insieme di credenziali delle chiavi di Azure (SQL Server)](https://msdn.microsoft.com/library/dn198405.aspx): SQL Server Connector per l'insieme di credenziali delle chiavi di Azure consente a SQL Server e a SQL in una VM di sfruttare il servizio di insieme di credenziali delle chiavi di Azure come provider EKM (Extensible Key Management) per proteggere le chiavi di crittografia per le applicazioni. L'articolo contiene esempi di Transparent Data Encryption, backup della crittografia e crittografia a livello di colonna.
- [Come distribuire certificati a macchine virtuali dall'insieme di credenziali delle chiavi](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : un'applicazione cloud in esecuzione su una VM in Azure necessita di un certificato. Come ottenere oggi il certificato per questa VM?
- [Come configurare l'insieme di credenziali delle chiavi con rotazione e controllo delle chiavi end-to-end](key-vault-key-rotation-log-monitoring.md): illustra come configurare la rotazione e il controllo delle chiavi con l'insieme di credenziali delle chiavi di Azure.
- [Deploying Azure Web App Certificate through Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) (Distribuzione del certificato dell'app Web di Azure tramite l'insieme di credenziali delle chiavi) offre istruzioni dettagliate per la distribuzione dei certificati archiviati nell'insieme di credenziali delle chiavi come parte dell'offerta del [certificato del servizio app](https://azure.microsoft.com/blog/internals-of-app-service-certificate/).
- [Concedere a molte applicazioni l'autorizzazione per accedere a un insieme di credenziali delle chiavi](key-vault-group-permissions-for-apps.md): i criteri di controllo di accesso di Key Vault supportano fino a 1024 voci. È tuttavia possibile creare un gruppo di sicurezza di Azure Active Directory. Aggiungere tutte le entità servizio associate a questo gruppo di sicurezza e quindi concedere a tale gruppo di accedere a Key Vault.
- Per indicazioni specifiche sulle attività relative all'integrazione e all'uso dell'insieme di credenziali delle chiavi con Azure, vedere gli [esempi di modelli di Azure Resource Manager di Ryan Jones per l'insieme di credenziali delle chiavi](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](key-vault-soft-delete-cli.md) descrive l'utilizzo e il ciclo di vita di un insieme di credenziali delle chiavi e di vari oggetti dell'insieme di credenziali delle chiavi con l'eliminazione temporanea abilitata.
- [Come usare l'eliminazione temporanea di Key Vault con PowerShell](key-vault-soft-delete-powershell.md) descrive l'utilizzo e il ciclo di vita di un insieme di credenziali delle chiavi e di vari oggetti dell'insieme di credenziali delle chiavi con l'eliminazione temporanea abilitata.

## <a name="integrated-with-key-vault"></a>Integrazione con l'insieme di credenziali delle chiavi

Questi articoli illustrano altri scenari e servizi che usano o si integrano con Key Vault.

- [Crittografia disco](../security/fundamentals/encryption-overview.md) di Azure sfrutta la funzionalità [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard del settore di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per il sistema operativo e i dischi dati. La soluzione è integrata con l'insieme di credenziali delle chiavi di Azure per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi sui dischi delle macchine virtuali siano crittografati nell'archiviazione di Azure.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) offre la possibilità di crittografare i dati archiviati nell'account. In merito alla gestione delle chiavi, Data Lake Store offre due modalità per la gestione delle chiavi di crittografia master (MEK) necessarie per decrittografare i dati archiviati in Data Lake Store. È possibile affidare la gestione delle chiavi MEK a Data Lake Store oppure mantenere la proprietà delle chiavi MEK usando il proprio account dell'insieme di credenziali delle chiavi di Azure. La modalità di gestione delle chiavi viene specificata durante la creazione dell'account di Data Lake Store.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) consente di gestire la propria chiave tenant. Ad esempio, anziché affidare a Microsoft la gestione della chiave tenant (impostazione predefinita), l'utente può gestire la propria chiave tenant per garantire la conformità alle normative specifiche che si applicano all'organizzazione. La gestione della propria chiave tenant viene definita Bring Your Own Key o BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Panoramiche e concetti su Key Vault

- [Comportamento di eliminazione temporanea di Key Vault](key-vault-ovw-soft-delete.md) descrive una funzionalità che consente il recupero di oggetti eliminati, sia che l'eliminazione sia stata accidentale sia che sia stata intenzionale.
- [Limitazione del client di Key Vault](key-vault-ovw-throttling.md) descrive i concetti di base della limitazione e offre un approccio per l'app.
- [Panoramica delle chiavi dell'account di archiviazione di Key Vault](key-vault-ovw-storage-keys.md) descrive le chiavi dell'account di archiviazione di Azure dell'integrazione di Key Vault.
- [Scenari di sicurezza di Key Vault](key-vault-ovw-security-worlds.md) descrive le relazioni tra le aree e le zone di sicurezza.

## <a name="social"></a>Social media

- [Blog sull'insieme di credenziali delle chiavi](https://aka.ms/kvblog)
- [Forum sull'insieme di credenziali delle chiavi](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Supporto di librerie

- [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) include le interfacce **IKey** e **IKeyResolver** per individuare le chiavi dagli identificatori ed eseguire operazioni con le chiavi.
- [Microsoft Azure Key Vault Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornisce funzionalità estese per Insieme di credenziali delle chiavi di Azure.
