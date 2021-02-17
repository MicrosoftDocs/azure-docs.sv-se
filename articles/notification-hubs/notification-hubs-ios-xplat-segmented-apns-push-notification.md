---
title: Skicka push-meddelanden till vissa iOS-enheter med Azure Notification Hubs | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till vissa iOS-enheter.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 2cb979491e247a4d44b9ae9ae27c433fb3f436d1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579227"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Självstudie: skicka push-meddelanden till vissa iOS-enheter med Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt

Den här självstudien visar hur du använder Azure Notification Hubs för att skicka meddelanden om att skicka meddelanden till en iOS-app. När du är klar kan du registrera dig för att dela upp nyheter-kategorier som du är intresse rad av och endast ta emot push-meddelanden för dessa kategorier. Det här scenariot är ett vanligt mönster för många appar där meddelanden måste skickas till olika grupper av användare som tidigare har deklarerat intresse för dem, t.ex. RSS-läsare, appar för musiklyssnare osv.

Du aktiverar sändningsscenarier genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg får de enheter som har registrerats för taggen ett meddelande. Eftersom taggar bara är strängar behöver de inte etableras i förväg. Mer information om taggar finns i [Notification Hubs-routning och tagguttryck](notification-hubs-tags-segment-push-message.md).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Lägg till ett kategori val i appen
> * Skicka taggade meddelanden
> * Skicka meddelanden från enheten
> * Kör appen och generera meddelanden

## <a name="prerequisites"></a>Förutsättningar

Det här avsnittet bygger på den app som du skapade i [Självstudier: push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs][get-started]. Innan du börjar den här självstudien måste du ha slutfört [Självstudier: push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Lägga till kategorival till appen

Det första steget är att lägga till GRÄNSSNITTs elementen i din befintliga storyboard som gör det möjligt för användaren att välja kategorier som ska registreras. De kategorier som valts av en användare lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. I **MainStoryboard_iPhone. storyboard** lägger du till följande komponenter från objekt biblioteket:

   * En etikett med texten "bryta nyheter"
   * Etiketter med kategori texter "världen", "politiken", "Business", "Technology", "vetenskap", "idrotts",
   * Sex växlar, en per kategori, anger att varje växel **tillstånd** ska vara **inaktiverat** som standard.
   * En knapp med etiketten "Prenumerera"

     Din storyboard bör se ut så här:

     ![Xcode Interface Builder][3]

2. I assistent redigeraren skapar du Utskicks alternativ för alla växlar och anropar dem "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Skapa en åtgärd för knappen med namnet `subscribe` . din `ViewController.h` ska innehålla följande kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Skapa en ny **kakao touch-klass** med namnet `Notifications` . Kopiera följande kod i avsnittet Interface i filen Notifications. h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Lägg till följande import direktiv i meddelanden. m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Kopiera följande kod i avsnittet implementering i filen Notifications. m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Den här klassen använder lokal lagring för att lagra och hämta de kategorier av nyheter som enheten tar emot. Det innehåller också en metod för att registrera för dessa kategorier med hjälp av en [mall](notification-hubs-templates-cross-platform-push-messages.md) registrering.

7. I `AppDelegate.h` filen lägger du till en import-sats för `Notifications.h` och lägger till en egenskap för en instans av `Notifications` klassen:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. I `didFinishLaunchingWithOptions` -metoden i `AppDelegate.m` lägger du till koden för att initiera meddelande instansen i början av metoden.  
    `HUBNAME` och `HUBLISTENACCESS` (definieras i `hubinfo.h` ) ska redan ha `<hub name>` `<connection string with listen access>` plats hållarna och har ersatts med namnet på Notification Hub och anslutnings strängen för *DefaultListenSharedAccessSignature* som du hämtade tidigare

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram vanligtvis inte är säkra bör du bara distribuera nyckeln för lyssningsåtkomst med din klientapp. Lyssningsåtkomst gör det möjligt för din app att registrera sig för meddelanden, men befintliga registreringar kan inte ändras och meddelanden kan inte skickas. Nyckeln för fullständig åtkomst används i en skyddad serverdelstjänst för att skicka meddelanden och ändra befintliga registreringar.

9. I `didRegisterForRemoteNotificationsWithDeviceToken` -metoden i `AppDelegate.m` ersätter du koden i-metoden med följande kod för att skicka enhetens token till- `notifications` klassen. - `notifications` Klassen utför registreringen för meddelanden med kategorier. Om användaren ändrar kategori val, anropar du `subscribeWithCategories` metoden som svar på knappen **Prenumerera** för att uppdatera dem.

    > [!NOTE]
    > Eftersom den enhets-token som tilldelats av Apple Push Notification Service (APN) kan ändras när som helst, bör du registrera dig för meddelanden ofta för att undvika aviserings problem. Det här exemplet registrerar för meddelande varje gång som appen startas. För appar som körs ofta, mer än en gång om dagen, kan du förmodligen hoppa över registreringen och spara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    I det här läget ska det inte finnas någon annan kod i `didRegisterForRemoteNotificationsWithDeviceToken` metoden.

10. Följande metoder bör redan finnas i `AppDelegate.m` att slutföra självstudierna [Kom igång med Notification Hubs][get-started] . Om inte, lägger du till dem.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Den här metoden hanterar meddelanden som tas emot när appen körs genom att visa en enkel **UIAlert**.

11. I `ViewController.m` lägger du till en `import` instruktion för `AppDelegate.h` och kopierar följande kod till den Xcode-genererade `subscribe` metoden. Den här koden uppdaterar meddelande registreringen så att den använder de nya kategori koder som användaren har valt i användar gränssnittet.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Den här metoden skapar en `NSMutableArray` av kategorierna och använder `Notifications` klassen för att lagra listan i den lokala lagrings platsen och registrerar motsvarande Taggar i Notification Hub. När kategorier ändras återskapas registreringen med de nya kategorierna.

12. I `ViewController.m` lägger du till följande kod i- `viewDidLoad` metoden för att ange användar gränssnittet baserat på de tidigare sparade kategorierna.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Appen kan nu lagra en uppsättning kategorier i enhetens lokala lagrings utrymme som används för att registrera dig hos Notification Hub när appen startar. Användaren kan ändra urvalet av kategorier vid körning och klicka på `subscribe` metoden för att uppdatera registreringen för enheten. Sedan uppdaterar du appen för att skicka de nya aviseringarna direkt i själva appen.

## <a name="optional-send-tagged-notifications"></a>valfritt Skicka taggade meddelanden

Om du inte har åtkomst till Visual Studio kan du gå vidare till nästa avsnitt och skicka meddelanden från själva appen. Du kan också skicka rätt mall-meddelande från [Azure Portal] med hjälp av fliken Felsök för Notification Hub.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>valfritt Skicka meddelanden från enheten

Vanliga aviseringar skickas av en server dels tjänst men du kan skicka meddelanden om att skicka meddelanden direkt från appen. Det gör du genom att uppdatera den `SendNotificationRESTAPI` metod som du definierade i själv studie kursen [komma igång med Notification Hubs][get-started] .

1. I `ViewController.m` uppdaterar du `SendNotificationRESTAPI` metoden enligt följande så att den accepterar en parameter för kategori tag gen och skickar rätt [mall](notification-hubs-templates-cross-platform-push-messages.md) -meddelande.

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. I `ViewController.m` uppdaterar du `Send Notification` åtgärden som visas i den kod som följer. Så att den skickar meddelanden med varje tagg individuellt och skickar dem till flera plattformar.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Återskapa projektet och se till att du inte har några build-fel.

## <a name="run-the-app-and-generate-notifications"></a>Kör appen och generera meddelanden

1. Tryck på Run (Kör) för att skapa projektet och starta appen. Välj några olika nyhets alternativ att prenumerera på och tryck sedan på knappen **Prenumerera** . Du bör se en dialog ruta som visar att aviseringarna har prenumererat på.

    ![Exempel meddelande på iOS][1]

    När du väljer **Prenumerera** konverterar appen de valda kategorierna till taggar och begär en ny enhets registrering för de valda taggarna från Notification Hub.

2. Ange ett meddelande som ska skickas som avbryter nyheter och tryck sedan på knappen **Skicka meddelande** . Du kan också köra .NET-konsol programmet för att generera meddelanden.

    ![Ändra aviserings inställningar i iOS][2]

3. Varje enhet som prenumererar på nyheter tar emot de nya meddelanden som du nyss skickat.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skickade du broadcast-meddelanden till vissa iOS-enheter som har registrerats för kategorierna. Om du vill lära dig mer om push-meddelanden kan du gå vidare till följande självstudie:

> [!div class="nextstepaction"]
>[Skicka lokaliserade meddelanden](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/dn530749(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[get-started]: ios-sdk-get-started.md
[Azure-portalen]: https://portal.azure.com
