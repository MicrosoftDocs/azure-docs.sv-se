---
title: Skicka push-meddelanden till iOS med Azure Notification Hubs och iOS SDK-versionen 3.0.0 Preview 1
description: I den här självstudien får du lära dig hur du använder Azure Notification Hubs och Apple Push Notification Service för att skicka push-meddelanden till iOS-enheter (version 3.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628327"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Självstudie: skicka push-meddelanden till iOS-appar med Azure Notification Hubs SDK för Apple

Den här självstudien visar hur du använder Azure Notification Hubs för att skicka push-meddelanden till ett iOS-program med hjälp av Azure Notification Hubs SDK för Apple.

Den här självstudien omfattar följande steg:

- Skapa en exempel-iOS-app.
- Anslut din iOS-app till Azure Notification Hubs.
- Skicka test-push-meddelanden.
- Verifiera att appen tar emot meddelanden.

Du kan ladda ned den fullständiga koden för den här självstudien [från GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande förutsättningar:

- En Mac som kör [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt certifikat för utvecklare som installerats i din nyckel Ring.
- En iPhone eller iPad som kör iOS version 10 eller senare.
- Din fysiska enhet som är registrerad i [Apples Portal](https://developer.apple.com/)och kopplad till ditt certifikat.

Innan du fortsätter bör du gå igenom föregående självstudie om att komma igång med [Azure Notification Hubs för iOS-appar](ios-sdk-get-started.md), för att konfigurera och konfigurera push-autentiseringsuppgifter i Notification Hub. Även om du inte har någon tidigare erfarenhet av iOS-utveckling bör du kunna följa dessa steg.

> [!NOTE]
> På grund av konfigurations krav för push-meddelanden måste du distribuera och testa push-meddelanden på en fysisk iOS-enhet (iPhone eller iPad) i stället för iOS-emulatorn.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Anslut iOS-appen till Notification Hubs

1. Skapa ett nytt iOS-projekt i Xcode och välj mallen **Program enkel vy**.

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Välj mall":::

2. När du anger alternativ för ditt nya projekt, ska du använda samma **produktnamn** och **organisations-ID** som du använde när du gjorde inställningarna för ID:t för programpaket på Apple Developer-portalen.

3. Under projekt navigering väljer du projekt namnet under **mål** och väljer sedan fliken **signerings & funktioner** . Se till att du väljer rätt **team** för ditt Apple Developer-konto. XCode bör automatiskt hämta den etableringsprofil du har skapat tidigare baserat på paket-ID.

   Om du inte ser den nya etableringsprofil som du skapade i Xcode, kan du försöka uppdatera profilerna för din signeringsidentitet. Klicka på **Xcode** på menyraden, sedan på **Inställningar** och på fliken **Konto**. Därefter klickar du på knappen **Visa detaljer** och sedan på din signeringsidentitet. Slutligen klickar du på uppdateringsknappen i det nedre högra hörnet.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Visa detaljer":::

4. På fliken **signerings & funktioner** väljer du **+ funktion**. Dubbelklicka på **push-meddelanden** för att aktivera det.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Kapacitet":::

5. Lägg till Azure Notification Hubs SDK-modulerna.

   Du kan integrera Azure Notification Hubs SDK i din app med hjälp av [Cocoapods](https://cocoapods.org/) eller genom att manuellt lägga till binärfilerna i projektet.

   - Integrering via Cocoapods: Lägg till följande beroenden till din Podfile för att inkludera Azure Notification Hubs SDK i din app:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Kör Pod install för att installera den nyligen definierade Pod och öppna din. xcworkspace.

         Om du ser ett fel meddelande om **att det inte gick att hitta en specifikation för AzureNotificationHubs-iOS** när du kör Pod install kör `pod repo update` du för att hämta de senaste poddar från Cocoapods-databasen och kör sedan Pod install.

   - Integrering via Carthage: Lägg till följande beroenden till din Cartfile för att inkludera Azure Notification Hubs SDK i din app:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Uppdatera sedan versions beroenden:

      ```shell
      $ carthage update
      ```

      Mer information om hur du använder Carthage finns i [Carthage GitHub-lagringsplatsen](https://github.com/Carthage/Carthage).

   - Integrering genom att kopiera binärfilerna till projektet:

      Du kan integrera genom att kopiera binärfilerna till projektet enligt följande:

        - Hämta [Azure Notification HUBS SDK](https://github.com/Azure/azure-notificationhubs-iOS/releases/) Framework som tillhandahålls som en zip-fil och packa upp den.

        - Högerklicka på ditt projekt i Xcode och klicka sedan på alternativet **Lägg till filer i** för att lägga till mappen **WindowsAzureMessaging.framework** till ditt Xcode-projekt. Välj **Alternativ** och se till att **Kopiera objekt vid behov** är markerat och klicka sedan på **Lägg till**.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Lägg till ramverk":::

6. Lägg till eller redigera en fil med namnet **DevSettings. plist** som innehåller två egenskaper `CONNECTION_STRING` för anslutnings strängen till Azure Notification Hub och `HUB_NAME` för namnet på Azure Notification Hub.

7. Lägg till information för att ansluta till Azure Notification Hubs i lämpligt `<string></string>` avsnitt.  Ersätt plats hållarna för sträng litteraler `--HUB-NAME--` och `--CONNECTION-STRING--` med hubb-och **DefaultListenSharedAccessSignature** som du tidigare har hämtat från portalen:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

8. Ersätt all kod efter följande kod i samma **AppDelegate. m** -fil `didFinishLaunchingWithOptions` :

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Den här koden ansluter till Notification Hub med anslutnings informationen som du angav i **DevSettings. plist**. Den ger sedan enhets-token till Notification Hub, så att hubben kan skicka meddelanden.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Skapa rubrik fil för NotificationDetailViewController

1. I likhet med föregående instruktioner lägger du till en annan rubrik fil med namnet **SetupViewController. h**. Ersätt innehållet i den nya rubrik filen med följande kod:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Lägg till implementerings filen **SetupViewController. m**. Ersätt innehållet i filen med följande kod, som implementerar UIViewController-metoderna:

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Skapa och kör appen på din enhet för att kontrollera att det inte finns några fel.

## <a name="send-test-push-notifications"></a>Skicka test-push-meddelanden

Du kan testa att ta emot meddelanden i appen med alternativet **Skicka test** i [Azure Portal](https://portal.azure.com/). Den skickar ett test-push-meddelande till enheten.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Testa att skicka":::

Push-meddelanden skickas vanligtvis via en serverdelstjänst, till exempel Mobile Apps eller ASP.NET, med hjälp av ett kompatibelt bibliotek. Om ett bibliotek inte är tillgängligt för Server delen kan du också använda REST API direkt för att skicka meddelanden.

Här är en lista över några andra själv studie kurser som du kanske vill granska för att skicka meddelanden:

- Azure-Mobile Apps: ett exempel på hur du skickar meddelanden från en Mobile Apps Server del som är integrerad med Notification Hubs finns i [lägga till push-meddelanden till din iOS-app](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: [använd Notification Hubs för att skicka push-meddelanden till användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Azure Notification Hubs Java SDK: Se [Använda Notification Hubs från Java](notification-hubs-java-push-notification-tutorial.md) för att skicka meddelanden från Java. Det här har testats i Eclipse för Android-utveckling.
- PHP: [Använda Notification Hubs från PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Verifiera att din app tar emot push-meddelanden

Om du vill testa push-meddelanden på iOS måste du distribuera appen till en fysisk iOS-enhet. Du kan inte skicka Apple Push-meddelanden med hjälp av iOS-simulatorn.

1. Kör appen och verifiera att registreringen kan genomföras. Tryck sedan på **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Registrera":::

2. Skicka sedan ett test-push-meddelande från [Azure Portal](https://portal.azure.com/), enligt beskrivningen i föregående avsnitt.

3. Push-meddelandet skickas till alla enheter som har registrerats för att ta emot meddelanden från den aktuella Notification Hub.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Skicka test":::

## <a name="next-steps"></a>Nästa steg

I det här enkla exemplet skickar du push-meddelanden till alla dina registrerade iOS-enheter. Om du vill veta hur du skickar push-meddelanden till vissa iOS-enheter går du vidare till följande självstudie:

[Självstudie: push-meddelanden till vissa enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Mer information finns i följande artiklar:

- [Översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST-API: er](/rest/api/notificationhubs/)
- [Notification Hubs SDK för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK på GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrera dig med programmets Server del](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registreringshantering](notification-hubs-push-notification-registration-management.md)
- [Arbeta med Taggar](notification-hubs-tags-segment-push-message.md)
- [Arbeta med anpassade mallar](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus åtkomst kontroll med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md)
- [Generera SAS-token via programmering](/rest/api/eventhub/generate-sas-token)
- [Apple-säkerhet: gemensam kryptering](https://developer.apple.com/security/)
- [Tid för UNIX-tid](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)