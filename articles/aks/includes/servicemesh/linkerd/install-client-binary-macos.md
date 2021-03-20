---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593726"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Scaricare e installare il file binario client Linkerd di Linkerd

In una shell basata su bash in MacOS usare `curl` per scaricare la versione di Linkerd come indicato di seguito:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Il `linkerd` file binario client viene eseguito nel computer client e consente di interagire con la mesh del servizio Linkerd. Usare i comandi seguenti per installare il `linkerd` file binario del client Linkerd in una shell basata su bash in MacOS. Questi comandi copiano il file binario del client `linkerd` nel percorso del programma dell'utente standard in `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se si vuole completare la riga di comando per il `linkerd` file binario client Linkerd, è necessario configurarlo come segue:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
