---
title: Registrera den aktuella användaren för push-meddelanden med hjälp av webb-API | Microsoft Docs
description: Lär dig hur du begär registrering av push-meddelanden i en iOS-app med Azure Notification Hubs när registreringen utförs av ASP.NET webb-API.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0819f5196fffca25a840dc16d1df04cdd0a55029
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86223320"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrera den aktuella användaren för push-meddelanden med ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Översikt

Det här avsnittet visar hur du begär registrering av push-meddelanden med Azure Notification Hubs när registreringen utförs av ASP.NET webb-API. I det här avsnittet utökas självstudien [meddela användare med Notification Hubs]. Du måste redan ha slutfört de steg som krävs i den här självstudien för att skapa den autentiserade mobil tjänsten. Mer information om scenariot meddela användare finns i [meddela användare med Notification Hubs].

## <a name="update-your-app"></a>Uppdatera din app

1. I MainStoryboard_iPhone. storyboard lägger du till följande komponenter från objekt biblioteket:

   * **Label**: "push to User with Notification Hubs"
   * **Label**: "InstallationId"
   * **Etikett**: "användare"
   * **Textfält**: "användare"
   * **Label**: "Password"
   * **Textfält**: "Password"
   * **Knapp**: "inloggning"

     I det här läget ser din storyboard ut ungefär så här:

     ![Skärm bild av appen MainStoryboard_iPhone. storyboard med de komponenter som lagts till.][0]

2. I assistent redigeraren skapar du utlämnade alternativ för alla växlade kontroller och anropar dem, ansluter textfälten med visnings styrenheten (ombud) och skapar en **åtgärd** för knappen **Logga in** .

    ![Skärm bild av assistent redigeraren i MainStoryboard_iPhone. storyboard-appen][1]

    Din BreakingNewsViewController. h-fil bör nu innehålla följande kod:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Skapa en klass med namnet `DeviceInfo` och kopiera följande kod till avsnittet Interface i filen deviceinfo. h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Kopiera följande kod i avsnittet implementering i filen DeviceInfo. m:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. I PushToUserAppDelegate. h lägger du till följande egenskap singleton:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. `didFinishLaunchingWithOptions`Lägg till följande kod i-metoden i PushToUserAppDelegate. m:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Den första raden initierar `DeviceInfo` singleton. Den andra raden startar registreringen för push-meddelanden, som redan finns om du redan har slutfört guiden [Kom igång med Notification Hubs] .
7. I PushToUserAppDelegate. m implementerar du metoden `didRegisterForRemoteNotificationsWithDeviceToken` i din AppDelegate och lägger till följande kod:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Detta anger enhets-token för begäran.

   > [!NOTE]
   > I det här läget ska det inte finnas någon annan kod i den här metoden. Om du redan har ett anrop till `registerNativeWithDeviceToken` metoden som lades till när du slutförde [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md) själv studie kursen, måste du kommentera ut eller ta bort anropet.

8. I `PushToUserAppDelegate.m` filen lägger du till följande hanterare-metod:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Den här metoden visar en avisering i användar gränssnittet när appen tar emot meddelanden när den körs.

9. Öppna `PushToUserViewController.m` filen och returnera tangent bordet i följande implementering:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. I `viewDidLoad` -metoden i `PushToUserViewController.m` filen initierar du etiketten på `installationId` följande sätt:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Lägg till följande egenskaper i gränssnittet i `PushToUserViewController.m` :

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Lägg sedan till följande implementering:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Kopiera följande kod till `login` hanterings metoden som skapats av Xcode:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Den här metoden hämtar både ett installations-ID och en kanal för push-meddelanden och skickar den, tillsammans med enhets typen, till den autentiserade webb-API-metoden som skapar en registrering i Notification Hubs. Detta webb-API definierades i [meddela användare med Notification Hubs].

Nu när klient programmet har uppdaterats går du tillbaka till [meddela användarna med Notification Hubs] och uppdaterar mobil tjänsten för att skicka meddelanden med hjälp av Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Meddela användare med Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Kom igång med Notification Hubs]: ios-sdk-get-started.md
