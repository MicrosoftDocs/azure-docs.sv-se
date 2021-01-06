---
title: Vägledning för att utveckla Azure Functions
description: Lär dig Azure Functions koncept och tekniker som du behöver för att utveckla funktioner i Azure, i alla programmeringsspråk och bindningar.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: dd9a517749030f9f99731d36947c4d4ff2f13b01
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936744"
---
# <a name="azure-functions-developer-guide"></a>Utvecklarguide för Azure Functions
I Azure Functions delar specifika funktioner några viktiga tekniska koncept och komponenter, oavsett vilket språk eller vilken bindning du använder. Innan du hoppar till inlärnings information som är specifik för ett specifikt språk eller en bindning bör du läsa igenom den här översikten som gäller för alla.

Den här artikeln förutsätter att du redan har läst [Översikt över Azure Functions](functions-overview.md).

## <a name="function-code"></a>Funktions kod
En *funktion* är det primära konceptet i Azure Functions. En funktion innehåller två viktiga delar – din kod, som kan skrivas på en rad olika språk, och en del konfiguration, function.jspå fil. För kompilerade språk genereras den här konfigurations filen automatiskt från anteckningar i din kod. För skript språk måste du ange konfigurations filen själv.

Filen function.jsi definierar funktionens utlösare, bindningar och andra konfigurations inställningar. Varje funktion har endast en utlösare. Körningen använder den här konfigurations filen för att bestämma vilka händelser som ska övervakas och hur data ska skickas till och returnera data från en funktions körning. Följande är ett exempel function.jsi filen.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

`bindings`Egenskapen är den plats där du konfigurerar både utlösare och bindningar. Varje bindning delar några vanliga inställningar och vissa inställningar som är specifika för en viss typ av bindning. Varje bindning kräver följande inställningar:

| Egenskap | Värden/typer | Kommentarer |
| --- | --- | --- |
| `type` |sträng |Bindnings typ. Ett exempel är `queueTrigger`. |
| `direction` |in, out |Anger om bindningen avser att ta emot data i funktionen eller skicka data från funktionen. |
| `name` |sträng |Namnet som används för de data som är kopplade till funktionen. För C# är detta ett argument namn. för Java Script är det nyckeln i en nyckel/värde-lista. |

## <a name="function-app"></a>Funktionsapp
En Function-app tillhandahåller en körnings kontext i Azure där dina funktioner körs. Därför är det enheten för distribution och hantering av dina funktioner. En Function-app består av en eller flera enskilda funktioner som hanteras, distribueras och skalas tillsammans. Alla funktioner i en Function-app delar samma pris plan, distributions metod och körnings version. Tänk på en Function-app som ett sätt att ordna och hantera dina funktioner gemensamt. Mer information finns i [hantera en Function-app](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Alla funktioner i en Function-app måste ha skapats på samma språk. I [tidigare versioner](functions-versions.md) av Azure Functions runtime krävdes detta inte.

## <a name="folder-structure"></a>Mappstruktur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Ovanstående är standard mappstrukturen för en Function-app. Om du vill ändra sökvägen till en funktions kod ändrar du `scriptFile` avsnittet i _function.jspå_ filen. Vi rekommenderar också att du använder [paket distribution](deployment-zip-push.md) för att distribuera ditt projekt till Function-appen i Azure. Du kan också använda befintliga verktyg som [kontinuerlig integrering och distribution](functions-continuous-deployment.md) och Azure-DevOps.

> [!NOTE]
> Om du distribuerar ett paket manuellt ska du se till att distribuera _host.jspå_ fil-och funktions-mappar direkt till `wwwroot` mappen. Inkludera inte `wwwroot` mappen i dina distributioner. Annars slutar du med `wwwroot\wwwroot` mappar.

#### <a name="use-local-tools-and-publishing"></a>Använd lokala verktyg och publicera
Function-appar kan skapas och publiceras med en rad olika verktyg, inklusive [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Sol förmörkelse](./functions-create-maven-eclipse.md)och [Azure Functions Core tools](./functions-develop-local.md). Mer information finns i [kod-och test Azure Functions lokalt](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Redigera funktioner i Azure Portal
Funktions redigeraren som är inbyggd i Azure Portal låter dig uppdatera koden och *function.jspå* filen direkt. Detta rekommenderas endast för små ändringar eller koncept bevis – bästa praxis är att använda ett lokalt utvecklingsverktyg som VS Code.

## <a name="parallel-execution"></a>Parallell körning
När flera Utlös ande händelser inträffar snabbare än en process med en enkel tråds funktion kan körningen köra funktionen flera gånger parallellt.  Om en Function-app använder [förbruknings värd planen](event-driven-scaling.md)kan Function-appen skala ut automatiskt.  Varje instans av Function-appen, oavsett om appen körs på förbruknings värd planen eller en vanlig [App Service värd plan](../app-service/overview-hosting-plans.md), kan bearbeta samtidiga funktions anrop parallellt med flera trådar.  Det maximala antalet samtidiga funktions anrop i varje Function-serverinstans varierar beroende på vilken typ av utlösare som används och vilka resurser som används av andra funktioner i Function-appen.

## <a name="functions-runtime-versioning"></a>Functions runtime-versioner

Du kan konfigurera versionen av Functions-körningen med `FUNCTIONS_EXTENSION_VERSION` appens inställning. Värdet "~ 3" anger till exempel att din Function-app ska använda 3. x som dess huvud version. Function-appar uppgraderas till varje ny del version när de släpps. Mer information, inklusive hur du visar den exakta versionen av din Function-app finns i [så här riktar du Azure Functions runtime-versioner](set-runtime-version.md).

## <a name="repositories"></a>Centrallager
Koden för Azure Functions är öppen källkod och lagras i GitHub-databaser:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions värd](https://github.com/Azure/azure-functions-host/)
* [Azure Functions Portal](https://github.com/azure/azure-functions-ux)
* [Azure Functions mallar](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindningar
Här är en tabell med alla bindningar som stöds.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Har du problem med fel som kommer från bindningarna? Läs dokumentationen om [Azure Functions bindnings fel koder](functions-bindings-error-pages.md) .

## <a name="reporting-issues"></a>Rapporterings problem
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Koda och testa Azure Functions lokalt](./functions-develop-local.md)
* [Metod tips för Azure Functions](functions-best-practices.md)
* [Referens för Azure Functions C#-utvecklare](functions-dotnet-class-library.md)
* [Referens för Azure Functions Node.js-utvecklare](functions-reference-node.md)