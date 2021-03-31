---
title: Azure Cosmos DB Gremlin-svarshuvuden
description: Referens dokumentation för Server svars-metadata som möjliggör ytterligare fel sökning
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600522"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin Server-svarshuvuden
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Den här artikeln beskriver rubriker som Cosmos DB Gremlin-servern returnerar till anroparen när begäran har körts. De här rubrikerna är användbara för att felsöka prestanda för förfrågningar, bygga program som integreras internt med Cosmos DB-tjänsten och förenklar kundsupporten.

Tänk på att om du tar hänsyn till dessa huvuden begränsar du portabiliteten för ditt program till andra Gremlin-implementeringar. I retur får du bättre integrering med Cosmos DB Gremlin. De här rubrikerna är inte en TinkerPop standard.

## <a name="headers"></a>Sidhuvuden

| Huvud | Typ | Exempel värde | När det ingår | Förklaring |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11,3243 | Lyckade och misslyckade | Mängd eller databas data flöde som förbrukas i [enheter för programbegäran (ru/s eller ru: er)](request-units.md) för ett del svars meddelande. Den här rubriken finns i varje fortsättning för förfrågningar som har flera segment. Det visar avgiften för ett visst svars segment. Endast för begär Anden som består av ett enskilt svar som denna rubrik matchar den totala kostnaden för Traversal. För majoriteten av komplexa bläddringskontroll motsvarar dock värdet en delvis kostnad. |
| **x-ms-total-request-charge** | double | 423,987 | Lyckade och misslyckade | Mängd eller databas data flöde som förbrukas i [enheter för programbegäran (ru/s eller ru: er)](request-units.md) för hela begäran. Den här rubriken finns i varje fortsättning för förfrågningar som har flera segment. Den anger ackumulerad avgift sedan begäran börjar. Värdet för den här rubriken i det sista segmentet visar att avgiften för begäran har slutförts. |
| **x-MS-Server-Time-MS** | double | 13,75 | Lyckade och misslyckade | Den här rubriken ingår i fel söknings syfte för svars tid. Det anger hur lång tid i millisekunder som Cosmos DB Gremlin-servern tog att köra och generera ett del svars meddelande. Genom att använda värdet i den här rubriken och jämföra det med övergripande svars tid för begäran kan du beräkna nätverks fördröjning. |
| **x-MS-total-Server-Time-MS** | double | 130,512 | Lyckade och misslyckade | Total tid i millisekunder som Cosmos DB Gremlin-servern tog att köra hela genom gången. Den här rubriken ingår i varje delvis svar. Den representerar ackumulerad körnings tid sedan begäran startades. Det sista svaret anger den totala körnings tiden. Den här rubriken är användbar för att skilja mellan klienten och servern som en fördröjnings källa. Du kan jämföra Traversal-körnings tiden på klienten med värdet för den här rubriken. |
| **x-ms-status-code** | long | 200 | Lyckade och misslyckade | Huvudet anger en intern orsak till att begäran har slutförts eller avslutas. Programmet uppmanas att titta på värdet för rubriken och vidta lämpliga åtgärder. |
| **x-MS-substatus-kod** | long | 1003 | Endast haveri | Cosmos DB är en databas för flera modeller som är byggd ovanpå Unified Storage Layer. Den här rubriken innehåller ytterligare insikter om fel orsaken när fel uppstår inom lägre lager med hög tillgänglighets stack. Programmet uppmanas att lagra sidhuvudet och använda det när du kontaktar Cosmos DB kund support. Värdet för den här rubriken är användbart för Cosmos DB teknikern för snabb fel sökning. |
| **x-ms-retry-after-ms** | sträng (TimeSpan) | "00:00:03.9500000" | Endast haveri | Den här rubriken är en sträng representation av en .NET- [TimeSpan](/dotnet/api/system.timespan) -typ. Det här värdet inkluderas bara i begär Anden som misslyckades på grund av förbrukade data flöde. Programmet ska skicka om genom gång igen efter en instruerad tids period. |
| **x-ms-activity-id** | sträng (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Lyckade och misslyckade | Huvudet innehåller en unik identifierare för Server sidan för en begäran. Varje begäran tilldelas en unik identifierare av servern för spårnings syfte. Program ska logga aktivitets identifierare som returneras av servern för förfrågningar som kunder kan kontakta kund support om. Cosmos DB support personal kan hitta vissa förfrågningar av dessa identifierare i Cosmos DB service telemetri. |

## <a name="status-codes"></a>Statuskoder

De flesta vanliga koder som returneras för `x-ms-status-code` attributet status av servern visas nedan.

| Status | Förklaring |
| --- | --- |
| **401** | Fel meddelandet `"Unauthorized: Invalid credentials provided"` returneras när lösen ordet för autentisering inte matchar Cosmos DB konto nyckeln. Navigera till ditt Cosmos DB Gremlin-konto i Azure Portal och bekräfta att nyckeln är korrekt.|
| **404** | Samtidiga åtgärder som försöker ta bort och uppdatera samma kant eller hörn samtidigt. Felmeddelandet `"Owner resource does not exist"` anger att den angivna databasen eller samlingen har det felaktiga formatet `/dbs/<database name>/colls/<collection or graph name>` för anslutningsparametrarna.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Detta inträffar vanligtvis när ett hörn eller en kant med en identifierare redan finns i grafen.| 
| **412** | Status koden kompletteras med ett fel meddelande `"PreconditionFailedException": One of the specified pre-condition is not met` . Det här felet är en indikation på en optimistisk samtidighets kontroll överträdelse mellan att läsa en gräns eller ett hörn och att skriva tillbaka den till lagret efter ändringen. De vanligaste situationerna när det här felet inträffar är till exempel egenskaps ändringar `g.V('identifier').property('name','value')` . Gremlin-motorn skulle läsa hörnet, ändra det och skriva tillbaka. Om det finns en annan genom gång som körs parallellt försöker skriva samma hörn eller en kant, kommer en av dem att få det här felet. Programmet ska skicka en överträdelse till servern igen.| 
| **429** | Begäran begränsades och bör provas igen efter värdet i **x-MS-retry-efter-MS**| 
| **500** | Ett felmeddelande som innehåller `"NotFoundException: Entity with the specified id does not exist in the system."` anger att en databas och/eller samling har återskapats med samma namn. Det här felet försvinner inom 5 minuter när ändringen sprids och upphäver cacheminnen i olika Cosmos DB-komponenter. Undvik det här problemet genom att använda unika databas- och samlingsnamn varje gång.| 
| **1000** | Den här status koden returneras när servern har parsat ett meddelande men inte kunde köras. Det tyder vanligt vis på ett problem med frågan.| 
| **1001** | Den här koden returneras när servern har slutfört bläddringskontroll, men inte kan serialisera tillbaka svar till klienten. Det här felet kan inträffa när en genom gång genererar ett komplext resultat som är för stort eller som inte uppfyller TinkerPop-protokollets specifikation. Programmet bör förenkla genom gången när det här felet påträffas. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` returneras när Traversal överskrider den tillåtna minnes gränsen. Minnes gränsen är **2 GB** per genom gång.| 
| **1004** | Den här status koden indikerar felaktig diagram förfrågan. Begäran kan vara felaktig när den avserialiseras, typen som inte är värdetyp avserialiseras eftersom värde typen eller en Gremlin-åtgärd som inte stöds begärdes. Programmet ska inte försöka utföra begäran igen eftersom det inte kommer att lyckas. | 
| **1007** | Vanligt vis returneras denna status kod med ett fel meddelande `"Could not process request. Underlying connection has been closed."` . Den här situationen kan inträffa om klient driv rutinen försöker använda en anslutning som stängs av servern. Programmet bör försöka över gången på en annan anslutning.
| **1008** | Cosmos DB Gremlin-servern kan avsluta anslutningar för att balansera om trafik i klustret. Klient driv rutinerna bör hantera den här situationen och endast använda Live-anslutningar för att skicka begär anden till servern. Ibland kanske klient driv rutinerna inte upptäcker att anslutningen har stängts. När programmet påträffar ett fel `"Connection is too busy. Please retry after sometime or open more connections."` bör det försöka att gå vidare med en annan anslutning.
| **1009** | Åtgärden slutfördes inte inom den angivna tiden och avbröts av servern. Optimera dina bläddringskontroll så att de snabbt kan köras genom att filtrera formhörn eller kanter på varje hopp med Traversal för att begränsa Sök omfånget. Timeout-värde för begäran är **60 sekunder**. |

## <a name="samples"></a>Exempel

Ett exempel på ett klient program baserat på Gremlin.Net som läser ett status-attribut:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Ett exempel som visar hur du läser status-attribut från Gremlin Java-klienten:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Nästa steg
* [HTTP-statuskod för Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Vanliga Azure Cosmos DB REST-svarshuvuden](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Driv Rutins krav för TinkerPop Graph]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
