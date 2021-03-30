---
title: Risoluzione dei problemi-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Risoluzione dei problemi in griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171347"
---
# <a name="common-issues"></a>Problemi comuni

Se si verificano problemi durante l'uso di griglia di eventi di Azure in IoT Edge nell'ambiente in uso, usare questo articolo come guida per la risoluzione dei problemi e la risoluzione.

## <a name="view-event-grid-module-logs"></a>Visualizzare i log dei moduli di griglia di eventi

Per risolvere il problemi, potrebbe essere necessario accedere ai log del modulo di griglia di eventi. A tale scopo, nella macchina virtuale in cui è distribuito il modulo eseguire il comando seguente:

In Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

In Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Non è possibile effettuare richieste HTTPS

* Assicurarsi prima di tutto che il modulo griglia di eventi abbia in **ingresso: serverAuth: tlsPolicy** impostato su **strict** o **Enabled**.

* Se le comunicazioni da modulo a modulo, assicurarsi di effettuare la chiamata sulla porta **4438** e il nome del modulo corrisponde a quello distribuito. 

  Ad esempio, se il modulo griglia di eventi è stato distribuito con il nome **eventgridmodule** , l'URL deve essere **https://eventgridmodule:4438** . Verificare che il numero di porta e di maiuscole e minuscole sia corretto.
    
* Se è un modulo diverso da Internet, assicurarsi che la porta della griglia di eventi sia mappata al computer host durante la distribuzione, ad esempio

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Non è possibile effettuare richieste HTTP

* Assicurarsi prima di tutto che il modulo griglia di eventi disponga di in **ingresso: serverAuth: tlsPolicy** impostato su **abilitato** o **disabilitato**.

* Se le comunicazioni da modulo a modulo, assicurarsi di effettuare la chiamata sulla porta **5888** e il nome del modulo corrisponde a quello distribuito. 

  Ad esempio, se il modulo griglia di eventi è stato distribuito con il nome **eventgridmodule** , l'URL deve essere **http://eventgridmodule:5888** . Verificare che il numero di porta e di maiuscole e minuscole sia corretto.
    
* Se è un modulo diverso da Internet, assicurarsi che la porta della griglia di eventi sia mappata al computer host durante la distribuzione, ad esempio

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>La catena di certificati è stata emessa da un'autorità non attendibile

Per impostazione predefinita, il modulo di griglia di eventi è configurato per autenticare i client con il certificato emesso dal daemon di sicurezza IoT Edge. Verificare che il client stia presentando un certificato radicato in questa catena.

La classe **IoTSecurity** in [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) Mostra come recuperare i certificati da IOT Edge daemon di sicurezza e usarli per configurare le chiamate in uscita.

Se si tratta di un ambiente non di produzione, è possibile disattivare l'autenticazione client. Per informazioni dettagliate su come eseguire questa operazione, vedere [sicurezza e autenticazione](security-authentication.md) .

## <a name="debug-events-not-received-by-subscriber"></a>Eventi di debug non ricevuti dal Sottoscrittore

I motivi tipici sono:

* L'evento non è stato inviato correttamente. È stato ricevuto un StatusCode HTTP di 200 (OK) per l'invio di un evento al modulo di griglia di eventi.

* Controllare la sottoscrizione dell'evento per verificare:
    * L'URL dell'endpoint è valido
    * Eventuali filtri della sottoscrizione non causano l'eliminazione dell'evento.

* Verificare che il modulo Sottoscrittore sia in esecuzione

* Accedere alla macchina virtuale in cui è distribuito il modulo di griglia di eventi e visualizzare i relativi log.

* Attivare la registrazione per recapito impostando **Broker: logDeliverySuccess = true** e ridistribuendo il modulo griglia di eventi e ritentando la richiesta. L'attivazione della registrazione per ogni recapito può compromettere la velocità effettiva e la latenza, quindi, al termine del debug, è consigliabile tornare a **Service Broker: logDeliverySuccess = false**  e ridistribuire il modulo griglia di eventi.

* Attivare la metrica impostando **metrica: reportertype = console** e Ridistribuisci modulo griglia di eventi. Qualsiasi operazione dopo questa operazione comporterà la registrazione delle metriche nella console del modulo di griglia di eventi, che può essere usato per eseguire il debug. Si consiglia di attivare le metriche solo per il debug e, una volta completate, per disattivarla impostando **metrica: reportertype = None** e ridistribuendo il modulo griglia di eventi.

## <a name="next-steps"></a>Passaggi successivi

Segnalare eventuali problemi, suggerimenti con l'uso di griglia di eventi su IoT Edge all'indirizzo [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .