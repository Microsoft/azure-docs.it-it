---
title: Configurare e gestire Azure Notebooks anteprima
description: Informazioni su come gestire i metadati del progetto, i file di progetto, l'ambiente del progetto e i passaggi di configurazione tramite l'interfaccia utente di Azure Notebooks e l'accesso diretto al terminale.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: devx-track-python
ms.openlocfilehash: fb106132cf0dfb14dc41c1d4b480fd3a1d7fd7fe
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093537"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a>Gestione e configurazione di progetti in Azure Notebooks anteprima

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Un progetto in Azure Notebooks Preview è essenzialmente una configurazione della macchina virtuale Linux sottostante in cui sono in esecuzione i notebook di Jupyter, insieme a una cartella di file e a metadati descrittivi. 

Il dashboard del progetto in Azure Notebooks consente di gestire i file ed eventualmente configurare le caratteristiche del progetto:

- Il livello di calcolo in cui viene eseguito il progetto, che può essere il livello gratuito o una macchina virtuale di Azure.
- I metadati del progetto, che includono un nome, una descrizione, un identificatore usato quando si condivide il progetto e una voce che indica se il progetto è pubblico o privato.
- Il notebook, i dati e altri file del progetto, che vengono gestiti come qualsiasi altro file system.
- Un ambiente di un progetto, gestito tramite script di avvio o direttamente con il terminale.
- Log, a cui si accede tramite il terminale.

> [!Note]
> Le funzionalità di gestione e configurazione descritte di seguito sono disponibili solo per il proprietario del progetto che ha creato il progetto iniziale. È possibile, tuttavia, clonare il progetto nel proprio account; in tal caso si diventa il proprietario e si può configurare il progetto in base alle proprie esigenze.

Azure Notebooks avvia la macchina virtuale sottostante ogni volta che viene eseguito un notebook o un altro file. Il server salva automaticamente i file e si arresta dopo 60 minuti di inattività. È comunque possibile arrestare il server in qualsiasi momento con il comando **Shutdown** (tasto di scelta rapida: h).

## <a name="compute-tier"></a>Livello di calcolo

Per impostazione predefinita, i progetti vengono eseguiti nel livello di **calcolo gratuito** , limitato a 4GB di memoria e 1 GB di dati per evitare abusi. È possibile ignorare queste limitazioni e aumentare la potenza di calcolo usando una macchina virtuale diversa di cui è stato effettuato il provisioning in una sottoscrizione di Azure. Per altre informazioni, vedere [come usare le macchine virtuali di Data Science](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Modificare i metadati del progetto

Nel dashboard del progetto selezionare **Project Settings** (Impostazioni progetto) e quindi selezionare la scheda **Information** (Informazioni), che contiene i metadati del progetto come descritto nella tabella seguente. È possibile modificare i metadati del progetto in qualsiasi momento.

| Impostazione | Descrizione |
| --- | --- |
| Project name (Nome progetto) | Nome descrittivo del progetto usato da Azure Notebooks per scopi di visualizzazione. Ad esempio: "Hello World in Python". |
| ID progetto | Identificatore personalizzato che diventa parte dell'URL usato per condividere un progetto. Questo ID può utilizzare solo lettere, numeri e trattini, è limitato a 30 caratteri e non può essere un [ID di progetto riservato](create-clone-jupyter-notebooks.md#reserved-project-ids). Se si hanno subbi sul tipo di ID da usare, in genere si sceglie la versione in caratteri minuscoli del nome del progetto convertendo gli spazi in segni meno, ad esempio "my-notebook-project" (troncato, se necessario per rispettare i limiti di lunghezza). |
| Public project (Progetto pubblico) | Se impostato, consente a chiunque disponga del collegamento di accedere al progetto. Quando si crea un progetto privato, deselezionare questa opzione. |
| Hide clones (Nascondi cloni) | Se impostata, gli altri utenti non possono vedere l'elenco dei cloni creati per il progetto. Questa impostazione è particolarmente utile nei progetti che vengono condivisi con molti utenti non appartenenti alla stessa organizzazione, ad esempio quando si usa un notebook per l'insegnamento in aula. |

> [!Important]
>
> Se si modifica l'ID del progetto, tutti i collegamenti al progetto condivisi in precedenza non saranno più validi.

## <a name="manage-project-files"></a>Gestire i file di progetto

Il dashboard del progetto illustra i contenuti del sistema di cartelle del progetto. È possibile usare vari comandi per gestire questi file.

### <a name="create-new-files-and-folders"></a>Creare nuovi file e cartelle

Il comando **+ New** (Nuovo) (tasto di scelta rapida: n) consente di creare nuovi file o cartelle. Quando si usa questo comando, selezionare prima il tipo di elemento da creare:

| Tipo di elemento | Descrizione | Comportamento del comando |
| --- | --- | --- |
| **Notebook** | Un notebook di Jupyter | Visualizza una finestra popup in cui devono essere specificati il nome file e la lingua del notebook. |
| **Cartella** | Una sottocartella | Crea un campo di modifica nell'elenco di file del progetto in cui è necessario specificare il nome della cartella. |
| **File vuoto** | Un file in cui è possibile archiviare qualsiasi contenuto, tra cui testo, dati e così via. | Crea un campo di modifica nell'elenco di file del progetto in cui è necessario specificare il nome del file. |
| **Markdown** | Un file markdown. | Crea un campo di modifica nell'elenco di file del progetto in cui è necessario specificare il nome del file. |

### <a name="upload-files"></a>Caricare i file

Il comando **upload** fornisce due opzioni per l'importazione di dati da altri percorsi: **da URL** e **dal computer**. Per altre informazioni, vedere [Usare file di dati in progetti di Azure Notebooks](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Selezionare i comandi specifici del file

Ogni elemento nell'elenco di file del progetto offre una serie di comandi tramite un menu di scelta rapida:

![Comandi in un menu di scelta rapida di un file](media/project-file-commands.png)

| Comando | Tasto di scelta rapida | Azione |
| --- | --- | --- |
| Esegui | r (o clic) | Esegue un file di notebook. Altri tipi di file sono aperti per la visualizzazione.  |
| Copy Link (Copia collegamento) | y | Copia un collegamento al file negli Appunti. |
| Run in Jupyter Lab (Esegui in JupyterLab) | j | Esegue un notebook in JupyterLab, un'interfaccia più orientata agli sviluppatori rispetto a quella normalmente associata a Jupyter. |
| Anteprima | p | Apre un'anteprima HTML del file; per i notebook, l'anteprima è un rendering di sola lettura del notebook. Per altre informazioni, vedere la sezione [Anteprima](#preview). |
| Edit file (Modifica file) | i | Apre il file per la modifica. |
| Download | d | Scarica un file con estensione zip contenente il file o il contenuto di una cartella. |
| Rinominare | a | Richiede un nuovo nome per il file o la cartella. |
| Elimina | x | Richiede una conferma e quindi rimuove definitivamente il file dal progetto. Non è possibile rimuovere un'eliminazione. |
| Sposta | m | Sposta un file in una cartella diversa dello stesso progetto. |

#### <a name="preview"></a>Anteprima

L'anteprima di un file o un notebook è una visualizzazione di sola lettura del relativo contenuto; l'esecuzione delle celle del notebook è disabilitata. L'anteprima viene illustrata a chiunque disponga di un collegamento al file o al notebook, ma non ha eseguito l'accesso ad Azure Notebooks. Dopo aver eseguito l'accesso, un utente può clonare il notebook sul proprio account oppure può scaricare il notebook nel computer locale.

La pagina di anteprima supporta vari comandi della barra degli strumenti con tasti di scelta rapida:

| Comando | Tasto di scelta rapida | Azione |
| --- | --- | --- |
| Condivisione | s | Consente di visualizzare la finestra popup di condivisione da cui è possibile ottenere un collegamento, condividere sui social media, ottenere HTML per l'incorporamento e inviare un messaggio di posta elettronica. |
| Clone | c  | Consente di clonare il notebook nel proprio account. |
| Esegui | r | Consente di eseguire il notebook, se si è autorizzati a eseguire questa operazione. |
| Download | d | Scarica una copia del notebook. |

## <a name="configure-the-project-environment"></a>Configurare l'ambiente del progetto

Esistono tre modi per configurare l'ambiente della macchina virtuale sottostante in cui vengono eseguiti i notebook:

- Includere uno script di inizializzazione unica
- Usare le impostazioni dell'ambiente del progetto per specificare i passaggi di configurazione
- Accedere alla macchina virtuale tramite un terminale.

Tutte le forme di configurazione del progetto vengono applicate ogni volta che viene avviata la macchina virtuale, interessando tutti i notebook all'interno del progetto.

### <a name="one-time-initialization-script"></a>Script di inizializzazione unica

La prima volta che Azure Notebooks crea un server per il progetto, Cerca un file nel progetto denominato *aznbsetup.sh*. Se il file è presente, Azure Notebooks lo esegue. L'output dello script viene archiviato nella cartella del progetto come *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Passaggi di configurazione dell'ambiente

È possibile usare le impostazioni dell'ambiente del progetto per creare i singoli passaggi della procedura di configurazione dell'ambiente.

Nel dashboard del progetto selezionare **Project Settings** (Impostazioni progetto) e quindi selezionare la scheda **Environment** (Ambiente) in cui è possibile aggiungere, rimuovere e modificare i passaggi di configurazione del progetto:

![Finestra popup delle impostazioni del progetto con la scheda Environment (Ambiente) selezionata](media/project-settings-environment-steps.png)

Per aggiungere un passaggio, selezionare prima **+ Add** (+ Aggiungi) e quindi scegliere un tipo di passaggio nell'elenco a discesa **Operation** (Operazione):

![Selettore dell'operazione per un nuovo passaggio di configurazione dell'ambiente](media/project-settings-environment-details.png)

Le informazioni che verranno visualizzate dipendono dal tipo di operazione scelto:

- **Requirements.txt**: nel secondo elenco a discesa selezionare un file di *requirements.txt* già presente nel progetto. Selezionare quindi una versione di Python dal terzo elenco a discesa visualizzato. Usando un file *requirements.txt*, Azure Notebooks esegue `pip install -r` con il file *requirements.txt* quando viene avviato un server notebook. Non è necessario installare esplicitamente i pacchetti dal notebook.

- **Script della shell**: nel secondo elenco a discesa selezionare uno script della shell bash nel progetto (in genere un file con estensione *sh* ) che contiene i comandi che si desidera eseguire per inizializzare l'ambiente.

- **Environment. yml**: nel secondo elenco a discesa selezionare un file *environments. yml* per i progetti python usando un ambiente conda.

   > [!WARNING]
   > Poiché si tratta di un servizio in anteprima in fase di sviluppo, esiste attualmente un problema noto per cui l'impostazione di `Environment.yml` non viene applicata al progetto come previsto. Il progetto e i notebook di Jupyter al suo interno non caricano attualmente il file dell'ambiente specificato.

Dopo aver aggiunto tutti i passaggi necessari, selezionare **Save** (Salva).

### <a name="use-the-terminal"></a>Usare il terminale

Nel dashboard del progetto, il comando **Terminal** consente di aprire un terminale Linux che offre l'accesso diretto al server. All'interno di terminale è possibile scaricare dati, modificare o gestire file, controllare processi e persino usare strumenti come vi e nano.

> [!Note]
> Se si dispone di script di avvio nell'ambiente del progetto, quando si apre il terminale potrebbe essere visualizzato un messaggio in cui si specifica che l'installazione è ancora in corso.

Nel terminale è possibile eseguire tutti i comandi Linux standard. È anche possibile usare `ls` nella home directory per visualizzare i diversi ambienti presenti nella macchina virtuale, ad esempio *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp* e *R*, oltre a una cartella *project* contenente il progetto:

![Terminale del progetto in Azure Notebooks](media/project-terminal.png)

Per intervenire su un ambiente specifico, modificare prima le directory presenti nella cartella dell'ambiente.

Per gli ambienti Python, è possibile trovare `pip` e `conda` nella cartella *bin* di ogni ambiente. È anche possibile usare alias predefiniti per gli ambienti:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Le modifiche apportate al server vengono applicate solo alla sessione corrente, ad eccezione dei file e delle cartelle create direttamente nella cartella *project*. La modifica di un file nella cartella del progetto, ad esempio, è persistente tra le sessioni, ma non sui pacchetti con `pip install`.

> [!Note]
> Se si usa `python` o `python3`, si richiamano le versioni di Python installate da sistema, che non vengono usate per i notebook. Se non si dispone neanche delle autorizzazioni per operazioni come `pip install`, assicurarsi di usare gli alias specifici della versione.

## <a name="access-notebook-logs"></a>Accedere ai log dei notebook

Se durante l'esecuzione di un notebook si verifica un problema, l'output di Jupyter viene archiviato in una cartella denominata *.nb.log*. È possibile accedere a questi log tramite il comando **Terminal** o il dashboard del progetto.

Se si esegue Jupyter in locale, è possibile che sia stato avviato da una finestra del terminale. Nella finestra del terminale viene visualizzato l'output, ad esempio lo stato del kernel.

Per visualizzare i log, usare il comando seguente nel terminale:

```bash
cat .nb.log
```

È possibile usare il comando anche da una cella di codice in un notebook Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Passaggi successivi

- [Procedura: utilizzare file di dati del progetto](work-with-project-data-files.md)
- [Accedere ai dati cloud in un notebook](access-data-resources-jupyter-notebooks.md)
