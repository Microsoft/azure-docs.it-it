---
title: Sviluppare ed eseguire funzioni di Azure in locale
description: Informazioni su come codificare e testare le funzioni di Azure nel computer locale prima di eseguirle in funzioni di Azure.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c37d51abcc8d612b777b845515cf07666369d4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168135"
---
# <a name="code-and-test-azure-functions-locally"></a>Scrivere codici per Funzioni di Azure e testarle in locale

Benché sia possibile sviluppare e testare Funzioni di Azure nel [portale di Azure], molti sviluppatori preferiscono un'esperienza di sviluppo locale. Funzioni di Azure semplificano l'uso dell'editor di codice e degli strumenti di sviluppo locale preferiti per creare e testare le funzioni nel computer locale. Le funzioni locali possono connettersi ai servizi di Azure attivi ed è possibile eseguirne il debug nel computer locale usando il runtime completo di Funzioni di Azure.

## <a name="local-development-environments"></a>Ambienti di sviluppo locale

Il modo in cui si sviluppano le funzioni nel computer locale dipende dal [linguaggio](supported-languages.md) e dalle preferenze relative agli strumenti. Gli ambienti nella tabella seguente supportano lo sviluppo locale:

|Ambiente                              |Linguaggi         |Descrizione|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (libreria di classi)](functions-dotnet-class-library.md), [script c# (. CSX)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](./create-first-function-vs-code-powershell.md), [Python](functions-reference-python.md) | L'[estensione di Funzioni di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) aggiunge il supporto di Funzioni di Azure a Visual Studio Code. Richiede gli strumenti di base. Supporta lo sviluppo in Linux, MacOS e Windows quando si usa la versione 2.x degli strumenti di base. Per altre informazioni, vedere [Creare la prima funzione con Visual Studio Code](./create-first-function-vs-code-csharp.md). |
| [Prompt dei comandi o terminal](functions-run-local.md) | [C# (libreria di classi)](functions-dotnet-class-library.md), [script c# (. CSX)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] fornisce il runtime di base e i modelli per la creazione di funzioni che consentono lo sviluppo locale. La versione 2.x supporta lo sviluppo in Linux, MacOS e Windows. Tutti gli ambienti si basano su Strumenti di base per il runtime Funzioni di Azure locale. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (libreria di classi)](functions-dotnet-class-library.md) | Gli strumenti di funzioni di Azure sono inclusi nel carico di lavoro di **sviluppo di Azure** di [Visual Studio 2019](https://www.visualstudio.com/vs/) e versioni successive. Consente di compilare le funzioni in una libreria di classi e di pubblicare il file con estensione dll in Azure. Include gli strumenti di base per il test locale. Per altre informazioni, vedere [Sviluppare Funzioni di Azure con Visual Studio](functions-develop-vs.md). |
| [Maven](./create-first-function-cli-java.md) (varie) | [Java](functions-reference-java.md) | Si integra con gli strumenti di base per consentire lo sviluppo di funzioni Java. La versione 2.x supporta lo sviluppo in Linux, MacOS e Windows. Per altre informazioni, vedere [Creare la prima funzione con Java e Maven](./create-first-function-cli-java.md). Supporta inoltre lo sviluppo con [Eclipse](functions-create-maven-eclipse.md) e [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Ognuno di questi ambienti di sviluppo locale consente di creare progetti di app delle funzioni e usare i modelli di Funzioni di Azure predefiniti per creare nuove funzioni. Ognuno di essi usa gli strumenti di base per consentire di testare ed eseguire il debug delle funzioni con il runtime reale di Funzioni di Azure nel proprio computer esattamente come qualsiasi altra app. È anche possibile pubblicare il progetto di app per le funzioni da uno di questi ambienti ad Azure.

## <a name="next-steps"></a>Passaggi successivi

+ Per altre informazioni sullo sviluppo locale di funzioni C# compilate con Visual Studio 2019, vedere [sviluppare funzioni di Azure con Visual Studio](functions-develop-vs.md).
+ Per altre informazioni sullo sviluppo locale di funzioni con VS Code in un computer Mac, Linux o Windows, vedere [distribuire funzioni di Azure da vs code](/azure/developer/javascript/tutorial-vscode-serverless-node-01).
+ Per altre informazioni sullo sviluppo di funzioni al prompt dei comandi o terminal, vedere la pagina relativa agli [strumenti di base per Funzioni di Azure](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows