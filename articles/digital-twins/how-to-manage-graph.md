---
title: Hantera tvillingdiagram med relationer
titleSuffix: Azure Digital Twins
description: Se hur du hanterar en graf med digitala dubbla, genom att ansluta dem till relationer.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102433326"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Hantera en graf med digitala dubbla med relationer

Hjärtat i Azure Digitals flätas är det [dubbla diagrammet](concepts-twins-graph.md) som representerar hela miljön. Det dubbla grafen består av enskilda digitala dubbla anslutningar via **relationer**. 

När du har en fungerande [Azure Digital-instansen](how-to-set-up-instance-portal.md) och har konfigurerat [autentiserings](how-to-authenticate-client.md) kod i din klient app, kan du använda [**DigitalTwins-API: er**](/rest/api/digital-twins/dataplane/twins) för att skapa, ändra och ta bort digitala dubbla och deras relationer i en digital Azure-instans. Du kan också använda [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)eller [Azure Digitals flätat CLI](how-to-use-cli.md).

Den här artikeln fokuserar på att hantera relationer och diagrammet som helhet. information om hur du arbetar med enskilda digitala dubbla, finns i [*så här gör du: hantera digitala dubbla*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Sätt att hantera diagram

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Du kan också göra ändringar i grafen med hjälp av Azure Digitals Explorer-exemplet, vilket gör att du kan visualisera dina dubbla och grafer och använda SDK: n bakom kulisserna. I nästa avsnitt beskrivs det här exemplet i detalj.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Skapa relationer

Relationerna beskriver hur olika digitala anslutningar är anslutna till varandra, som utgör grunden för det dubbla diagrammet.

Relationer skapas med hjälp av `CreateOrReplaceRelationshipAsync()` anropet. 

Om du vill skapa en relation måste du ange:
* Källans dubbla ID ( `srcId` i kod exemplet nedan): ID: t för den dubbla där relationen kommer.
* Målets dubbla ID ( `targetId` i kod exemplet nedan): ID: t för den dubbla där relationen kommer.
* Ett Relations namn ( `relName` i kod exemplet nedan): den generiska typen av relation, något som _innehåller_.
* Ett relations-ID ( `relId` i kod exemplet nedan): det angivna namnet för den här relationen, t. ex. _Relationship1_.

Relations-ID: t måste vara unikt inom den aktuella källan. Det behöver inte vara globalt unikt.
Till exempel måste varje ID för en speciell relation vara unikt för den dubbla *foo*. En annan rad dubbla *staplar* kan dock ha en utgående relation som matchar samma ID för en *foo* -relation.

Följande kod exempel illustrerar hur du skapar en relation i din Azure Digital-instansen. SDK-anropet (markerat) används i en anpassad metod som kan visas i ett större programs kontext.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Den här anpassade funktionen kan nu anropas för att skapa en _contains_ -relation så här: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Om du vill skapa flera relationer kan du upprepa anrop till samma metod och skicka olika Relations typer till argumentet. 

Mer information om hjälp klassen `BasicRelationship` finns i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Skapa flera relationer mellan dubbla

Relationer kan klassificeras som antingen: 

* Utgående relationer: relationer som hör till den här dubbla som punkten utåt för att ansluta den till andra dubbla. `GetRelationshipsAsync()`Metoden används för att hämta utgående relationer för en dubbel.
* Inkommande relationer: relationer som hör till andra dubbla platser som pekar mot den här dubbla för att skapa en "inkommande" länk. `GetIncomingRelationshipsAsync()`Metoden används för att hämta inkommande relationer av en dubbel.

Det finns ingen begränsning för antalet relationer som du kan ha mellan två dubbla, och du kan ha så många relationer som du vill. 

Det innebär att du kan uttrycka flera olika typer av relationer mellan två dubblas samtidigt. Till exempel kan *dubbla a* ha både en *lagrad* *relation och en* relation med *dubbla B*.

Du kan till och med skapa flera instanser av samma typ av relation mellan samma två dubbla, om du vill. I det här exemplet kan *dubbla A* ha två olika *lagrade* relationer med *dubbla B*, så länge relationerna har olika relations-ID.

## <a name="list-relationships"></a>List relationer

Om du vill få åtkomst till listan över **utgående** relationer för en specifik i grafen kan du använda `GetRelationships()` metoden som detta:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Detta returnerar en `Azure.Pageable<T>` eller `Azure.AsyncPageable<T>` , beroende på om du använder en synkron eller asynkron version av anropet.

Här är ett exempel som hämtar en lista med relationer. SDK-anropet (markerat) används i en anpassad metod som kan visas i ett större programs kontext.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Nu kan du anropa den här anpassade metoden för att se de utgående relationerna för de dubblarna så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Du kan använda de hämtade relationerna för att navigera till andra dubbla i grafen. Det gör du genom att läsa `target` fältet från relationen som returneras och använda det som ID för nästa anrop till `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Hitta inkommande relationer till en digital, dubbel

Azure Digitals dubbla är också ett API för att hitta alla **inkommande** relationer till en specifik kontakt. Detta är ofta användbart för omvänd navigering, eller när du tar bort en dubbel.

>[!NOTE]
> `IncomingRelationship` anrop returnerar inte hela bröd texten i relationen. Mer information om `IncomingRelationship` klassen finns i [referens dokumentationen](/dotnet/api/azure.digitaltwins.core.incomingrelationship).

Kod exemplet i föregående avsnitt fokuserade på att söka efter utgående relationer från en dubbel. Följande exempel är strukturerat på samma sätt, men hittar *inkommande* relationer till den dubbla i stället. I det här exemplet används även SDK-anropet (markerat) i en anpassad metod som kan visas i ett större programs kontext.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Nu kan du anropa den här anpassade metoden för att se de ingående relationerna för de dubblarna så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Lista alla dubbla egenskaper och relationer

Med hjälp av ovanstående metoder för att visa utgående och inkommande relationer till en dubbel, kan du skapa en metod som skriver ut fullständig dubbel information, inklusive de dubbla egenskaperna och båda typerna av dess relationer. Här är ett exempel på en anpassad metod som visar hur du kombinerar ovanstående metoder för det här ändamålet.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Nu kan du anropa den här anpassade funktionen så här: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Uppdatera relationer

Relationerna uppdateras med hjälp av- `UpdateRelationship` metoden. 

>[!NOTE]
>Den här metoden används för att uppdatera **egenskaperna** för en relation. Om du behöver ändra källans dubbla eller målets dubbla Relations hip måste du [ta bort relationen](#delete-relationships) och återskapa den med [hjälp av de](#create-relationships) nya dubbla.

De parametrar som krävs för klient anropet är ID: t för den dubbla källan (den dubbla där relationen härstammar), ID: t för den relation som ska uppdateras och ett [JSON-patch](http://jsonpatch.com/) -dokument som innehåller de egenskaper och nya värden som du vill uppdatera.

Här är exempel kod som visar hur du använder den här metoden. I det här exemplet används SDK-anropet (markerat) i en anpassad metod som kan visas i ett större programs kontext.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Här är ett exempel på ett anrop till den här anpassade metoden, genom att skicka ett JSON-dokument med information för att uppdatera en egenskap.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Ta bort relationer

Den första parametern anger den dubbla källan (den dubbla där relationen kommer). Den andra parametern är relations-ID. Du behöver både det dubbla ID: t och relations-ID: t eftersom relations-ID: n endast är unika inom omfånget för en dubbel.

Här är exempel kod som visar hur du använder den här metoden. I det här exemplet används SDK-anropet (markerat) i en anpassad metod som kan visas i ett större programs kontext.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Nu kan du anropa den här anpassade metoden för att ta bort en relation så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Körbara, två diagram exempel

Följande körbara-kodfragment använder Relations åtgärderna från den här artikeln för att skapa ett sammanflätat diagram från digitala dubbla och relationer.

### <a name="set-up-the-runnable-sample"></a>Konfigurera körbara-exemplet

Kodfragmentet använder [*Room.jspå*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) och [*Floor.jspå*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) modell definitioner från [*självstudien: utforska Azure Digitals med en exempel klient program*](tutorial-command-line-app.md). Du kan använda dessa länkar om du vill gå direkt till filerna eller ladda ned dem som en del av det fullständiga exempel projektet från början till slut [här](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Innan du kör exemplet gör du följande:
1. Ladda ned modell filerna, placera dem i projektet och Ersätt `<path-to>` plats hållarna i koden nedan för att tala om för programmet var de finns.
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

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Här är konsol resultatet av programmet ovan: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Konsol utdata som visar den dubbla informationen, inkommande och utgående relationer för de dubbla." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Det dubbla diagrammet är ett koncept för att skapa relationer mellan dubbla. Om du vill visa den visuella representationen av den dubbla grafen, se [*visualiserings*](how-to-manage-graph.md#visualization) avsnittet i den här artikeln. 

## <a name="create-graph-from-a-csv-file"></a>Skapa graf från en CSV-fil

I praktiska användnings fall kommer dubbla hierarkier ofta att skapas från data som lagras i en annan databas eller kanske i ett kalkyl blad eller en CSV-fil. I det här avsnittet visas hur du läser data från en CSV-fil och skapar ett sammanflätat diagram.

Tänk på följande data tabell som beskriver en uppsättning digitala dubbla och relationer.

|  Modell-ID    | Dubbla ID (måste vara unikt) | Relations namn  | Mål, dubbla ID  | Dubbla init-data |
| --- | --- | --- | --- | --- |
| dtmi: exempel: våning; 1    | Floor1 | innehåller | Room1 | |
| dtmi: exempel: våning; 1    | Floor0 | innehåller | Room0 | |
| dtmi: exempel: Room; 1    | Room1 | | | {"Temperatur": 80} |
| dtmi: exempel: Room; 1    | Room0 | | | {"Temperatur": 70} |

Ett sätt att hämta dessa data till Azure Digitals, är att konvertera tabellen till en CSV-fil och skriva kod för att tolka filen till kommandon för att skapa dubbla och relationer. I följande kod exempel illustreras hur du läser data från CSV-filen och hur du skapar en dubbel graf i Azure Digitals flätas.

I koden nedan kallas CSV-filen *data.csv*, och det finns en plats hållare som representerar **värd namnet** för din Azure Digital-instans. Exemplet använder också flera paket som du kan lägga till i projektet för att hjälpa dig med den här processen.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att skicka frågor till en Azure Digitals dubbla graf:
* [*Begrepp: frågespråk*](concepts-query-language.md)
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)