---
title: Localizzazione Azure Media Player
description: Supporto di più lingue per gli utenti di impostazioni locali diverse dall'inglese.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: e78ff237fb488a995d9161814fe61590fb1c6d5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449841"
---
# <a name="azure-media-player-localization"></a>Localizzazione Azure Media Player #

Il supporto di più lingue consente agli utenti di impostazioni locali diverse dall'inglese di interagire in modo nativo con il lettore. Azure Media Player creerà un'istanza con un dizionario globale di linguaggi, che consente di localizzare i messaggi di errore in base alla lingua della pagina. Uno sviluppatore può anche creare un'istanza del lettore con un linguaggio di set forzato. La lingua predefinita è l'inglese (en).

> [!NOTE]
> Questa funzionalità è ancora in fase di test e, di conseguenza, è soggetta a bug.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player supporta attualmente le lingue seguenti con i codici di lingua corrispondenti:

| Linguaggio            | Codice | Linguaggio                | Codice   | Linguaggio                | Codice         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Italian {default}   | en   | Croato                | hr     | Rumeno                | ro           |
| Arabo              | ar   | Ungherese               | hu     | Slovacco                  | sk           |
| Bulgaro           | bg   | Indonesiano              | id     | Sloveno                 | sl           |
| Catalano             | ca   | Islandese               | is     | Serbo - Alfabeto cirillico      | Sr-Cyrl-CS   |
| Ceco               | cs   | Italiano                 | it     | Serbo - Alfabeto latino         | sr-latn-rs   |
| Danese              | da   | Giapponese                | ja     | Russo                 | ru           |
| Tedesco              | de   | Kazako                  | kk     | Svedese                 | sv           |
| Greco               | el   | Coreano                  | ko     | Thai                    | th           |
| Spagnolo             | es   | Lituano              | lt     | Tagalog                 | TL           |
| Estone            | et   | Lettone                 | lv     | Turco                 | tr           |
| Basco              | eu   | Ringgit               | ms     | Ucraino               | uk           |
| Farsi               | fa   | Norvegese-BokmÃ ¥ l     | nb     | Urdu                    | ur           |
| Finlandese             | fi   | Olandese                   | nl     | Vietnamita              | vi           |
| Francese              | fr   | Norvegese-Nynorsk     | nn     | Cinese semplificato    | zh-hans      |
| Galiziano            | gl   | Polacco                  | pl     | Cinese tradizionale   | zh-hant      |
| Ebraico              | he   | Portoghese (Brasile)     | pt-br  |                         |              |
| Hindi               | hi   | Portoghese (Portogallo)   | pt-pt  |                         |              |


> [!NOTE]
> Se non si desidera che si verifichi alcuna localizzazione, è necessario forzare la lingua in inglese

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)
