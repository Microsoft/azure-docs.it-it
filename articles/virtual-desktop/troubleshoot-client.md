---
title: Risolvere i problemi relativi a Desktop remoto desktop virtuale Windows client-Azure
description: Come risolvere i problemi quando si configurano le connessioni client in un ambiente tenant di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e42a4d738d375b4bceeb32f6159b4476843cf281
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612657"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Risolvere i problemi relativi al client di Desktop remoto

Questo articolo descrive i problemi comuni con il client di Desktop remoto e come risolverli.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Desktop remoto client per Windows 7 o Windows 10 smette di rispondere o non può essere aperto

A partire dalla versione 1.2.790, è possibile reimpostare i dati utente dalla pagina about o usando un comando.

Usare il comando seguente per rimuovere i dati utente, ripristinare le impostazioni predefinite e annullare la sottoscrizione da tutte le aree di lavoro.

```cmd
msrdcw.exe /reset [/f]
```

Se si usa una versione precedente del client di Desktop remoto, è consigliabile disinstallare e reinstallare il client.

## <a name="web-client-wont-open"></a>Il client Web non verrà aperto

Per prima cosa, testare la connessione Internet aprendo un altro sito Web nel browser; ad esempio, [www.Bing.com](https://www.bing.com).

Usare **nslookup** per confermare che DNS può risolvere il nome di dominio completo:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Provare a connettersi con un altro client, ad esempio Desktop remoto client per Windows 7 o Windows 10, e verificare se è possibile aprire il client Web.

### <a name="opening-another-site-fails"></a>L'apertura di un altro sito non riesce

Ciò è in genere causato da problemi di connessione di rete o da un'interruzione di rete. È consigliabile contattare il supporto di rete.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup: Impossibile risolvere il nome

Ciò è in genere causato da problemi di connessione di rete o da un'interruzione di rete. È consigliabile contattare il supporto di rete.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Il client non è in grado di connettersi, ma altri client nella rete possono connettersi

Se il browser inizia a funzionare o smette di funzionare mentre si usa il client Web, seguire queste istruzioni per risolvere i problemi:

1. Riavvia il browser.
2. Cancella i cookie del browser. Vedere [come eliminare i file dei cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Cancellare la cache del browser. Vedere [Cancella cache del browser per il browser](https://binged.it/2RKyfdU).
4. Aprire il browser in modalità privata.

## <a name="web-client-stops-responding-or-disconnects"></a>Il client Web smette di rispondere o si disconnette

Provare a connettersi usando un altro browser o client.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Anche altri browser e client malfunzionanti o non vengono aperti

Se i problemi continuano anche dopo aver cambiato i browser, il problema potrebbe non essere quello del browser, ma con la rete. È consigliabile contattare il supporto di rete.

## <a name="web-client-keeps-prompting-for-credentials"></a>Il client Web continua a richiedere le credenziali

Se il client Web continua a richiedere le credenziali, seguire queste istruzioni:

1. Verificare che l'URL del client Web sia corretto.
2. Verificare che le credenziali utilizzate siano per l'ambiente di desktop virtuale Windows associato all'URL.
3. Cancella i cookie del browser. Per ulteriori informazioni, vedere [come eliminare i file dei cookie in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Cancellare la cache del browser. Per altri dettagli, vedere [deselezionare la cache del browser per il browser](https://binged.it/2RKyfdU).
5. Aprire il browser in modalità privata.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un ambiente desktop virtuale Windows e di un pool host in un ambiente desktop virtuale Windows, vedere la pagina relativa alla [creazione di ambienti e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per un'esercitazione per la risoluzione dei problemi, vedere [esercitazione: risolvere i problemi relativi alle distribuzioni di modelli gestione risorse](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).