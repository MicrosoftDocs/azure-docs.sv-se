---
title: Registrerings hantering
description: I det här avsnittet beskrivs hur du registrerar enheter med Notification Hub för att ta emot push-meddelanden.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7807d28da459656938acb399eb8c621e4c292372
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001577"
---
# <a name="registration-management"></a>Registreringshantering

I det här avsnittet beskrivs hur du registrerar enheter med Notification Hub för att ta emot push-meddelanden. Avsnittet beskriver registreringar på en hög nivå och presenterar sedan de två huvud mönstren för att registrera enheter: att registrera från enheten direkt till Notification Hub och registrera dig via en program Server del.

## <a name="what-is-device-registration"></a>Vad är enhets registrering

Enhets registrering med en Notification Hub görs med hjälp av en **registrering** eller **installation**.

### <a name="registrations"></a>Registreringar

Vid registreringen associeras PNS-handtaget (Platform Notification Service) för en enhet med taggar och eventuellt en mall. PNS-referensen kan vara en ChannelURI, Device token eller FCM registrerings-ID. Taggar används för att dirigera meddelanden till rätt uppsättning enhets referenser. Mer information finns i avsnittet om [Routning och tagg uttryck](notification-hubs-tags-segment-push-message.md). Mallar används för att implementera omvandling per registrering. Mer information finns i [Mallar](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Azure Notification Hubs stöder högst 60 Taggar per enhet.

### <a name="installations"></a>Installationer

En installation är en förbättrad registrering som innehåller en uppsättning push-relaterade egenskaper. Det är den senaste och bästa metoden för att registrera dina enheter. Men det stöds inte av .NET SDK för klient sidan ([Notification Hub SDK för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) ännu.  Det innebär att om du registrerar från själva klient enheten skulle du behöva använda [Notification Hubs REST API](/rest/api/notificationhubs/create-overwrite-installation) -metoden för att stödja installationer. Om du använder en backend-tjänst bör du kunna använda [Notification Hub SDK för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Följande är några viktiga fördelar med att använda installationer:

- Att skapa eller uppdatera en installation är helt idempotenta. Så du kan försöka igen utan några problem om dubbla registreringar.
- Installations modellen stöder ett särskilt tagg format ( `$InstallationId:{INSTALLATION_ID}` ) som gör det möjligt att skicka ett meddelande direkt till den specifika enheten. Om appens kod till exempel anger ett installations-ID för `joe93developer` för den här enheten kan en utvecklare Rikta enheten när den skickar ett meddelande till- `$InstallationId:{joe93developer}` taggen. På så sätt kan du rikta in dig på en speciell enhet utan att behöva göra ytterligare kodning.
- Med hjälp av installationer kan du också göra partiella registrerings uppdateringar. Den partiella uppdateringen av en installation begärs med en KORRIGERINGs metod med hjälp av [JSON-patch-standarden](https://tools.ietf.org/html/rfc6902). Detta är användbart när du vill uppdatera Taggar i registreringen. Du behöver inte hämta hela registreringen och sedan skicka om alla tidigare Taggar igen.

En installation kan innehålla följande egenskaper. En fullständig lista över installations egenskaperna finns i [skapa eller skriva över en installation med REST API](/rest/api/notificationhubs/create-overwrite-installation) eller [installations egenskaper](/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Som standard upphör inte registreringar och installationer att gälla.

Registreringar och installationer måste innehålla en giltig PNS-referens för varje enhet/kanal. Eftersom PNS-referenser bara kan hämtas i en klient app på enheten, är ett mönster att registrera sig direkt på enheten med klient programmet. Å andra sidan kan säkerhets överväganden och affärs logik som är relaterade till taggar kräva att du hanterar enhets registrering i appens Server del.

> [!NOTE]
> API: erna för installation stöder inte Baidu-tjänsten (även om registrerings-API: et). 

### <a name="templates"></a>Mallar

Om du vill använda [mallar](notification-hubs-templates-cross-platform-push-messages.md)innehåller enhets installationen även alla mallar som är kopplade till enheten i ett JSON-format (se exemplet ovan). Mallens namn hjälper till att rikta olika mallar för samma enhet.

Varje mallnamn mappar till en malldel och en valfri uppsättning taggar. Dessutom kan varje plattform ha ytterligare mall egenskaper. För Windows Store (med WNS) och Windows Phone 8 (med MPNS) kan en extra uppsättning huvuden vara en del av mallen. Om det gäller APN kan du ange en förfallo egenskap som antingen en konstant eller ett frågeuttryck. En fullständig lista över installations egenskaperna finns i [skapa eller skriva över en installation med rest](/rest/api/notificationhubs/create-overwrite-installation) -ämnet.

### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundära paneler för Windows Store-appar

För Windows Store-klientprogram är sändning av meddelanden till sekundära paneler detsamma som att skicka dem till den primära. Detta stöds också i installationer. Sekundära paneler har olika ChannelUri, som SDK: n i klient programmet hanterar transparent.

SecondaryTiles-ordlistan använder samma TileId som används för att skapa SecondaryTiles-objektet i din Windows Store-app. Precis som med den primära ChannelUri kan ChannelUris av sekundära paneler ändras när som helst. För att installationen av Notification Hub ska kunna fortsätta måste enheten uppdatera dem med den aktuella ChannelUris av de sekundära panelerna.

## <a name="registration-management-from-the-device"></a>Registrerings hantering från enheten

Vid hantering av enhets registrering från klient program är Server delen bara ansvarig för att skicka meddelanden. Klient program håller PNS-handtagen uppdaterade och registrerar taggar. Följande bild illustrerar det här mönstret.

![Registrering från enhet](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Enheten hämtar först PNS-referensen från PNS och registrerar sedan med Notification Hub direkt. När registreringen är klar kan appens Server del skicka ett meddelande som riktar sig till registreringen. Mer information om hur du skickar meddelanden finns i avsnittet om [Routning och tagg uttryck](notification-hubs-tags-segment-push-message.md).

I det här fallet använder du bara lyssnings rättigheter för att komma åt dina Notification Hub från enheten. Mer information finns i [säkerhet](notification-hubs-push-notification-security.md).

Att registrera från enheten är den enklaste metoden, men den har vissa nack delar:

- En klient app kan bara uppdatera sina taggar när appen är aktiv. Om en användare till exempel har två enheter som registrerar Taggar relaterade till sport team, och den första enheten registrerar sig för en extra tagg (till exempel Seahawks), kommer den andra enheten inte att ta emot meddelanden om Seahawks tills appen på den andra enheten körs en andra gång. I allmänhet är det ett önskvärt alternativ när Taggar påverkas av flera enheter.
- Eftersom appar kan bli hackiga måste det vara extra viktigt att skydda registreringen till särskilda taggar, enligt beskrivningen i artikel [säkerheten](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exempel kod för registrering med en Notification Hub från en enhet med hjälp av en installation

För tillfället stöds detta endast med [Notification Hubs REST API](/rest/api/notificationhubs/create-overwrite-installation).

Du kan också använda KORRIGERINGs metoden med hjälp av [JSON-patch-standarden](https://tools.ietf.org/html/rfc6902) för uppdatering av installationen.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored an installation ID in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exempel kod som ska registreras med en Notification Hub från en enhet med hjälp av en registrering

Dessa metoder skapar eller uppdaterar en registrering för den enhet som de anropas på. Det innebär att du måste skriva över hela registreringen för att kunna uppdatera referensen eller taggarna. Kom ihåg att registreringar är tillfälliga, så du bör alltid ha en tillförlitlig butik med de aktuella taggar som en speciell enhet behöver.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
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

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Registrerings hantering från en server del

Att hantera registreringar från Server delen kräver att du skriver ytterligare kod. Appen från enheten måste tillhandahålla den uppdaterade PNS-referensen till Server delen varje gång appen startas (tillsammans med taggar och mallar) och Server delen måste uppdatera den här referensen i Notification Hub. Följande bild illustrerar den här designen.

![Registreringshantering](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Fördelarna med att hantera registreringar från Server delen är möjligheten att ändra taggar till registreringar även när motsvarande app på enheten är inaktiv och för att autentisera klient programmet innan du lägger till en tagg i registreringen.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exempel kod för registrering med en Notification Hub från en server del med en installation

Klient enheten får fortfarande sitt PNS-handtag och relevanta installations egenskaper som innan och anropar ett anpassat API på Server delen som kan utföra registrerings-och auktorisera-taggarna osv. Server delen kan utnyttja [SDK för Notification Hub för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Du kan också använda KORRIGERINGs metoden med hjälp av [JSON-patch-standarden](https://tools.ietf.org/html/rfc6902) för uppdatering av installationen.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exempel kod för registrering med en Notification Hub från en enhet med ett registrerings-ID

Från din app server del kan du utföra grundläggande CRUDS-åtgärder för registreringar. Exempel:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Server delen måste hantera samtidighet mellan registrerings uppdateringar. Service Bus erbjuder optimistisk concurrency-kontroll för registrerings hantering. På HTTP-nivå implementeras detta med hjälp av ETag vid registrerings hanterings åtgärder. Den här funktionen används transparent av Microsoft SDK: er, vilket genererar ett undantag om en uppdatering avvisas av samtidiga orsaker. Appens Server del ansvarar för hantering av dessa undantag och försöker uppdatera igen om det behövs.
