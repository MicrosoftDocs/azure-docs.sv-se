---
title: Hantera digitala tvillingar
titleSuffix: Azure Digital Twins
description: Se hur du hämtar, uppdaterar och tar bort enskilda dubbla och relationer.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9f140594ef18df7f9a6a3b919998962c966cde76
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587607"
---
# <a name="manage-digital-twins"></a>Hantera digitala tvillingar

Entiteter i din miljö representeras av [digitala dubbla](concepts-twins-graph.md). Att hantera digitala dubbla, kan vara att skapa, ändra och ta bort. Om du vill utföra dessa åtgärder kan du använda [**DigitalTwins-API: er**](how-to-use-apis-sdks.md), [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)eller [Azure Digitals flätade CLI](how-to-use-cli.md).

Den här artikeln fokuserar på att hantera digitala dubbla, information om hur du arbetar med relationer och det [dubbla diagrammet](concepts-twins-graph.md) som helhet finns i [*instruktion: hantera den dubbla grafen med relationer*](how-to-manage-graph.md).

> [!TIP]
> Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

## <a name="create-a-digital-twin"></a>Skapa en digital, dubbel

Om du vill skapa en dubbel, använder du `CreateDigitalTwin` -metoden på tjänst klienten så här:

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

Om du vill skapa en digital Digital måste du ange:
* Önskat ID för den digitala dubbla
* Den [modell](concepts-models.md) som du vill använda 

Om du vill kan du ange startvärden för alla egenskaper för den digitala, dubbla. 

Värdena modell och initial egenskap anges via `initData` parametern, som är en JSON-sträng som innehåller relevanta data. Fortsätt till nästa avsnitt om du vill ha mer information om att strukturera objektet.

> [!TIP]
> När du har skapat eller uppdaterat en dubbel, kan det finnas en fördröjning på upp till 10 sekunder innan ändringarna visas i [frågor](how-to-query-graph.md). `GetDigitalTwin`API (beskrivs [längre fram i den här artikeln](#get-data-for-a-digital-twin)) förväntar sig inte den här fördröjningen, så Använd API-anropet i stället för att fråga om du vill se dina nyskapade dubblare om du behöver ett direkt svar. 

### <a name="initialize-model-and-properties"></a>Initiera modell och egenskaper

Det dubbla skapande-API: et accepterar ett objekt som är serialiserat i en giltig JSON-Beskrivning av de dubbla egenskaperna. Se [*begrepp: digitala garn och den dubbla grafen*](concepts-twins-graph.md) för en beskrivning av JSON-formatet för en dubbel. 

Först ska du skapa ett data objekt som representerar den dubbla och dess egenskaps data. Sedan kan du använda `JsonSerializer` för att skicka en serialiserad version av detta till API-anropet för `initdata` parametern.

Du kan skapa ett parameter objekt antingen manuellt eller med hjälp av en angiven hjälp klass. Här är ett exempel på var och en.

#### <a name="create-twins-using-manually-created-data"></a>Skapa dubbla med manuellt skapade data

Utan att använda anpassade hjälp klasser kan du representera en dubbels egenskaper i a `Dictionary<string, object>` , där `string` är namnet på egenskapen och `object` är ett objekt som representerar egenskapen och dess värde.

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Skapa dubbla med hjälp av klassen

Med hjälp klassen i `BasicDigitalTwin` kan du lagra egenskaps fält i ett "" ""-objekt mer direkt. Du kanske fortfarande vill bygga listan med egenskaper med hjälp av en `Dictionary<string, object>` , som sedan kan läggas till i det dubbla objektet som dess `CustomProperties` direkt.

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

>[!NOTE]
> `BasicDigitalTwin` objekt levereras med ett `Id` fält. Du kan lämna fältet tomt, men om du lägger till ett ID-värde måste det matcha ID-parametern som skickas till `CreateDigitalTwin` anropet. I exemplet ovan skulle det se ut så här:
>
>```csharp
>twin.Id = "myNewRoomID";
>```

## <a name="get-data-for-a-digital-twin"></a>Hämta data för en digital, dubbel

Du kan komma åt alla digitala data genom att anropa:

```csharp
object result = await client.GetDigitalTwin(id);
```

Anropet returnerar dubbla data som en JSON-sträng. 

> [!TIP]
> Endast egenskaper som har angetts minst en gång returneras när du hämtar ett dubbla med `GetDigitalTwin` .

Om du vill hämta flera multiplar med ett enda API-anrop, se fråge-API-exemplen i [*How-to: fråga det dubbla diagrammet*](how-to-query-graph.md).

Tänk på följande modell (skrivet i [Digitals definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) som definierar en *måne*:

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

Resultatet av att ringa `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` på en *måne*-typ kan se ut så här:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

De definierade egenskaperna för den digitala kanten returneras som toppnivå egenskaper på den digitala dubbla. Metadata-eller system information som inte ingår i DTDL-definitionen returneras med ett `$` prefix. Metadata-egenskaper inkluderar:
* ID: t för den digitala dubbla i den här Azure Digital-instansen, som `$dtId` .
* `$etag`, ett standard-HTTP-fält som tilldelas av webb servern
* Andra egenskaper i ett `$metadata` avsnitt. Dessa omfattar:
    - DTMI för den digitala dubbla.
    - Synkroniseringsstatus för varje skrivbar egenskap. Detta är mest användbart för enheter, där det är möjligt att tjänsten och enheten har avvikande status (till exempel när en enhet är offline). Den här egenskapen gäller för närvarande endast för fysiska enheter som är anslutna till IoT Hub. Med data i avsnittet metadata är det möjligt att förstå fullständig status för en egenskap samt de senast ändrade tidsstämplar. Mer information om synkroniseringsstatus finns i [den här IoT Hub själv studie kursen](../iot-hub/tutorial-device-twins.md) om synkronisering av enhets status.
    - Tjänstspecifika metadata, t. ex. från IoT Hub eller Azure digitala dubbla. 

Du kan parsa den returnerade JSON-filen för den dubbla med ett JSON-tolknings bibliotek som du väljer, till exempel `System.Text.Json` .

Du kan också använda den serialiserande hjälp klassen `BasicDigitalTwin` som ingår i SDK: n, vilket returnerar de dubbla metadata och egenskaperna i förparsat formulär. Här är ett exempel:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Du kan läsa mer om serialiserings hjälp klasser i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Uppdatera en digital delad

Om du vill uppdatera egenskaperna till en digital, skriver du den information som du vill ersätta i [JSON patch](http://jsonpatch.com/) -format. På så sätt kan du ersätta flera egenskaper samtidigt. Sedan skickar du JSON-korrigerings dokumentet till en `Update` metod:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Ett korrigerings anrop kan uppdatera så många egenskaper på samma sätt som du vill (även alla). Om du behöver uppdatera egenskaper över flera multiplar behöver du ett separat uppdaterings anrop för var och en.

> [!TIP]
> När du har skapat eller uppdaterat en dubbel, kan det finnas en fördröjning på upp till 10 sekunder innan ändringarna visas i [frågor](how-to-query-graph.md). `GetDigitalTwin`API: et (beskrivs [tidigare i den här artikeln](#get-data-for-a-digital-twin)) förväntar sig inte den här fördröjningen, så Använd API-anropet istället för att fråga om du vill se dina nyligen uppdaterade dubbla om du behöver ett direkt svar. 

Här är ett exempel på en JSON-patch-kod. Det här dokumentet ersätter *Mass* *-och RADIUS-* egenskapsvärdena för det digitala dubbla objektet som tillämpas på.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

Du kan skapa uppdateringar manuellt eller genom att använda en serialiserande hjälp klass i [SDK](how-to-use-apis-sdks.md). Här är ett exempel på var och en.

#### <a name="create-patches-manually"></a>Skapa korrigeringar manuellt

```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Skapa korrigeringar med hjälp av klassen

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Uppdatera egenskaper i digitala dubbla komponenter

Kom ihåg att en modell kan innehålla komponenter, så att den kan bestå av andra modeller. 

Om du vill uppdatera egenskaper i en digital, dubbla komponenter använder du Path-syntax i JSON-korrigering:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Uppdatera en digital Garns modell

`Update`Funktionen kan också användas för att migrera en digital, dubbel till en annan modell. 

Anta till exempel följande JSON-korrigerings dokument som ersätter det digitala `$model` området metadata:

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Den här åtgärden kan bara utföras om den digitala filen som ändras av korrigeringen överensstämmer med den nya modellen. 

Se följande exempel:
1. Föreställ dig ett digitalt med en modell av *foo_old*. *foo_old* definierar en obligatorisk egenskaps *vikt*.
2. Den nya modell *foo_new* definierar en egenskaps vikt och lägger till en ny obligatorisk egenskaps *temperatur*.
3. Efter korrigeringen måste den digitala, dubbla, ha både en egenskap för massa och temperatur. 

Korrigeringen för den här situationen måste uppdatera både modellen och den dubbla egenskapen temperatur, så här:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Hantera motstridiga uppdaterings anrop

Azure Digitals dubbla, säkerställer att alla inkommande begär Anden bearbetas efter den andra. Det innebär att även om flera funktioner försöker uppdatera samma egenskap på en enhet samtidigt, **behöver du inte** skriva explicit låsnings kod för att hantera konflikten.

Det här beteendet sker per nivå. 

Ett exempel är att anta ett scenario där de här tre anropen kommer till samma tidpunkt: 
*   Skriv egenskap A på *Twin1*
*   Skriv egenskap B på *Twin1*
*   Skriv egenskap A på *Twin2*

De två anropen som ändrar *Twin1* körs en efter en annan och ändrings meddelanden genereras för varje ändring. Anropet till Modify *Twin2* kan köras samtidigt utan konflikter, så snart det kommer.

## <a name="delete-a-digital-twin"></a>Ta bort en digital delad

Du kan ta bort dubbla med `DeleteDigitalTwin(ID)` . Men du kan bara ta bort en dubbel när den inte har fler relationer. Du måste först ta bort alla relationer. 

Här är ett exempel på koden för:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Ta bort alla digitala dubbla

Ett exempel på hur du tar bort alla dubbla på en gång finns i den exempel app som används i [*självstudien: utforska grunderna med ett exempel på ett klient program*](tutorial-command-line-app.md). *CommandLoop.cs* -filen gör detta i en `CommandDeleteAllTwins` funktion.

## <a name="manage-twins-with-cli"></a>Hantera dubbla med CLI

Uppdelade kan också hanteras med hjälp av Azure Digitals flätade CLI. Kommandona finns i [*anvisningar: använda Azure Digitals flätade CLI*](how-to-use-cli.md).

## <a name="next-steps"></a>Nästa steg

Se hur du skapar och hanterar relationer mellan digitala dubbla:
* [*Anvisningar: hantera den dubbla grafen med relationer*](how-to-manage-graph.md)