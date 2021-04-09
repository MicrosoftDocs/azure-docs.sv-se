---
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/14/2020
ms.author: sethm
ms.openlocfilehash: fb3c95b74128f1da7b29a290e17fefe21987dd76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019435"
---
## <a name="webapi-project"></a>WebAPI-projekt

1. Öppna det **AppBackend** -projekt som du skapade i självstudien **meddela användare** i Visual Studio.
2. I Notifications. cs ersätter du hela **meddelande** klassen med följande kod. Se till att ersätta plats hållarna med din anslutnings sträng (med fullständig åtkomst) för Notification Hub och hubbens namn. Du kan hämta dessa värden från [Azure Portal](https://portal.azure.com). Den här modulen representerar nu de olika säkra meddelanden som kommer att skickas. I en fullständig implementering kommer meddelandena att lagras i en databas. för enkelhetens skull lagrar vi dem i minnet.

   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

3. I NotificationsController. cs ersätter du koden inuti definitionen av **NotificationsController** -klassen med följande kod. Den här komponenten implementerar ett sätt för enheten att hämta meddelandet på ett säkert sätt och ger också ett sätt (i den här självstudien) att utlösa en säker push-överföring till dina enheter. Observera att när du skickar meddelandet till Notification Hub skickar vi bara ett RAW-meddelande med ID för meddelandet (och inget faktiskt meddelande):

   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Observera att `Post` metoden nu inte skickar ett popup-meddelande. Den skickar ett RAW-meddelande som endast innehåller meddelande-ID och inte något känsligt innehåll. Se också till att kommentera sändnings åtgärden för de plattformar som du inte har konfigurerade autentiseringsuppgifter för i Notification Hub, eftersom de leder till fel.

1. Nu kommer vi att omdistribuera den här appen till en Azure-webbplats för att göra den tillgänglig från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
2. Välj Azure-webbplats som publicerings mål. Logga in med ditt Azure-konto och välj en befintlig eller ny webbplats och anteckna URL-egenskapen för **målet** på fliken **anslutning** . Vi kommer att referera till denna URL som *backend-slutpunkt* senare i den här självstudien. Klicka på **Publicera**.
