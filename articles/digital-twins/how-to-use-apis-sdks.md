---
title: Använda Azure Digital Twins-API:er och -SDK:er
titleSuffix: Azure Digital Twins
description: Se hur du arbetar med Azure Digital Twins API:er, inklusive via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efa5061a49978ed5e7766c0e7bf9b56a1e73cf5d
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389765"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Använda Azure Digital Twins-API:er och -SDK:er

Azure Digital Twins har både **kontrollplans-API:er och** **API:er för dataplanet** för att hantera din instans och dess element. 
* API:erna för kontrollplanet [är Azure Resource Manager(ARM)-API:er](../azure-resource-manager/management/overview.md) och täcker resurshanteringsåtgärder som att skapa och ta bort din instans. 
* API:erna för dataplanet är Azure Digital Twins API:er och används för datahanteringsåtgärder som att hantera modeller, tvillingar och grafen.

Den här artikeln ger en översikt över de API:er som är tillgängliga och metoder för att interagera med dem. Du kan antingen använda REST-API:erna direkt med deras associerade Swaggers (via ett verktyg som [Postman](how-to-use-postman.md)) eller via ett SDK.

## <a name="overview-control-plane-apis"></a>Översikt: API:er för kontrollplan

API:erna för kontrollplanet är [ARM-API:er](../azure-resource-manager/management/overview.md) som används för att hantera din Azure Digital Twins som helhet, så de täcker åtgärder som att skapa eller ta bort hela instansen. Du kommer också att använda dessa för att skapa och ta bort slutpunkter.

Den senaste API-versionen för kontrollplanet _**är 2020-12-01.**_

Så här använder du API:er för kontrollplanet:
* Du kan anropa API:erna direkt genom att referera till den senaste Swagger-mappen på [kontrollplanets Swagger-lagringsplatsen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Den här mappen innehåller också en mapp med exempel som visar användningen.
* Du kan för närvarande komma åt SDK:er för kontroll-API:er i...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([referens [automatiskt genererad]](/dotnet/api/overview/azure/digitaltwins/management)) ([källa](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([referens [automatiskt genererad]](/java/api/overview/azure/digitaltwins)) ([källa](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) [(källa)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([källa](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([källa](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

Du kan också använda API:er för kontrollplan genom att interagera Azure Digital Twins via [Azure Portal](https://portal.azure.com) och [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Översikt: API:er för dataplanet

API:erna för dataplanet är Azure Digital Twins API:er som används för att hantera elementen i din Azure Digital Twins instans. De omfattar åtgärder som att skapa vägar, ladda upp modeller, skapa relationer och hantera tvillingar. De kan delas upp i följande kategorier:
* **DigitalTwinModels** – Kategorin DigitalTwinModels innehåller API:er för att hantera [modeller i](concepts-models.md) en Azure Digital Twins instans. Hanteringsaktiviteter omfattar uppladdning, validering, hämtning och borttagning av modeller som har skrivits i DTDL.
* **DigitalTwins** – Kategorin DigitalTwins innehåller API:er som gör att utvecklare kan skapa, ändra och ta bort [digitala](concepts-twins-graph.md) tvillingar och deras relationer i en Azure Digital Twins instans.
* **Fråga** – Med frågekategorin kan [utvecklare hitta uppsättningar med digitala tvillingar i tvillingdiagrammet](how-to-query-graph.md) över relationer.
* **Händelsevägar** – Kategorin Händelsevägar innehåller API:er för [att dirigera data,](concepts-route-events.md)via systemet och till underordnade tjänster.

Den senaste VERSIONEN av API:et för dataplanet _**är 2020-10-31.**_

Så här använder du API:er för dataplanet:
* Du kan anropa API:erna direkt genom att...
   - referera till den senaste Swagger-mappen på [dataplanets Swagger-lagringsplatsen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Den här mappen innehåller också en mapp med exempel som visar användningen. 
   - visa [API-referensdokumentationen](/rest/api/azure-digitaltwins/).
* Du kan använda **.NET (C#) SDK.** Om du vill använda .NET SDK...
   - du kan visa och lägga till paketet från NuGet: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - du kan visa [SDK-referensdokumentationen](/dotnet/api/overview/azure/digitaltwins/client).
   - du hittar SDK-källan, inklusive en mapp med exempel, i GitHub: [Azure IoT Digital Twins klientbibliotek för .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - du kan se detaljerad information och användningsexempel genom att fortsätta till [*avsnittet .NET (C#) SDK (dataplan)*](#net-c-sdk-data-plane) i den här artikeln.
* Du kan använda **Java SDK.** Om du vill använda Java SDK...
   - du kan visa och installera paketet från Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - du kan visa [SDK-referensdokumentationen](/java/api/overview/azure/digitaltwins/client)
   - du hittar SDK-källan i GitHub: [Azure IoT Digital Twins klientbibliotek för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Du kan använda **JavaScript SDK.** Om du vill använda JavaScript SDK...
   - du kan visa och installera paketet från npm: [Azure Azure Digital Twins Core-klientbibliotek för JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - du kan visa [SDK-referensdokumentationen](/javascript/api/@azure/digital-twins-core/).
   - du hittar SDK-källan i GitHub: [Azure Azure Digital Twins Core-klientbiblioteket för JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Du kan använda **Python SDK.** Om du vill använda Python SDK...
   - du kan visa och installera paketet från PyPi: [Azure Azure Digital Twins Core-klientbiblioteket för Python](https://pypi.org/project/azure-digitaltwins-core/).
   - du kan visa [SDK-referensdokumentationen](/python/api/azure-digitaltwins-core/azure.digitaltwins.core).
   - du hittar SDK-källan i GitHub: [Azure Azure Digital Twins Core-klientbiblioteket för Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Du kan generera en SDK för ett annat språk med autorest. Följ instruktionerna i [*Anvisningar: Skapa anpassade SDK:er för Azure Digital Twins med AutoRest*](how-to-create-custom-sdks.md).

Du kan också använda API:er för datumplan genom att interagera Azure Digital Twins via [CLI.](how-to-use-cli.md)

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (dataplan)

Den Azure Digital Twins .NET (C#) SDK är en del av Azure SDK för .NET. Den har öppen källkod och baseras på de Azure Digital Twins API:erna för dataplanet.

> [!NOTE]
> Mer information om SDK-design finns i de allmänna [designprinciperna för Azure SDK:er](https://azure.github.io/azure-sdk/general_introduction.html) och de specifika [designriktlinjerna för .NET.](https://azure.github.io/azure-sdk/dotnet_introduction.html)

Om du vill använda SDK:n inkluderar du NuGet-paketet **Azure.DigitalTwins.Core** med ditt projekt. Du behöver även den senaste versionen av **Azure.Identity-paketet.** I Visual Studio kan du lägga till dessa paket med hjälp av NuGet Package Manager (nås via *Verktyg > NuGet Package Manager > Hantera NuGet-paket för lösning).* Du kan också använda .NET-kommandoradsverktyget med kommandona i NuGet-paketlänkarna nedan för att lägga till dem i projektet:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Det här är paketet för [Azure Digital Twins SDK för .NET](/dotnet/api/overview/azure/digitaltwins/client). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Det här biblioteket innehåller verktyg som hjälper dig med autentisering mot Azure.

En detaljerad genomgång av hur du använder API:erna i praktiken finns i [*Självstudie: Koda en klientapp.*](tutorial-code.md) 

### <a name="net-sdk-usage-examples"></a>Användningsexempel för .NET SDK

Här är några kodexempel som illustrerar användningen av .NET SDK.

Autentisera mot tjänsten:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Ladda upp en modell:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Lista modeller:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Skapa tvillingar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Fråga tvillingar och loopa igenom resultat:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

En genomgång [*av den här exempelappkoden finns*](tutorial-code.md) i Självstudie: Koda en klientapp. 

Du kan också hitta ytterligare exempel i [GitHub-lagringsplatsen för .NET (C#) SDK.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)

#### <a name="serialization-helpers"></a>Serialiseringshjälpare

Serialiseringshjälpfunktioner är hjälpfunktioner som är tillgängliga i SDK:n för att snabbt skapa eller deserialisera tvillingdata för åtkomst till grundläggande information. Eftersom de grundläggande SDK-metoderna returnerar tvillingdata som JSON som standard kan det vara bra att använda dessa hjälpklasser för att dela upp tvillingdata ytterligare.

De tillgängliga hjälpklasserna är:
* `BasicDigitalTwin`: Representerar huvuddata för en digital tvilling
* `BasicDigitalTwinComponent`: Representerar en komponent i egenskaperna för `Contents` en `BasicDigitalTwin`
* `BasicRelationship`: Representerar grundläggande data i en relation
* `DigitalTwinsJsonPropertyName`: Innehåller strängkonstanterna för användning i JSON-serialisering och deserialisering för anpassade digitala tvillingtyper

##### <a name="deserialize-a-digital-twin"></a>Deserialisera en digital tvilling

Du kan alltid deserialisera tvillingdata med hjälp av det JSON-bibliotek som du väljer, `System.Text.Json` till exempel eller `Newtonsoft.Json` . För grundläggande åtkomst till en tvilling kan hjälpklasserna göra detta enklare.

Hjälpklassen `BasicDigitalTwin` ger dig också åtkomst till egenskaper som definierats för tvillingen via en `Dictionary<string, object>` . Om du vill visa egenskaper för tvillingen kan du använda:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` använder `System.Text.Json` attribut. För att kunna använda med DigitalTwinsClient måste du antingen initiera klienten med standardkonstruktorn eller, om du vill anpassa `BasicDigitalTwin` serialiseraralternativet, använda [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true). [](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)

##### <a name="create-a-digital-twin"></a>Skapa en digital tvilling

Med klassen `BasicDigitalTwin` kan du förbereda data för att skapa en tvillinginstans:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Koden ovan motsvarar följande "manuella" variant:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Deserialisera en relation

Du kan alltid deserialisera relationsdata till valfri typ. För grundläggande åtkomst till en relation använder du typen `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Hjälpklassen `BasicRelationship` ger dig också åtkomst till egenskaper som definierats för relationen via en `IDictionary<string, object>` . Om du vill visa en lista över egenskaper kan du använda:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Skapa en relation

Med klassen `BasicRelationship` kan du också förbereda data för att skapa relationer på en tvillinginstans:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Skapa en korrigering för tvillinguppdatering

Uppdateringssamtal för tvillingar och relationer använder [JSON-korrigeringsstruktur.](http://jsonpatch.com/) Om du vill skapa listor över JSON-korrigeringsåtgärder kan du använda `JsonPatchDocument` som visas nedan.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Allmän information om API/SDK-användning

> [!NOTE]
> Observera att Azure Digital Twins inte stöder resursdelning **för korsande ursprung (CORS).** Mer information om effekt- och lösningsstrategier finns i avsnittet Resursdelning för korsande ursprung [*(CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) i Begrepp: Säkerhet för *Azure Digital Twins lösningar.*

Följande lista innehåller ytterligare information och allmänna riktlinjer för att använda API:er och SDK:er.

* Du kan använda ett HTTP REST-testningsverktyg som Postman för att göra direkta anrop till Azure Digital Twins API:er. Mer information om den här processen finns i [*How-to: Make requests with Postman*](how-to-use-postman.md).
* Om du vill använda SDK:n instansierar du `DigitalTwinsClient` klassen . Konstruktorn kräver autentiseringsuppgifter som kan hämtas med en mängd olika autentiseringsmetoder i `Azure.Identity` paketet. Mer information om `Azure.Identity` finns i dess [namnområdesdokumentation](/dotnet/api/azure.identity). 
* Det kan vara användbart när du kommer igång, men det finns flera andra alternativ, inklusive autentiseringsuppgifter för hanterad identitet , som du förmodligen kommer att använda för att autentisera Azure-funktioner som har ställts in med `InteractiveBrowserCredential` [MSI](../app-service/overview-managed-identity.md?tabs=dotnet) [](/dotnet/api/azure.identity.interactivebrowsercredential)mot Azure Digital Twins. Mer information om `InteractiveBrowserCredential` finns i dess [klassdokumentation](/dotnet/api/azure.identity.interactivebrowsercredential).
* Begäranden till Azure Digital Twins-API:er kräver en användare eller tjänstens [](../active-directory/fundamentals/active-directory-whatis.md) huvudnamn som är en del av samma Azure Active Directory-klientorganisation (Azure AD) där Azure Digital Twins-instansen finns. För att förhindra att obehöriga aktörer genomsöker URL:er för att identifiera var Azure Digital Twins-instanserna finns, returneras felmeddelandet "404 Sub-Domain not found" (404 Sub-Domain hittades inte). Det här felet returneras *även* om användaren eller tjänstens huvudnamn har fått rollen Azure Digital Twins dataägare eller Azure Digital Twins dataläsare via [Azure AD B2B-samarbete.](../active-directory/external-identities/what-is-b2b.md)
* Alla tjänst-API-anrop exponeras som medlemsfunktioner i `DigitalTwinsClient` klassen .
* Alla tjänstfunktioner finns i synkrona och asynkrona versioner.
* Alla tjänstfunktioner returnerar ett undantag för returstatusen 400 eller högre. Se till att omsluta anrop `try` i ett avsnitt och fånga minst `RequestFailedExceptions` . Mer information om den här typen av undantag finns [här.](/dotnet/api/azure.requestfailedexception)
* De flesta tjänstmetoder `Response<T>` returnerar eller ( `Task<Response<T>>` för asynkrona anrop), där `T` är klassen för returobjektet för tjänstanropet. Klassen [`Response`](/dotnet/api/azure.response-1) kapslar in tjänstretur och visar returvärden i `Value` fältet.  
* Tjänstmetoder med sidindelade resultat `Pageable<T>` returnerar `AsyncPageable<T>` eller som resultat. Mer information om `Pageable<T>` klassen finns [här.](/dotnet/api/azure.pageable-1)Mer information om `AsyncPageable<T>` finns [här.](/dotnet/api/azure.asyncpageable-1)
* Du kan iterera över sidindelade resultat med hjälp av en `await foreach` -loop. Mer information om den här processen finns [här.](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)
* Den underliggande SDK:n är `Azure.Core` . Se [Azure-namnområdesdokumentationen](/dotnet/api/azure) för referens om SDK-infrastruktur och -typer.


Tjänstmetoder returnerar starkt typifierade objekt där det är möjligt. Men eftersom Azure Digital Twins baseras på modeller som konfigurerats av användaren vid körning (via DTDL-modeller som laddats upp till tjänsten), tar många tjänst-API:er och returnerar tvillingdata i JSON-format.

## <a name="monitor-api-metrics"></a>Övervaka API-mått

API-mått som begäranden, svarstid och felfrekvens kan visas i Azure Portal [.](https://portal.azure.com/) 

Från portalens startsida söker du efter din Azure Digital Twins instans för att hämta information om den. Välj alternativet **Mått** på Azure Digital Twins instansens meny för att öppna *sidan* Mått.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Skärmbild som visar måttsidan för Azure Digital Twins":::

Härifrån kan du visa måtten för din instans och skapa anpassade vyer.

## <a name="next-steps"></a>Nästa steg

Se hur du gör direkta begäranden till API:erna med Postman:
* [*Så här gör du: Skicka begäranden med Postman*](how-to-use-postman.md)

Eller öva med att använda .NET SDK genom att skapa en klientapp med den här självstudien:
* [*Självstudie: Koda en klientapp*](tutorial-code.md)
