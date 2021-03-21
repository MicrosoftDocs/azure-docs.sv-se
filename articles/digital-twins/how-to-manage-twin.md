---
title: Hantera digitala tvillingar
titleSuffix: Azure Digital Twins
description: Se hur du hämtar, uppdaterar och tar bort enskilda dubbla och relationer.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 98b50673b464044af2a038fa93c3b6a022fa2899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149711"
---
# <a name="manage-digital-twins"></a>Hantera digitala tvillingar

Entiteter i din miljö representeras av [digitala dubbla](concepts-twins-graph.md). Att hantera digitala dubbla, kan vara att skapa, ändra och ta bort. Om du vill utföra dessa åtgärder kan du använda [**DigitalTwins-API: er**](/rest/api/digital-twins/dataplane/twins), [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)eller [Azure Digitals flätade CLI](how-to-use-cli.md).

Den här artikeln fokuserar på att hantera digitala dubbla, information om hur du arbetar med relationer och det [dubbla diagrammet](concepts-twins-graph.md) som helhet finns i [*instruktion: hantera den dubbla grafen med relationer*](how-to-manage-graph.md).

> [!TIP]
> Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Olika sätt att hantera dubbla

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Skapa en digital, dubbel

Om du vill skapa en dubbel, använder du `CreateOrReplaceDigitalTwinAsync()` -metoden på tjänst klienten så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

Om du vill skapa en digital Digital måste du ange:
* Önskat ID för den digitala dubbla
* Den [modell](concepts-models.md) som du vill använda

Om du vill kan du ange startvärden för alla egenskaper för den digitala, dubbla. Egenskaperna behandlas som valfria och kan ställas in senare, men **de visas inte som en del av den dubbla tills de har angetts.**

>[!NOTE]
>Även om dubbla egenskaper **inte behöver initieras, måste** alla [komponenter](concepts-models.md#elements-of-a-model) på den dubbla anges när den skapas. De kan vara tomma objekt, men själva komponenterna måste finnas.

Modellen och eventuella inledande egenskaps värden tillhandahålls via `initData` parametern, som är en JSON-sträng som innehåller relevanta data. Fortsätt till nästa avsnitt om du vill ha mer information om att strukturera objektet.

> [!TIP]
> När du har skapat eller uppdaterat en dubbel, kan det finnas en fördröjning på upp till 10 sekunder innan ändringarna visas i [frågor](how-to-query-graph.md). `GetDigitalTwin`API (beskrivs [längre fram i den här artikeln](#get-data-for-a-digital-twin)) förväntar sig inte den här fördröjningen, så om du behöver ett direkt svar använder du API-anropet i stället för att fråga för att se dina nyligen skapade dubbla. 

### <a name="initialize-model-and-properties"></a>Initiera modell och egenskaper

Du kan initiera egenskaperna för en dubbel vid den tidpunkt då den dubbla skapas. 

Det dubbla skapande-API: et accepterar ett objekt som är serialiserat i en giltig JSON-Beskrivning av de dubbla egenskaperna. Se [*begrepp: digitala garn och den dubbla grafen*](concepts-twins-graph.md) för en beskrivning av JSON-formatet för en dubbel. 

Först kan du skapa ett data objekt som representerar den dubbla och dess egenskaps data. Du kan skapa ett parameter objekt antingen manuellt eller med hjälp av en angiven hjälp klass. Här är ett exempel på var och en.

#### <a name="create-twins-using-manually-created-data"></a>Skapa dubbla med manuellt skapade data

Utan att använda anpassade hjälp klasser kan du representera en dubbels egenskaper i a `Dictionary<string, object>` , där `string` är namnet på egenskapen och `object` är ett objekt som representerar egenskapen och dess värde.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Skapa dubbla med hjälp av klassen

Med hjälp klassen i `BasicDigitalTwin` kan du lagra egenskaps fält i ett "" ""-objekt direkt. Du kanske fortfarande vill bygga listan med egenskaper med hjälp av en `Dictionary<string, object>` , som sedan kan läggas till i det dubbla objektet som dess `CustomProperties` direkt.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` objekt levereras med ett `Id` fält. Du kan lämna fältet tomt, men om du lägger till ett ID-värde måste det matcha ID-parametern som skickas till `CreateOrReplaceDigitalTwinAsync()` anropet. Exempel:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Hämta data för en digital, dubbel

Du kan komma åt information om alla digitala dubbla genom att anropa- `GetDigitalTwin()` metoden så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Det här anropet returnerar dubbla data som en starkt skriven objekt typ, t `BasicDigitalTwin` . ex.. `BasicDigitalTwin` är en hjälp klass för serialisering som ingår i SDK, vilket returnerar de dubbla metadata och egenskaperna i förparsat formulär. Här är ett exempel på hur du kan använda den för att Visa dubbel information:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Endast egenskaper som har angetts minst en gång returneras när du hämtar en delad med- `GetDigitalTwin()` metoden.

>[!TIP]
>`displayName`För en enhet är en del av modellens metadata, så den visas inte när data hämtas för den dubbla instansen. Om du vill se det här värdet kan du [Hämta det från modellen](how-to-manage-model.md#retrieve-models).

Om du vill hämta flera multiplar med ett enda API-anrop, se fråge-API-exemplen i [*How-to: fråga det dubbla diagrammet*](how-to-query-graph.md).

Tänk på följande modell (skrivet i [Digitals definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) som definierar en *måne*:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

Resultatet av att ringa `object result = await client.GetDigitalTwinAsync("my-moon");` på en *måne*-typ kan se ut så här:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
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

De definierade egenskaperna för den digitala kanten returneras som toppnivå egenskaper på den digitala dubbla. Metadata-eller system information som inte ingår i DTDL-definitionen returneras med ett `$` prefix. Metadata-egenskaperna innehåller följande värden:
* `$dtId`: ID: t för den digitala dubbla i den här Azure Digital-instansen
* `$etag`: Ett standard-HTTP-fält som tilldelas av webb servern. Detta uppdateras till ett nytt värde varje gång den dubbla uppdateras, vilket kan vara användbart för att avgöra om den dubbla data har uppdaterats på servern sedan en tidigare kontroll. Den kan också användas i HTTP-huvuden på följande sätt:
  - med Läs åtgärder för att undvika att hämta innehåll som inte har ändrats
  - med Skriv åtgärder för att stödja optimistisk samtidighet
* `$metadata`: En uppsättning andra egenskaper, inklusive:
  - DTMI för den digitala dubbla.
  - Synkroniseringsstatus för varje skrivbar egenskap. Detta är mest användbart för enheter, där det är möjligt att tjänsten och enheten har avvikande status (till exempel när en enhet är offline). Den här egenskapen gäller för närvarande endast för fysiska enheter som är anslutna till IoT Hub. Med data i avsnittet metadata är det möjligt att förstå fullständig status för en egenskap samt de senast ändrade tidsstämplar. Mer information om synkroniseringsstatus finns i [den här IoT Hub själv studie kursen](../iot-hub/tutorial-device-twins.md) om synkronisering av enhets status.
  - Tjänstspecifika metadata, t. ex. från IoT Hub eller Azure digitala dubbla. 

Du kan läsa mer om serialiserings hjälp klasser som `BasicDigitalTwin` i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md).

## <a name="view-all-digital-twins"></a>Visa alla digitala dubbla

Om du vill visa alla digitala dubbla i din instans använder du en [fråga](how-to-query-graph.md). Du kan köra en fråga med [fråge-API: erna](/rest/api/digital-twins/dataplane/query) eller [CLI-kommandona](how-to-use-cli.md).

Här är bröd texten i den grundläggande frågan som returnerar en lista över alla digitala, dubbla, i instansen:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Uppdatera en digital delad

Om du vill uppdatera egenskaperna för en digital, så skriver du den information som du vill ersätta i [JSON patch](http://jsonpatch.com/) -format. På så sätt kan du ersätta flera egenskaper samtidigt. Sedan skickar du JSON-korrigerings dokumentet till en `UpdateDigitalTwin()` metod:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Ett korrigerings anrop kan uppdatera så många egenskaper på samma sätt som du vill (även alla). Om du behöver uppdatera egenskaper över flera multiplar behöver du ett separat uppdaterings anrop för var och en.

> [!TIP]
> När du har skapat eller uppdaterat en dubbel, kan det finnas en fördröjning på upp till 10 sekunder innan ändringarna visas i [frågor](how-to-query-graph.md). `GetDigitalTwin`API: et (beskrivs [tidigare i den här artikeln](#get-data-for-a-digital-twin)) förväntar sig inte den här fördröjningen, så Använd API-anropet istället för att fråga om du vill se dina nyligen uppdaterade dubbla om du behöver ett direkt svar. 

Här är ett exempel på en JSON-patch-kod. Det här dokumentet ersätter *Mass* *-och RADIUS-* egenskapsvärdena för det digitala dubbla objektet som tillämpas på.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Du kan skapa uppdateringar med hjälp av Azure .NET SDK: s [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument). Här är ett exempel.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>Uppdatera egenskaper i digitala dubbla komponenter

Kom ihåg att en modell kan innehålla komponenter, så att den kan bestå av andra modeller. 

Om du vill korrigera egenskaper i en digital-enhets komponent kan du använda Path-syntax i JSON-korrigering:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>Uppdatera en digital Garns modell

`UpdateDigitalTwin()`Funktionen kan också användas för att migrera en digital, dubbel till en annan modell. 

Anta till exempel följande JSON-korrigerings dokument som ersätter det digitala `$model` området metadata:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Den här åtgärden kan bara utföras om den digitala filen som ändras av korrigeringen överensstämmer med den nya modellen. 

Se följande exempel:
1. Föreställ dig ett digitalt med en modell av *foo_old*. *foo_old* definierar en obligatorisk egenskaps *vikt*.
2. Den nya modell *foo_new* definierar en egenskaps vikt och lägger till en ny obligatorisk egenskaps *temperatur*.
3. Efter korrigeringen måste den digitala, dubbla, ha både en egenskap för massa och temperatur. 

Korrigeringen för den här situationen måste uppdatera både modellen och den dubbla egenskapen temperatur, så här:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Hantera motstridiga uppdaterings anrop

Azure Digitals dubbla, säkerställer att alla inkommande begär Anden bearbetas efter den andra. Det innebär att även om flera funktioner försöker uppdatera samma egenskap på en enhet samtidigt, **behöver du inte** skriva explicit låsnings kod för att hantera konflikten.

Det här beteendet sker per nivå. 

Ett exempel är att anta ett scenario där de här tre anropen kommer till samma tidpunkt: 
*   Skriv egenskap A på *Twin1*
*   Skriv egenskap B på *Twin1*
*   Skriv egenskap A på *Twin2*

De två anropen som ändrar *Twin1* körs en efter en annan och ändrings meddelanden genereras för varje ändring. Anropet till Modify *Twin2* kan köras samtidigt utan konflikter, så snart det kommer.

## <a name="delete-a-digital-twin"></a>Ta bort en digital delad

Du kan ta bort dubbla med- `DeleteDigitalTwin()` metoden. Men du kan bara ta bort en dubbel när den inte har fler relationer. Så ta bort de dubbla inkommande och utgående relationerna först.

Här är ett exempel på koden för att ta bort dubbla och deras relationer. `DeleteDigitalTwin`SDK-anropet är markerat för att klargöra var det hamnar i den bredare exempel kontexten.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Ta bort alla digitala dubbla

Ett exempel på hur du tar bort alla dubbla på en gång finns i den exempel app som används i [*självstudien: utforska grunderna med ett exempel på ett klient program*](tutorial-command-line-app.md). Filen *CommandLoop. cs* gör detta i en `CommandDeleteAllTwins()` funktion.

## <a name="runnable-digital-twin-code-sample"></a>Körbara digital kod exempel

Du kan använda körbara-kod exemplet nedan för att skapa en dubbel, uppdatera dess information och ta bort den dubbla. 

### <a name="set-up-the-runnable-sample"></a>Konfigurera körbara-exemplet

Kodfragmentet använder [Room.jspå](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) modell definitionen från [*självstudien: utforska digitala Azure-enheter med ett exempel på en klient-app*](tutorial-command-line-app.md). Du kan använda den här länken för att gå direkt till filen eller ladda ned den som en del av ett fullständigt exempel projekt från början till slut [här](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Innan du kör exemplet gör du följande:
1. Ladda ned modell filen, placera den i projektet och Ersätt `<path-to>` plats hållaren i koden nedan för att tala om för programmet var du hittar det.
2. Ersätt plats hållaren `<your-instance-hostname>` med din Azure Digital-instansen värdnamn.
3. Lägg till två beroenden i projektet som behövs för att arbeta med Azure Digital-dubbla. Det första är paketet för [Azure Digitals-SDK: n för .net](/dotnet/api/overview/azure/digitaltwins/client), den andra innehåller verktyg som hjälper dig att autentisera mot Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Du måste också konfigurera lokala autentiseringsuppgifter om du vill köra exemplet direkt. Nästa avsnitt går igenom detta.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Kör exemplet

När du har slutfört ovanstående steg kan du köra följande exempel kod direkt.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

Här är konsol resultatet av programmet ovan: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Konsol resultat som visar att den dubbla skapas, uppdateras och tas bort" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Nästa steg

Se hur du skapar och hanterar relationer mellan digitala dubbla:
* [*Anvisningar: hantera den dubbla grafen med relationer*](how-to-manage-graph.md)