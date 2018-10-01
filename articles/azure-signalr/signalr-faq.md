# Domande frequenti

- [Azure SignalR Service è pronto per un uso in produzione?](#production-use)
- [La connessione del Client si chiude con il messaggio di errore "No server available". Cosa vuol dire?](#no-server-available)
- [Quando ci sono application server multipli, i messaggi dei client vengono mandati a tutti o solo a uno di loro?](#client-server-mapping)
- [Se uno dei miei application server è down, il servizio SignalR se ne accorge? Verranno notificati anche i client?](#server-down)
- [Perchè il mio `IUserIdProvider` custom solleva eccezioni quando passo da ASP.NET Core SignalR all'SDK del servizio Azure SignalR?](#limited-context)
- [Posso configurare i trasporti disponibili lato server in Azure SignalR Service come in ASP.NET Core SignalR?](#configure-transports)

<a name="production-use"></a>
## Azure SignalR Service è pronto per un uso in produzione?

Sì, se stai usando [ASP.NET Core SignalR](https://github.com/aspnet/SignalR) con Azure SignalR Service.
L'annuncio di disponibilità generale si trova [qui](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/).

Il supporto per [ASP.NET SignalR](https://github.com/SignalR/SignalR) è ancora in *public preview*.

<a name="no-server-available"></a>
## La connessione del Client si chiude con il messaggio di errore "No server available". Cosa vuol dire?

Questo errore si verifica solo quando i client stanno inviando messaggi al servizio SignalR.

Se non hai nessun application server e usi solo le API REST del servizio SignalR, è così **per design**.
In un'architettura senza server, le connessioni client sono in modalità **ASCOLTO** e non dovrebbero inviare nessun messaggio al servizio SignalR.
Clicca qui per ulteriori informaizioni sulle [API REST](./rest-api.md) dei server SignalR.

Se hai un application server, questo significa che non ci sono application server connessi alla tua istanza di servizio SignalR.
Le possibili cause principali sono:
- Nessuno dei tuoi application server è connesso al servizio SignalR.
Verifica nei log degli application server se ci sono errori di connessione.
Potrebbe verificarsi molto raramente quando hai application server multipli per gestire situazioni di high availability.
- Ci sono problemi di connettività nelle istanze di servizio SignalR.
Dovrebbe essere una situazione temporanea e rientrare automaticamente nel giro di un paio di minuti.
Se permane per più di un'ora, per favore [apri una issue su GitHub](https://github.com/Azure/azure-signalr/issues/new) o [crea una richiesta di supporto Azure](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request).
A questo punto possiamo lavorare con te per risolvere il problema.

<a name="client-server-mapping"></a>
## Quando ci sono application server multipli, i messaggi dei client vengono mandati a tutti o solo a uno di loro?

La mappatura fra client e application server è uno-a-uno ([connessioni al server](./signalr-internals.md#server-connections)), quindi i messaggi di un client saranno sempre inviati al medesimo application server.

La mappatura fra client e application server nel servizio SignalR è valida finchè il client o l'application server si disconnettono.

<a name="server-down"></a>
## Se uno dei miei application server è down, il servizio SignalR se ne accorge? Verranno notificati anche i client?

Sì.

Il servizio SignalR monitora heartbeat dagli application server.
Se non viene ricevuto un heartbeat per un determinato periodo, l'application server verrà considerato offline.
Tutte le connessioni ai client che sono servite da quel determinato application server saranno quindi disconnesse.

<a name="limited-context"></a>
## Perchè il mio `IUserIdProvider` custom solleva eccezioni quando passo da ASP.NET Core SignalR all'SDK del servizio Azure SignalR

Il parametro `HubConnectionContext context` è diverso tra ASP.NET Core SignalR e l'SDK del servizio Azure SignalR quando `IUserIdProvider` viene chiamato.
In ASP.NET Core SignalR, `HubConnectionContext context` è il contesto della connessione al client fisica e ha valori validi per tutte le proprietà.

Nell'SDK del servizio Azure SignalR, `HubConnectionContext context` è un contesto di connessione logica al client.
La connessione fisica è collegata all'istanza di servizio SignalR, quindi solo alcune proprietà sono fornite.
Per ora, solo `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` sono disponibili per l'accesso.
Puoi verificare il sorgente [qui](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

<a name="configure-transports"></a>
## Posso configurare i trasporti disponibili lato server in Azure SignalR Service come in ASP.NET Core SignalR? Per esempio, il trasporto WebSocket è disabilitato.

No.

Il servizio Azure SignalR fornisce tutti e 3 i trasporti di default e non può essere configurato.
In effetti, non devi preoccuparti del trasporto perchè i client sono tutti connessi al servizio Azure SignalR e il servizio provvede autonomamente alla gestione delle connessioni.

Puoi sempre configurare i trasporti permessi lato client come documentato [qui](https://docs.microsoft.com/en-us/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
