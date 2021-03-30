---
title: Diagnostik för Azure Service Fabric omvänd proxy
description: Lär dig hur du övervakar och diagnostiserar bearbetning av begär anden i den omvända proxyn för ett Azure Service Fabric-program.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75645471"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Övervaka och diagnostisera bearbetning av begär anden i den omvända proxyn

Från och med 5,7-versionen av Service Fabric är omvänd proxy-händelser tillgängliga för insamling. Händelserna är tillgängliga i två kanaler, en med endast fel händelser relaterade till begär ande bearbetnings fel på den omvända proxyn och andra kanalen som innehåller utförliga händelser med poster för både lyckade och misslyckade förfrågningar.

Se [samla in omvänd proxy-händelser](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) för att aktivera insamling av händelser från dessa kanaler i lokala och Azure Service Fabric-kluster.

## <a name="troubleshoot-using-diagnostics-logs"></a>Felsöka med hjälp av diagnostikloggar
Här följer några exempel på hur du tolkar de vanliga fel loggar som en kan stöta på:

1. Omvänd proxy returnerar svars status kod 504 (tids gräns).

    En orsak kan bero på att tjänsten inte kan svara inom tids gränsen för begäran.
   I den första händelsen nedan loggas information om begäran som tagits emot på den omvända proxyn. 
   Den andra händelsen indikerar att begäran misslyckades vid vidarebefordran till tjänsten, på grund av "internt fel = ERROR_WINHTTP_TIMEOUT" 

    Nytto lasten innehåller:

   * **traceId**: detta GUID kan användas för att korrelera alla händelser som motsvarar en enskild begäran. I de två följande händelserna är traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, vilket innebär att de tillhör samma begäran.
   * **requestUrl**: URL: en (omvänd proxy-URL) som förfrågan skickades till.
   * **verb**: http-verb.
   * **remoteAddress**: adressen till klienten som skickar begäran.
   * **resolvedServiceUrl**: tjänstens slut punkts-URL dit den inkommande begäran löstes. 
   * **errorDetails**: Mer information om det här problemet.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Omvänd proxy returnerar svars status kod 404 (hittades inte). 
    
    Här är en exempel händelse där omvänd proxy returnerar 404 sedan det inte gick att hitta matchande tjänst slut punkt.
    Följande nytto Last poster är:
   * **processRequestPhase**: anger fasen vid bearbetning av begäran när felet inträffade, **_TryGetEndpoint_** , dvs. vid försök att hämta slut punkten för tjänsten att vidarebefordra till. 
   * **errorDetails**: visar slut punkts Sök kriterierna. Här kan du se att listenerName anges = **FrontEndListener** , medan replik slut punkts listan endast innehåller en lyssnare med namnet **OldListener**.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Ett annat exempel där omvänd proxy kan returnera 404 som inte hittas är: ApplicationGateway\Http Configuration parameter **SecureOnlyMode** har angetts till true med den omvända proxyn som lyssnar på **https**, men alla replik slut punkter är osäkra (lyssnar på http).
     Omvänd proxy returnerar 404 eftersom det inte går att hitta en slut punkt som lyssnar på HTTPS för att vidarebefordra begäran. Att analysera parametrarna i händelse nytto lasten hjälper till att begränsa problemet:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Begäran till omvänd proxy Miss lyckas med ett tids gräns fel. 
    Händelse loggarna innehåller en händelse med information om mottagna begär Anden (visas inte här).
    Nästa händelse visar att tjänsten svarade med status kod 404 och omvänd proxy initierar en ny lösning. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    När du samlar in alla händelser visas ett tåg av händelser som visar varje lösning och ett vidarebefordrings försök.
    Den sista händelsen i serien visar att bearbetningen av begäran misslyckades med en tids gräns, tillsammans med antalet lyckade matchnings försök.
    
    > [!NOTE]
    > Vi rekommenderar att du håller insamlingen för utförlig kanal inaktive rad som standard och aktiverar den för fel sökning på grund av behov.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Om samling är aktive rad för kritiska/fel-händelser visas en händelse med information om tids gränsen och antalet matchnings försök. 
    
    Tjänster som ska skicka en status kod för 404 tillbaka till användaren, ska lägga till ett "X-ServiceFabric"-huvud i svaret. När du har lagt till rubriken i svaret vidarebefordrar omvänd proxy status koden tillbaka till klienten.  

4. Fall när klienten har kopplat från begäran.

    Följande händelse registreras när omvänd proxy vidarebefordrar svaret till klienten, men klienten kopplar från:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Omvänd proxy returnerar 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST fel returneras om URI-schemat inte har angetts för tjänst slut punkten i tjänst manifestet.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Lös problemet genom att ange URI-schemat i manifestet.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Händelser relaterade till bearbetning av WebSocket-begäran loggas inte för tillfället. Detta kommer att läggas till i nästa version.

## <a name="next-steps"></a>Nästa steg
* [Händelse agg regering och insamling med hjälp av Windows Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md) för att aktivera logg insamling i Azure-kluster.
* Om du vill visa Service Fabric händelser i Visual Studio, se [övervaka och diagnostisera lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Se [Konfigurera omvänd proxy för att ansluta till säkra tjänster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) för Azure Resource Manager mall exempel för att konfigurera säker omvänd proxy med olika validerings alternativ för tjänst certifikat.
* Läs [Service Fabric omvänd proxy](service-fabric-reverseproxy.md) för mer information.
