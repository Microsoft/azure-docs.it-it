---
title: Regole e gruppi di regole CRS
titleSuffix: Azure Web Application Firewall
description: Questa pagina offre informazioni sulle regole e i gruppi di regole CRS del Web application firewall.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 37e09612491d41887c5945920488569d3620bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85052016"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Regole e gruppi di regole CRS del Web Application Firewall

Il Web application firewall (WAF) di Gateway applicazione protegge le applicazioni Web da vulnerabilità ed exploit comuni. Questa operazione viene eseguita tramite regole definite in base ai set di regole OWASP Core 3,1, 3,0 o 2.2.9. Queste regole possono essere disabilitate in base alla regola. Questo articolo contiene le regole e i set di regole attualmente disponibili.

## <a name="core-rule-sets"></a>Set di regole principali

Il gateway applicazione WAF viene preconfigurato con CRS 3,0 per impostazione predefinita. È invece possibile scegliere di usare CRS 3,1 o CRS 2.2.9. CRS 3,1 offre nuovi set di regole che si difendono da infezioni Java, un insieme iniziale di controlli di caricamento dei file, corretti falsi positivi e altro ancora. CRS 3,0 offre falsi positivi ridotti rispetto a CRS 2.2.9. Puoi anche [personalizzare le regole in base alle tue esigenze](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Gestisce le regole](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF protegge dalle vulnerabilità Web seguenti:

- Attacchi SQL injection
- Attacchi di tipo cross-site scripting (XSS)
- Altri attacchi comuni, ad esempio l'inserimento di comandi, il contrabbando di richieste HTTP, la suddivisione di risposte HTTP e l'inclusione di file remoti
- Violazioni del protocollo HTTP
- Anomalie del protocollo HTTP, ad esempio l'agente utente host mancante e le intestazioni Accept
- Bot, crawler e scanner
- Configurazioni errate delle applicazioni comuni (ad esempio, Apache e IIS)

### <a name="owasp-crs-31"></a>CRS 3,1 DI OWASP

CRS 3,1 include 13 gruppi di regole, come illustrato nella tabella seguente. Ogni gruppo contiene più regole, che possono essere disabilitate.

> [!NOTE]
> CRS 3,1 è disponibile solo nello SKU WAF_v2.

|Gruppo di regole|Descrizione|
|---|---|
|**[Generale](#general-31)**|Gruppo generale|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Metodi di blocco (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Proteggi dagli scanner di porta e ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Protezione da problemi di protocollo e codifica|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Proteggi dall'inserimento di intestazioni, dal contrabbando di richieste e dalla suddivisione delle risposte|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Proteggi dagli attacchi di file e percorsi|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Protezione da attacchi di inclusione di file remoti|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Proteggi gli attacchi di esecuzione del codice remoto|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Proteggi da attacchi PHP-Injection|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Protezione da attacchi di scripting tra siti|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Proteggi da attacchi SQL injection|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Proteggi dagli attacchi di fissazione della sessione|
|**[RICHIESTA-944-APPLICAZIONE-ATTACCO-SESSIONE-JAVA](#crs944-31)**|Proteggi da attacchi JAVA|

### <a name="owasp-crs-30"></a>CRS 3,0 DI OWASP

CRS 3,0 include 12 gruppi di regole, come illustrato nella tabella seguente. Ogni gruppo contiene più regole, che possono essere disabilitate.

|Gruppo di regole|Descrizione|
|---|---|
|**[Generale](#general-30)**|Gruppo generale|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Metodi di blocco (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Proteggi dagli scanner di porta e ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Protezione da problemi di protocollo e codifica|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Proteggi dall'inserimento di intestazioni, dal contrabbando di richieste e dalla suddivisione delle risposte|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Proteggi dagli attacchi di file e percorsi|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Protezione da attacchi di inclusione di file remoti|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Proteggi gli attacchi di esecuzione del codice remoto|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Proteggi da attacchi PHP-Injection|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Protezione da attacchi di scripting tra siti|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Proteggi da attacchi SQL injection|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Proteggi dagli attacchi di fissazione della sessione|

### <a name="owasp-crs-229"></a>2.2.9 CRS OWASP

CRS 2.2.9 include 10 gruppi di regole, come illustrato nella tabella seguente. Ogni gruppo contiene più regole, che possono essere disabilitate.

|Gruppo di regole|Descrizione|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Proteggi da violazioni del protocollo (ad esempio caratteri non validi o GET con il corpo della richiesta)|
|**[crs_21_protocol_anomalies](#crs21)**|Proteggi da informazioni di intestazione non corrette|
|**[crs_23_request_limits](#crs23)**|Proteggi da argomenti o file che superano le limitazioni|
|**[crs_30_http_policy](#crs30)**|Protezione da metodi limitati, intestazioni e tipi di file|
|**[crs_35_bad_robots](#crs35)**|Proteggi da crawler e scanner web|
|**[crs_40_generic_attacks](#crs40)**|Protezione da attacchi generici (ad esempio, la fissazione della sessione, l'inclusione di file remoti e l'inserimento di PHP|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Proteggi da attacchi SQL injection|
|**[crs_41_xss_attacks](#crs41xss)**|Protezione da attacchi di scripting tra siti|
|**[crs_42_tight_security](#crs42)**|Proteggi dagli attacchi Path-Traversal|
|**[crs_45_trojans](#crs45)**|Proteggi da Trojan backdoor|

Le regole e i gruppi di regole seguenti sono disponibili quando si usa il Web Application Firewall nel gateway applicazione.

# <a name="owasp-31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Set di regole

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Generale</p>

|ID regola|Descrizione|
|---|---|
|200004|Possibile limite multiparte senza corrispondenza.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|ID regola|Descrizione|
|---|---|
|911100|Metodo non consentito da criteri|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|ID regola|Descrizione|
|---|---|
|913100|Trovato agente utente associato ad analisi della sicurezza|
|913101|Trovato agente utente associato a client HTTP generico/di scripting|
|913102|Trovato agente utente associato a bot/agente di ricerca Web|
|913110|Trovata intestazione della richiesta associata ad analisi della sicurezza|
|913120|Trovato argomento/nome file della richiesta associato ad analisi della sicurezza|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|ID regola|Descrizione|
|---|---|
|920100|Riga della richiesta HTTP non valida|
|920120|Tentativo di bypass dati multipart/form-data|
|920121|Tentativo di bypass dati multipart/form-data|
|920130|Impossibile analizzare corpo della richiesta|
|920140|Convalida del corpo della richiesta multipart non riuscita|
|920160|Intestazione HTTP Content-Length non numerica|
|920170|Richiesta GET o HEAD con contenuto del corpo|
|920171|Richiesta GET o HEAD con la codifica di trasferimento.|
|920180|Richiesta POST senza intestazione Content-Length|
|920190|Intervallo: valore ultimo byte non valido|
|920200|Intervallo: troppi campi (6 o più)|
|920201|Intervallo: troppi campi per richiesta PDF (35 o più)|
|920202|Intervallo: troppi campi per richiesta PDF (6 o più)|
|920210|Trovati dati intestazione di connessione multipli/in conflitto|
|920220|Tentativo di attacco con uso improprio codifica URL|
|920230|Rilevata codifica multipla URL|
|920240|Tentativo di attacco con uso improprio codifica URL|
|920250|Tentativo di attacco con uso improprio codifica UTF8|
|920260|Tentativo di attacco con uso improprio metà larghezza/larghezza intera Unicode|
|920270|Carattere non valido nella richiesta (carattere null)|
|920271|Carattere non valido nella richiesta (caratteri non stampabili)|
|920272|Carattere non valido nella richiesta (non compreso nei caratteri stampabili sotto ASCII 127)|
|920273|Carattere non valido nella richiesta (non compreso in set molto restrittivo)|
|920274|Carattere non valido nelle intestazioni della richiesta (non compreso in set con restrizioni elevate)|
|920280|Richiesta senza intestazione host|
|920290|Intestazione host vuota|
|920300|Richiesta senza intestazione Accept|
|920310|Richiesta con intestazione Accept vuota|
|920311|Richiesta con intestazione Accept vuota|
|920320|Intestazione agente utente mancante|
|920330|Intestazione agente utente vuota|
|920340|Richiesta con contenuto ma senza intestazione Content-Type|
|920341|Per la richiesta contenente contenuto è necessaria l'intestazione Content-Type|
|920350|Intestazione host costituita da un indirizzo IP numerico|
|920360|Nome argomento troppo lungo|
|920370|Valore argomento troppo lungo|
|920380|Troppi argomenti nella richiesta|
|920390|Superate dimensioni totali argomenti|
|920400|Dimensione file caricato troppo grande|
|920410|Dimensione totale dei file caricati troppo grande|
|920420|Tipo di contenuto della richiesta non consentito da criteri|
|920430|Versione protocollo HTTP non consentita da criteri|
|920440|Estensione file URL limitata da criteri|
|920450|Intestazione HTTP limitata da criteri (%@{MATCHED_VAR})|
|920460|Caratteri di escape anomali|
|920470|Intestazione Content-Type non valida|
|920480|Limitare il parametro charset nell'intestazione Content-Type|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|ID regola|Descrizione|
|---|---|
|921110|Attacco di tipo HTTP Request Smuggling|
|921120|Attacco di tipo HTTP Response Splitting|
|921130|Attacco di tipo HTTP Response Splitting|
|921140|Attacco di tipo HTTP Header Injection tramite intestazioni|
|921150|Attacco di tipo HTTP Header Injection tramite payload (rilevato CR/LF)|
|921151|Attacco di tipo HTTP Header Injection tramite payload (rilevato CR/LF)|
|921160|Attacco di tipo HTTP Header Injection tramite payload (rilevati CR/LF e nome intestazione)|
|921170|Inquinamento parametri HTTP|
|921180|Inquinamento parametri HTTP (% {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|ID regola|Descrizione|
|---|---|
|930100|Attacco di tipo Path Traversal (/../)|
|930110|Attacco di tipo Path Traversal (/../)|
|930120|Tentativo di accesso a file del sistema operativo|
|930130|Tentativo di accesso a file con restrizioni|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|ID regola|Descrizione|
|---|---|
|931100|Possibile attacco di tipo Remote File Inclusion (RFI): parametro URL con indirizzo IP|
|931110|Possibile attacco di tipo Remote File Inclusion (RFI): nome parametro vulnerabile a RFI comune usato con payload URL|
|931120|Possibile attacco di tipo Remote File Inclusion (RFI): payload URL usato con carattere punto interrogativo (?) finale|
|931130|Possibile attacco di tipo Remote File Inclusion (RFI): collegamento/riferimento fuori dominio|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|ID regola|Descrizione|
|---|---|
|932100|Esecuzione comando remoto: inserimento comandi UNIX|
|932105|Esecuzione comando remoto: inserimento comandi UNIX|
|932106|Esecuzione comando remoto: inserimento comandi UNIX|
|932110|Esecuzione comando remoto: inserimento comandi di Windows|
|932115|Esecuzione comando remoto: inserimento comandi di Windows|
|932120|Esecuzione comandi in remoto: trovato comando di Windows PowerShell|
|932130|Esecuzione comandi in remoto: trovata espressione shell Unix|
|932140|Esecuzione comandi in remoto: trovato comando FOR/IF di Windows|
|932150|Esecuzione comando remoto: esecuzione comando Direct UNIX|
|932160|Esecuzione comandi in remoto: trovato codice shell Unix|
|932170|Esecuzione comandi in remoto: Shellshock (CVE-2014-6271)|
|932171|Esecuzione comandi in remoto: Shellshock (CVE-2014-6271)|
|932180|Tentativo di caricamento file limitato|
|932190|Esecuzione comando remoto: tentativo di tecnica di bypass con caratteri jolly|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|ID regola|Descrizione|
|---|---|
|933100|Attacco PHP injection: trovato tag di apertura/chiusura|
|933110|Attacco PHP injection: trovato caricamento file script PHP|
|933111|Attacco PHP injection: trovato caricamento file script PHP|
|933120|Attacco PHP injection: trovata direttiva di configurazione|
|933130|Attacco PHP injection: trovate variabili|
|933131|Attacco PHP injection: sono state trovate variabili|
|933140|Attacco PHP injection: trovato flusso di I/O|
|933150|Attacco PHP injection: trovato nome funzione PHP ad alto rischio|
|933151|Attacco PHP injection: trovato Medium-Risk nome della funzione PHP|
|933160|Attacco PHP injection: trovata chiamata di funzione PHP ad alto rischio|
|933161|Attacco PHP injection: trovato Low-Value chiamata di funzione PHP|
|933170|Attacco PHP injection: inserimento di oggetti serializzati|
|933180|Attacco PHP injection: trovata chiamata di funzione variabile|
|933190|Attacco PHP injection: trovato tag di chiusura PHP|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|ID regola|Descrizione|
|---|---|
|941100|Rilevato attacco XSS tramite libinjection|
|941101|Rilevato attacco XSS tramite libinjection|
|941110|Filtro XSS, categoria 1: vettore tag script|
|941130|Filtro XSS, categoria 3: vettore attributi|
|941140|Filtro XSS, categoria 4: vettore URI Javascript|
|941150|Filtro XSS, categoria 5: attributi HTML non consentiti|
|941160|NoScript XSS InjectionChecker: inserimento HTML|
|941170|NoScript XSS InjectionChecker: inserimento di attributi|
|941180|Parole chiave in blacklist convalida nodi|
|941190|XSS con fogli di stile|
|941200|XSS con frame la|
|941210|XSS con JavaScript offuscato|
|941220|XSS con script VB offuscato|
|941230|XSS con tag ' embed '|
|941240|XSS con attributo ' Import ' o ' implementation '|
|941250|Filtri XSS IE-attacco rilevato|
|941260|XSS con tag ' meta '|
|941270|XSS con ' link ' href|
|941280|XSS con tag ' base '|
|941290|XSS con tag ' applet '|
|941300|XSS con tag ' Object '|
|941310|Filtro XSS per codifica US-ASCII in formato non valido: rilevato attacco|
|941320|Rilevato possibile attacco XSS: gestore tag HTML|
|941330|Filtri XSS IE: rilevato attacco|
|941340|Filtri XSS IE: rilevato attacco|
|941350|XSS IE con codifica UTF-7: rilevato attacco|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|ID regola|Descrizione|
|---|---|
|942100|Rilevato attacco SQL injection tramite libinjection|
|942110|Attacco SQL injection: rilevato test di inserimento comune|
|942120|Attacco SQL injection: rilevato operatore SQL|
|942130|Attacco SQL injection: rilevato tautologia SQL.|
|942140|Attacco SQL injection: rilevati nomi DB comuni|
|942150|Attacco SQL injection|
|942160|Rilevamento test di blind SQL injection con sleep() o benchmark()|
|942170|Rilevamento tentativi di SQL injection con benchmark e sleep e query condizionali|
|942180|Rileva i tentativi di base per il bypass SQL authentication 1/3|
|942190|Rileva l'esecuzione di codice MSSQL e tentativi di raccolta di informazioni|
|942200|Rileva injection offuscati nello spazio/con commento MySQL e terminazioni con apice inverso|
|942210|Rileva i tentativi di inserimento SQL injection concatenati 1/2|
|942220|Alla ricerca di attacchi con overflow di Integer, questi vengono ricavati da Skipfish, eccetto 3.0.00738585072|
|942230|Rilevamento tentativi di SQL injection condizionale|
|942240|Rileva l'opzione charset MySQL e i tentativi MSSQL DoS|
|942250|Rileva la corrispondenza, unisce ed esegue inserimenti immediati|
|942251|Rilevamento inserimenti HAVING|
|942260|Rileva tentativi di ignorare l'autenticazione SQL di base (2/3)|
|942270|Ricerca di SQL injection di base. Stringa di attacco comune per MySQL Oracle e altre|
|942280|Rileva il Postgres pg_sleep injection, gli attacchi di ritardo e i tentativi di arresto del database|
|942290|Ricerca tentativi di SQL injection di base in MongoDB|
|942300|Rileva ch(a)r injection, condizioni e commenti MySQL|
|942310|Rileva i tentativi di inserimento SQL injection concatenati 2/2|
|942320|Rilevamento inserimenti di stored procedure/funzioni MySQL e PostgreSQL|
|942330|Rileva sondaggi di SQL injection classici (1/2)|
|942340|Rileva tentativi di ignorare l'autenticazione SQL di base (3/3)|
|942350|Rilevamento di inserimento di funzioni definite dall'utente MySQL e altri tentativi di manipolazione dati/struttura|
|942360|Rileva tentativi SQL/LFI e di SQL injection di base concatenati|
|942361|Rileva SQL injection di base in base alla parola chiave ALTER o Union|
|942370|Rileva sondaggi di SQL injection classici (2/2)|
|942380|Attacco SQL injection|
|942390|Attacco SQL injection|
|942400|Attacco SQL injection|
|942410|Attacco SQL injection|
|942420|Rilevamento anomalie dei caratteri SQL limitati (cookie): numero di caratteri speciali superato (8)|
|942421|Rilevamento anomalie dei caratteri SQL limitati (cookie): numero di caratteri speciali superato (3)|
|942430|Rilevamento anomalie caratteri SQL con restrizioni (args): n. di caratteri speciali in eccesso (12)|
|942431|Rilevamento anomalie dei caratteri SQL limitati (args): numero di caratteri speciali superato (6)|
|942432|Rilevamento anomalie dei caratteri SQL limitati (args): numero di caratteri speciali superato (2)|
|942440|Rilevata sequenza commenti SQL|
|942450|Identificata codifica esadecimale SQL|
|942460|Avviso di rilevamento anomalie metacaratteri: caratteri non alfanumerici ripetitivi|
|942470|Attacco SQL injection|
|942480|Attacco SQL injection|
|942490|Rileva i sondaggi SQL injection classici 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ID regola|Descrizione|
|---|---|
|943100|Possibile attacco di tipo Session Fixation: impostazione valori cookie in HTML|
|943110|Possibile attacco di tipo Session Fixation: nome parametro SessionID con referrer fuori dominio|
|943120|Possibile attacco di tipo Session Fixation: nome parametro SessionID senza referrer|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">RICHIESTA-944-APPLICAZIONE-ATTACCO-SESSIONE-JAVA</p>

|ID regola|Descrizione|
|---|---|
|944120|Possibile esecuzione del payload e esecuzione del comando remoto|
|944130|Classi Java sospette|
|944200|Exploit della deserializzazione Java Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Set di regole

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Generale</p>

|ID regola|Descrizione|
|---|---|
|200004|Possibile limite multiparte senza corrispondenza.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|ID regola|Descrizione|
|---|---|
|911100|Metodo non consentito da criteri|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|ID regola|Descrizione|
|---|---|
|913100|Trovato agente utente associato ad analisi della sicurezza|
|913110|Trovata intestazione della richiesta associata ad analisi della sicurezza|
|913120|Trovato argomento/nome file della richiesta associato ad analisi della sicurezza|
|913101|Trovato agente utente associato a client HTTP generico/di scripting|
|913102|Trovato agente utente associato a bot/agente di ricerca Web|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|ID regola|Descrizione|
|---|---|
|920100|Riga della richiesta HTTP non valida|
|920130|Impossibile analizzare corpo della richiesta|
|920140|Convalida del corpo della richiesta multipart non riuscita|
|920160|Intestazione HTTP Content-Length non numerica|
|920170|Richiesta GET o HEAD con contenuto del corpo|
|920180|Richiesta POST senza intestazione Content-Length|
|920190|Intervallo: valore ultimo byte non valido|
|920210|Trovati dati intestazione di connessione multipli/in conflitto|
|920220|Tentativo di attacco con uso improprio codifica URL|
|920240|Tentativo di attacco con uso improprio codifica URL|
|920250|Tentativo di attacco con uso improprio codifica UTF8|
|920260|Tentativo di attacco con uso improprio metà larghezza/larghezza intera Unicode|
|920270|Carattere non valido nella richiesta (carattere null)|
|920280|Richiesta senza intestazione host|
|920290|Intestazione host vuota|
|920310|Richiesta con intestazione Accept vuota|
|920311|Richiesta con intestazione Accept vuota|
|920330|Intestazione agente utente vuota|
|920340|Richiesta con contenuto ma senza intestazione Content-Type|
|920350|Intestazione host costituita da un indirizzo IP numerico|
|920380|Troppi argomenti nella richiesta|
|920360|Nome argomento troppo lungo|
|920370|Valore argomento troppo lungo|
|920390|Superate dimensioni totali argomenti|
|920400|Dimensione file caricato troppo grande|
|920410|Dimensione totale dei file caricati troppo grande|
|920420|Tipo di contenuto della richiesta non consentito da criteri|
|920430|Versione protocollo HTTP non consentita da criteri|
|920440|Estensione file URL limitata da criteri|
|920450|Intestazione HTTP limitata da criteri (%@{MATCHED_VAR})|
|920200|Intervallo: troppi campi (6 o più)|
|920201|Intervallo: troppi campi per richiesta PDF (35 o più)|
|920230|Rilevata codifica multipla URL|
|920300|Richiesta senza intestazione Accept|
|920271|Carattere non valido nella richiesta (caratteri non stampabili)|
|920320|Intestazione agente utente mancante|
|920272|Carattere non valido nella richiesta (non compreso nei caratteri stampabili sotto ASCII 127)|
|920202|Intervallo: troppi campi per richiesta PDF (6 o più)|
|920273|Carattere non valido nella richiesta (non compreso in set molto restrittivo)|
|920274|Carattere non valido nelle intestazioni della richiesta (non compreso in set con restrizioni elevate)|
|920460|Caratteri di escape anomali|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|ID regola|Descrizione|
|---|---|
|921100|Attacco di tipo HTTP Request Smuggling|
|921110|Attacco di tipo HTTP Request Smuggling|
|921120|Attacco di tipo HTTP Response Splitting|
|921130|Attacco di tipo HTTP Response Splitting|
|921140|Attacco di tipo HTTP Header Injection tramite intestazioni|
|921150|Attacco di tipo HTTP Header Injection tramite payload (rilevato CR/LF)|
|921160|Attacco di tipo HTTP Header Injection tramite payload (rilevati CR/LF e nome intestazione)|
|921151|Attacco di tipo HTTP Header Injection tramite payload (rilevato CR/LF)|
|921170|Inquinamento parametri HTTP|
|921180|Accesso non autorizzato a parametri HTTP (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|ID regola|Descrizione|
|---|---|
|930100|Attacco di tipo Path Traversal (/../)|
|930110|Attacco di tipo Path Traversal (/../)|
|930120|Tentativo di accesso a file del sistema operativo|
|930130|Tentativo di accesso a file con restrizioni|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|ID regola|Descrizione|
|---|---|
|931100|Possibile attacco di tipo Remote File Inclusion (RFI): parametro URL con indirizzo IP|
|931110|Possibile attacco di tipo Remote File Inclusion (RFI): nome parametro vulnerabile a RFI comune usato con payload URL|
|931120|Possibile attacco di tipo Remote File Inclusion (RFI): payload URL usato con carattere punto interrogativo (?) finale|
|931130|Possibile attacco di tipo Remote File Inclusion (RFI): collegamento/riferimento fuori dominio|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|ID regola|Descrizione|
|---|---|
|932120|Esecuzione comandi in remoto: trovato comando di Windows PowerShell|
|932130|Esecuzione comandi in remoto: trovata espressione shell Unix|
|932140|Esecuzione comandi in remoto: trovato comando FOR/IF di Windows|
|932160|Esecuzione comandi in remoto: trovato codice shell Unix|
|932170|Esecuzione comandi in remoto: Shellshock (CVE-2014-6271)|
|932171|Esecuzione comandi in remoto: Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|ID regola|Descrizione|
|---|---|
|933100|Attacco PHP injection: trovato tag di apertura/chiusura|
|933110|Attacco PHP injection: trovato caricamento file script PHP|
|933120|Attacco PHP injection: trovata direttiva di configurazione|
|933130|Attacco PHP injection: trovate variabili|
|933150|Attacco PHP injection: trovato nome funzione PHP ad alto rischio|
|933160|Attacco PHP injection: trovata chiamata di funzione PHP ad alto rischio|
|933180|Attacco PHP injection: trovata chiamata di funzione variabile|
|933151|Attacco PHP injection: trovato nome funzione PHP a medio rischio|
|933131|Attacco PHP injection: trovate variabili|
|933161|Attacco PHP injection: trovata chiamata di funzione PHP con valore basso|
|933111|Attacco PHP injection: trovato caricamento file script PHP|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|ID regola|Descrizione|
|---|---|
|941100|Rilevato attacco XSS tramite libinjection|
|941110|Filtro XSS, categoria 1: vettore tag script|
|941130|Filtro XSS, categoria 3: vettore attributi|
|941140|Filtro XSS, categoria 4: vettore URI Javascript|
|941150|Filtro XSS, categoria 5: attributi HTML non consentiti|
|941180|Parole chiave in blacklist convalida nodi|
|941190|XSS con fogli di stile|
|941200|XSS con frame la|
|941210|XSS con JavaScript offuscato|
|941220|XSS con script VB offuscato|
|941230|XSS con tag ' embed '|
|941240|XSS con attributo ' Import ' o ' implementation '|
|941260|XSS con tag ' meta '|
|941270|XSS con ' link ' href|
|941280|XSS con tag ' base '|
|941290|XSS con tag ' applet '|
|941300|XSS con tag ' Object '|
|941310|Filtro XSS per codifica US-ASCII in formato non valido: rilevato attacco|
|941330|Filtri XSS IE: rilevato attacco|
|941340|Filtri XSS IE: rilevato attacco|
|941350|XSS IE con codifica UTF-7: rilevato attacco|
|941320|Rilevato possibile attacco XSS: gestore tag HTML|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|ID regola|Descrizione|
|---|---|
|942100|Rilevato attacco SQL injection tramite libinjection|
|942110|Attacco SQL injection: rilevato test di inserimento comune|
|942130|Attacco SQL injection: rilevato tautologia SQL.|
|942140|Attacco SQL injection: rilevati nomi DB comuni|
|942160|Rilevamento test di blind SQL injection con sleep() o benchmark()|
|942170|Rilevamento tentativi di SQL injection con benchmark e sleep e query condizionali|
|942190|Rileva l'esecuzione di codice MSSQL e tentativi di raccolta di informazioni|
|942200|Rileva injection offuscati nello spazio/con commento MySQL e terminazioni con apice inverso|
|942230|Rilevamento tentativi di SQL injection condizionale|
|942260|Rileva tentativi di ignorare l'autenticazione SQL di base (2/3)|
|942270|Ricerca di SQL injection di base. Stringa di attacco comune per MySQL, Oracle e altri|
|942290|Ricerca tentativi di SQL injection di base in MongoDB|
|942300|Rileva ch(a)r injection, condizioni e commenti MySQL|
|942310|Rileva i tentativi di inserimento SQL injection concatenati 2/2|
|942320|Rilevamento inserimenti di stored procedure/funzioni MySQL e PostgreSQL|
|942330|Rileva sondaggi di SQL injection classici (1/2)|
|942340|Rileva tentativi di ignorare l'autenticazione SQL di base (3/3)|
|942350|Rilevamento di inserimento di funzioni definite dall'utente MySQL e altri tentativi di manipolazione dati/struttura|
|942360|Rileva tentativi SQL/LFI e di SQL injection di base concatenati|
|942370|Rileva sondaggi di SQL injection classici (2/2)|
|942150|Attacco SQL injection|
|942410|Attacco SQL injection|
|942430|Rilevamento anomalie caratteri SQL con restrizioni (args): n. di caratteri speciali in eccesso (12)|
|942440|Rilevata sequenza commenti SQL|
|942450|Identificata codifica esadecimale SQL|
|942251|Rilevamento inserimenti HAVING|
|942460|Avviso di rilevamento anomalie metacaratteri: caratteri non alfanumerici ripetitivi|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ID regola|Descrizione|
|---|---|
|943100|Possibile attacco di tipo Session Fixation: impostazione valori cookie in HTML|
|943110|Possibile attacco di tipo Session Fixation: nome parametro SessionID con referrer fuori dominio|
|943120|Possibile attacco di tipo Session Fixation: nome parametro SessionID senza referrer|

# <a name="owasp-229"></a>[2.2.9 OWASP](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Set di regole

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|ID regola|Descrizione|
|---|---|
|960911|Riga della richiesta HTTP non valida|
|981227|Errore Apache: URI non valido nella richiesta|
|960912|Impossibile analizzare corpo della richiesta|
|960914|Convalida del corpo della richiesta multipart non riuscita|
|960915|Il parser multiparte ha rilevato un possibile limite senza corrispondenza|
|960016|Intestazione HTTP Content-Length non numerica|
|960011|Richiesta GET o HEAD con contenuto del corpo|
|960012|Richiesta POST senza intestazione Content-Length|
|960902|Uso non valido codifica identità|
|960022|Intestazione Expect non consentita per HTTP 1.0|
|960020|L'intestazione Pragma richiede l'intestazione Cache-Control per le richieste HTTP/1.1|
|958291|Intervallo: campo esistente che inizia con 0|
|958230|Intervallo: valore ultimo byte non valido|
|958295|Trovati dati intestazione di connessione multipli/in conflitto|
|950107|Tentativo di attacco con uso improprio codifica URL|
|950109|Rilevata codifica multipla URL|
|950108|Tentativo di attacco con uso improprio codifica URL|
|950801|Tentativo di attacco con uso improprio codifica UTF8|
|950116|Tentativo di attacco con uso improprio metà larghezza/larghezza intera Unicode|
|960901|Carattere non valido nella richiesta|
|960018|Carattere non valido nella richiesta|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|ID regola|Descrizione|
|---|---|
|960008|Richiesta senza intestazione host|
|960007|Intestazione host vuota|
|960015|Richiesta senza intestazione Accept|
|960021|Richiesta con intestazione Accept vuota|
|960009|Richiesta senza intestazione agente utente|
|960006|Intestazione agente utente vuota|
|960904|Richiesta con contenuto ma senza intestazione Content-Type|
|960017|Intestazione host costituita da un indirizzo IP numerico|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|ID regola|Descrizione|
|---|---|
|960209|Nome argomento troppo lungo|
|960208|Valore argomento troppo lungo|
|960335|Troppi argomenti nella richiesta|
|960341|Superate dimensioni totali argomenti|
|960342|Dimensione file caricato troppo grande|
|960343|Dimensione totale dei file caricati troppo grande|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|ID regola|Descrizione|
|---|---|
|960032|Metodo non consentito da criteri|
|960010|Tipo di contenuto della richiesta non consentito da criteri|
|960034|Versione protocollo HTTP non consentita da criteri|
|960035|Estensione file URL limitata da criteri|
|960038|Intestazione HTTP limitata da criteri|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|ID regola|Descrizione|
|---|---|
|990002|La richiesta indica che è stata eseguita l'analisi della sicurezza sul sito|
|990901|La richiesta indica che è stata eseguita l'analisi della sicurezza sul sito|
|990902|La richiesta indica che è stata eseguita l'analisi della sicurezza sul sito|
|990012|Agente di ricerca siti Web non autorizzato|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|ID regola|Descrizione|
|---|---|
|960024|Avviso di rilevamento anomalie metacaratteri: caratteri non alfanumerici ripetitivi|
|950008|Inserimento di tag ColdFusion non documentati|
|950010|Attacco LDAP injection|
|950011|Attacco SSI injection|
|950018|Rilevato URL Universal PDF XSS|
|950019|Attacco e-mail injection|
|950012|Attacco di tipo HTTP Request Smuggling|
|950910|Attacco di tipo HTTP Response Splitting|
|950911|Attacco di tipo HTTP Response Splitting|
|950117|Attacco di tipo Remote File Inclusion|
|950118|Attacco di tipo Remote File Inclusion|
|950119|Attacco di tipo Remote File Inclusion|
|950120|Possibile attacco di tipo Remote File Inclusion (RFI): collegamento/riferimento fuori dominio|
|981133|Regola 981133|
|981134|Regola 981134|
|950009|Attacco di tipo Session Fixation|
|950003|Session Fixation|
|950000|Session Fixation|
|950005|Tentativo di accesso a file remoto|
|950002|Accesso a comandi di sistema|
|950006|Inserimento di comandi di sistema|
|959151|Attacco PHP injection|
|958976|Attacco PHP injection|
|958977|Attacco PHP injection|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|ID regola|Descrizione|
|---|---|
|981231|Rilevata sequenza commenti SQL|
|981260|Identificata codifica esadecimale SQL|
|981320|Attacco SQL injection: rilevati nomi DB comuni|
|981300|Regola 981300|
|981301|Regola 981301|
|981302|Regola 981302|
|981303|Regola 981303|
|981304|Regola 981304|
|981305|Regola 981305|
|981306|Regola 981306|
|981307|Regola 981307|
|981308|Regola 981308|
|981309|Regola 981309|
|981310|Regola 981310|
|981311|Regola 981311|
|981312|Regola 981312|
|981313|Regola 981313|
|981314|Regola 981314|
|981315|Regola 981315|
|981316|Regola 981316|
|981317|Avviso di rilevamento anomalie in istruzione SQL SELECT|
|950007|Attacco blind SQL injection|
|950001|Attacco SQL injection|
|950908|Attacco SQL injection|
|959073|Attacco SQL injection|
|981272|Rilevamento test di blind SQL injection con sleep() o benchmark()|
|981250|Rilevamento tentativi di SQL injection con benchmark e sleep e query condizionali|
|981241|Rilevamento tentativi di SQL injection condizionale|
|981276|Ricerca di SQL injection di base. Stringa di attacco comune per MySQL, Oracle e altri|
|981270|Ricerca tentativi di SQL injection di base in MongoDB|
|981253|Rilevamento inserimenti di stored procedure/funzioni MySQL e PostgreSQL|
|981251|Rilevamento di inserimento di funzioni definite dall'utente MySQL e altri tentativi di manipolazione dati/struttura|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|ID regola|Descrizione|
|---|---|
|973336|Filtro XSS, categoria 1: vettore tag script|
|973338|Filtro XSS, categoria 3: vettore URI Javascript|
|981136|Regola 981136|
|981018|Regola 981018|
|958016|Attacco di tipo cross-site scripting (XSS)|
|958414|Attacco di tipo cross-site scripting (XSS)|
|958032|Attacco di tipo cross-site scripting (XSS)|
|958026|Attacco di tipo cross-site scripting (XSS)|
|958027|Attacco di tipo cross-site scripting (XSS)|
|958054|Attacco di tipo cross-site scripting (XSS)|
|958418|Attacco di tipo cross-site scripting (XSS)|
|958034|Attacco di tipo cross-site scripting (XSS)|
|958019|Attacco di tipo cross-site scripting (XSS)|
|958013|Attacco di tipo cross-site scripting (XSS)|
|958408|Attacco di tipo cross-site scripting (XSS)|
|958012|Attacco di tipo cross-site scripting (XSS)|
|958423|Attacco di tipo cross-site scripting (XSS)|
|958002|Attacco di tipo cross-site scripting (XSS)|
|958017|Attacco di tipo cross-site scripting (XSS)|
|958007|Attacco di tipo cross-site scripting (XSS)|
|958047|Attacco di tipo cross-site scripting (XSS)|
|958410|Attacco di tipo cross-site scripting (XSS)|
|958415|Attacco di tipo cross-site scripting (XSS)|
|958022|Attacco di tipo cross-site scripting (XSS)|
|958405|Attacco di tipo cross-site scripting (XSS)|
|958419|Attacco di tipo cross-site scripting (XSS)|
|958028|Attacco di tipo cross-site scripting (XSS)|
|958057|Attacco di tipo cross-site scripting (XSS)|
|958031|Attacco di tipo cross-site scripting (XSS)|
|958006|Attacco di tipo cross-site scripting (XSS)|
|958033|Attacco di tipo cross-site scripting (XSS)|
|958038|Attacco di tipo cross-site scripting (XSS)|
|958409|Attacco di tipo cross-site scripting (XSS)|
|958001|Attacco di tipo cross-site scripting (XSS)|
|958005|Attacco di tipo cross-site scripting (XSS)|
|958404|Attacco di tipo cross-site scripting (XSS)|
|958023|Attacco di tipo cross-site scripting (XSS)|
|958010|Attacco di tipo cross-site scripting (XSS)|
|958411|Attacco di tipo cross-site scripting (XSS)|
|958422|Attacco di tipo cross-site scripting (XSS)|
|958036|Attacco di tipo cross-site scripting (XSS)|
|958000|Attacco di tipo cross-site scripting (XSS)|
|958018|Attacco di tipo cross-site scripting (XSS)|
|958406|Attacco di tipo cross-site scripting (XSS)|
|958040|Attacco di tipo cross-site scripting (XSS)|
|958052|Attacco di tipo cross-site scripting (XSS)|
|958037|Attacco di tipo cross-site scripting (XSS)|
|958049|Attacco di tipo cross-site scripting (XSS)|
|958030|Attacco di tipo cross-site scripting (XSS)|
|958041|Attacco di tipo cross-site scripting (XSS)|
|958416|Attacco di tipo cross-site scripting (XSS)|
|958024|Attacco di tipo cross-site scripting (XSS)|
|958059|Attacco di tipo cross-site scripting (XSS)|
|958417|Attacco di tipo cross-site scripting (XSS)|
|958020|Attacco di tipo cross-site scripting (XSS)|
|958045|Attacco di tipo cross-site scripting (XSS)|
|958004|Attacco di tipo cross-site scripting (XSS)|
|958421|Attacco di tipo cross-site scripting (XSS)|
|958009|Attacco di tipo cross-site scripting (XSS)|
|958025|Attacco di tipo cross-site scripting (XSS)|
|958413|Attacco di tipo cross-site scripting (XSS)|
|958051|Attacco di tipo cross-site scripting (XSS)|
|958420|Attacco di tipo cross-site scripting (XSS)|
|958407|Attacco di tipo cross-site scripting (XSS)|
|958056|Attacco di tipo cross-site scripting (XSS)|
|958011|Attacco di tipo cross-site scripting (XSS)|
|958412|Attacco di tipo cross-site scripting (XSS)|
|958008|Attacco di tipo cross-site scripting (XSS)|
|958046|Attacco di tipo cross-site scripting (XSS)|
|958039|Attacco di tipo cross-site scripting (XSS)|
|958003|Attacco di tipo cross-site scripting (XSS)|
|973300|Rilevato possibile attacco XSS: gestore tag HTML|
|973301|Rilevato attacco XSS|
|973302|Rilevato attacco XSS|
|973303|Rilevato attacco XSS|
|973304|Rilevato attacco XSS|
|973305|Rilevato attacco XSS|
|973306|Rilevato attacco XSS|
|973307|Rilevato attacco XSS|
|973308|Rilevato attacco XSS|
|973309|Rilevato attacco XSS|
|973311|Rilevato attacco XSS|
|973313|Rilevato attacco XSS|
|973314|Rilevato attacco XSS|
|973331|Filtri XSS IE: rilevato attacco|
|973315|Filtri XSS IE: rilevato attacco|
|973330|Filtri XSS IE: rilevato attacco|
|973327|Filtri XSS IE: rilevato attacco|
|973326|Filtri XSS IE: rilevato attacco|
|973346|Filtri XSS IE: rilevato attacco|
|973345|Filtri XSS IE: rilevato attacco|
|973324|Filtri XSS IE: rilevato attacco|
|973323|Filtri XSS IE: rilevato attacco|
|973348|Filtri XSS IE: rilevato attacco|
|973321|Filtri XSS IE: rilevato attacco|
|973320|Filtri XSS IE: rilevato attacco|
|973318|Filtri XSS IE: rilevato attacco|
|973317|Filtri XSS IE: rilevato attacco|
|973329|Filtri XSS IE: rilevato attacco|
|973328|Filtri XSS IE: rilevato attacco|

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|ID regola|Descrizione|
|---|---|
|950103|Attacco di tipo Path Traversal|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|ID regola|Descrizione|
|---|---|
|950110|Accesso backdoor|
|950921|Accesso backdoor|
|950922|Accesso backdoor|

---

## <a name="next-steps"></a>Passaggi successivi

- [Personalizzare le regole del Web Application Firewall usando il portale di Azure](application-gateway-customize-waf-rules-portal.md)
