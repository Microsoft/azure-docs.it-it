---
title: Elenco di controllo pre-distribuzione per la distribuzione di Azure Stack dispositivo GPU Pro Edge | Microsoft Docs
description: Questo articolo descrive le informazioni che è possibile raccogliere prima di distribuire il dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2021
ms.author: alkohli
ms.openlocfilehash: 13de2b024bf4541c6234dd6bfba601597de59434
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716228"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Elenco di controllo della distribuzione per il dispositivo GPU Pro Azure Stack Edge  

Questo articolo descrive le informazioni che possono essere raccolte in anticipo rispetto alla distribuzione effettiva del dispositivo Azure Stack Edge Pro. 

Usare l'elenco di controllo seguente per assicurarsi di avere queste informazioni dopo aver effettuato un ordine per un dispositivo Azure Stack Edge Pro e prima di aver ricevuto il dispositivo. 

## <a name="deployment-checklist"></a>Elenco di controllo distribuzione 

| Fase                             | Parametro                                                                                                                                                                                                                           | Dettagli                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gestione dei dispositivi               | <li>Sottoscrizione di Azure</li><li>Provider di risorse registrati</li><li>Account di archiviazione di Azure</li>|<li>Abilitata per l'accesso Pro/Data Box Gateway, proprietario o collaboratore Azure Stack Edge.</li><li>In portale di Azure passare a **Home > sottoscrizioni > i provider di risorse > sottoscrizione**. Cercare `Microsoft.DataBoxEdge` e registrare. Ripetere l'installazione per `Microsoft.Devices` se si distribuiscono carichi di lavoro Internet.</li><li>Sono necessarie le credenziali di accesso</li> |
| Installazione dispositivi               | Cavi di alimentazione nel pacchetto. <br>Per Microsoft, viene spedito un cavo SVE 18/3 valutato per 125 V e 15 amp con un connettore NEMA da 5 15P a C13 (input in output). | Per ulteriori informazioni, vedere l'elenco dei [cavi di alimentazione supportati per paese](azure-stack-edge-technical-specifications-power-cords-regional.md)  |
|                                   | <li>Almeno 1 cavo di rete RJ-45 da 1 GbE per la porta 1  </li><li> Per la porta 3, la porta 4, la porta 5 o la porta 6, almeno 1 X 25-GbE. cavo di rame</li>| Il cliente deve acquistare questi cavi.<br>Per un elenco completo dei cavi di rete supportati, dei commutatori e dei ricetrasmettitori per le schede di rete del dispositivo, vedere la pagina relativa alla [matrice di interoperabilità Cavium FastlinQ serie 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e ai [prodotti compatibili con la scheda di rete Mellanox Dual Port 25](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Connessione del dispositivo per la prima volta      | <li>Portatile le cui impostazioni IPv4 possono essere modificate. Questo portatile si connette alla porta 1 tramite uno switch o un adattatore da USB a Ethernet.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Accesso al dispositivo                       | Password amministratore del dispositivo, da 8 a 16 caratteri, inclusi tre dei tipi di carattere seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri speciali.                                            | La password predefinita è *Password1*, che scade al primo accesso.                                                     |
| Impostazioni di rete                  | Il dispositivo è dotato di porte di rete da 2 x 1 GbE, 4 x 25 GbE. <li>La porta 1 viene utilizzata per configurare solo le impostazioni di gestione. Una o più porte dati possono essere connesse e configurate. </li><li> Almeno un'interfaccia di rete dati da una porta 2 alla 6 deve essere connessa a Internet (con connettività ad Azure).</li><li> Sono supportate la configurazione DHCP e IPv4 statica. | Per la configurazione IPv4 statica sono necessari IP, server DNS e gateway predefinito.   |
| Impostazioni di rete di calcolo     | <li>Sono necessari 2 IP disponibili, statici, contigui per i nodi Kubernetes e 1 IP statico per il servizio IoT Edge.</li><li>Richiedere un indirizzo IP aggiuntivo per ogni servizio o modulo aggiuntivo che verrà distribuito.</li>| È supportata solo la configurazione IPv4 statica.|
| Opzionale Impostazioni proxy Web     | <li>IP/FQDN server proxy Web, porta </li><li>Nome utente proxy Web, password</li> |  |
| Impostazioni del firewall e della porta        | Se si usa il firewall, assicurarsi che siano consentiti i [modelli e le porte degli URL elencati](azure-stack-edge-system-requirements.md#networking-port-requirements) per gli IP del dispositivo. |  |
| Consigliabile Impostazioni temporali       | Configurare il fuso orario, il server NTP primario, il server NTP secondario. | Configurare il server NTP primario e secondario nella rete locale.<br>Se il server locale non è disponibile, è possibile configurare i server NTP pubblici.                                                    |
| Opzionale Aggiornare le impostazioni del server | <li>Richiedi indirizzo IP del server di aggiornamento nella rete locale, percorso del server WSUS. </li> | Per impostazione predefinita, viene usato il server pubblico di Windows Update.|
| Impostazioni del dispositivo | <li>Nome di dominio completo (FQDN) del dispositivo </li><li>Dominio DNS</li> | |
| Opzionale Certificati  | Per testare i carichi di lavoro non di produzione, usare l' [opzione genera certificati](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) <br><br> Se si usano certificati personalizzati, incluse le catene di firma, [aggiungere i certificati](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) nel formato appropriato.| Configurare i certificati solo se si modifica il nome del dispositivo e/o il dominio DNS. |
| Activation  | Richiedere la chiave di attivazione dalla risorsa Pro/Data Box Gateway di Azure Stack Edge.    | Una volta generato, la chiave scade tra 3 giorni. |

<!--
| (Optional) MAC Address            | If MAC address needs to be on the allowed list, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Passaggi successivi

Preparare la distribuzione del [dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).
