---
title: 'Skapa SDK: er med anpassade språk med AutoRest'
titleSuffix: Azure Digital Twins
description: 'Lär dig hur du använder AutoRest för att generera SDK: er för anpassade språk, för att skriva Azure Digitals dubbla kod på andra språk som inte har några publicerade SDK: er.'
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561848"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Skapa SDK: er för anpassade språk för Azure Digitals med AutoRest

Om du behöver arbeta med digitala Digital-objekt med Azure med ett språk som inte har någon [publicerad Azure Digitals-SDK](how-to-use-apis-sdks.md), visar den här artikeln hur du använder AutoRest för att skapa din egen SDK på valfritt språk. 

I exemplen i den här artikeln visas hur du skapar ett [data plan SDK](how-to-use-apis-sdks.md#overview-data-plane-apis), men den här processen fungerar även för att skapa ett  [Control plan SDK](how-to-use-apis-sdks.md#overview-control-plane-apis) .

## <a name="prerequisites"></a>Förutsättningar

Om du vill generera ett SDK måste du först slutföra följande installation på den lokala datorn:
* Installera [**AutoRest**](https://github.com/Azure/autorest), version 2.0.4413 (version 3 stöds inte för närvarande)
* Installera [**Node.js**](https://nodejs.org), vilket är ett krav för att använda AutoRest
* Installera [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Hämta den senaste Azure Digital- **dataplans Swagger** -filen (openapi) från [dataplan-Swagger-mappen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins), tillsammans med dess medföljande mapp med exempel. Swagger-filen är den som kallas *digitaltwins.jspå*.

>[!TIP]
> Om du vill skapa ett **Control plan SDK** i stället slutför du stegen i den här artikeln med hjälp av den senaste **kontroll Plans Swagger** (openapi) från [mappen Control plan Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) i stället för data planet en.

När datorn är utrustad med allt från listan ovan är du redo att använda AutoRest för att skapa en SDK.

## <a name="create-the-sdk-using-autorest"></a>Skapa SDK med hjälp av AutoRest 

När du har Node.js installerat kan du köra det här kommandot för att kontrol lera att du har den version av AutoRest som krävs:
```cmd/sh
npm install -g autorest@2.0.4413
```

Följ dessa steg om du vill köra AutoRest mot Azure Digitals Swagger-filen:
1. Kopiera Azure Digitals Swagger-fil och dess tillhör ande mapp med exempel till en arbets katalog.
2. Använd ett kommando tolks fönster för att växla till den arbets katalogen.
3. Kör AutoRest med följande kommando. Ersätt `<language>` plats hållaren med önskat språk: `python` ,, `java` `go` och så vidare. (Du hittar den fullständiga listan med alternativ i README- [filen för AutoRest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Därför visas en ny mapp med namnet *DigitalTwinsApi* i din arbets katalog. De genererade SDK-filerna kommer att ha namn området *DigitalTwinsApi*. Du kommer att fortsätta att använda det namn området genom resten av användnings exemplen i den här artikeln.

AutoRest har stöd för en mängd olika språk kods generatorer.

## <a name="make-the-sdk-into-a-class-library"></a>Gör SDK till ett klass bibliotek

Du kan inkludera de filer som genereras av AutoRest direkt i en .NET-lösning. Det är dock troligt att du vill ta med Azure Digitals-SDK: n i flera separata projekt (dina klient program, Azure Functions appar med mera). Därför kan det vara praktiskt att skapa ett separat projekt (ett .NET-klass bibliotek) från de genererade filerna. Sedan kan du inkludera detta klass biblioteks projekt i flera lösningar som en projekt referens.

Det här avsnittet innehåller anvisningar om hur du skapar SDK som ett klass bibliotek, vilket är ett eget projekt och kan ingå i andra projekt. De här stegen är beroende av **Visual Studio**.

Här är stegen:

1. Skapa en ny Visual Studio-lösning för ett klass bibliotek
2. Använd *DigitalTwinsApi* som projekt namn
3. I Solution Explorer högerklickar du på *DigitalTwinsApi* -projektet för den genererade lösningen och väljer *Lägg till > befintligt objekt...*
4. Hitta mappen där du genererade SDK och välj filerna på rotnivån
5. Tryck på OK
6. Lägg till en mapp i projektet (Högerklicka på projektet i Solution Explorer och välj *Lägg till > ny mapp*)
7. Namnge mapparna *modeller*
8. Högerklicka på mappen *modeller* i lösnings Utforskaren och välj *Lägg till > befintligt objekt...*
9. Välj filerna i mappen *modeller* i den genererade SDK: n och tryck på OK

För att utveckla SDK: n måste ditt projekt ha följande referenser:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Om du vill lägga till dessa öppnar du *verktyg > NuGet Package Manager > hantera NuGet-paket för lösningen.*...

1. I panelen kontrollerar du att fliken *Bläddra* är markerad
2. Sök efter *Microsoft. rest*
3. Välj `ClientRuntime` `ClientRuntime.Azure` paketen och Lägg till dem i din lösning

Nu kan du skapa projektet och inkludera det som en projekt referens i Azure Digitals dubbla program som du skriver.

## <a name="tips-for-using-the-sdk"></a>Tips om hur du använder SDK

Det här avsnittet innehåller allmän information och rikt linjer för att använda den genererade SDK: n.

### <a name="synchronous-and-asynchronous-calls"></a>Synkrona och asynkrona anrop

Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

### <a name="typed-and-untyped-data"></a>Skrivna och data utan typ

REST API-anrop returnerar vanligt vis starkt skrivna objekt. Men eftersom Azure Digital-enheter låter användare definiera sina egna anpassade typer för dubbla, finns det inget sätt att fördefiniera statiska retur data för många Azure Digital-samtal. I stället returnerar API: erna starkt skrivna omslutnings typer där det är tillämpligt och de anpassade dubbla data är i Json.NET-objekt (används där data typen "objekt" visas i API-signaturer). Du kan omvandla dessa objekt korrekt i din kod.

### <a name="error-handling"></a>Felhantering

När ett fel uppstår i SDK (inklusive HTTP-fel som 404), kommer SDK att utlösa ett undantag. Därför är det viktigt att kapsla in alla API-anrop inom try/catch-block.

Här är ett kodfragment som försöker lägga till en dubbel och fångar upp eventuella fel i den här processen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Sidindelning

AutoRest genererar två typer av sid indelnings mönster för SDK:
* En för alla API: er utom fråge-API
* En för fråge-API

Här är en exempel metod som visar hur du hämtar en lista över utgående relationer från Azure Digitals:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Det andra mönstret genereras bara för fråge-API: et. Den använder `continuationToken` uttryckligen.

>[!TIP]
> Ett huvud skäl för att hämta sidor är att beräkna [enhets avgifterna](concepts-query-units.md) för ett fråge-API-anrop.

Här är ett exempel med det här mönstret:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Nästa steg

Gå igenom stegen för att skapa en klient-app där du kan använda ditt SDK:
* [*Självstudie: koda en klient app*](tutorial-code.md)
