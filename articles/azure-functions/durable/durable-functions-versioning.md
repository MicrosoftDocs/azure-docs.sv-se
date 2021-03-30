---
title: Versions hantering i Durable Functions – Azure
description: Lär dig hur du implementerar versioner i Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74232760"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versions hantering i Durable Functions (Azure Functions)

Det är oundvikligt att funktioner kommer att läggas till, tas bort och ändras under ett programs livs längd. [Durable Functions](durable-functions-overview.md) tillåter länkning på ett sätt som inte tidigare var möjligt, och den här länkningen påverkar hur du kan hantera versions hantering.

## <a name="how-to-handle-breaking-changes"></a>Så här hanterar du avbryter ändringar

Det finns flera exempel på hur du kan bryta ändringar för att vara medveten om. Den här artikeln beskriver de vanligaste. Huvud temat bakom alla är att både nya och befintliga funktions dirigeringar påverkas av ändringar i funktions koden.

### <a name="changing-activity-or-entity-function-signatures"></a>Ändra signaturer för aktivitet eller entitets funktion

En signatur ändras till en ändring i namn, indata eller utdata för en funktion. Om den här typen av ändring görs i en aktivitet eller en enhets funktion kan den bryta alla Orchestrator-funktioner som är beroende av den. Om du uppdaterar Orchestrator-funktionen för att hantera den här ändringen kan du bryta befintliga instansen av flyg.

Anta till exempel att vi har följande Orchestrator-funktion.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Den här förenklad-funktionen tar resultatet av **foo** och skickar det till **bar**. Vi antar att vi behöver ändra returvärdet för **foo** från `bool` till som stöd för `int` en större mängd resultat värden. Resultatet ser ut så här:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Föregående C#-exempel riktar sig Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

Den här ändringen fungerar bra för alla nya instanser av Orchestrator-funktionen men alla eventuella instansen bryts. Anta till exempel att en Dirigerings instans anropar en funktion med namnet `Foo` , hämtar ett booleskt värde och sedan kontroll punkter. Om ändring av signaturen har distribuerats i det här läget Miss kan den inloggade instansen omedelbart när den återupptas och spelar upp anropet till `context.CallActivityAsync<int>("Foo")` . Det här felet uppstår eftersom resultatet i historik tabellen är `bool` men den nya koden försöker deserialisera den till `int` .

Det här exemplet är bara en av många olika sätt att en signaturverifiering kan bryta befintliga instanser. I allmänhet är det troligt att om en Orchestrator behöver ändra hur den anropar en funktion, är ändringen sannolikt problematisk.

### <a name="changing-orchestrator-logic"></a>Ändra Orchestrator-logik

Den andra klassen av versions problem kommer att ändra koden för Orchestrator-funktionen på ett sätt som innebär att omuppspelnings logiken för instansen i flyget aktive.

Överväg följande Orchestrator-funktion:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nu ska vi anta att du vill göra en synes Innocent ändring för att lägga till ett annat funktions anrop.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Föregående C#-exempel riktar sig Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext` . Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

Den här ändringen lägger till ett nytt funktions anrop till **SendNotification** mellan **foo** och **bar**. Det finns inga ändringar i signaturen. Problemet uppstår när en befintlig instans återupptas från anropet till **bar**. Om det ursprungliga anropet till **foo** returnerades under uppspelningen, `true` kommer Orchestrator-uppspelningen att anropa till **SendNotification**, som inte finns i dess körnings historik. Därför Miss lyckas det varaktiga aktivitets ramverket med en `NonDeterministicOrchestrationException` eftersom det påträffade ett anrop till **SendNotification** när det förväntades Visa ett anrop till **bar**. Samma typ av problem kan uppstå när du lägger till anrop till "varaktiga" API: er, inklusive `CreateTimer` , `WaitForExternalEvent` osv.

## <a name="mitigation-strategies"></a>Strategier för minskning

Här följer några av strategierna för att hantera problem med versions hantering:

* Gör ingenting
* Stoppa alla instansen av flyg
* Distributioner sida vid sida

### <a name="do-nothing"></a>Gör ingenting

Det enklaste sättet att hantera en brytande ändring är att tillåta att instansen av flygbaserade Orchestration-instanser inte fungerar. Nya instanser har kört den ändrade koden.

Om den här typen av fel är ett problem beror på hur viktigt dina instansen är. Om du arbetar i aktiv utveckling och inte bryr dig om instansen av flyg kan detta vara tillräckligt tillräckligt. Du måste dock hantera undantag och fel i din diagnostiska pipeline. Om du vill undvika dessa saker bör du överväga de andra versions alternativen.

### <a name="stop-all-in-flight-instances"></a>Stoppa alla instansen av flyg

Ett annat alternativ är att stoppa alla instansen av flyg. Du kan stoppa alla instanser genom att rensa innehållet i köerna för intern **kontroll-kö** och **WorkItem-** köer. Instanserna kommer alltid att ha fastnat där de är, men de kommer inte att göra dina loggar med meddelanden om problem. Den här metoden är idealisk i snabb prototyp utveckling.

> [!WARNING]
> Informationen om dessa köer kan ändras med tiden, så du behöver inte använda den här metoden för produktions arbets belastningar.

### <a name="side-by-side-deployments"></a>Distributioner sida vid sida

Det vanligaste sättet att se till att de hårda ändringarna distribueras på ett säkert sätt är att distribuera dem sida vid sida med dina äldre versioner. Detta kan göras med någon av följande metoder:

* Distribuera alla uppdateringar som helt nya funktioner och lämna befintliga funktioner i befintligt skick. Detta kan vara svårt eftersom anroparna i de nya funktions versionerna måste uppdateras och följer samma rikt linjer.
* Distribuera alla uppdateringar som en ny function-app med ett annat lagrings konto.
* Distribuera en ny kopia av Function-appen med samma lagrings konto, men med ett uppdaterat `taskHub` namn. Distributioner sida vid sida är den rekommenderade metoden.

### <a name="how-to-change-task-hub-name"></a>Ändra namn på aktivitets hubb

Aktivitets navet kan konfigureras i *host.jspå filen på* följande sätt:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functions 2,0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Standardvärdet för Durable Functions v1. x `DurableFunctionsHub` . Från och med Durable Functions v 2.0 är standard namnet på aktivitets navet samma som namnet på appens funktion i Azure, eller `TestHubName` om det körs utanför Azure.

Alla Azure Storage entiteter namnges baserat på `hubName` konfiguration svärdet. Genom att ge huvudhubben ett nytt namn ser du till att separata köer och historik tabell skapas för den nya versionen av programmet. Function-appen kommer dock att sluta bearbeta händelser för dirigeringar eller entiteter som skapats under föregående aktivitets nav namn.

Vi rekommenderar att du distribuerar den nya versionen av Function-appen till en ny [distributions plats](../functions-deployment-slots.md). Med distributions platser kan du köra flera kopior av funktions programmet sida vid sida med bara en av dem som aktiv *produktions* plats. När du är redo att exponera den nya organisations logiken i din befintliga infrastruktur kan det vara så enkelt som att byta ut den nya versionen till produktions platsen.

> [!NOTE]
> Den här strategin fungerar bäst när du använder HTTP-och webhook-utlösare för Orchestrator functions. För icke-HTTP-utlösare, till exempel köer eller Event Hubs, ska utlösnings definitionen [härledas från en app-inställning](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) som uppdateras som en del av växlings åtgärden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar problem med prestanda och skalning](durable-functions-perf-and-scale.md)
