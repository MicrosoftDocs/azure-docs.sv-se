---
title: C#-självstudie om att använda Faces för att under lätta navigering
titleSuffix: Azure Cognitive Search
description: Fortsätt från "växlings resultat" för att lägga till en fasett-navigering. Lär dig hur ansikts kan användas för att enkelt begränsa en sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789810"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Självstudie: Lägg till en fasett-navigering med .NET SDK

FACET aktiverar självriktad navigering genom att tillhandahålla en uppsättning länkar för att filtrera resultat. I den här självstudien placeras en aspektad navigerings struktur på vänster sida av sidan med etiketter och text som kan beskäras för att trimma resultatet.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Ange modell egenskaper som _IsFacetable_
> * Lägg till aspekt navigering till din app

## <a name="overview"></a>Översikt

Facets baseras på fält i Sök indexet. En fråga som inkluderar fasett = [sträng] ger fältet att fasetta. Det är vanligt att inkludera flera ansikten, t. ex. `&facet=category&facet=amenities` , var och en avgränsade med ett et-tecken (&). Om du implementerar en aspektad navigerings struktur måste du ange både fasetter och filter. Filtret används vid en klickning-händelse för att begränsa resultaten. Om du till exempel klickar på "budget" filtreras resultaten utifrån dessa kriterier.

Den här självstudien utökar växlings projektet som skapats i självstudien [Lägg till växling i Sök Resultat](tutorial-csharp-paging.md) .

En färdig version av koden i den här självstudien finns i följande projekt:

* [4 – Lägg till aspekt – navigering (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Krav

* [GitHub-lösning (2a-Add-Siding)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) . Projektet kan antingen vara din egen version som skapats från den föregående själv studie kursen eller en kopia från GitHub.

Den här självstudien har uppdaterats med [Azure.Search.Documents-paketet (version 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . En tidigare version av .NET SDK finns i [kod exemplet Microsoft. Azure. search (version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="set-model-properties-as-isfacetable"></a>Ange modell egenskaper som IsFacetable

För att en modell egenskap ska finnas i en aspekt-sökning måste den märkas med **IsFacetable**.

1. Granska **hotell** klassen. **Kategori** och **taggar** är till exempel Taggade som **IsFacetable**, men **HotelName** och **Description** är inte. 

    ```cs
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
    ```

1. Vi kommer inte att ändra några taggar som en del av den här självstudien, så Stäng hotel.cs-filen utan ändringar.

    > [!Note]
    > En aspekts ökning genererar ett fel om ett fält som begärs i sökningen inte är korrekt taggat.

## <a name="add-facet-navigation-to-your-app"></a>Lägg till aspekt navigering till din app

I det här exemplet ska vi göra det möjligt för användaren att välja en kategori av hotellet eller en Amenity från listor över länkar som visas till vänster om resultatet. Användaren startar genom att ange en Sök text och sedan progressivt begränsa Sök resultatet genom att välja en kategori eller Amenity.

Det är kontrollantens jobb att skicka listor över ansikte till vyn. Om du vill behålla användar valen när sökningen pågår använder vi tillfällig lagring som mekanism för konserverings tillstånd.

![Använda aspekt navigering för att begränsa en sökning av "pool"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Lägg till filter strängar i SearchData-modellen

1. Öppna filen SearchData.cs och Lägg till sträng egenskaper till **SearchData** -klassen för att innehålla fasett filter strängarna.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Lägg till åtgärds metoden aspekt

Hem styrenheten behöver en ny åtgärd, **aspekt** och uppdateringar av dess befintliga **index** -och **sid** åtgärder och till **RunQueryAsync** -metoden.

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Byt ut åtgärds metoden **index (SearchData Model)** .

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Ersätt åtgärds metoden **PageAsync (SearchData Model)** .

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. Lägg till en **FacetAsync (SearchData Model)** -åtgärds metod som aktive ras när användaren klickar på en aspekt länk. Modellen kommer att innehålla antingen ett kategori-eller Amenity Sök filter. Lägg till den efter **PageAsync** -åtgärden.

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>Konfigurera Sök filtret

När en användare väljer en viss aspekt, till exempel, klickar på kategorin **anläggning och Spa** , kommer bara hotell som anges som den här kategorin att returneras i resultaten. Vi måste skapa ett _filter_ för att begränsa en sökning på det här sättet.

1. Ersätt **RunQueryAsync** -metoden med följande kod. Främst tar det en kategori filter sträng och en Amenity filter sträng, och anger **filter** parametern för **SearchOptions**.

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    Observera att egenskaperna för **kategori** och **taggar** läggs till i listan med **valda** objekt att returnera. Detta tillägg är inte ett krav för att aspekt navigering ska fungera, men vi använder den här informationen för att kontrol lera att filtren fungerar som de ska.

### <a name="add-lists-of-facet-links-to-the-view"></a>Lägg till listor över aspekt länkar till vyn

Vyn kommer att kräva några betydande ändringar. 

1. Börja med att öppna filen Hotels. CSS (i mappen wwwroot/CSS) och Lägg till följande klasser.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. I vyn ordnar du resultatet i en tabell för att justera fasett-listorna till vänster och resultatet till höger. Öppna filen index. cshtml. Ersätt hela innehållet i HTML Body- &lt; &gt; taggarna med följande kod.

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    Observera användningen av **HTML. ActionLink-** anropet. Det här anropet kommunicerar giltiga filter strängar till kontroll enheten när användaren klickar på en aspekt länk. 

### <a name="run-and-test-the-app"></a>Kör och testa appen

Fördelen med aspekt navigering till användaren är att de kan begränsa sökningar med ett enda klick, som vi kan visa i följande ordning.

1. Kör appen, skriv "flyg plats" som Sök text. Kontrol lera att listan över ansikte visas snyggt till vänster. Dessa ansikte är allt som gäller för hotell som har "flyg plats" i sina text data, med en räkning av hur ofta de inträffar.

    ![Använda aspekt navigering för att begränsa en sökning efter "flyg plats"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. Klicka på kategorin **anläggning och Spa** . Kontrol lera att alla resultat finns i den här kategorin.

    ![Begränsa sökningen till "utväg och Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Klicka på den **kontinentala frukost** Amenity. Kontrol lera att alla resultat fortfarande finns i kategorin "utväg och Spa" med den valda Amenity.

    ![Begränsa sökningen till "fast frukost"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Försök att välja någon annan kategori, sedan en Amenity och Visa de smala resultaten. Prova sedan på det andra sättet, en Amenity, och sedan en kategori. Skicka en tom sökning för att återställa sidan.

    >[!Note]
    > När ett val görs i en fasett-lista (t. ex. kategori) åsidosätts alla tidigare val i kategori listan.

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Det är absolut nödvändigt att markera varje aspekt bara fält med egenskapen **IsFacetable** för inkludering i aspekt navigering.
* Facets kombineras med filter för att minska resultatet.
* Ansikte är kumulativa, med varje val som bygger på föregående ett för att ytterligare begränsa resultaten.

## <a name="next-steps"></a>Nästa steg

I nästa självstudie tittar vi på att beställa resultat. Fram till den här punkten beställs resultatet enkelt i den ordning som de finns i databasen.

> [!div class="nextstepaction"]
> [C#-självstudie: ordna resultaten – Azure Kognitiv sökning](tutorial-csharp-orders.md)
