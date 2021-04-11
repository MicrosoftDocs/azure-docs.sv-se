---
title: Data beständighet och serialisering i Durable Functions – Azure
description: Lär dig hur Durable Functions-tillägget för Azure Functions sparar data
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 91c04b3943af5eee436bb4a18a946000b76cff3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057991"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Data beständighet och serialisering i Durable Functions (Azure Functions)

Durable Functions behåller automatiskt funktions parametrar, retur värden och andra tillstånd till en varaktig Server del för att tillhandahålla tillförlitlig körning. Mängden och frekvensen av data som behålls till varaktig lagring kan dock påverka kostnader för program prestanda och lagrings transaktioner. Beroende på vilken typ av data dina program lagrar, så kan data lagrings-och sekretess principer också behöva beaktas.

## <a name="azure-storage"></a>Azure Storage

Som standard behåller Durable Functions data till köer, tabeller och blobbar i ett [Azure Storage](https://azure.microsoft.com/services/storage/) konto som du anger.

### <a name="queues"></a>Köer

Durable Functions använder Azure Storage köer för att schemalägga alla funktions körningar på ett tillförlitligt sätt. Dessa Kömeddelanden innehåller funktioner eller utdata, beroende på om meddelandet används för att schemalägga en körning eller returnerar ett värde tillbaka till en anropande funktion. Dessa Kömeddelanden innehåller även ytterligare metadata som Durable Functions används för internt bruk, som Routning och slutpunkt-till-slutpunkt-korrelation. När en funktion har slutfört körningen som svar på ett mottaget meddelande, tas meddelandet bort och resultatet av körningen kan också vara bestående av antingen Azure Storage tabeller eller Azure Storage blobbar.

I en enda [aktivitets hubb](durable-functions-task-hubs.md)skapar Durable Functions och lägger till meddelanden i en kö för *arbets objekt* med namnet `<taskhub>-workitem` för schemaläggning av aktiviteter och en eller flera *kontroll köer* `<taskhub>-control-##` som heter schema-eller återuppta funktioner för Orchestrator och entitet. Antalet kontroll köer är lika med antalet partitioner som har kon figurer ATS för ditt program. Mer information om köer och partitioner finns i dokumentationen om [prestanda och skalbarhet](durable-functions-perf-and-scale.md).

### <a name="tables"></a>Tables

När dirigeringen bearbetar meddelanden sparas poster för deras resulterande åtgärder i *Historik* tabellen med namnet `<taskhub>History` . Orchestration-indata, utdata och anpassade status data sparas också i *instans* tabellen med namnet `<taskhub>Instances` .

### <a name="blobs"></a>Blobar

I de flesta fall använder Durable Functions inte Azure Storage blobbar för att bevara data. Köer och tabeller har dock [storleks gränser](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) som kan förhindra Durable Functions från att behålla alla data som krävs till en lagrings rad eller ett Queue-meddelande. Om t. ex. en data mängd som måste sparas i en kö är större än 45 KB, komprimerar Durable Functions data och lagrar dem i en BLOB i stället. När du bevarar data till Blob Storage på det här sättet lagrar en beständig funktion en referens till denna BLOB i tabell raden eller Queue meddelandet. När Durable Functions behöver hämta data hämtas de automatiskt från blobben. Dessa blobbar lagras i BLOB-behållaren `<taskhub>-largemessages` .

> [!NOTE]
> Stegen för extra komprimering och blob-åtgärd för stora meddelanden kan vara dyra vad gäller CPU-och I/O-latenens kostnader. Dessutom måste Durable Functions läsa in sparade data i minnet och kan göra det för många olika funktions körningar samtidigt. Det innebär att även om du behåller stora data nytto laster kan det också orsaka hög minnes användning. För att minimera minnes belastningen bör du överväga att bevara stora data nytto laster manuellt (till exempel i Blob Storage) och i stället skicka runt referenser till dessa data. På så sätt kan din kod bara läsa in data när de behövs för att undvika redundanta inläsningar när [Orchestrator-funktionen spelas upp](durable-functions-orchestrations.md#reliability). Att lagra nytto laster på disk rekommenderas dock *inte* eftersom det inte är säkert att det är tillgängligt eftersom funktioner kan köras på olika virtuella datorer under deras livstid.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Typer av data som är serialiserade och beständiga
Följande är en lista med de olika typer av data som kommer att serialiseras och sparas när du använder funktioner i Durable Functions:

- Alla indata och utdata från funktioner för Orchestrator, aktivitet och entitet, inklusive ID: n och ohanterade undantag
- Funktions namn för Orchestrator, aktivitet och entitet
- Externa händelse namn och nytto laster
- Status nytto laster för anpassad dirigering
- Meddelanden om Dirigerings avslutning
- Varaktiga timer-nyttolaster
- Varaktiga URL: er, sidhuvud och nytto laster för HTTP-begäran och svar
- Enhets anrop och signal nytto laster
- Enhets tillstånds nytto laster

### <a name="working-with-sensitive-data"></a>Arbeta med känsliga data
När du använder Azure Storage krypteras alla data automatiskt i vila. Alla som har åtkomst till lagrings kontot kan dock läsa data i okrypterad form. Om du behöver starkare skydd för känsliga data bör du överväga att först kryptera data med hjälp av dina egna krypterings nycklar så att Durable Functions sparar data i ett förkrypterat format.

.NET-användare kan också välja att implementera anpassade serialiserings-providers som tillhandahåller automatisk kryptering. Ett exempel på anpassad serialisering med kryptering finns i [det här GitHub-exemplet](https://github.com/charleszipp/azure-durable-entities-encryption).

> [!NOTE]
> Om du bestämmer dig för att implementera kryptering på program nivå måste du vara medveten om att dirigeringar och entiteter kan finnas för obestämd tid. Detta är viktigt när det tar tid att rotera dina krypterings nycklar eftersom en dirigering eller entiteter kan köras längre än din nyckel rotations princip. Om en nyckel rotation inträffar kanske den nyckel som används för att kryptera dina data inte längre är tillgänglig för att dekryptera den nästa gång din dirigering eller entitet körs. Kund kryptering rekommenderas därför endast när dirigeringar och entiteter förväntas köras under relativt korta tids perioder.

## <a name="customizing-serialization-and-deserialization"></a>Anpassa serialisering och deserialisering

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Standard logik för serialisering

Durable Functions använder [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) internt för att serialisera Orchestration-och Entity-data till JSON. Standardinställningarna som Durable Functions används för Json.NET är:

**Indata, utdata och tillstånd:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Undantag**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Läs mer detaljerad dokumentation om `JsonSerializerSettings` [detta](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>Anpassa serialisering med .NET-attribut

Vid serialisering av data söker Json.NET efter [olika attribut](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) i klasser och egenskaper som styr hur data serialiseras och avserialiseras från JSON. Om du äger käll koden för den datatyp som skickas till Durable Functions API: er kan du överväga att lägga till dessa attribut till typen för att anpassa serialisering och deserialisering.

## <a name="customizing-serialization-with-dependency-injection"></a>Anpassa serialisering med beroende inmatning

Function-appar som är riktade till .NET och körs på Functions v3-körningen kan använda [beroende inmatning (di)](../functions-dotnet-dependency-injection.md) för att anpassa hur data och undantag serialiseras. Exempel koden nedan visar hur du använder DI för att åsidosätta standardvärden för Json.NET-serialisering med anpassade implementeringar `IMessageSerializerSettingsFactory` av `IErrorSerializerSettingsFactory` gränssnitten och.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Logik för serialisering och deserialisering

Azure Functions-Node-program använder [ `JSON.stringify()` för serialisering](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) och [ `JSON.Parse()` deserialisering](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). De flesta typer ska serialisera och deserialisera sömlöst. I de fall där standard logiken är otillräckligt, `toJSON()` kapas serialiserings logiken genom att definiera en metod i objektet. Det finns dock ingen analog för avserialisering av objekt.

För fullständig anpassning av pipelinen för serialisering/deserialisering bör du överväga att hantera serialisering och deserialisering med din egen kod och skicka runt data som strängar.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Logik för serialisering och deserialisering

Vi rekommenderar starkt att du använder typ anteckningar för att säkerställa Durable Functions serialiserar och deserialiserar dina data på rätt sätt. Även om många inbyggda typer hanteras automatiskt, kräver vissa inbyggda data typer typ anteckningar för att bevara typen under deserialisering.

För anpassade data typer måste du definiera JSON-serialisering och deserialisering av en datatyp genom att exportera en statisk `to_json` metod och en `from_json` metod från din klass.

---
