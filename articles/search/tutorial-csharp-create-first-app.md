---
title: C#-självstudie för att skapa din första app
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar ditt första C#-sökprogram steg för steg. I självstudien finns både en nedladdnings länk till en fungerande app på GitHub och den fullständiga processen för att bygga appen från grunden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a57e45b264badffd0305eb6ac5b3c8f7c42adf3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695132"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Självstudie: skapa din första Sökapp med hjälp av .NET SDK

Den här självstudien visar hur du skapar en webbapp som frågar och returnerar resultat från ett sökindex med hjälp av Azure Kognitiv sökning och Visual Studio.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera en utvecklings miljö
> * Modell data strukturer
> * Skapa en webb sida för att samla in indata från frågor och Visa resultat
> * Definiera en Sök metod
> * Testa appen

Du får också lära dig hur enkelt ett Sök samtal är. Nyckel uttrycken i koden som du utvecklar kapslas in på följande rader.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Bara ett anrop frågar indexet och returnerar resultat.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Söker efter * pool *" border="true":::

## <a name="overview"></a>Översikt

I den här självstudien används hotell-exempel-indexet, som du kan skapa snabbt på din egen Sök tjänst genom att stega genom snabb starten för att [Importera data](search-get-started-portal.md). Indexet innehåller fiktiva hotell data, som är tillgängliga som en inbyggd data källa i varje Sök tjänst.

Den första lektionen i den här självstudien skapar en grundläggande fråga-struktur och Sök sida, som du kan förbättra i efterföljande lektioner för att ta med sid indelning, ansikte och en typ i förväg-upplevelse.

Du hittar en färdig version av koden i följande projekt:

* [1 – grundläggande – Sök-sida (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Den här självstudien har uppdaterats med Azure.Search.Documents-paketet (version 11). En tidigare version av .NET SDK finns i [kod exemplet Microsoft. Azure. search (version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="prerequisites"></a>Förutsättningar

* [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

* Skapa hotell-exempel-indexet med hjälp av anvisningarna i [snabb start: skapa ett sökindex](search-get-started-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Klient bibliotek för Azure Kognitiv sökning (version 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Installera och kör projektet från GitHub

Om du vill gå vidare till en fungerande app följer du stegen nedan för att ladda ned och köra den färdiga koden.

1. Leta upp exemplet på GitHub: [skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11).

1. I [rotmappen](https://github.com/Azure-Samples/azure-search-dotnet-samples)väljer du **kod** följt av **kloning** eller **Ladda ned ZIP** för att göra din privata lokala kopia av projektet.

1. Använd Visual Studio, gå till och öppna lösningen för den grundläggande Sök sidan ("1-Basic-search-Page") och välj **starta utan fel sökning** (eller tryck på F5) för att skapa och köra programmet.

1. Detta är ett hotell index, så skriv in några ord som du kan använda för att söka efter hotell (till exempel "WiFi", "View", "bar", "parkering") och granska resultaten.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Söker efter * WiFi *" border="true":::

Förhoppnings vis kommer det här projektet att köras smidigt och du har en webbapp igång. Många av de viktigaste komponenterna för mer avancerade sökningar ingår i den här appen, så det är en bra idé att gå igenom den och återskapa den steg för steg. Följande avsnitt beskriver de här stegen.

## <a name="set-up-a-development-environment"></a>Konfigurera en utvecklings miljö

Börja med ett Visual Studio-projekt för att skapa projektet från grunden och på så sätt förbättra koncepten i Azure Kognitiv sökning i åtanke.

1. I Visual Studio väljer du **nytt**  >  **projekt** och **ASP.net Core webb program**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Skapa ett moln projekt" border="true":::

1. Ge projektet ett namn, till exempel "FirstSearchApp" och ange platsen. Välj **Skapa**.

1. Välj projekt mal len **webb program (modell-View-Controller)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Skapa ett MVC-projekt" border="true":::

1. Installera klient biblioteket. I **verktyg**  >  **NuGet Package Manager**  >  **Hantera NuGet-paket för lösning...**, Välj **Bläddra** och Sök efter "azure.search.documents". Installera **Azure.Search.Documents** (version 11 eller senare) och godkänn licens avtalet och beroenden.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Använda NuGet för att lägga till Azure-bibliotek" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Initiera Azure-Kognitiv sökning

För det här exemplet använder du offentligt tillgängliga hotell data. Dessa data är en godtycklig samling med 50 fiktiva hotell namn och beskrivningar, som skapas enbart för att tillhandahålla demo data. Ange ett namn och en API-nyckel för att komma åt dessa data.

1. Öppna **appsettings.jspå** och ersätt standard raderna med Sök tjänstens URL (i formatet `https://<service-name>.search.windows.net` ) och en [administratör eller API-nyckel](search-security-api-keys.md) för Sök tjänsten. Eftersom du inte behöver skapa eller uppdatera ett index kan du använda Frågeparametern för den här självstudien.

    ```csharp
    {
        "SearchServiceName": "<YOUR-SEARCH-SERVICE-URI>",
        "SearchServiceQueryApiKey": "<YOUR-SEARCH-SERVICE-API-KEY>"
    }
    ```

1. I Solution Explorer väljer du filen och i egenskaper ändrar du inställningen **Kopiera till utdata-katalog** till **Kopiera om nyare**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Kopiera appens inställningar till utdata" border="true":::

## <a name="model-data-structures"></a>Modell data strukturer

Modeller (C#-klasser) används för att kommunicera data mellan klienten (vyn), servern (kontrollanten) och Azure-molnet med MVC-arkitekturen (modell, View, Controller). Dessa modeller visar vanligt vis strukturen för de data som ska nås.

I det här steget ska du modellera data strukturerna för Sök indexet, samt Sök strängen som används i Visa/kontrol Lanterna kommunikation. I hotell indexet har varje hotell många rum och varje hotell har en adress i flera delar. Den fullständiga åter givningen av ett hotell är helt en hierarkisk och kapslad data struktur. Du behöver tre klasser för att skapa varje komponent.

Uppsättningen **hotell**-, **adress**-och **rums** klasser kallas [*komplexa typer*](search-howto-complex-data-types.md), en viktig funktion i Azure kognitiv sökning. Komplexa typer kan vara många nivåer djup i klasser och underklasser och gör det möjligt att visa mycket mer komplexa data strukturer än att använda *enkla typer* (en klass som bara innehåller primitiva medlemmar).

1. I Solution Explorer högerklickar du på **modeller**  >  **Lägg till**  >  **nytt objekt**.

1. Välj **klass** och namnge objektet hotell. cs. Ersätt allt innehåll i hotellet. CS med följande kod. Lägg märke till klassens **adress** och **rums** medlemmar, dessa fält är klasser själva så att du även behöver modeller för dem.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. Upprepa samma process för att skapa en modell för **adress** klassen och namnge filen address. cs. Ersätt innehållet med följande.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. Och återigen följer du samma process för att skapa **rums** klassen och namnger filen Room. cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. Den sista modellen som du skapar i den här självstudien är en klass med namnet **SearchData** och den representerar användarens indata (**searchText**) och sökningens utdata (**resultList**). Typen av utdata är kritisk, **SearchResults &lt; hotellet &gt;**, eftersom den här typen exakt matchar resultatet från sökningen och du måste skicka den här referensen till vyn. Ersätt standard mal len med följande kod.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Skapa en webb sida

Project-mallar levereras med ett antal klient visningar som finns i mappen **vyer** . De exakta vyerna beror på vilken version av Core .NET du använder (3,1 används i det här exemplet). I den här självstudien kommer du att ändra **index. cshtml** för att inkludera elementen på en Sök sida.

Ta bort innehållet i index. cshtml i sin helhet och återskapa filen i följande steg.

1. I självstudien används två små bilder i vyn: en Azure-logotyp och en sökskärms visnings ikon (azure-logo.png och search.png). Kopiera mellan bilderna från GitHub-projektet till mappen **wwwroot/images** i ditt projekt.

1. Den första raden i index. cshtml ska referera till den modell som används för att kommunicera data mellan klienten (vyn) och servern (kontrollanten), som är den **SearchData** -modell som skapades tidigare. Lägg till den här raden i filen index. cshtml.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. Det är standard praxis att ange en rubrik för vyn, så att följande rader ska vara:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Efter rubriken anger du en referens till en HTML-formatmall som du kommer att skapa snart.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. Bröd texten i vyn hanterar två användnings fall. Först måste du ange en tom sida vid första användningen, innan Sök texten skrivs in. För det andra måste det hantera resultat, förutom Sök text rutan för upprepade frågor.

   För att hantera båda fallen måste du kontrol lera om modellen som har angetts till vyn är null. En null-modell indikerar det första användnings fallet (den första körningen av appen). Lägg till följande i filen index. cshtml och Läs igenom kommentarerna.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Lägg till format mal len. I Visual Studio, i **filen** >  **ny**  >  **fil**, väljer du **formatmall** (med **Allmänt** markerat).

   Ersätt standard koden med följande. Vi kommer inte att gå till den här filen i mer detalj, formatmallarna är standard-HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. Spara format filen som Hotels. CSS, i mappen **wwwroot/CSS** , tillsammans med standard platsen. css-filen.

Vi slutför vyn. I det här läget har båda modellerna och vyerna slutförts. Endast kontroll enheten är kvar för att knyta samman allt.

## <a name="define-methods"></a>Definiera metoder

I det här steget ändrar du till innehållet i **Start styrenheten**.

1. Öppna filen HomeController. CS och Ersätt **using** -instruktionerna med följande.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Lägg till index metoder

I en MVC-app är metoden **index ()** en standard åtgärds metod för alla kontroller. Den öppnar index-HTML-sidan. Standard metoden, som inte tar några parametrar, används i den här självstudien för att starta användnings fallet för program: rendera en tom Sök sida.

I det här avsnittet utökar vi metoden för att stödja ett andra användnings fall: rendera sidan när en användare har angett söktext. För att stödja det här fallet utökas index metoden för att ta en modell som en parameter.

1. Lägg till följande metod efter standard **index ()** -metoden.

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Lägg märke till metodens **asynkrona** deklaration och **await** -anropet till **RunQueryAsync**. De här nyckelorden tar hand om asynkrona anrop och undviker därför att blockera trådar på servern.

    **Catch** -blocket använder fel modellen som skapades som standard.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Observera fel hanteringen och andra standardvyer och metoder

Beroende på vilken version av .NET Core som du använder skapas en något annorlunda uppsättning standardvyer som standard. För .NET Core 3,1 är standardvyerna index, sekretess och fel. Du kan visa dessa standard sidor när du kör appen och kontrol lera hur de hanteras i kontrollanten.

Du kommer att testa fel visningen senare i den här självstudien.

Oanvända vyer och deras associerade åtgärder tas bort i GitHub-exemplet.

### <a name="add-the-runqueryasync-method"></a>Lägg till RunQueryAsync-metoden

Azure Kognitiv sökning-anropet är inkapslat i vår **RunQueryAsync** -metod.

1. Lägg först till några statiska variabler för att konfigurera Azure-tjänsten och ett anrop för att initiera dem.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Lägg nu till själva **RunQueryAsync** -metoden.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    I den här metoden kontrollerar du först att vår Azure-konfiguration är initierad och anger sedan några sökalternativ. Alternativet **Select** anger vilka fält som ska returneras i resultat och matchar därmed egenskaps namnen i **hotell** klassen. Om du utelämnar **Välj**, returneras alla dolda fält som kan vara ineffektiva om du bara är intresse rad av alla möjliga fält.

    Det asynkrona anropet till sökningen formulerar begäran (modell som **searchText**) och svar (modell som **searchResult**). Om du felsöker den här koden är **SearchResult** -klassen en bra kandidat för att ange en Bryt punkt om du behöver undersöka innehållet i **Model. resultList**. Du bör se att det är intuitivt, som ger dig de data du bad om och inte mycket annat.

### <a name="test-the-app"></a>Testa appen

Nu kontrollerar vi om appen fungerar som den ska.

1. Välj **fel sökning**  >  **starta utan fel sökning** eller tryck på **F5**. Om appen körs som förväntat ska du hämta den inledande index vyn.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Öppnar appen" border="true":::

1. Ange en frågesträng, till exempel "strand" (eller någon text som kommer till åtanke) och klicka på Sök ikonen för att skicka begäran.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Söker efter * strand *" border="true":::

1. Försök att ange "fem stjärnor". Observera att frågan inte returnerar några resultat. En mer avancerad sökning skulle behandla "fem stjärnor" som synonymer för "lyxen" och returnera resultaten. Stöd för [synonymer](search-synonyms.md) är tillgängligt i Azure kognitiv sökning, men omfattas inte av den här själv studie serien.

1. Försök att ange "Het" som söktext. Det returnerar _inte_ poster med ordet "hotell" i dem. Sök funktionen söker bara hela ord, även om några resultat returneras.

1. Försök med andra ord: "pool", "solsken", "View" och vad som är. Du kommer att se Azure Kognitiv sökning som arbetar på det enklaste, men ändå övertygandenivå.

## <a name="test-edge-conditions-and-errors"></a>Villkor för testning och fel

Det är viktigt att kontrol lera att våra fel hanterings funktioner fungerar som de ska, även om saker fungerar perfekt. 

1. I **index** -metoden, efter **try {** Call, anger du raden **Throw New Exception ()**. Detta undantag innebär ett fel när du söker efter text.

2. Kör appen, ange "bar" som söktext och klicka på Sök ikonen. Undantaget bör resultera i vyn fel.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Framtvinga ett fel" border="true":::

    > [!Important]
    > Det betraktas som en säkerhets risk för att returnera interna fel nummer på felsidor. Om din app är avsedd för allmän användning kan du utföra en undersökning i säkra och bästa metoder för vad som ska returneras när ett fel uppstår.

3. Ta bort **Utlös nytt undantag ()** när du är nöjd med fel hanteringen som det ska.

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Ett Azure Kognitiv sökning-anrop är koncist och det är enkelt att tolka resultaten.
* Asynkrona anrop lägger till en liten mängd komplexitet för kontrollanten, men är den bästa metoden om du planerar att utveckla kvalitets appar.
* Den här appen utförde en enkel texts ökning, som definieras av vad som har ställts in i **searchOptions**. Den här en klass kan dock fyllas i med många medlemmar som lägger till riktigt ambitiös i en sökning. Inte mycket ytterligare arbete krävs för att göra den här appen avsevärt mer kraftfull.

## <a name="next-steps"></a>Nästa steg

Du kan förbättra användar upplevelsen genom att lägga till fler funktioner, särskilt sid indelning (antingen med sid nummer eller oändlig rullning) och Autoavsluta/förslag. Du kan också överväga mer avancerade sökalternativ (till exempel geografisk sökning på hotell inom en angiven radie av en given punkt och Sök Resultat ordningen).

Dessa nästa steg behandlas i de återstående självstudierna. Vi börjar med växling.

> [!div class="nextstepaction"]
> [C#-självstudie: sid brytning för Sök Resultat – Azure Kognitiv sökning](tutorial-csharp-paging.md)