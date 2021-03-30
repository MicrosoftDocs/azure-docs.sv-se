---
title: Skicka lokaliserade push-meddelanden till iOS med Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du använder push-lokaliserade meddelanden till iOS-enheter med hjälp av Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a78d3a76e2b13a120e9e744e181c95bfcb330f27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92313914"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Självstudie: skicka lokaliserade push-meddelanden till iOS med Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Den här självstudien visar hur du använder funktionen [mallar](notification-hubs-templates-cross-platform-push-messages.md) i Azure Notification Hubs för att skicka meddelanden om att skicka meddelanden som har lokaliserats av språk och enhet. I den här självstudien börjar du med iOS-appen som du skapade i [använd Notification Hubs för att skicka de senaste nyheterna]. När du är klar kan du registrera dig för kategorier som du är intresse rad av, ange ett språk som du vill ta emot meddelanden från och endast ta emot push-meddelanden för de valda kategorierna på det språket.

Det finns två delar i det här scenariot:

* med iOS-appen kan klient enheter ange ett språk och prenumerera på olika avbrutna nyhets kategorier.
* Backend-servern skickar meddelanden med hjälp av funktionerna för **taggar** och **mallar** i Azure Notification Hubs.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Uppdatera app-användargränssnittet
> * Bygg iOS-appen
> * Skicka meddelanden om lokaliserade mallar från .NET-konsol program
> * Skicka meddelanden om lokaliserade mallar från enheten

## <a name="overview"></a>Översikt

När du [använder Notification Hubs för att skicka]avbrutna nyheter skapade du en app som använde **taggar** för att prenumerera på meddelanden för olika nyhets kategorier. Många appar är dock riktade till flera marknader och kräver lokalisering. Det innebär att innehållet i själva meddelandena måste lokaliseras och levereras till rätt uppsättning enheter. I den här självstudien får du lära dig hur du använder **mall** -funktionen i Notification Hubs för att enkelt leverera lokaliserade nyhets meddelanden.

> [!NOTE]
> Ett sätt att skicka lokaliserade meddelanden är att skapa flera versioner av varje tagg. Om du till exempel vill stödja engelska, franska och mandariner behöver du tre olika taggar för World nyheterna: "world_en", "world_fr" och "world_ch". Sedan måste du skicka en lokaliserad version av World nyheterna till var och en av dessa taggar. I det här avsnittet använder du mallar för att undvika spridning av taggar och kravet på att skicka flera meddelanden.

Mallar är ett sätt att ange hur en speciell enhet ska ta emot ett meddelande. Mallen anger det exakta nyttolastformatet genom att referera till egenskaper som ingår i meddelandet som skickas av appserverdelen. I ditt fall skickar du ett oberoende-meddelande som innehåller alla språk som stöds:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Sedan ser du till att enheterna registreras med en mall som refererar till rätt egenskap. Till exempel en iOS-app som vill registrera för franska nyhets register med hjälp av följande syntax:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Mer information om mallar finns i avsnittet [mallar](notification-hubs-templates-cross-platform-push-messages.md) .

## <a name="prerequisites"></a>Förutsättningar

* Slutför självstudien om [push-meddelanden till specifika iOS-enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md) och har koden tillgänglig, eftersom den här självstudien bygger direkt på koden.
* Visual Studio 2019 är valfritt.

## <a name="update-the-app-user-interface"></a>Uppdatera app-användargränssnittet

I det här avsnittet ändrar du den nya appen med nya nyheter som du skapade i avsnittet [använd Notification Hubs för att skicka de senaste] nyheterna för att skicka översatta nyheter med mallar.

I `MainStoryboard_iPhone.storyboard` lägger du till en segmenterad kontroll med de tre språken: engelska, franska och mandariner.

![Skapar iOS-ANVÄNDARGRÄNSSNITTets storyboard][13]

Se sedan till att lägga till en IBOutlet i din ViewController. h som visas i följande bild:

![Skapa utsändnings möjligheter för växlar][14]

## <a name="build-the-ios-app"></a>Bygg iOS-appen

1. I `Notification.h` lägger du till- `retrieveLocale` metoden och ändrar lagrings-och prenumerations metoderna enligt följande kod:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    I din `Notification.m` ändrar du `storeCategoriesAndSubscribe` metoden genom att lägga till `locale` parametern och lagra den i användarens standardvärden:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Ändra sedan *prenumerations* metoden så att den inkluderar språkvarianten:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Du använder-metoden `registerTemplateWithDeviceToken` i stället för `registerNativeWithDeviceToken` . När du registrerar dig för en mall måste du ange JSON-mallen och ett namn för mallen (som appen kanske vill registrera olika mallar). Se till att registrera dina kategorier som taggar, som du vill se till att ta emot meddelanden för dessa nyheter.

    Lägg till en metod för att hämta språk inställningarna från användarnas standardinställningar:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Nu när du ändrade `Notifications` klassen, måste du se till att `ViewController` använda den nya `UISegmentControl` . Lägg till följande rad i- `viewDidLoad` metoden för att se till att visa de språk som är markerade för tillfället:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    `subscribe`Ändra sedan ditt anrop till följande kod i din metod `storeCategoriesAndSubscribe` :

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Slutligen måste du uppdatera- `didRegisterForRemoteNotificationsWithDeviceToken` metoden i din AppDelegate. m så att du kan uppdatera registreringen korrekt när appen startar. Ändra ditt anrop till `subscribe` metoden för meddelanden med följande kod:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>valfritt Skicka meddelanden om lokaliserade mallar från .NET-konsol program

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>valfritt Skicka meddelanden om lokaliserade mallar från enheten

Om du inte har åtkomst till Visual Studio, eller om du bara vill testa att skicka meddelanden om lokaliserade mallar direkt från appen på enheten. Du kan lägga till lokaliserade mallparametrar till den `SendNotificationRESTAPI` metod som du definierade i föregående självstudie.

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
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

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

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skickat lokaliserade meddelanden till iOS-enheter. Om du vill lära dig att skicka meddelanden till vissa användare av iOS-appar går du vidare till följande självstudie:

> [!div class="nextstepaction"]
>[Skicka push-meddelanden till specifika användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/reference/dn223264(v=azure.100)