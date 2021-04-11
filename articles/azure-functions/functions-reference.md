---
title: Vägledning för att utveckla Azure Functions
description: Lär dig Azure Functions koncept och tekniker som du behöver för att utveckla funktioner i Azure, i alla programmeringsspråk och bindningar.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614827"
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

| Egenskap    | Värden | Typ | Kommentarer|
|---|---|---|---|
| typ  | Bindnings namn.<br><br>Till exempel `queueTrigger`. | sträng | |
| riktning | `in`, `out`  | sträng | Anger om bindningen avser att ta emot data i funktionen eller skicka data från funktionen. |
| name | Funktions identifierare.<br><br>Till exempel `myQueue`. | sträng | Namnet som används för de data som är kopplade till funktionen. För C# är detta ett argument namn. för Java Script är det nyckeln i en nyckel/värde-lista. |

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


## <a name="connections"></a>Anslutningar

Ditt funktions projekt refererar anslutnings information efter namn från dess Konfigurationsprovider. Anslutnings informationen accepteras inte direkt, så att de kan ändras i olika miljöer. En utlösnings definition kan till exempel innehålla en `connection` egenskap. Detta kan referera till en anslutnings sträng, men du kan inte ange anslutnings strängen direkt i en `function.json` . I stället skulle du ange `connection` namnet på en miljö variabel som innehåller anslutnings strängen.

Standardprovidern för konfiguration använder miljövariabler. De kan ställas in genom [program inställningar](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) när de körs i Azure Functionss tjänsten eller från den [lokala inställnings filen](functions-run-local.md#local-settings-file) när du utvecklar lokalt.

### <a name="connection-values"></a>Anslutnings värden

När anslutnings namnet matchar ett enda exakt värde, identifierar körningen värdet som en _anslutnings sträng_, som vanligt vis innehåller en hemlighet. Information om en anslutnings sträng definieras av den tjänst som du vill ansluta till.

Ett anslutnings namn kan dock också referera till en samling med flera konfigurations objekt. Miljövariabler kan behandlas som en samling med hjälp av ett delat prefix som slutar med dubbel under streck `__` . Gruppen kan sedan refereras genom att ange anslutnings namnet till det här prefixet.

Till exempel `connection` kan egenskapen för en definition av en Azure Blob-utlösare vara `Storage1` . Så länge det inte finns något enskilt sträng värde konfigurerat med `Storage1` som namn, `Storage1__serviceUri` skulle användas för `serviceUri` anslutningens egenskap. Anslutnings egenskaperna är olika för varje tjänst. Läs dokumentationen för tillägget som använder-anslutningen.

### <a name="configure-an-identity-based-connection"></a>Konfigurera en Identity-baserad anslutning

Vissa anslutningar i Azure Functions konfigureras för att använda en identitet i stället för en hemlighet. Stödet är beroende av tillägget med anslutningen. I vissa fall kan en anslutnings sträng fortfarande krävas i functions även om tjänsten som du ansluter till har stöd för identitetsbaserade anslutningar.

> [!IMPORTANT]
> Även om ett bindnings tillägg har stöd för identitetsbaserade anslutningar kanske den konfigurationen inte stöds än i förbruknings planen. Se support tabellen nedan.

Identitetsbaserade anslutningar stöds av följande utlösare och bindnings tillägg:

| Tilläggs namn | Tilläggsversion                                                                                     | Stöds i förbruknings planen |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Azure-blobb     | [Version 5.0.0 – beta1 eller senare](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | Inga                                    |
| Azure Queue    | [Version 5.0.0 – beta1 eller senare](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | Inga                                    |
| Azure Event Hubs    | [Version 5.0.0 – beta1 eller senare](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | Inga                                    |

> [!NOTE]
> Stöd för identitetsbaserade anslutningar är ännu inte tillgängligt för lagrings anslutningar som används av Functions-körningen för kärn funktioner. Det innebär att `AzureWebJobsStorage` inställningen måste vara en anslutnings sträng.

#### <a name="connection-properties"></a>Anslutningsegenskaper

En identitets baserad anslutning för en Azure-tjänst godkänner följande egenskaper:

| Egenskap    | Krävs för tillägg | Miljövariabel | Beskrivning |
|---|---|---|---|
| Tjänst-URI | Azure-Blob, Azure-kö | `<CONNECTION_NAME_PREFIX>__serviceUri` |  Data planens URI för tjänsten som du ansluter till. |
| Fullständigt kvalificerat namn område | Event Hubs | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Det fullständigt kvalificerade Event Hub-namnområdet. |

Ytterligare alternativ kan ha stöd för en specifik Anslutnings typ. Se dokumentationen för komponenten som gör anslutningen.

När den Azure Functions tjänsten används en [hanterad identitet](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)i identitetsbaserade anslutningar. Den systemtilldelade identiteten används som standard. När den körs i andra kontexter, till exempel lokal utveckling, används din utvecklares identitet istället, även om detta kan anpassas med alternativa anslutnings parametrar.

##### <a name="local-development"></a>Lokal utveckling

När du kör lokalt visas konfigurationen ovan för körning av den lokala utvecklarens identitet. Anslutningen kommer att försöka hämta en token från följande platser i ordning:

- En lokal cache som delas mellan Microsoft-program
- Den aktuella användar kontexten i Visual Studio
- Den aktuella användar kontexten i Visual Studio Code
- Den aktuella användar kontexten i Azure CLI

Om inget av dessa alternativ lyckas uppstår ett fel.

I vissa fall kanske du vill ange användning av en annan identitet. Du kan lägga till konfigurations egenskaper för den anslutning som pekar på den alternativa identiteten.

> [!NOTE]
> Följande konfigurations alternativ stöds inte när tjänsten Azure Functions körs.

Om du vill ansluta med ett Azure Active Directory tjänstens huvud namn med ett klient-ID och en hemlighet definierar du anslutningen med följande obligatoriska egenskaper utöver [anslutnings egenskaperna](#connection-properties) ovan:

| Egenskap    | Miljövariabel | Beskrivning |
|---|---|---|
| Klient-ID:t | `<CONNECTION_NAME_PREFIX>__tenantId` | ID för Azure Active Directory klient organisation (katalog). |
| Klient-ID | `<CONNECTION_NAME_PREFIX>__clientId` |  Klient-ID för en app-registrering i klienten. |
| Klienthemlighet | `<CONNECTION_NAME_PREFIX>__clientSecret` | En klient hemlighet som genererades för registrering av appen. |

Exempel på `local.settings.json` egenskaper som krävs för Identity-baserad anslutning med Azure Blob: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Bevilja behörighet till identiteten

Vilken identitet som används måste ha behörighet att utföra de avsedda åtgärderna. Detta görs vanligt vis genom att tilldela en roll i Azure RBAC eller ange identiteten i en åtkomst princip, beroende på vilken tjänst som du ansluter till. Läs dokumentationen för varje tjänst om vilka behörigheter som krävs och hur de kan ställas in.

> [!IMPORTANT]
> Vissa behörigheter kan exponeras av tjänsten som inte behövs för alla sammanhang. Om möjligt följer du **principen om minsta behörighet** och beviljar endast behörighet som krävs av identiteten. Om appen till exempel bara behöver läsa från en BLOB, använder du rollen [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) som [lagrings data ägarens BLOB-dataägare](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) , inklusive för hög behörighet för en Läs åtgärd.


## <a name="reporting-issues"></a>Rapporterings problem
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Koda och testa Azure Functions lokalt](./functions-develop-local.md)
* [Metod tips för Azure Functions](functions-best-practices.md)
* [Referens för Azure Functions C#-utvecklare](functions-dotnet-class-library.md)
* [Referens för Azure Functions Node.js-utvecklare](functions-reference-node.md)
