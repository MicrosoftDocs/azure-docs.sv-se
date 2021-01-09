---
title: 'Skapa anpassade SDK: er för Azure Digitals dubbla med AutoRest'
titleSuffix: Azure Digital Twins
description: 'Se hur du genererar anpassade SDK: er för att använda Azure Digitals dubbla med andra språk än C#.'
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: 3bc24e88368af056e4d4506a5cf688e1172d4930
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051572"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Skapa anpassade SDK: er för Azure Digitals dubbla med AutoRest

Just nu är de enda publicerade data Plans SDK: erna för att interagera med Azures digitala dubbla API: er för .NET (C#), Java Script och Java. Du kan läsa om dessa SDK: er och API: erna i allmänhet i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md). Om du arbetar på ett annat språk visar den här artikeln hur du skapar ett eget data plan SDK på det språk som du väljer med hjälp av AutoRest.

>[!NOTE]
> Du kan också använda AutoRest för att generera ett Control plan SDK om du vill. Det gör du genom att följa anvisningarna i den här artikeln med hjälp av den senaste **kontroll planet Swagger** (openapi) från [mappen Control plan Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) i stället för data planet en.

## <a name="set-up-your-machine"></a>Konfigurera din dator

Om du vill generera ett SDK behöver du:
* [AutoRest](https://github.com/Azure/autorest), version 2.0.4413 (version 3 stöds inte för närvarande)
* [Node.js](https://nodejs.org) som ett krav för AutoRest
* Den senaste Azure Digital- **dataplans Swagger** -filen (openapi) från [Dataplans Swagger-mappen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)och dess medföljande mapp med exempel.  Hämta filen Swagger *digitaltwins.jspå* och dess mapp med exempel till din lokala dator.

När datorn är utrustad med allt från listan ovan är du redo att använda AutoRest för att skapa SDK: n.

## <a name="create-the-sdk-with-autorest"></a>Skapa SDK med AutoRest 

Om du har Node.js installerat kan du köra det här kommandot för att kontrol lera att du har rätt version av AutoRest installerat:
```cmd/sh
npm install -g autorest@2.0.4413
```

Följ dessa steg om du vill köra AutoRest mot Azure Digitals Swagger-filen:
1. Kopiera Azure Digitals Swagger-fil och dess tillhör ande mapp med exempel till en arbets katalog.
2. Använd ett kommando tolks fönster för att växla till den arbets katalogen.
3. Kör AutoRest med följande kommando. Ersätt `<language>` plats hållaren med önskat språk: `python` ,, `java` `go` och så vidare. (Du hittar den fullständiga listan med alternativ i README- [filen för AutoRest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Därför visas en ny mapp med namnet *ADTApi* i din arbets katalog. De genererade SDK-filerna kommer att ha namn området *ADTApi*. Du kommer att fortsätta att använda det namn området genom resten av användnings exemplen i den här artikeln.

AutoRest har stöd för en mängd olika språk kods generatorer.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Lägg till SDK i ett Visual Studio-projekt

Du kan inkludera de filer som genereras av AutoRest direkt i en .NET-lösning. Det är dock troligt att du vill lägga till Azure Digitals-SDK: n i flera separata projekt (dina klient program, Azure Functions appar och så vidare). Därför kan det vara praktiskt att skapa ett separat projekt (ett .NET-klass bibliotek) från de genererade filerna. Sedan kan du inkludera detta klass biblioteks projekt i flera lösningar som en projekt referens.

Det här avsnittet innehåller anvisningar om hur du skapar SDK som ett klass bibliotek, vilket är ett eget projekt och kan ingå i andra projekt. De här stegen är beroende av **Visual Studio** (du kan installera den senaste versionen [härifrån).](https://visualstudio.microsoft.com/downloads/)

Här är stegen:

1. Skapa en ny Visual Studio-lösning för ett klass bibliotek
2. Använd *ADTApi* som projekt namn
3. I Solution Explorer högerklickar du på *ADTApi* -projektet för den genererade lösningen och väljer *Lägg till > befintligt objekt...*
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

## <a name="general-guidelines-for-generated-sdks"></a>Allmänna rikt linjer för genererade SDK: er

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

Här är ett exempel med det här mönstret:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Nästa steg

Gå igenom stegen för att skapa en klient-app där du kan använda ditt SDK:
* [*Självstudie: koda en klient app*](tutorial-code.md)
