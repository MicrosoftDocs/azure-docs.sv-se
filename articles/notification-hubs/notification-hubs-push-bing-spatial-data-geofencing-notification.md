---
title: Skicka push-meddelanden med data från Azure Notification Hubs och Bing-spatialdata | Microsoft Docs
description: I den här självstudiekurskursen får lära du dig att leverera platsbaserade push-meddelanden med Azure Notification Hub och Bing Spatial Data.
services: notification-hubs
documentationcenter: windows
keywords: push-meddelanden, push-meddelanden
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 51ad23e67a77c28d0ad8a147168a0094f5de1796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578010"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Självstudie: skicka platsbaserade push-meddelanden med Notification Hubs data och Bing-spatialdata

I den här självstudiekurskursen får lära du dig att leverera platsbaserade push-meddelanden med Azure Notification Hub och Bing Spatial Data.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Konfigurera datakällan
> * Konfigurera UWP-programmet
> * Konfigurera serverdelen
> * Testa push-meddelanden i appen Universal Windows Platform (UWP)

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du  [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) eller senare ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* Senaste versionen av [Azure SDK](https://azure.microsoft.com/downloads/).
* [Konto på Bing Maps Dev Center](https://www.bingmapsportal.com/) (du kan skapa ett gratis och associera det med ditt Microsoft-konto).

## <a name="set-up-the-data-source"></a>Konfigurera datakällan

1. Logga in på [Bing Maps Dev Center](https://www.bingmapsportal.com/).
2. Välj **Datakällor** i det övre navigeringsfältet och välj sedan **Hantera datakällor**.

    ![Skärm bild av Bing Maps dev Center på sidan Hantera data källor med alternativet överför data som en data källa som beskrivs i rött.](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Om du inte har någon befintlig datakälla visas en länk genom vilken du kan skapa en datakälla. Välj **Ladda upp data som en datakälla**. Du kan också använda **data källor**  >  **Ladda upp data** -menyn.

    ![Skärm bild av dialog rutan Ladda upp en data källa.](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Skapa en fil `NotificationHubsGeofence.pipe` på hård disken med följande innehåll: i den här självstudien använder du en pipe-baserad fil som ramar ett områden i San Francisco Waterfront:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Pipe-filen representerar denna entitet:

    ![Skärm bild av en karta över San Francisco-Waterfront med en röd polygon som visar en del av Piers.](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Gör följande på sidan **Överför en datakälla**:
   1. Välj **pipe** som **Dataformat**.
   2. Bläddra och välj den `NotificationHubGeofence.pipe`-fil som du skapade i föregående steg.
   3. Välj knappen **Ladda upp**.

      > [!NOTE]
      > Du kan uppmanas att ange en ny nyckel för **huvudnyckel** n. Denna skiljer sig från **frågenyckeln**. Du behöver bara skapa en ny nyckel via instrumentpanelen och uppdatera överföringssidan för datakällan.
6. När du har laddat upp datafilen måste du se till att publicera datakällan. Välj **data källor**  ->  **Hantera data källor** som du gjorde tidigare.
7. Markera datakällan i listan och välj **Publicera** i kolumnen **Åtgärder**.

    ![Skärm bild av Bing Maps dev Center på sidan Hantera data källor med fliken hårdkodad data vald och publicerings alternativet som beskrivs i rött.](./media/notification-hubs-geofence/publish-button.png)
8. Växla till fliken **Publicerade datakällor** och bekräfta att du ser din datakälla i listan.

    ![Skärm bild av Bing Maps dev Center på sidan Hantera data källor med fliken publicerade data källor vald.](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Välj **Redigera**. Du ser (i en snabb överblick) vilka platser du introducerade i informationen.

    ![Skärm bild av sidan Redigera entitets data som visar en karta över västra USA och en magenta prick över San Francisco-Waterfront.](./media/notification-hubs-geofence/bing-maps-data-details.png)

    I det här läget visar inte portalen gränserna för det geofence som du skapade. Det enda du behöver är en bekräftelse på att den angivna platsen ligger ungefär i rätt område.
10. Nu är alla krav uppfyllda för datakällan. Om du vill ha information om API-anropets URL-adress för begäran i Bing Maps Dev Center, väljer du **Datakällor** och väljer **Information om datakällan**.

    ![Skärm bild av Bing Maps dev Center på sidan information om data källa.](./media/notification-hubs-geofence/bing-maps-data-info.png)

    **Fråge-URL:en** är den slutpunkt mot vilken du kan köra frågor som kontrollerar om enheten för närvarande befinner sig inom platsgränserna eller inte. Du utför den här kontrollen helt enkelt genom att köra ett GET-anrop mot fråge-URL:en med följande tillagda parametrar:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Bing Maps utför automatiskt beräkningar för att kontrollera om enheten ligger inom geofence-området. När du kör begäran via en webbläsare (eller cURL), får du ett JSON-standardsvar:

    ![Skärm bild av standard-JSON-svaret.](./media/notification-hubs-geofence/bing-maps-json.png)

    Du får bara tillbaka detta svar när punkten verkligen ligger inom de angivna gränserna. Om så inte är fallet får du en tom **resultat**-bucket:

    ![Skärm bild av ett JSON-svar med en tom resultat-Bucket.](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Konfigurera UWP-programmet

1. Starta ett nytt projekt av typen **tom app (Universal Windows)** i Visual Studio.

    ![Skärm bild av dialog rutan nytt projekt i Visual Studio med alternativet tom app (universellt Windows Visual C# markerat).](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    När projektet har skapats har du en stomme för själva appen. Nu ska vi ställa in allt för Geo-staket-infrastrukturen. Eftersom du ska använda Bing-tjänster för den här lösningen, så finns det en offentlig REST-API-slutpunkt med vilken du kan skicka förfrågningar till specifika platsramar:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Få det att fungera genom att ange följande parametrar:

   * **Datakällans ID** och **Datakällnamn** – I Bing Maps API innehåller datakällorna flera bucketgrupperade metadata, som platser och öppettider.  
   * **Entitetsnamn** – Den entitet som du vill använda som referenspunkt för meddelandet.
   * **Bing Maps API-nyckel** – Den nyckel som du fick tidigare när du skapade Bing Dev Center-kontot.

     Nu när datakällan är redo kan du börja jobba med UWP-appen.
2. Aktivera platstjänster för ditt program. Det gör du genom att öppna `Package.appxmanifest`-filen i **Solution Explorer**.

    ![Skärm bild av Solution Explorer med Package. appxmanifest-filen markerat.](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Välj **Funktioner** och sedan **Plats** på fliken för paketegenskaper som just öppnats.

    ![Skärm bild av dialog rutan paket egenskaper som visar fliken funktioner med alternativet plats markerat.](./media/notification-hubs-geofence/vs-package-location.png)
4. Skapa en ny mapp i din lösning med namnet `Core` och lägg till en ny fil i den med namnet `LocationHelper.cs`:

    ![Skärm bild av Solution Explorer med den nya Core-mappen markerad.](./media/notification-hubs-geofence/vs-location-helper.png)

    Klassen `LocationHelper` har koden för att hämta användarplatsen via system-API:et:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Mer information om hur du hämtar användarens plats i UWP-appar finns i[Hämta användarens plats](/windows/uwp/maps-and-location/get-location).
5. Om du vill kontrollera att platsinsamlingen faktiskt fungerar, kan du öppna din huvudsidas kodsida (`MainPage.xaml.cs`). Skapa en ny händelsehanterare för `Loaded`-händelsen i `MainPage`-konstruktorn.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    Implementeringen av händelsehanteraren fungerar så här:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Kör programmet och tillåt att det får åtkomst till din plats.

    ![Skärm bild av dialog rutan låt Notification Hubs geo-stängsel åtkomst till din plats.](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. När appen startas ska du kunna se koordinaterna i fönstret **Utmatning**:

    ![Skärm bild av fönstret utdata som visar koordinaterna.](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Nu när du vet att plats förvärvet fungerar kan du ta bort den inlästa händelse hanteraren om du vill eftersom du inte längre använder den.
8. Nästa steg är att samla in platsförändringar. Lägg till händelsehanteraren för `PositionChanged` i klassen `LocationHelper`:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    Implementeringen visar platskoordinaterna i fönstret **Utdata**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>Konfigurera serverdelen

1. Hämta [Exempel för .NET-serverdel från GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).
2. När hämtningen är klar, öppnar du mappen `NotifyUsers` och därefter filen `NotifyUsers.sln` i Visual Studio.
3. Ange projektet `AppBackend` som **startprojektet** och starta det.

    ![Skärm bild av lösningen genom att högerklicka på menyn med alternativet Ange som start projekt markerat.](./media/notification-hubs-geofence/vs-startup-project.png)

    Projektet har redan konfigurerats för att skicka push-meddelanden till målenheterna så du behöver bara göra två saker: ange rätt anslutningssträng för meddelandehubben och lägga till gränsidentifieringar för att endast skicka meddelanden när användaren befinner sig inom geofence-området.

4. Öppna `Notifications.cs` i mappen `Models` för att konfigurera anslutningssträngen. Funktionen `NotificationHubClient.CreateClientFromConnectionString` bör innehålla den information om meddelandehubben som du kan hämta i [Azure Portal](https://portal.azure.com) (titta på sidan **Åtkomstprinciper** under **Inställningar**). Spara den uppdaterade konfigurationsfilen.
5. Skapa en modell för Bing Maps API-resultatet. Det enklaste sättet att göra det är att öppna `Models` mappen och välja **Lägg till**  >  **klass**. Ge den namnet `GeofenceBoundary.cs`. När det är klart kan du kopiera JSON från det API-svar du fick i det första avsnittet. I Visual Studio använder du **Redigera**  >  **Klistra in Special**  >  **Klistra in JSON som klasser**.

    På så sätt ser du till att objektet avserialiseras exakt på det sätt som du vill att det ska. Du bör nu ha en klassuppsättning som ser ut ungefär så här:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Därefter öppnar du `Controllers` > `NotificationsController.cs`. Uppdatera efteranropet till kontot så att målets longitud och latitud kommer med. För att kunna göra detta måste du lägga till två strängar i funktionssignaturen – `latitude` och `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Skapa en ny klass i projektet med namnet `ApiHelper.cs` – du använder den för att ansluta till Bing till gränsernas skärningspunkter. Implementera en `IsPointWithinBounds`-funktion så som visas i följande kod:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Ersätt API-slutpunkten med fråge-URL:en som du tidigare hämtade från Bing Dev Center (det samma gäller för API-nyckeln).

    Om du får resultat från begäran så innebär det att den angivna punkten befinner sig inom gränserna för geofence-området. Därför returnerar funktionen `true`. Om du inte får några resultat kommer talar Bing om för dig att punkten ligger utanför uppslagsramen. Därför returnerar funktionen `false`.
8. Skapa en kontroll precis före switch-utrycket i `NotificationsController.cs`:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Testa push-meddelanden i UWP-appen

1. Nu bör du kunna testa meddelandefunktionen i UWP-appen. Skapa en ny funktion – `SendLocationToBackend` –  i klassen `LocationHelper`.

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Konfigurera `POST_URL` till platsen för din distribuerade webbapp. Nu är det OK att köra det lokalt, men när du arbetar med att distribuera en offentlig version måste du vara värd för den med en extern provider.
2. Registrera UWP-appen för push-meddelanden. I Visual Studio väljer du **projekt**  >  **Store**  >  **associera appen med butiken**.

    ![Skärm bild av lösningen genom att högerklicka på menyn med Store och associera appen med butiks alternativen markerade.](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Kontrollera att du väljer en befintlig app, eller skapa en ny, och associera paketet med den när du loggar in på ditt utvecklarkonto.
4. Gå till Dev Center och öppna den app som du har skapat. Välj **tjänster**  >  **push-meddelanden**  >  **Live Services-webbplatsen**.

    ![Skärm bild av Windows Dev Center som visar sidan push-meddelanden med Live Services-webbplatsen markerad.](./media/notification-hubs-geofence/ms-live-services.png)
5. När du är på webbplatsen skriver du ned **apphemligheten** och **paket-SID:et**. Du behöver båda i Azure Portal – öppna din Notification Hub, Välj **Inställningar**  >  **Notification Services**  >  **Windows (WNS)** och ange informationen i de obligatoriska fälten.

    ![Skärm bild som visar sidan inställningar med alternativen Notification Services och Windows (WNS) markerade och värdena för paket-SID och säkerhets nyckel ifyllda.](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Välj **Spara**.
7. Öppna **Referenser** i **Solution Explorer** och välj **Hantera NuGet-paket**. Lägg till en referens till **hanteringsbiblioteket för Microsoft Azure Service Bus**. Du behöver bara söka efter `WindowsAzure.Messaging.Managed` och lägga till den i projektet.

    ![Skärm bild av dialog rutan hantera NuGet-paket med WindowsAzure. Messaging. Managed-paketet markerat.](./media/notification-hubs-geofence/vs-nuget.png)
8. Skapa i testsyfte händelsehanteraren `MainPage_Loaded` igen och lägg till det här kodfragmentet i den:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    Koden registrerar appen i meddelandehubben. Du är nu redo att sätta igång!
9. I `LocationHelper`, inuti hanteraren `Geolocator_PositionChanged`, kan du lägga till en del av en testkod som med tvång placerar platsen inom geofence-området:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Eftersom du inte skickar de faktiska koordinaterna (som kanske inte ligger inom gränserna för tillfället) och använder fördefinierade testvärden ser du en avisering som visas i uppdateringen:

    ![Skärm bild av Windows-skrivbordet som visar TEST meddelandet.](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Nästa steg

Det finns några steg som du kan behöva följa för att göra lösningen produktionsklar.

1. Först och främst måste du se till att geofence-områdena är dynamiska. Detta kräver lite extraarbete med Bing-API:iet så att det ska gå att ladda upp nya gränser inom den befintliga datakällan. Mer information finns i [Bing Spatial Data Services API-dokumentationen](/bingmaps/spatial-data-services/).
2. Och för det andra: När du jobbar med att säkerställa att leveransen görs till rätt deltagare kanske du vill rikta in dig på dem med hjälp av [taggning](notification-hubs-tags-segment-push-message.md).

Lösningen i den här självstudien beskriver ett scenario där du kan ha en stor mängd olika målplattformar. Därför begränsar den inte denna geofencing till systemspecifika funktioner. Men det bör understrykas att UWP har inbyggda, [kraftfulla funktioner för att detektera geofence-områden](/windows/uwp/maps-and-location/set-up-a-geofence).
