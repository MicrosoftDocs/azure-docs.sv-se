---
title: Azure Notification Hubs säker push för Windows
description: Lär dig hur du skickar säkra push-meddelanden i Azure. Kodexempel som skrivits i C# med hjälp av .NET-API.
author: sethmanheim
manager: femila
editor: thsomasu
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 98e587103e63cd5cc26eab5b00864d00e0b9007f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019436"
---
# <a name="send-secure-push-notifications-from-azure-notification-hubs"></a>Skicka säkra push-meddelanden från Azure Notification Hubs

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

Det är viktigt att Observera att i föregående flöde (och i den här självstudien) förutsätter vi att enheten lagrar en autentiseringstoken i lokal lagring efter att användaren har loggat in. Detta garanterar en helt sömlös upplevelse eftersom enheten kan hämta meddelandets säkra nytto last med denna token. Om ditt program inte lagrar autentiseringstoken på enheten, eller om dessa token kan ha upphört att gälla, bör enhetens app vid mottagandet av meddelandet Visa ett allmänt meddelande där användaren uppmanas att starta appen. Appen autentiserar sedan användaren och visar meddelande nytto lasten.

I den här självstudien visas hur du skickar ett push-meddelande på ett säkert sätt. Själv studie Kursen bygger vidare på själv studie kursen om att [meddela användarna](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , så du bör slutföra stegen i den här självstudien först.

> [!NOTE]
> I den här självstudien förutsätter vi att du har skapat och konfigurerat din Notification Hub enligt beskrivningen i [skicka meddelanden till universell Windows-plattform appar](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Observera också att Windows Phone 8,1 kräver Windows-autentiseringsuppgifter (inte Windows Phone) och att bakgrunds aktiviteterna inte fungerar på Windows Phone 8,0 eller Silverlight 8,1. För Windows Store-program kan du få aviseringar via en bakgrunds aktivitet endast om appen är aktive rad (Klicka i kryss rutan i appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Ändra Windows Phone-projektet

1. I **NotifyUserWindowsPhone** -projektet lägger du till följande kod i app. XAML. cs för att registrera push-bakgrunds aktiviteten. Lägg till följande kodrad i slutet av metoden `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```

2. Lägg fortfarande till följande kod direkt efter metoden i app. XAML. cs `OnLaunched()` :

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```

3. Lägg till följande- `using` instruktioner överst i filen app. XAML. CS:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```

4. Klicka på **Spara alla** från menyn **Arkiv** i Visual Studio.

## <a name="create-the-push-background-component"></a>Skapa komponenten för push-bakgrund

Nästa steg är att skapa komponenten push-bakgrund.

1. I Solution Explorer högerklickar du på noden på översta nivån i lösningen (**lösningens SecurePush** i det här fallet) och klickar sedan på **Lägg till** och sedan på **nytt projekt**.
2. Expandera **Store-appar**, klicka på **Windows Phone appar** och klicka sedan på **Windows Runtime komponent (Windows Phone)**. Namnge projektet **PushBackgroundComponent** och klicka sedan på **OK** för att skapa projektet.

    ![Skärm bild av dialog rutan Lägg till nytt projekt med alternativet Windows Runtime komponent (Windows Phone) Visual C# markerat.][12]
3. I Solution Explorer högerklickar du på projektet **PushBackgroundComponent (Windows Phone 8,1)** och klickar sedan på **Lägg till** och sedan på **klass**. Ge den nya klassen ett namn `PushBackgroundTask.cs` . Klicka på **Lägg till** för att skapa klassen.
4. Ersätt hela innehållet i `PushBackgroundComponent` namn områdes definitionen med följande kod och ersätt plats hållaren `{back-end endpoint}` med backend-slutpunkten när du distribuerade Server delen:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```

5. I Solution Explorer högerklickar du på projektet **PushBackgroundComponent (Windows Phone 8,1)** och klickar sedan på **Hantera NuGet-paket**.
6. Klicka på **Online** till vänster.
7. Skriv **Http-klient** i rutan **Sök**.
8. I listan resultat klickar du på **Microsoft http klient bibliotek** och klickar sedan på **Installera**. Slutför installationen.
9. Gå tillbaka till rutan **Sök** i NuGet och skriv **Json.net**. Installera **JSON.net** -paketet och stäng sedan fönstret NuGet Package Manager.
10. Lägg till följande- `using` uttryck högst upp i `PushBackgroundTask.cs` filen:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```

11. I Solution Explorer i projektet **NotifyUserWindowsPhone (Windows Phone 8,1)** högerklickar du på **referenser** och klickar sedan på **Lägg till referens...**. I dialog rutan referens hanterare markerar du kryss rutan bredvid **PushBackgroundComponent** och klickar sedan på **OK**.
12. I Solution Explorer dubbelklickar du på **Package. appxmanifest** i **NotifyUserWindowsPhone-projektet (Windows Phone 8,1)** . Under **aviseringar** ställer du in **popup-funktion** till **Ja**.

    ![Skärm bild av Solution Explorers fönstret som fokuserar på Package. appxmanifest med alternativet för popup-alternativ inställt på Ja visas i rött.][3]
13. Klicka fortfarande på menyn **deklarationer** längst upp i **Package. appxmanifest**. I list rutan **tillgängliga deklarationer** klickar du på **bakgrunds aktiviteter** och sedan på **Lägg till**.
14. I **Package. appxmanifest**, under **Egenskaper**, kontrollerar du **push-meddelande**.
15. I **Package. appxmanifest** under **appinställningar** skriver du **PushBackgroundComponent. PushBackgroundTask** i fältet **Start punkt** .

    ![Skärm bild av Solution Explorers fönstret som fokuserar på Package. appxmanifest med tillgängliga deklarationer, deklarationer som stöds, push-meddelanden och start punkts alternativ som beskrivs i rött.][13]
16. Från **Arkiv**-menyn klickar du på **Spara alla**.

## <a name="run-the-application"></a>Kör programmet

Gör så här för att köra programmet:

1. I Visual Studio kör du **AppBackend** Web API-programmet. En ASP.NET-webbsida visas.
2. I Visual Studio kör du appen **NotifyUserWindowsPhone (Windows Phone 8,1)** Windows Phone. Windows Phone-emulatorn kör och laddar appen automatiskt.
3. Ange ett användar namn och lösen ord i **NotifyUserWindowsPhone** -appens användar gränssnitt. Det kan vara valfri sträng, men de måste vara samma värde.
4. I **NotifyUserWindowsPhone** -appens användar gränssnitt klickar du på **Logga in och registrera**. Klicka sedan på **skicka push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
