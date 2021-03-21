---
title: Skicka push-meddelanden till vissa användare med hjälp av Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du skickar push-meddelanden till vissa iOS-användare med hjälp av Azure Notification Hubs.
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016969"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Självstudie: skicka push-meddelanden till vissa användare med Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Denna självstudie visar hur du använder Azure Notification Hubs till att skicka push-meddelanden till en specifik appanvändare på en specifik enhet. En ASP.NET WebAPI-Server del används för att autentisera klienter och generera meddelanden, som du ser i hjälp avsnittet [Registrera från din app server](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)del.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett WebAPI-projekt
> * Autentisera klienter mot WebAPI-serverdelen
> * Registrera för meddelanden med hjälp av WebAPI-serverdelen
> * Skicka meddelanden från WebAPI-serverdelen
> * Publicera den nya WebAPI-serverdelen
> * Ändra din iOS-app
> * Testa programmet

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du har skapat och konfigurerat din Notification Hub enligt beskrivningen i [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md). Den här själv studie kursen är också nödvändig för självstudierna [säker push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Om du vill använda Mobile Apps som backend-tjänst, se [Mobile Apps kom igång med push](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Ändra din iOS-app

1. Öppna den enkla sidan Visa app som du skapade i guiden [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md) själv studie kursen.

   > [!NOTE]
   > Det här avsnittet förutsätter att ditt projekt har kon figurer ATS med ett tomt organisations namn. Om inte, lägga du ditt organisations namn till alla klass namn.

2. I `Main.storyboard` filen lägger du till de komponenter som visas i skärm bilden från objekt biblioteket.

    ![Redigera storyboard i Xcode Interface Builder][1]

   * **Användar namn**: en UITextField med platshållartext, *Ange användar namn* direkt under etiketten skicka resultat och begränsas till vänster och höger marginaler och under etiketten skicka resultat.
   * **Lösen ord**: ett UITextField med platshållartext, *Ange lösen ordet* direkt under textfältet username och begränsat till vänster och höger marginaler och under text fältet användar namn. Markera alternativet för **säker text inmatning** i modulen attribut under *RETUR nyckel*.
   * **Logga in**: en UIButton som märks direkt under fältet lösen ords text och avmarkerar alternativet **aktive rad** i kontrollen attribut under *kontroll-Content*
   * **WNS**: etikett och växel för att aktivera sändning av Notification Service för meddelande tjänsten om den har kon figurer ATS i hubben. Se själv studie kursen om [Windows komma igång](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
   * **GCM**: etikett och växel för att aktivera sändning av meddelandet till Google Cloud Messaging om det har kon figurer ATS i hubben. Se själv studie kursen om [Android komma igång](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APN**: etikett och växel för att aktivera sändning av meddelandet till Apple Platform Notification Service.
   * **Mottagarens användar namn: en** UITextField med platshållartext, *taggen mottagarens användar namn*, direkt under etiketten GCM och är begränsad till vänster och höger MARGINALer och under GCM-etiketten.

     Vissa komponenter har lagts till i guiden [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md) själv studie kursen.

3. **CTRL** dra från komponenterna i vyn till `ViewController.h` och Lägg till dessa nya utsändnings möjligheter:

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

4. I `ViewController.h` lägger du till följande `#define` efter dina import-instruktioner. Ersätt `<Your backend endpoint>` plats hållaren med den mål-URL som du använde för att distribuera appens Server del i föregående avsnitt. Till exempel `http://your_backend.azurewebsites.net` :

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. I ditt projekt skapar du en ny enhets touch `RegisterClient` -klass med namnet till gränssnittet med ASP.net-backend som du skapade. Skapa klassen som ärver från `NSObject` . Lägg sedan till följande kod i `RegisterClient.h` :

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. I `RegisterClient.m` uppdaterar du `@interface` avsnittet:

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

7. Ersätt `@implementation` avsnittet i RegisterClient. m med följande kod:

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

    Den här koden implementerar logiken som beskrivs i vägledningen som [registreras från din app-backend](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) med NSURLSession för att utföra rest-anrop till din app-backend och NSUserDefaults för att lokalt lagra registrationId som returnerades av Notification Hub.

    Den här klassen kräver `authorizationHeader` att egenskapen anges för att fungera korrekt. Den här egenskapen anges av `ViewController` klassen efter inloggningen.

8. I `ViewController.h` lägger du till en `#import` instruktion för `RegisterClient.h` . Lägg sedan till en deklaration för enhetens token och referens till en `RegisterClient` instans i `@interface` avsnittet:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. I ViewController. m lägger du till en deklaration för privat metod i `@interface` avsnittet:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Följande kodfragment är inte ett säkert autentiseringsschema, du bör ersätta implementeringen av    `createAndSetAuthenticationHeaderWithUsername:AndPassword:` med din speciella autentiseringsmekanism som genererar en autentiseringstoken som ska användas av den registrerade klient klassen, t. ex. OAuth, Active Directory.

10. I `@implementation` avsnittet i `ViewController.m` lägger du till följande kod, som lägger till implementeringen för att ange enhets-token och Authentication-huvudet.

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

    Observera hur du anger enhets-token som aktiverar knappen **Logga in** . Det beror på att som en del av inloggnings åtgärden registrerar visnings styrenheten för push-meddelanden med appens Server del. Du vill inte att **inloggnings** åtgärden ska vara tillgänglig förrän enhetens token har kon figurer ATS korrekt. Du kan koppla från inloggningen från push-registreringen så länge den tidigare inträffar före den senare.

11. I ViewController. m använder du följande kodfragment för att implementera åtgärds metoden för **inloggnings** knappen och en metod för att skicka meddelandet med hjälp av ASP.net-backend-servern.

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

12. Uppdatera åtgärden för knappen **Skicka meddelande** för att använda ASP.NET-Server delen och skicka till valfri PNS som aktive ras av en växel.

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

13. I `ViewDidLoad` funktionen lägger du till följande för att instansiera `RegisterClient` instansen och ange ombudet för dina textfält.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Ta nu `AppDelegate.m` bort allt innehåll för metoden i `application:didRegisterForPushNotificationWithDeviceToken:` och ersätt den med följande (för att se till att visnings styrenheten innehåller den senaste enhets-token som hämtats från APN):

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

15. `AppDelegate.m`Se till att du har följande metod:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testa programmet

1. I XCode kör du appen på en fysisk iOS-enhet (push-meddelanden fungerar inte i simulatorn).
2. Ange samma värde för både användar namn och lösen ord i iOS-appens användar gränssnitt. Klicka sedan på **Logga in**.

    ![test program för iOS][2]

3. Du bör se ett popup-meddelande som informerar dig om att registreringen är klar. Klicka på **OK**.

    ![test meddelande för iOS visas][3]

4. I text fältet **mottagarens användar* namn anger du taggen User Name som används vid registreringen från en annan enhet.
5. Ange ett meddelande och klicka på **Skicka meddelande**. Endast de enheter som har en registrering med taggen mottagarens användar namn får meddelandet. Den skickas endast till dessa användare.

    ![meddelande om att iOS-test har taggats][4]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om push-meddelanden till specifika användare som har taggar associerade med sina registreringar. Information om hur du skickar platsbaserade meddelanden finns i nästa självstudie:

> [!div class="nextstepaction"]
>[Skicka platsbaserade push-meddelanden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
