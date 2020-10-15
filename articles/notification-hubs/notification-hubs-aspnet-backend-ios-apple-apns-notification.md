---
title: Inviare notifiche push a utenti specifici usando hub di notifica di Azure | Microsoft Docs
description: Informazioni su come inviare notifiche push a specifici utenti iOS usando hub di notifica di Azure.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302190"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Esercitazione: inviare notifiche push a utenti specifici usando hub di notifica di Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche viene usato un back-end di API Web ASP.NET, come illustrato nell'argomento [Registering from your app backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) (Registrazione dal back-end dell'app).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare il progetto WebAPI
> * Autenticare client con il back-end WebAPI
> * Eseguire la registrazione per le notifiche tramite il back-end WebAPI
> * Inviare notifiche dal back-end WebAPI
> * Pubblicare il nuovo back-end WebAPI
> * Modificare l'app per iOS
> * Test dell'applicazione

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che sia stato creato e configurato l'hub di notifica come descritto in [inviare notifiche push alle app iOS usando hub di notifica di Azure](ios-sdk-get-started.md). È inoltre propedeutica all'esercitazione [Push sicuro (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Se si desidera usare le app per dispositivi mobili come servizio back-end, vedere [Introduzione alle app per dispositivi mobili con notifiche push](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modificare l'app per iOS

1. Aprire l'app visualizzazione a pagina singola creata nell'esercitazione [inviare notifiche push a app iOS usando hub di notifica di Azure](ios-sdk-get-started.md) .

   > [!NOTE]
   > In questa sezione si presuppone che il progetto sia configurato con un nome di organizzazione vuoto. In caso contrario, anteporre il nome dell'organizzazione a tutti i nomi di classe.

2. Nel file `Main.storyboard` aggiungere i componenti illustrati nello screenshot dalla libreria di oggetti.

    ![Modifica storyboard in Xcode Interface Builder][1]

   * **Nome utente**: oggetto UITextField con testo segnaposto, *Enter Username*, immediatamente sotto l'etichetta per l'invio dei risultati e limitato dai margini sinistro e destro.
   * **Password**: oggetto UITextField con testo segnaposto, *Enter Password*, immediatamente sotto il campo di testo del nome utente e limitato dai margini sinistro e destro. Selezionare l'opzione **Secure Text Entry** in Attribute Inspector sotto *Return Key*.
   * **Accesso**: oggetto UIButton con etichetta immediatamente sotto il campo di testo della password; deselezionare l'opzione **Abilitato** in Attributes Inspector sotto *Control-Content*.
   * **WNS**: etichetta e opzione per consentire l'invio della notifica al servizio di notifica Windows se è stato configurato nell'hub. Vedere l'esercitazione [Introduzione a Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
   * **GCM**: etichetta e opzione per consentire l'invio della notifica a Google Cloud Messaging se è stato configurato nell'hub. Vedere l'esercitazione [Introduzione ad Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNS**: etichetta e opzione per consentire l'invio della notifica al servizio di notifica della piattaforma di Apple.
   * **Nome utente del destinatario**: oggetto UITextField con testo segnaposto, *Recipient username tag*, immediatamente sotto l'etichetta GCM e limitato dai margini sinistro e destro.

     Alcuni componenti sono stati aggiunti nell'esercitazione [inviare notifiche push ad app iOS usando hub di notifica di Azure](ios-sdk-get-started.md) .

3. **Premere CTRL** tra i componenti nella visualizzazione `ViewController.h` e aggiungere i nuovi Outlet:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. In `ViewController.h` aggiungere l'elemento seguente `#define` sotto le istruzioni di importazione. Sostituire il segnaposto `<Your backend endpoint>` con l'URL di destinazione usato per distribuire il back-end dell'app nella sezione precedente. Ad esempio, `http://your_backend.azurewebsites.net`:

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. Nel progetto creare una nuova classe Cocoa Touch denominata `RegisterClient` da usare come interfaccia con il back-end ASP.NET creato. Creare la classe che eredita da `NSObject`. Aggiungere quindi il codice seguente in `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. In `RegisterClient.m` aggiornare la sezione `@interface`:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Sostituire la sezione `@implementation` in RegisterClient.m con il codice seguente:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Questo codice implementa la logica illustrata nell'articolo di istruzioni [Registrazione dal back-end dell'app](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) usando NSURLSession per eseguire chiamate REST al back-end dell'app e NSUserDefaults per archiviare in locale l'ID registrazione restituito dall'hub di notifica.

    Per il corretto funzionamento di questa classe è necessario che sia impostata la relativa proprietà `authorizationHeader`. Questa proprietà viene impostata dalla classe `ViewController` dopo l'accesso.

8. In `ViewController.h` aggiungere un'istruzione `#import` per `RegisterClient.h`. Aggiungere quindi una dichiarazione per il token del dispositivo e fare riferimento a un'istanza di `RegisterClient` nella sezione `@interface`:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. In ViewController.m aggiungere una dichiarazione di metodo privato nella sezione `@interface` :

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Il frammento seguente non è uno schema di autenticazione sicuro. È consigliabile sostituire l'implementazione di `createAndSetAuthenticationHeaderWithUsername:AndPassword:` con il meccanismo di autenticazione specifico che genera un token di autenticazione che deve essere usato dalla classe client di registrazione, ad esempio OAuth o Active Directory.

10. Aggiungere quindi nella sezione `@implementation` di `ViewController.m` il codice seguente, che aggiunge l'implementazione per l'impostazione del token del dispositivo e dell'intestazione di autenticazione.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Si noti come l'impostazione del token del dispositivo Abilita il pulsante **Accedi** . Questo dipende dal fatto che, nell'ambito dell'azione di accesso, il controller di visualizzazione esegue la registrazione per le notifiche push con il back-end dell'app. Non si vuole che l'azione di **accesso** sia accessibile fino a quando il token del dispositivo non è stato configurato correttamente. È possibile separare l'accesso dalla registrazione push, purché la prima azione avvenga prima della seconda.

11. In ViewController. m usare i frammenti di codice seguenti per implementare il metodo di azione per il pulsante di **accesso** e un metodo per inviare il messaggio di notifica tramite il back-end ASP.NET.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Aggiornare l'azione per il pulsante **Send Notification** per usare il back-end ASP.NET e inviare a qualsiasi PNS abilitato da un'opzione.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. Nella funzione `ViewDidLoad` aggiungere quanto segue per creare l'istanza di `RegisterClient` e impostare il delegato per i campi di testo.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. In `AppDelegate.m` rimuovere tutto il contenuto del metodo `application:didRegisterForPushNotificationWithDeviceToken:` e sostituirlo con il seguente (per assicurarsi che il controller di visualizzazione contenga il token del dispositivo più recente recuperato da Apple Push Notification Service):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Infine in `AppDelegate.m` verificare che sia presente il metodo seguente:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Test dell'applicazione

1. In XCode eseguire l'app su un dispositivo iOS fisico (le notifiche push non funzionano nel simulatore).
2. Nell'interfaccia utente dell'app per iOS immettere lo stesso valore per il nome utente e per la password. Fare quindi clic su **Accedi**.

    ![Test dell'applicazione iOS][2]

3. Verrà visualizzata una finestra popup che informa che la registrazione è stata completata. Fare clic su **OK**.

    ![Notifica del test iOS visualizzata][3]

4. Nel campo di testo **Recipient username tag* (Tag nome utente destinatario) immettere il tag del nome utente usato con la registrazione da un altro dispositivo.
5. Immettere un messaggio di notifica e fare clic su **Send Notification**. Solo i dispositivi che hanno una registrazione con il tag del nome utente del destinatario riceveranno il messaggio di notifica. Viene inviato solo a tali utenti.

    ![Notifica con tag del test iOS][4]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come inviare notifiche push a utenti specifici che hanno tag associati alle loro registrazioni. Per informazioni sulle procedure per effettuare il push di notifiche in base alla posizione, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche basate sulla posizione](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
