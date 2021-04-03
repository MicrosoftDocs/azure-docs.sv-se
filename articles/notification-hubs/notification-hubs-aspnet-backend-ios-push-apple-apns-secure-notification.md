---
title: Azure Notification Hubs säker push för iOS
description: Lär dig hur du skickar säkra push-meddelanden till en iOS-app från Azure. Kod exempel som skrivits i mål-C och C#.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a6c85ba017656bd312ddfe3d5f6d98014a3dc89a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090354"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs säker push

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

Med stöd för push-meddelanden i Microsoft Azure kan du komma åt en lättanvänd och utskalad push-infrastruktur som kan användas i flera plattformar, vilket avsevärt fören klar implementeringen av push-meddelanden för både konsument-och företags program för mobila plattformar.

På grund av regler eller säkerhets begränsningar kan ett program ibland vilja inkludera något i meddelandet som inte kan skickas via standard infrastrukturen för push-meddelanden. I den här självstudien beskrivs hur du uppnår samma upplevelse genom att skicka känslig information via en säker, autentiserad anslutning mellan klienten het och appens Server del.

På hög nivå är flödet följande:

1. Appens backend-sida:
   * Lagrar säker nytto Last i backend-databasen.
   * Skickar ID för det här meddelandet till enheten (ingen säker information skickas).
2. Appen på enheten när meddelandet tas emot:
   * Enheten kontaktar Server dels förfrågan om säker nytto Last.
   * Appen kan visa nytto lasten som ett meddelande på enheten.

Det är viktigt att Observera att i föregående flöde (och i den här självstudien) förutsätter vi att enheten lagrar en autentiseringstoken i lokal lagring efter att användaren har loggat in. Detta garanterar en sömlös upplevelse eftersom enheten kan hämta meddelandets säkra nytto last med denna token. Om ditt program inte lagrar autentiseringstoken på enheten, eller om dessa token kan ha upphört att gälla, bör enhetens app vid mottagandet av meddelandet Visa ett allmänt meddelande där användaren uppmanas att starta appen. Appen autentiserar sedan användaren och visar meddelande nytto lasten.

Den här säkra självstudien visar hur du skickar ett push-meddelande på ett säkert sätt. Själv studie Kursen bygger vidare på själv studie kursen om att [meddela användarna](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , så du bör slutföra stegen i den här självstudien först.

> [!NOTE]
> Den här självstudien förutsätter att du har skapat och konfigurerat din Notification Hub enligt beskrivningen i [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Ändra iOS-projektet

Nu när du ändrade appens Server del för att skicka bara *ID: t* för ett meddelande måste du ändra din iOS-app för att hantera meddelandet och anropa Server delen för att hämta det säkra meddelandet som ska visas.

För att uppnå det här målet måste vi skriva logiken för att hämta det säkra innehållet från appens Server del.

1. I `AppDelegate.m` kontrollerar du att appen registrerar sig för meddelanden om tyst meddelande så att den bearbetar det meddelande-ID som skickas från Server delen. Lägg till `UIRemoteNotificationTypeNewsstandContentAvailability` alternativet i didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. I `AppDelegate.m` avsnittet Lägg till ett implementering överst med följande deklaration:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Lägg sedan till följande kod i avsnittet implementering och ersätt plats hållaren `{back-end endpoint}` med slut punkten för din backend-server tidigare:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Den här metoden anropar appens Server del för att hämta meddelande innehållet med hjälp av de autentiseringsuppgifter som lagras i delade inställningar.

4. Hantera nu det inkommande meddelandet och Använd metoden ovan för att hämta innehållet som ska visas. Börja med att låta din iOS-app köras i bakgrunden när du tar emot ett push-meddelande. I **Xcode** väljer du ditt app-projekt på den vänstra panelen och klickar sedan på ditt huvud program mål i avsnittet **mål** i fönstret Central.
5. Klicka sedan på fliken **funktioner** överst i det centrala fönstret och markera rutan **fjärraviseringar** .

    ![Skärm bild av XCode med valt app-projekt och fliken funktioner öppen. Kryss rutan fjärraviseringar är markerad.][IOS1]

6. I `AppDelegate.m` Lägg till följande metod för att hantera push-meddelanden:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Observera att det är bättre att hantera de fall då egenskap för Authentication Header saknas eller att avvisas av Server delen. Den specifika hanteringen av dessa fall beror främst på din mål användar upplevelse. Ett alternativ är att visa ett meddelande med en allmän prompt för användaren att autentisera sig för att hämta det faktiska meddelandet.

## <a name="run-the-application"></a>Kör programmet

Gör så här för att köra programmet:

1. I XCode kör du appen på en fysisk iOS-enhet (push-meddelanden fungerar inte i simulatorn).
2. Ange ett användar namn och lösen ord i iOS-appens användar gränssnitt. Det kan vara valfri sträng, men de måste vara samma värde.
3. I iOS-appens användar gränssnitt klickar du på **Logga in**. Klicka sedan på **skicka push**. Du bör se det säkra meddelandet som visas i meddelande centret.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
