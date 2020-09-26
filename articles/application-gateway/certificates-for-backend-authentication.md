---
title: Certificati necessari per consentire i server back-end
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce esempi di come un certificato TLS/SSL può essere convertito in un certificato di autenticazione e in un certificato radice attendibile necessario per consentire le istanze back-end in applicazione Azure gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/17/2020
ms.author: absha
ms.openlocfilehash: 69d388b12e564b307cd117c3a86ae960dabaa937
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362713"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Creare certificati per consentire il back-end con applicazione Azure gateway

Per eseguire TLS end-to-end, il gateway applicazione richiede che le istanze back-end siano consentite caricando i certificati radice attendibili e di autenticazione. Per lo SKU V1 sono necessari i certificati di autenticazione, ma per i certificati radice attendibili dello SKU V2 sono necessari per consentire i certificati.

In questo articolo vengono illustrate le operazioni seguenti:


- Esportare il certificato di autenticazione da un certificato back-end (per lo SKU V1)
- Esportare un certificato radice trusted da un certificato back-end (per lo SKU v2)

## <a name="prerequisites"></a>Prerequisiti

Per generare i certificati di autenticazione o i certificati radice attendibili necessari per consentire le istanze back-end con il gateway applicazione, è necessario un certificato back-end esistente. Il certificato back-end può corrispondere al certificato TLS/SSL o a un valore diverso per la sicurezza aggiuntiva. Il gateway applicazione non fornisce alcun meccanismo per creare o acquistare un certificato TLS/SSL. A scopo di test, è possibile creare un certificato autofirmato, ma non è consigliabile usarlo per i carichi di lavoro di produzione. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Esporta certificato di autenticazione (per lo SKU V1)

Per consentire le istanze back-end nello SKU del gateway applicazione V1, è necessario un certificato di autenticazione. Il certificato di autenticazione è la chiave pubblica dei certificati del server back-end in base 64 con codifica X. 509 (. Formato CER). In questo esempio si userà un certificato TLS/SSL per il certificato back-end ed esporterà la chiave pubblica da usare come certificazione di autenticazione. Inoltre, in questo esempio si userà lo strumento Gestione certificati di Windows per esportare i certificati necessari. È possibile scegliere di utilizzare qualsiasi altro strumento utile.

Dal certificato TLS/SSL esportare il file con estensione cer della chiave pubblica (non la chiave privata). La procedura seguente consente di esportare il file con estensione cer in base 64 con codifica X. 509 (. Formato CER) per il certificato:

1. Per ottenere un file con estensione cer dal certificato, aprire **Gestire i certificati utente**. Individuare il certificato, in genere in ' Certificates-Current Corrente\personale\certificati ' e fare clic con il pulsante destro del mouse. Fare clic su **Tutte le attività** e quindi su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**. Se non è possibile trovare il certificato in Utente corrente\Personale\Certificati, è possibile aver accidentalmente aperto Certificati - Computer locale" invece di "Certificati - Utente corrente". Se si vuole aprire Gestione certificati nell'ambito dell'utente corrente usando PowerShell, digitare *certmgr* nella finestra della console.

   ![Screenshot mostra il gestore di certificati con certificati selezionati e un menu contestuale con tutte le attività, quindi Esporta selezionato.](./media/certificates-for-backend-authentication/export.png)

2. Nella procedura guidata, fare clic su **Avanti**.

   ![Esportare il certificato](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.

   ![Non esportare la chiave privata](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Nella pagina **Formato file di esportazione** selezionare **Codificato Base 64 X.509 (.CER)** e quindi fare clic su **Avanti**.

   ![Codifica Base 64](./media/certificates-for-backend-authentication/base64.png)

5. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Next**.

   ![Screenshot che mostra l'esportazione guidata certificati in cui si specifica un file da esportare.](./media/certificates-for-backend-authentication/browse.png)

6. Fare clic su **Fine** per esportare il certificato.

   ![Screenshot mostra l'esportazione guidata certificati dopo aver completato l'esportazione del file.](./media/certificates-for-backend-authentication/finish.png)

7. Il certificato è stato esportato correttamente.

   ![Screenshot che mostra l'esportazione guidata certificati con un messaggio di operazione completata.](./media/certificates-for-backend-authentication/success.png)

   Il certificato esportato è simile al seguente:

   ![Screenshot mostra un simbolo del certificato.](./media/certificates-for-backend-authentication/exported.png)

8. Se si apre il certificato esportato usando il Blocco note, viene visualizzato un certificato simile a questo esempio. La sezione in blu contiene le informazioni caricate nel gateway applicazione. Se si apre il certificato con il blocco note e questo non è simile a questo, in genere ciò significa che non è stato esportato usando il codificatore base 64 X. 509 (. Formato CER). Se si vuole usare un editor di testo diverso, è anche utile sapere che alcuni editor possono introdurre una formattazione imprevista in background. Ciò può creare problemi quando il testo viene caricato da questo certificato in Azure.

   ![Apri con il Blocco note](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Esporta certificato radice trusted (per lo SKU v2)

Il certificato radice attendibile è necessario per consentire le istanze back-end nello SKU del gateway applicazione V2. Il certificato radice è un X. 509 con codifica base 64 (. CER) formattare il certificato radice dai certificati del server back-end. In questo esempio viene usato un certificato TLS/SSL per il certificato back-end, viene esportata la chiave pubblica e quindi viene esportato il certificato radice della CA attendibile dalla chiave pubblica nel formato con codifica Base64 per ottenere il certificato radice attendibile. I certificati intermedi devono essere aggregati con il certificato del server e installati nel server back-end.

La procedura seguente consente di esportare il file con estensione CER per il certificato:

1. Usare i passaggi 1-8 indicati nella sezione precedente [esportare il certificato di autenticazione (per lo SKU V1)](#export-authentication-certificate-for-v1-sku) per esportare la chiave pubblica dal certificato back-end.

2. Una volta esportata la chiave pubblica, aprire il file.

   ![Apri certificato di autorizzazione](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![informazioni sul certificato](./media/certificates-for-backend-authentication/general.png)

3. Passare alla visualizzazione percorso certificazione per visualizzare l'autorità di certificazione.

   ![Dettagli certificato](./media/certificates-for-backend-authentication/certdetails.png)

4. Selezionare il certificato radice e fare clic su **Visualizza certificato**.

   ![percorso certificato](./media/certificates-for-backend-authentication/rootcert.png)

   Verranno visualizzati i dettagli del certificato radice.

   ![informazioni sul certificato](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Passare alla visualizzazione **Dettagli** e fare clic su **copia su file...**

   ![copia certificato radice](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. A questo punto, sono stati estratti i dettagli del certificato radice dal certificato back-end. Verrà visualizzata l' **esportazione guidata certificati**. Usare ora i passaggi 2-9 menzionati nella sezione **esportare il certificato di autenticazione da un certificato back-end (per lo SKU V1)** sopra per esportare il certificato radice attendibile in X. 509 con codifica base 64 (. Formato CER).

## <a name="next-steps"></a>Passaggi successivi

A questo punto è disponibile il certificato di autenticazione/certificato radice attendibile in base 64 con codifica X. 509 (. Formato CER). È possibile aggiungerlo al gateway applicazione per consentire ai server back-end la crittografia TLS end-to-end. Vedere [configurare TLS end-to-end usando il gateway applicazione con PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

