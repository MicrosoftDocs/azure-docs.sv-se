---
title: ta med fil
description: ta med filen som innehåller koden för att skapa ett ASP .NET-WebAPI-projekt för serverdel.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6f490b6f25112ed8a10bbd865070bd07ea3ee84f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016970"
---
## <a name="create-the-webapi-project"></a>Skapa ett WebAPI-projekt

I följande avsnitt beskrivs skapandet av en ny ASP.NET-WebAPI-serverdel. Den här processen har tre huvudsakliga syften:

- **Autentisera klienter**: Du lägger till en meddelandehanterare för att autentisera klientbegäranden och associera användaren med begäran.
- **Registrera dig för meddelanden med hjälp av WebAPI-serverdel**: Du lägger till en domänkontrollant för att hantera nya registreringar för en klientenhet för att ta emot meddelanden. Det autentiserade användarnamnet läggs automatiskt till i registreringen som en [tagg](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md).
- **Skicka meddelanden till klienter**: Du lägger till en kontrollant som gör att användare kan utlösa en säker push-överföring till enheter och klienter som är associerade med taggen.

Skapa en ny ASP.NET-WebAPI-serverdel genom att göra följande:

> [!IMPORTANT]
> Om du använder Visual Studio 2015 eller tidigare ska du kontrollera att du har installerat den senaste versionen av NuGet Package Manager för Visual Studios innan du påbörjar den här självstudiekursen.
>
>Börja med att starta Visual Studio. På menyn **Verktyg** väljer du **Tillägg och uppdateringar**. Sök efter **NuGet Package Manager** i din version av Visual Studio och kontrollera att du har den senaste versionen. Om din version inte är den senaste versionen, avinstallera den och installera sedan om NuGet Package Manager.

![Skärm bild av dialog rutan tillägg och uppdateringar med NuGet-paketet hantera för Visual Studios-paketet markerat.][B4]

> [!NOTE]
> Kontrol lera att du har installerat Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) för webbplats distribution.

1. Starta Visual Studio eller Visual Studio Express.

2. Välj **Server Explorer** och logga in på ditt Azure-konto. För att skapa webbplatsresurser på ditt konto, måste du vara inloggad.

3. Högerklicka på Visual Studio-lösningen i Visual Studio, peka på **Lägg till** och klicka på **Nytt projekt**.
4. Expandera **Visual C#**, välj **Webb** och klicka på **ASP.NET-webbprogram**.

5. I rutan **Namn** skriver du **AppBackend** och väljer sedan **OK**.

    ![Fönstret Nytt projekt][B1]

6. I fönstret **Nytt ASP.NET-projekt** väljer du kryssrutan **Web API** och sedan **OK**.

    ![Fönstret Nytt ASP.NET-projekt][B2]

7. I fönstret **Konfigurera Microsoft Azure Web App** väljer du en prenumeration och sedan i listan **App Service plan** gör du någon av följande åtgärder:

    * Välj en app service-plan som du redan har skapat.
    * Välj **Skapa en ny app service-plan**, och skapa sedan en.

   Du behöver ingen databas för den här självstudiekursen. När du har valt app service-plan väljer du **OK** för att skapa projektet.

    ![Konfigurera Microsoft Azure Web App-fönstret][B5]

    Om du inte ser den här sidan för att konfigurera App Service-plan fortsätter du med självstudien. Du kan konfigurera den när du publicerar appen senare. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>Autentisera klienter mot WebAPI-serverdelen

I det här avsnittet skapar du en ny meddelandehanterarklass med namnet **AuthenticationTestHandler** för den nya serverdelen. Den här klassen härleds från [DelegatingHandler](/previous-versions/visualstudio/hh193679(v=vs.118)) och läggs till som en meddelandehanterare så att den kan bearbeta alla begäranden som kommer till serverdelen.

1. I Solution Explorer högerklickar du på projektet **AppBackend** och väljer sedan **Lägg till** och sedan **Klass**.
2. Ge den nya klassen namnet **AuthenticationTestHandler.cs** och välj sedan **Lägg till** för att generera klassen. Den här klassen autentiserar användare med hjälp av *Grundläggande autentisering* för enkelhetens skull. Din app kan använda alla autentiseringsscheman.
3. Lägg till följande `using`-instruktioner i AuthenticationTestHandler.cs:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. Ersätt `AuthenticationTestHandler`-klassdefinitionen i AuthenticationTestHandler.cs med följande kod:

    Hanteraren godkänner begäran om följande tre villkor är uppfyllda:

   * Begäran innehåller en *auktoriseringsrubrik*.
   * Begäran använder *grundläggande* autentisering.
   * Strängen för användarnamn och lösenordssträngen är samma sträng.

   I annat fall avvisas begäran. Den här begäran är inte en riktig autentiserings- och auktoriseringsmetod. Det är bara ett enkelt exempel för den här självstudien.

   Om meddelandebegäran autentiseras och godkänns av `AuthenticationTestHandler`, kopplas användaren med den grundläggande autentiseringen till den aktuella begäran i [HttpContext](/dotnet/api/system.web.httpcontext.current). Senare används användarinformationen i HttpContext av en annan kontrollant (RegisterController) för att lägga till en [tagg](/previous-versions/azure/azure-services/dn530749(v=azure.100)) till meddelanderegistreringsbegäran.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Säkerhetsmeddelande: Klassen `AuthenticationTestHandler` tillhandahåller inte riktig autentisering. Den används endast för att efterlikna grundläggande autentisering och är inte säker. Du måste implementera en säker autentiseringsmekanism i dina tjänster och program i produktionsmiljön.
5. Registrera meddelandehanteraren genom att lägga till följande kod i slutet av `Register`-metoden i klassen **App_Start/WebApiConfig.cs**:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Spara ändringarna.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Registrera för meddelanden med hjälp av WebAPI-serverdelen

I det här avsnittet lägger du till en ny kontrollant till WebAPI-serverdelen som ska hantera begäranden för att registrera en användare och en enhet för meddelanden med hjälp av klientbiblioteket för meddelandehubbar. Kontrollanten lägger till en användartagg för den användare som autentiserades och kopplades till HttpContext av `AuthenticationTestHandler`. Taggen har strängformatet `"username:<actual username>"`.

1. Högerklicka på **AppBackend**-projektet i Solution Explorer och välj sedan **Hantera NuGet-paket**.

2. I den vänstra rutan välj **Online** och sedan, i rutan **Sök** skriver du **Microsoft.Azure.NotificationHubs**.

3. Välj **Microsoft Azure Notification Hubs** i resultatlistan och sedan **Installera**. Slutför installationen och stäng sedan fönstret för NuGet-pakethanteraren.

    Den här åtgärden lägger till en referens i Azure Notification Hubs SDK med hjälp av [Microsoft.Azure.Notification Hubs-NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

4. Skapa en ny klassfil som representerar anslutningen med meddelandehubben som används för att skicka meddelanden. I Solution Explorer högerklickar du på mappen **Modeller**, välj **Lägg till** och sedan **Klass**. Ge den nya klassen namnet **Notifications.cs** och välj sedan **Lägg till** för att generera den nya klassen.

    ![Fönstret Lägg till nytt objekt][B6]

5. Lägg till följande `using`-instruktion överst i Notifications.cs-filen:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Ersätt `Notifications`-klassdefinitionen med följande kod och ersätt de två platshållarna med anslutningssträngen (med fullständig åtkomst) för din meddelandehubb samt hubbnamnet (tillgängligt på [Azure Portal](https://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > Ange **namn** och **DefaultFullSharedAccessSignature** för navet innan du fortsätter. 
    
7. Skapa nu en ny kontrollant med namnet **RegisterController**. I Solution Explorer högerklickar du på mappen **Styrenheter**. Välj sedan **Lägg till** och sedan **Styrenhet**.

8. Välj **Web API 2-styrenhet – tom** och välj sedan **Lägg till**.

    ![Fönstret Lägg till Kodskelett][B7]

9. I rutan **Kontrollnamn** skriver du **RegisterController** som namn på den nya klassen och väljer sedan **Lägg till**.

    ![Fönstret Lägg till styrenhet][B8]

10. Lägg till följande `using`-instruktioner i RegisterController.cs:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Lägg till följande kod i `RegisterController`-klassdefinitionen. Lägg till en användartagg för den användare som är kopplad till HttpContext i den här koden. Användaren autentiserades och kopplades till HttpContext med hjälp av meddelandefiltret som du la till, `AuthenticationTestHandler`. Du kan också lägga till valfria kontroller som kontrollerar att användaren har behörighet att registrera sig för de begärda taggarna.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Spara ändringarna.

## <a name="send-notifications-from-the-webapi-backend"></a>Skicka meddelanden från WebAPI-serverdelen

I det här avsnittet kan du lägga till en ny domänkontrollant som exponerar en metod för klientenheter för att skicka en avisering. Meddelandet baseras på taggen för användarnamnet som använder Azure Notification Hubs .NET-biblioteket i ASP.NET-WebAPI-serverdelen.

1. Skapa en ny domänkontrollant med namnet **NotificationsController** på samma sätt som du skapade **RegisterController** i föregående avsnitt.

2. Lägg till följande `using`-instruktioner i NotificationsController.cs:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Lägg till följande metod i **NotificationsController**-klassen:

    Den här koden skickar en meddelandetyp som är baserad på PNS-parametern (Platform Notification Service) `pns`. Värdet för `to_tag` används för att ställa in *usernamne*-taggen för meddelandet. Den här taggen måste matcha en username-tagg för en aktiv meddelandehubbsregistrering. Meddelandet hämtas från innehållsdelen i POST-begäran och formateras för PNS-måltjänsten.

    Beroende på det PNS-system som dina stödda enheter använder för att ta emot meddelanden, stöds meddelanden i olika format. På Windows-enheter kan du till exempel använda ett [popup-meddelande med WNS](/uwp/schemas/tiles/toastschema/schema-root) som inte stöds direkt av en annan PNS. I dessa fall måste din serverdel alltså formatera meddelandet till ett meddelande som stöds för PNS-tjänsten för enheter som du vill ha stöd för. Därefter använder du lämpligt överförings-API i [NotificationHubClient-klassen](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Tryck på **F5** för att köra programmet och för att kontrollera att allt fungerar som det ska hittills. Appen öppnar en webbläsare och den visas på startsidan för ASP.NET.

## <a name="publish-the-new-webapi-backend"></a>Publicera den nya WebAPI-serverdelen

Därefter distribuerar du appen till en Azure-webbplats så att den är tillgänglig från alla enheter.

1. Högerklicka på **AppBackend**-projektet och välj sedan **Publicera**.

2. Välj **Microsoft Azure App Service** som publiceringsmål och välj sedan \*\*Publicera. Fönstret Skapa App Service öppnas. Här kan du skapa alla nödvändiga Azure-resurser för att köra ASP.NET-webbapp i Azure.

    ![Panelen Microsoft Azure App Service][B15]

3. Välj ditt Azure-konto i fönstret **Skapa App Service**. Välj **ändra typ**  >  **Web App**. Behåll **standardnamnet på webbappen** och välj sedan **Prenumeration**, **Resursgrupp** och **App Service Plan**.

4. Välj **Skapa**.

5. Notera egenskapen **Plats-URL** i avsnittet **Sammanfattning**. Den här URL:en är din *slutpunkt för serverdel* senare under självstudien.

6. Välj **Publicera**.

När du har slutfört guiden publiceras ASP.NET-webbappen till Azure och sedan öppnas appen i standardwebbläsaren.  Programmet kan visas i Azure App Services.

URL:en använder webbappens namn som du angav tidigare, i formatet http://<appnamn>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG