---
title: Skapa SDK:er för anpassat språk med AutoRest
titleSuffix: Azure Digital Twins
description: Lär dig hur du använder AutoRest för att generera SDK:er på anpassat språk för att skriva Azure Digital Twins kod på andra språk som inte har publicerade SDK:er.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751120"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Skapa SDK:er för anpassat språk Azure Digital Twins autorest

Om du behöver arbeta med Azure Digital Twins med ett språk som inte har ett publicerat [Azure Digital Twins SDK](how-to-use-apis-sdks.md)visar den här artikeln hur du använder AutoRest för att generera din egen SDK på det språk du föredrar. 

Exemplen i den här artikeln visar hur du skapar [en SDK för dataplanet,](how-to-use-apis-sdks.md#overview-data-plane-apis)men den här processen fungerar även för att generera ett  [SDK för](how-to-use-apis-sdks.md#overview-control-plane-apis) kontrollplanet.

## <a name="prerequisites"></a>Förutsättningar

Om du vill generera en SDK måste du först slutföra följande konfiguration på den lokala datorn:
* Installera [**AutoRest**](https://github.com/Azure/autorest), version 2.0.4413 (version 3 stöds inte för närvarande)
* Installera [**Node.js**](https://nodejs.org), vilket är ett krav för att använda AutoRest
* Installera [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Ladda ned den Azure Digital Twins **swagger-filen (OpenAPI)** från [swagger-mappen för dataplanet,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)tillsammans med tillhörande mapp med exempel. Swagger-filen är den somdigitaltwins.js *på*.

>[!TIP]
> Om du i stället vill skapa **ett SDK** för kontrollplanet slutför du stegen i den här artikeln med hjälp av den senaste **Swagger-filen** (OpenAPI) för kontrollplanet från [Swagger-mappen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) i stället för dataplanet ett.

När datorn är utrustad med allt från listan ovan är du redo att använda AutoRest för att skapa en SDK.

## <a name="create-the-sdk-using-autorest"></a>Skapa SDK med AutoRest 

När du har Node.js installerat kan du köra det här kommandot för att kontrollera att du har den version av AutoRest som krävs installerad:
```cmd/sh
npm install -g autorest@2.0.4413
```

Följ dessa steg om du Azure Digital Twins AutoRest mot swagger-filen:
1. Kopiera Azure Digital Twins Swagger-filen och tillhörande mapp med exempel till en arbetskatalog.
2. Använd ett kommandotolkfönster för att växla till den arbetskatalogen.
3. Kör AutoRest med följande kommando. Ersätt `<language>` platshållaren med val av språk: `python` , , och så `java` `go` vidare. (Du hittar en fullständig lista över alternativ i [AutoRest README](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Därför visas en ny mapp med namnet *DigitalTwinsApi* i arbetskatalogen. De genererade SDK-filerna har namnrymden *DigitalTwinsApi*. Du fortsätter att använda det namnområdet via resten av användningsexempel i den här artikeln.

AutoRest stöder en mängd olika språkkodgeneratorer.

## <a name="make-the-sdk-into-a-class-library"></a>Göra SDK till ett klassbibliotek

Du kan inkludera de filer som genereras av AutoRest direkt i en .NET-lösning. Det är dock troligt att du vill inkludera Azure Digital Twins SDK i flera olika projekt (dina klientappar, Azure Functions appar med mera). Därför kan det vara användbart att skapa ett separat projekt (ett .NET-klassbibliotek) från de genererade filerna. Sedan kan du inkludera det här klassbiblioteksprojektet i flera lösningar som en projektreferens.

Det här avsnittet innehåller instruktioner om hur du skapar SDK:n som ett klassbibliotek, som är ett eget projekt och som kan ingå i andra projekt. De här stegen förlitar **sig på Visual Studio**.

Här är stegen:

1. Skapa en ny Visual Studio lösning för ett klassbibliotek
2. Använd *DigitalTwinsApi* som projektnamn
3. I Solution Explorer du projektet *DigitalTwinsApi* för den genererade lösningen och väljer *Lägg till > befintligt objekt...*
4. Leta upp mappen där du genererade SDK:n och välj filerna på rotnivå
5. Tryck på "OK"
6. Lägg till en mapp i projektet (högerklicka på projektet i Solution Explorer och välj Lägg *till > Ny mapp*)
7. Ge mappen namnet *Models (Modeller)*
8. Högerklicka på mappen *Modeller i* Solution Explorer och välj Lägg till > *befintligt objekt...*
9. Välj filerna i mappen *Models (Modeller)* för den genererade SDK:n och tryck på Ok

För att sdk ska kunna byggas behöver projektet följande referenser:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Om du vill lägga till dessa *öppnar du Tools > NuGet Package Manager > Manage NuGet Packages for Solution...*.

1. Kontrollera att fliken *Bläddra är* markerad på panelen
2. Sök efter *Microsoft.Rest*
3. Välj `ClientRuntime` paketen `ClientRuntime.Azure` och och lägg till dem i din lösning

Nu kan du skapa projektet och inkludera det som en projektreferens i alla Azure Digital Twins som du skriver.

## <a name="tips-for-using-the-sdk"></a>Tips för att använda SDK

Det här avsnittet innehåller allmän information och riktlinjer för att använda den genererade SDK:n.

### <a name="synchronous-and-asynchronous-calls"></a>Synkrona och asynkrona anrop

Alla SDK-funktioner finns i synkrona och asynkrona versioner.

### <a name="typed-and-untyped-data"></a>Typade och otypade data

REST API-anrop returnerar vanligtvis starkt typade objekt. Men eftersom Azure Digital Twins låter användarna definiera sina egna anpassade typer för tvillingar, finns det inget sätt att definiera statiska returdata i förväg för många Azure Digital Twins anrop. API:erna returnerar i stället starkt typifierade omslutningstyper där det är tillämpligt, och de anpassade tvillingdata finns i Json.NET-objekt (används oavsett var datatypen "objekt" visas i API-signaturerna). Du kan skapa dessa objekt på rätt sätt i koden.

### <a name="error-handling"></a>Felhantering

När ett fel inträffar i SDK:n (inklusive HTTP-fel som 404) ylar SDK:n ett undantag. Därför är det viktigt att kapsla in alla API-anrop i try/catch-block.

Här är ett kodfragment som försöker lägga till en tvilling och fånga upp eventuella fel i den här processen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Sidindelning

AutoRest genererar två typer av växlingsmönster för SDK:
* Ett för alla API:er utom fråge-API:et
* Ett för fråge-API:et

Här är ett exempel på en metod som visar hur du hämtar en växlingslista över utgående relationer från en Azure Digital Twins:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Det andra mönstret genereras bara för fråge-API:et. Den använder en `continuationToken` explicit.

>[!TIP]
> En viktig orsak till att hämta sidor är att beräkna avgifter för [frågeenhet för](concepts-query-units.md) ett fråge-API-anrop.

Här är ett exempel med det här mönstret:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Nästa steg

Gå igenom stegen för att skapa en klientapp där du kan använda din SDK:
* [*Självstudie: Koda en klientapp*](tutorial-code.md)
