---
title: Configurare le app nel portale
description: Informazioni su come configurare le impostazioni comuni per un'app del servizio app nel portale di Azure. Impostazioni dell'app, stringhe di connessione, piattaforma, stack di lingue, contenitore e così via.
keywords: servizio app di Azure, app Web, impostazioni dell'app, variabili di ambiente
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 4990862bacbf2afe2d4b5f39c2b9d31a7c48b78e
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962894"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Configurare un'app del servizio app nel portale di Azure

Questo argomento illustra come configurare le impostazioni comuni per le app Web, il back-end per dispositivi mobili o l'app per le API usando il [portale di Azure].

## <a name="configure-app-settings"></a>Configurare le impostazioni applicazione

Nel servizio app le impostazioni dell'app sono variabili passate come variabili di ambiente al codice dell'applicazione. Per le app Linux e i contenitori personalizzati, il servizio app passa le impostazioni dell'app al contenitore usando il `--env` flag per impostare la variabile di ambiente nel contenitore.

Nel [Azure portal] cercare e selezionare **Servizi app**, quindi selezionare la propria app. 

![Cerca servizi app](./media/configure-common/search-for-app-services.png)

Nel menu a sinistra dell'app selezionare **configurazione**  >  **Impostazioni applicazione**.

![Impostazioni dell'applicazione](./media/configure-common/open-ui.png)

Per gli sviluppatori ASP.NET e ASP.NET Core, l'impostazione delle impostazioni dell'app nel servizio app è simile alla relativa impostazione in `<appSettings>` *Web.config* o *appsettings.json*, ma i valori nel servizio app sostituiscono quelli in *Web.config* o *appsettings.json*. È possibile usare le impostazioni di sviluppo (ad esempio, la password di MySQL locale) in *Web.config* o *appsettings.js*, ma i segreti di produzione, ad esempio la password del database MySQL di Azure, sono sicuri nel servizio app. Lo stesso codice usa le impostazioni di sviluppo quando si esegue il debug in locale e usa i segreti di produzione durante la distribuzione in Azure.

Gli stack di altri linguaggi, in modo analogo, ottengono le impostazioni dell'app come variabili di ambiente in fase di esecuzione. Per i passaggi specifici dello stack di linguaggio, vedere:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Contenitori personalizzati](configure-custom-container.md#configure-environment-variables)

Le impostazioni dell'app vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

> [!NOTE]
> Le impostazioni dell'app possono anche essere risolte da [Key Vault](../key-vault/index.yml) usando [Key Vault riferimenti](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostra valori nascosti

Per impostazione predefinita, i valori per le impostazioni dell'app sono nascosti nel portale per la sicurezza. Per visualizzare un valore nascosto di un'impostazione dell'app, fare clic sul campo **valore** di tale impostazione. Per visualizzare i valori di tutte le impostazioni dell'app, fare clic sul pulsante **Mostra valore** .

### <a name="add-or-edit"></a>Aggiungi o modifica

Per aggiungere una nuova impostazione di app, fare clic su **nuova impostazione applicazione**. Nella finestra di dialogo è possibile [posizionare l'impostazione sullo slot corrente](deploy-staging-slots.md#which-settings-are-swapped).

Per modificare un'impostazione, fare clic sul pulsante **modifica** sul lato destro.

Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** indietro nella pagina di **configurazione** .

> [!NOTE]
> In un contenitore Linux predefinito o in un contenitore Linux personalizzato, qualsiasi struttura di chiavi JSON annidate nel nome dell'impostazione dell'app, ad esempio, `ApplicationInsights:InstrumentationKey` deve essere configurata nel servizio app come `ApplicationInsights__InstrumentationKey` nome della chiave. In altre parole, qualsiasi `:` deve essere sostituito da `__` (doppio carattere di sottolineatura).
>

### <a name="edit-in-bulk"></a>Modifica in blocco

Per aggiungere o modificare in blocco le impostazioni dell'app, fare clic sul pulsante **modifica avanzata** . Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** indietro nella pagina di **configurazione** .

Le impostazioni dell'app hanno la formattazione JSON seguente:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Configurare le stringhe di connessione

Nel [Azure portal] cercare e selezionare **Servizi app**, quindi selezionare la propria app. Nel menu a sinistra dell'app selezionare **configurazione**  >  **Impostazioni applicazione**.

![Impostazioni dell'applicazione](./media/configure-common/open-ui.png)

Per gli sviluppatori ASP.NET e ASP.NET Core, l'impostazione delle stringhe di connessione nel servizio app è simile alla relativa impostazione in `<connectionStrings>` *Web.config*, ma i valori impostati nel servizio app sostituiscono quelli in *Web.config*. È possibile continuare a usare le impostazioni di sviluppo (ad esempio, un file di database) in *Web.config* e i segreti di produzione (ad esempio, le credenziali del database SQL) in modo sicuro nel servizio app. Lo stesso codice usa le impostazioni di sviluppo quando si esegue il debug in locale e usa i segreti di produzione durante la distribuzione in Azure.

Per gli stack di altri linguaggi, è preferibile usare [le impostazioni dell'app](#configure-app-settings) , perché le stringhe di connessione richiedono una formattazione speciale nelle chiavi della variabile per accedere ai valori. Ecco un'unica eccezione. Tuttavia, quando si configurano le stringhe di connessione nell'app, viene eseguito il backup di alcuni tipi di database di Azure insieme all'app. Per altre informazioni, vedere [elementi di cui viene eseguito il backup](manage-backup.md#what-gets-backed-up). Se questo backup automatico non è necessario, usare le impostazioni dell'app.

In fase di esecuzione, le stringhe di connessione sono disponibili come variabili di ambiente, con prefisso i tipi di connessione seguenti:

* SqlServer `SQLCONNSTR_`  
* MySQL: `MYSQLCONNSTR_` 
* SQLAzure `SQLAZURECONNSTR_` 
* Personalizzato: `CUSTOMCONNSTR_`
* PostgreSQL `POSTGRESQLCONNSTR_`  

Ad esempio, è possibile accedere a una stringa di connessione MySql denominata *connectionstring1* come variabile di ambiente `MYSQLCONNSTR_connectionString1` . Per i passaggi specifici dello stack di linguaggio, vedere:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Contenitori personalizzati](configure-custom-container.md#configure-environment-variables)

Le stringhe di connessione vengono sempre crittografate quando sono archiviate (crittografia dei dati inattivi).

> [!NOTE]
> Le stringhe di connessione possono anche essere risolte da [Key Vault](../key-vault/index.yml) usando [Key Vault riferimenti](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Mostra valori nascosti

Per impostazione predefinita, i valori per le stringhe di connessione sono nascosti nel portale per la sicurezza. Per visualizzare un valore nascosto di una stringa di connessione, è sufficiente fare clic sul campo del **valore** della stringa. Per visualizzare i valori di tutte le stringhe di connessione, fare clic sul pulsante **Mostra valore** .

### <a name="add-or-edit"></a>Aggiungi o modifica

Per aggiungere una nuova stringa di connessione, fare clic su **nuova stringa di connessione**. Nella finestra di dialogo è possibile [mantenere la stringa di connessione allo slot corrente](deploy-staging-slots.md#which-settings-are-swapped).

Per modificare un'impostazione, fare clic sul pulsante **modifica** sul lato destro.

Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** indietro nella pagina di **configurazione** .

### <a name="edit-in-bulk"></a>Modifica in blocco

Per aggiungere o modificare in blocco le stringhe di connessione, fare clic sul pulsante **modifica avanzata** . Al termine, fare clic su **Aggiorna**. Non dimenticare di fare clic su **Salva** indietro nella pagina di **configurazione** .

La formattazione JSON delle stringhe di connessione è la seguente:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Configurare le impostazioni generali

Nel [Azure portal] cercare e selezionare **Servizi app**, quindi selezionare la propria app. Nel menu a sinistra dell'app selezionare **configurazione**  >  **Impostazioni generali**.

![Impostazioni generali](./media/configure-common/open-general.png)

Qui è possibile configurare alcune impostazioni comuni per l'app. Per alcune impostazioni è necessario [scalare fino a piani tariffari più elevati](manage-scale-up.md).

- **Impostazioni dello stack**: lo stack software per eseguire l'app, incluse le versioni del linguaggio e dell'SDK. Per le app Linux e le app contenitore personalizzate, è anche possibile impostare un comando o un file di avvio facoltativo.
- **Impostazioni piattaforma**: consente di configurare le impostazioni per la piattaforma di hosting, tra cui:
    - **Bit**: 32 bit o 64 bit.
    - **Protocollo WebSocket**: per [ASP.NET signalr] o [Socket.io](https://socket.io/), ad esempio.
    - **Always on**: mantiene l'app caricata anche se non è presente traffico. È obbligatorio per i processi Web continui o per i processi Web che vengono attivati usando un'espressione CRON.
      > [!NOTE]
      > Con la funzionalità Always On, il servizio di bilanciamento del carico front-end invia una richiesta alla radice dell'applicazione. Non è possibile configurare l'endpoint dell'applicazione del servizio app.
    - **Versione pipeline gestita**: [modalità pipeline]IIS. Impostarla su **classica** se si dispone di un'app legacy che richiede una versione precedente di IIS.
    - **Versione http**: impostare su **2,0** per abilitare il supporto per il protocollo [https/2](https://wikipedia.org/wiki/HTTP/2) .
    > [!NOTE]
    > I browser più recenti supportano il protocollo HTTP/2 solo su TLS, mentre il traffico non crittografato continua a usare il protocollo HTTP/1.1. Per assicurarsi che i browser client si connettano all'app con HTTP/2, proteggere il nome DNS personalizzato. Per altre informazioni, vedere [proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel servizio app Azure](configure-ssl-bindings.md).
    - **Affinità arr**: in una distribuzione a più istanze, verificare che il client venga indirizzato alla stessa istanza per il ciclo di vita della sessione. È possibile impostare questa opzione su **off** per le applicazioni senza stato.
- **Debug**: abilitare il debug remoto per le app [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)o [Node.js](configure-language-nodejs.md#debug-remotely) . Questa opzione si disattiva automaticamente dopo 48 ore.
- **Certificati client in ingresso**: richiedere certificati client nell' [autenticazione reciproca](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Configurare i documenti predefiniti

Questa impostazione è solo per le app di Windows.

Nel [Azure portal] cercare e selezionare **Servizi app**, quindi selezionare la propria app. Nel menu a sinistra dell'app selezionare **Configuration**  >  **Default Documents**.

![Documenti predefiniti](./media/configure-common/open-documents.png)

Il documento predefinito è la pagina Web visualizzata nell'URL radice di un sito Web. Viene utilizzato il primo file corrispondente dell'elenco. Per aggiungere un nuovo documento predefinito, fare clic su **nuovo documento**. Non dimenticare di fare clic su **Salva**.

Se l'app usa moduli che vengono indirizzati in base all'URL anziché al contenuto statico, non sono necessari documenti predefiniti.

## <a name="configure-path-mappings"></a>Configurare i mapping dei percorsi

Nel [Azure portal] cercare e selezionare **Servizi app**, quindi selezionare la propria app. Nel menu a sinistra dell'app selezionare mapping dei percorsi di **configurazione**  >  **Path mappings**.

![Mapping dei percorsi](./media/configure-common/open-path.png)

La pagina **mapping percorsi** Mostra diversi elementi basati sul tipo di sistema operativo.

### <a name="windows-apps-uncontainerized"></a>App di Windows (non in contenitori)

Per le app di Windows, è possibile personalizzare i mapping del gestore IIS e le applicazioni e le directory virtuali.

I mapping dei gestori consentono di aggiungere processori script personalizzati per gestire le richieste di estensioni di file specifiche. Per aggiungere un gestore personalizzato, fare clic su **nuovo gestore**. Configurare il gestore come segue:

- **Estensione**. Estensione di file che si desidera gestire, ad esempio * \* . php* o *handler. fcgi*.
- **Processore di script**. Percorso assoluto del processore di script. Le richieste ai file che corrispondono all'estensione di file vengono elaborate dal processore di script. Utilizzare il percorso `D:\home\site\wwwroot` per fare riferimento alla directory radice della propria app.
- **Argomenti**. Argomenti della riga di comando facoltativi per il processore di script.

Ogni app ha il percorso radice predefinito ( `/` ) mappato a `D:\home\site\wwwroot` , in cui il codice viene distribuito per impostazione predefinita. Se la radice dell'app si trova in una cartella diversa o se il repository ha più di un'applicazione, è possibile modificare o aggiungere qui le applicazioni e le directory virtuali. Fare clic su **nuova applicazione virtuale o directory**.

Per configurare le applicazioni e le directory virtuali, specificare ogni directory virtuale e il percorso fisico corrispondente rispetto alla radice del sito Web ( `D:\home` ). È facoltativamente possibile selezionare la casella di controllo **Applicazione** in modo da contrassegnare una directory virtuale come applicazione.

### <a name="containerized-apps"></a>App in contenitori

È possibile [aggiungere spazio di archiviazione personalizzato per l'app in contenitori](configure-connect-to-azure-storage.md). Le app in contenitori includono tutte le app Linux e anche i contenitori personalizzati Windows e Linux in esecuzione nel servizio app. Fare clic su **nuovo archiviazione di Azure montare** e configurare l'archiviazione personalizzata come indicato di seguito:

- **Nome**: nome visualizzato.
- **Opzioni di configurazione**: **Basic** o **Advanced**.
- **Account di archiviazione**: l'account di archiviazione con il contenitore desiderato.
- **Tipo di archiviazione**: **BLOB di Azure** o **file di Azure**.
  > [!NOTE]
  > Le app contenitore di Windows supportano solo File di Azure.
- **Contenitore di archiviazione**: per la configurazione di base, il contenitore desiderato.
- **Nome condivisione**: per la configurazione avanzata, il nome della condivisione file.
- **Chiave di accesso**: per la configurazione avanzata, il tasto di accesso.
- **Percorso di montaggio**: il percorso assoluto nel contenitore per montare l'archiviazione personalizzata.

Per altre informazioni, vedere [accedere ad archiviazione di Azure come una condivisione di rete da un contenitore nel servizio app](configure-connect-to-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Configurare le impostazioni dello stack di lingue

Per le app Linux, vedere:

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Configurare contenitori personalizzati

Vedere [configurare un contenitore Linux personalizzato per il servizio app Azure](configure-custom-container.md)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare un nome di dominio personalizzato nel servizio app di Azure]
- [Configurare gli ambienti di gestione temporanea in Servizio app di Azure]
- [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md)
- [Abilitare i log di diagnostica](troubleshoot-diagnostic-logs.md)
- [Aumentare le prestazioni di un'app nel Servizio app di Azure]
- [Informazioni di base sul monitoraggio nel Servizio app di Azure]
- [Modificare le impostazioni di applicationHost.config con applicationHost. XDT](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portale di Azure]: https://portal.azure.com/
[Configurare un nome di dominio personalizzato nel servizio app di Azure]: ./app-service-web-tutorial-custom-domain.md
[Configurare gli ambienti di gestione temporanea in Servizio app di Azure]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Informazioni di base sul monitoraggio nel Servizio app di Azure]: ./web-sites-monitor.md
[modalità pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Aumentare le prestazioni di un'app nel Servizio app di Azure]: ./manage-scale-up.md