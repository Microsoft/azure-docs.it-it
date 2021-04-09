---
title: Usare SSH con Hadoop - Azure HDInsight
description: È possibile accedere a HDInsight tramite Secure Shell (SSH). Questo documento fornisce informazioni sulla connessione a HDInsight usando i comandi SSH dei client Windows, Linux, UNIX o macOS.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: a8ad0c787235b94a600d14a3b447bbfee052321b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864857"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Connettersi a HDInsight (Apache Hadoop) con SSH

Questo articolo illustra come usare [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) per stabilire una connessione sicura a Apache Hadoop in Azure HDInsight. Per informazioni sulla connessione tramite una rete virtuale, vedere l' [architettura di rete virtuale di Azure HDInsight](./hdinsight-virtual-network-architecture.md). Vedere anche [pianificare una distribuzione di rete virtuale per i cluster HDInsight di Azure](./hdinsight-plan-virtual-network-deployment.md).

La tabella seguente contiene le informazioni relative a indirizzo e porta necessarie per la connessione a HDInsight tramite un client SSH:

| Indirizzo | Porta | Connessione a |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nodo head primario |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nodo head secondario |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | nodo perimetrale (ML Services in HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | nodo perimetrale (qualsiasi altro tipo di cluster, se è presente un nodo perimetrale) |

Sostituire `<clustername>` con il nome del cluster. Sostituire `<edgenodename>` con il nome del nodo perimetrale.

Se il cluster contiene un nodo perimetrale, è consigliabile __connettersi sempre al nodo perimetrale__ usando SSH. I nodi head ospitano servizi critici per l'integrità di Hadoop. Il nodo perimetrale esegue solo quanto inserito dall'utente nel nodo stesso. Per altre informazioni sull'uso dei nodi perimetrali, vedere l'articolo su come [usare nodi perimetrali in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> Alla prima connessione a HDInsight, è possibile che il client SSH visualizzi un avviso relativo all'impossibilità di confermare l'autenticità dell'host. Quando richiesto, selezionare 'sì' per aggiungere l'host all'elenco di server attendibili del client SSH.
>
> Se è già stata effettuata in precedenza una connessione a un server con lo stesso nome, è possibile che venga visualizzato un avviso relativo alla mancata corrispondenza della chiave host archiviata con la chiave host del server. Per informazioni su come rimuovere la voce esistente per il nome del server, vedere la documentazione per il client SSH.

## <a name="ssh-clients"></a>Client SSH

Nei sistemi Linux, Unix e macOS sono disponibili i comandi `ssh` e `scp`. Il client `ssh` viene in genere usato per creare una sessione della riga di comando remota con un sistema basato su Linux o Unix. Il client `scp` viene usato per la copia sicura dei file tra il client e il sistema remoto.

Microsoft Windows non installa alcun client SSH per impostazione predefinita. I client `ssh` e `scp` sono disponibili per Windows nei pacchetti seguenti:

* [Client OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse). Questo client è una funzionalità facoltativa introdotta in Windows 10 Fall Creators Update.

* [Bash in Ubuntu in Windows 10](/windows/wsl/about).

* [Azure cloud Shell](../cloud-shell/quickstart.md). Il Cloud Shell fornisce un ambiente bash nel browser.

* [Git](https://git-scm.com/).

Sono disponibili anche diversi client SSH grafici, ad esempio [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) e [MobaXterm](https://mobaxterm.mobatek.net/). Anche se questi client possono essere usati per connettersi a HDInsight, il processo di connessione è diverso rispetto a quello tramite l'utilità `ssh`. Per ulteriori informazioni, vedere la documentazione del client grafico in uso.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Autenticazione: chiavi SSH

Le chiavi SSH usano la [crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) per autenticare le sessioni SSH. Le chiavi SSH sono più sicure delle password e consentono di proteggere facilmente l'accesso al cluster Hadoop.

Se l'account SSH è protetto con una chiave, al momento della connessione il client deve fornire la chiave privata corrispondente:

* La maggior parte dei client può essere configurata per l'uso di una __chiave predefinita__. Negli ambienti Linux e Unix, ad esempio, il client `ssh` cerca una chiave privata in `~/.ssh/id_rsa`.

* È possibile specificare il __percorso di una chiave privata__. Con il client `ssh`, per specificare il percorso della chiave privata viene usato il parametro `-i`. Ad esempio: `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Se si hanno __più chiavi private__ per server diversi, considerare la possibilità di usare un'utilità come [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). L'utilità `ssh-agent` consente di selezionare automaticamente la chiave da usare quando si stabilisce una sessione SSH.

> [!IMPORTANT]  
> Se si protegge la chiave privata con una passphrase, è necessario immettere la passphrase quando si usa la chiave. Per praticità, le utilità come `ssh-agent` possono memorizzare la password nella cache.

### <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Usare il comando `ssh-keygen` per creare i file di chiave pubblica e privata. Il comando seguente genera una coppia di chiavi RSA a 2048 bit che può essere usata con HDInsight:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

Verranno richieste informazioni durante il processo di creazione della chiave. ad esempio la posizione di archiviazione delle chiavi o se deve essere usata una passphrase. Al termine del processo vengono creati due file: una chiave pubblica e una chiave privata.

* La __chiave pubblica__ viene usata per creare un cluster HDInsight e ha estensione `.pub`.

* La __chiave privata__ viene usata per autenticare il client nel cluster HDInsight.

> [!IMPORTANT]  
> È possibile proteggere le chiavi con una passphrase, che è di fatto una password per la chiave privata. Se qualcuno entra in possesso della chiave privata, per usarla deve avere la passphrase.

### <a name="create-hdinsight-using-the-public-key"></a>Creare cluster HDInsight con la chiave pubblica

| Metodo di creazione | Come usare la chiave pubblica |
| ------- | ------- |
| Portale di Azure | Deselezionare __Usa password di accesso del cluster per SSH__, quindi selezionare __chiave pubblica__ come tipo di autenticazione SSH. Selezionare infine il file di chiave pubblica oppure incollare il contenuto di testo del file nel campo __Chiave pubblica SSH__.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png" alt-text="Finestra di dialogo per la chiave pubblica SSH nella creazione di cluster HDInsight"::: |
| Azure PowerShell | Usare il `-SshPublicKey` parametro del cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) e passare il contenuto della chiave pubblica sotto forma di stringa.|
| Interfaccia della riga di comando di Azure | Usare il `--sshPublicKey` parametro del [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) comando e passare il contenuto della chiave pubblica sotto forma di stringa. |
| Modello di Resource Manager | Per un esempio dell'uso di SSH con un modello, vedere l'articolo su come [distribuire HDInsight in Linux con una chiave SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/). L'elemento `publicKeys` del file [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) viene usato per passare le chiavi ad Azure durante la creazione del cluster. |

## <a name="authentication-password"></a>Autenticazione: password

Gli account SSH possono essere protetti con una password. Quando si esegue la connessione a HDInsight tramite SSH, viene richiesto di immettere la password.

> [!WARNING]  
> Microsoft sconsiglia di usare l'autenticazione tramite password per SSH. Le password sono intuibili e vulnerabili ad attacchi di forza bruta. È consigliabile usare invece [chiavi SSH per l'autenticazione](#sshkey).

> [!IMPORTANT]  
> La password dell'account SSH scade 70 giorni dopo aver creato il cluster HDInsight. Se la password scade, è possibile modificarla tramite le informazioni contenute nel documento [Gestire HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Creare cluster HDInsight con una password

| Metodo di creazione | Come specificare la password |
| --------------- | ---------------- |
| Portale di Azure | Per impostazione predefinita, l'account utente SSH ha la stessa password dell'account di accesso del cluster. Per usare una password diversa, deselezionare __Usa password di accesso del cluster per SSH__ e quindi immettere la password nel campo __password ssh__ .</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png" alt-text="Finestra di dialogo per la password SSH nella creazione di cluster HDInsight":::|
| Azure PowerShell | Usare il `--SshCredential` parametro del cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) e passare un `PSCredential` oggetto che contiene il nome e la password dell'account utente SSH. |
| Interfaccia della riga di comando di Azure | Usare il `--ssh-password` parametro del [`az hdinsight create`](/cli/azure/hdinsight#az-hdinsight-create) comando e specificare il valore della password. |
| Modello di Resource Manager | Per un esempio dell'uso di una password con un modello, vedere l'articolo su come [distribuire HDInsight in Linux con una password SSH](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). L'elemento `linuxOperatingSystemProfile` del file [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) viene usato per passare il nome e la password dell'account SSH ad Azure durante la creazione del cluster.|

### <a name="change-the-ssh-password"></a>Modificare la password SSH

Per informazioni sulla modifica della password dell'account utente SSH, vedere la sezione __Modificare le password__ del documento relativo alla [gestione di HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="authentication-domain-joined-hdinsight"></a>HDInsight aggiunto al dominio di autenticazione

Se si usa un __cluster HDInsight aggiunto al dominio__, è necessario usare il `kinit` comando dopo la connessione con l'utente locale SSH. Questo comando richiede di specificare un utente di dominio e una password e autentica la sessione con il dominio di Azure Active Directory associato al cluster.

È anche possibile abilitare l'autenticazione Kerberos in ogni nodo aggiunto a un dominio (ad esempio, nodo Head, nodo perimetrale) a SSH usando l'account di dominio. A tale scopo, modificare il file di configurazione sshd:

```bash
sudo vi /etc/ssh/sshd_config
```

rimuovere il commento e modificare `KerberosAuthentication` su `yes`

```bash
sudo service sshd restart
```

Usare `klist` il comando per verificare se l'autenticazione Kerberos ha avuto esito positivo.

Per altre informazioni, vedere [Configurare i cluster HDInsight aggiunti al dominio](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-nodes"></a>Stabilire la connessione ai nodi

È possibile accedere ai nodi head e al nodo perimetrale (se ne esiste uno) tramite Internet sulle porte 22 e 23.

* Quando ci si connette ai __nodi head__, usare la porta __22__ per la connessione al nodo head primario e la porta __23__ per la connessione al nodo head secondario. Il nome di dominio completo da usare è `clustername-ssh.azurehdinsight.net`, dove `clustername` è il nome del cluster.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* Quando ci si connette al __nodo perimetrale__, usare la porta 22. Il nome di dominio completo è `edgenodename.clustername-ssh.azurehdinsight.net`, dove `edgenodename` è un nome specificato durante la creazione del nodo perimetrale. `clustername` è il nome del cluster.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> Gli esempi precedenti presuppongono l'uso dell'autenticazione della password o che l'autenticazione del certificato avvenga automaticamente. Se si usa una coppia di chiavi SSH per l'autenticazione e il certificato non viene usato automaticamente, usare il parametro `-i` per specificare la chiave privata. Ad esempio: `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Una volta stabilita la connessione, la richiesta cambia per indicare il nome utente SSH e il nodo a cui si è connessi. Quando ad esempio si è connessi al nodo head primario come `sshuser`, il prompt è `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Connettersi a nodi del ruolo di lavoro e Apache Zookeeper

I nodi del ruolo di lavoro e i nodi Zookeeper non sono direttamente accessibili da Internet. ma dai nodi head o perimetrali del cluster. Di seguito è riportata la procedura generale da seguire per connettersi ad altri nodi:

1. Usare SSH per connettersi a un nodo head o perimetrale:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. Dalla connessione SSH al nodo head o perimetrale, usare il comando `ssh` per connettersi a un nodo di lavoro nel cluster:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Per recuperare un elenco dei nomi di nodo, vedere il documento [Gestire i cluster HDInsight con l'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

Se l'account SSH viene protetto usando una __password__, immetterla quando ci si connette.

Se l'account SSH viene protetto usando __chiavi SSH__, verificare che l'inoltro SSH sia abilitato nel client.

> [!NOTE]  
> Un altro modo per accedere direttamente a tutti i nodi del cluster è installare HDInsight in una rete virtuale di Azure. È quindi possibile aggiungere il computer remoto alla stessa rete virtuale e accedere direttamente a tutti i nodi del cluster.
>
> Per altre informazioni, vedere [pianificare una rete virtuale per HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>Configurare l'inoltro dell'agente SSH

> [!IMPORTANT]  
> La procedura seguente presuppone l'uso di un sistema basato su Linux o UNIX e di Bash per Windows 10. Se la procedura non funziona per il sistema corrente, è consigliabile vedere la documentazione del client SSH.

1. Usando un editor di testo, aprire `~/.ssh/config`. Se questo file non esiste, è possibile crearlo immettendo `touch ~/.ssh/config` nella riga di comando.

2. Aggiungere il testo seguente al file `config`.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Sostituire le informazioni di __Host__ con l'indirizzo del nodo a cui ci si connette con SSH. L'esempio precedente usa il nodo perimetrale. Questa voce configura l'inoltro dell'agente SSH per il nodo specificato.

3. Testare l'inoltro dell'agente SSH tramite il comando seguente dal terminale:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Questo comando restituisce informazioni simili al testo seguente:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Se non viene restituito alcun risultato, `ssh-agent` non è in esecuzione. Per altre informazioni, vedere il contenuto relativo agli script di avvio agente in [Uso di ssh-agent con ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) o la documentazione del client SSH.

4. Dopo aver verificato che **ssh-agent** sia in esecuzione, usare il comando seguente per aggiungere la chiave privata SSH all'agente:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Se la chiave privata viene archiviata in un altro file, sostituire `~/.ssh/id_rsa` con il percorso del file.

5. Connettersi ai nodi head o al nodo perimetrale del cluster con SSH. Usare quindi il comando SSH per connettersi a un nodo del ruolo di lavoro o Zookeeper. La connessione viene stabilita usando la chiave inoltrata.

## <a name="next-steps"></a>Passaggi successivi

* [Usare il tunneling SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Usare nodi perimetrali in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [Usare SCP con HDInsight](./use-scp.md)