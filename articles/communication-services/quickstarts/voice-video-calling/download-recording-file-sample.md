---
title: Registrera och ladda ned anrop med Event Grid – Azure Communication Services snabbstart
titleSuffix: An Azure Communication Services quickstart
description: I den här snabbstarten lär du dig att registrera och ladda ned anrop med hjälp av Event Grid.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730585"
---
# <a name="record-and-download-calls-with-event-grid"></a>Registrera och ladda ned anrop med Event Grid

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Kom igång med Azure Communication Services genom att registrera dina Communication Services-anrop med Azure Event Grid.

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En aktiv Communication Services resurs. [Skapa en Communication Services resurs](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)NuGet-paketet.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Skapa en webhook och prenumerera på inspelningshändelserna
Vi använder *webhooks och händelser för* *att underlätta* anrop av inspelning och nedladdning av mediefiler. 

Först skapar vi en webhook. Din Communication Services resurs använder Event Grid för att meddela denna webhook när händelsen utlöses och sedan igen när inspelat medium är redo `recording` att laddas ned.

Du kan skriva en egen anpassad webhook för att ta emot dessa händelsemeddelanden. Det är viktigt att den här webhooken svarar på inkommande meddelanden med valideringskoden för att kunna prenumerera webhooken på händelsetjänsten.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


Koden ovan beror på `Microsoft.Azure.EventGrid` NuGet-paketet. Mer information om Event Grid finns i dokumentationen för [slutpunktsverifiering](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation)

Sedan prenumererar vi den här webhooken på `recording` händelsen:

1. Välj `Events` bladet från Azure Communication Services resurs.
2. Välj `Event Subscription` enligt nedan.
![Skärmbild som visar användargränssnittet för Event Grid](./media/call-recording/image1-event-grid.png)
3. Konfigurera händelseprenumerationen `Call Recording File Status Update` och välj som `Event Type` . Välj `Webhook` som `Endpoint type` .
![Skapa händelseprenumeration](./media/call-recording/image2-create-subscription.png)
4. Ange webhookens URL i `Subscriber Endpoint` .
![Prenumerera på händelse](./media/call-recording/image3-subscribe-to-event.png)

Webhooken meddelas nu när din Communication Services används för att registrera ett anrop.

## <a name="notification-schema"></a>Meddelandeschema
När inspelningen är tillgänglig för nedladdning skickar Communication Services en avisering med följande händelseschema. Dokument-ID:erna för inspelningen kan hämtas från `documentId` fälten i varje `recordingChunk` .

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>Ladda ned de inspelade mediefilerna

När vi har fått dokument-ID:t för den fil som vi vill ladda ned anropar vi API:erna Azure Communication Services nedan för att ladda ned de registrerade medierna och metadata med hjälp av HMAC-autentisering.

Den maximala inspelningsfilstorleken är 1,5 GB. När den här filstorleken överskrids delar inspelaren automatiskt in inspelade media i flera filer.

Klienten ska kunna ladda ned alla mediefiler med en enda begäran. Om det uppstår ett problem kan klienten försöka igen med ett intervallhuvud för att undvika att ladda ned segment som redan har laddats ned igen.

Så här laddar du ned inspelat medium: 
- Metod: `GET` 
- Url: https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Så här laddar du ned inspelade mediemetadata: 
- Metod: `GET` 
- Url: https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Autentisering
Om du vill ladda ned registrerade media och metadata använder du HMAC-autentisering för att autentisera begäran mot Azure Communication Services API:er.

Skapa en `HttpClient` och lägg till de nödvändiga rubrikerna med hjälp av `HmacAuthenticationUtils` nedanstående:

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
Verktygen nedan kan användas för att hantera ditt HMAC-arbetsflöde.

**Skapa innehållshashar**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**Lägga till HMAC-huvuden**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du vill rensa och ta bort en Communication Services prenumeration kan du ta bort resursen eller resursgruppen. När du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort. Läs mer om [att rensa resurser.](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources)


## <a name="next-steps"></a>Nästa steg
Mer information finns i följande artiklar:

- Kolla in vårt [webbanropsexempel](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Lär dig mer [om anrop av SDK-funktioner](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Läs mer om [hur anrop fungerar](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
