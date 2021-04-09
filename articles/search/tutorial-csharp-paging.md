---
title: C#-självstudie om sid brytning för Sök Resultat
titleSuffix: Azure Cognitive Search
description: Lägg till sid brytning och navigerings knappar för att söka efter resultat och skapa ett befintligt hotell projekt för att lägga till de första, nästa, föregående och sista och numrerade knapparna. Ett andra växlings system använder oändlig rullning som utlöses genom att flytta en lodrät rullnings list till dess nedre gräns.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: bad4bc4d0016b2898b315bfb9799dc8972be7b12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785877"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Självstudie: Lägg till sid indelning i Sök resultat med hjälp av .NET SDK

Lär dig hur du implementerar två olika växlings system, det första baserat på sid nummer och det andra vid oändlig rullning. Båda växlings systemen används ofta och valet av rätt är beroende av den användar upplevelse som du vill ha med resultatet. 

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Utöka din app med numrerad sid indelning
> * Utöka din app med oändlig rullning

## <a name="overview"></a>Översikt

Den här självstudien översätter ett växlings system till ett tidigare skapat projekt som beskrivs i själv studie kursen [skapa din första sökning](tutorial-csharp-create-first-app.md) .

Färdiga versioner av koden som du kommer att utveckla i den här självstudien finns i följande projekt:

* [2a – Lägg till växling (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2b-Lägg till oändlig rullning (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>Förutsättningar

* [1 – Basic-search-Page-projekt (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) . Projektet kan antingen vara din egen version som skapats från den föregående själv studie kursen eller en kopia från GitHub.

Den här självstudien har uppdaterats med [Azure.Search.Documents-paketet (version 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . En tidigare version av .NET SDK finns i [kod exemplet Microsoft. Azure. search (version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="extend-your-app-with-numbered-paging"></a>Utöka din app med numrerad sid indelning

Numrerad växling är det växlings system som du väljer för de huvudsakliga huvud webb sökmotorer och många andra Sök webbplatser. Numrerad sid indelning innehåller vanligt vis alternativen "Nästa" och "föregående" utöver ett intervall av faktiska sid nummer. Dessutom kan alternativet "första sidan" och "sista sidan" också vara tillgängligt. De här alternativen ger en användar kontroll för att navigera genom sidbaserade resultat.

I den här självstudien lägger du till ett system som omfattar första, föregående, nästa och sista alternativ, tillsammans med sid nummer som inte börjar från 1, utan omger den aktuella sidan som användaren är på (till exempel om användaren tittar på sidan 10, kanske sid nummer 8, 9, 10, 11 och 12 visas).

Systemet är tillräckligt flexibelt för att tillåta att antalet synliga sid nummer anges i en global variabel.

Systemet kommer att hantera knapparna längst till vänster och höger-högst sidor som särskilda, vilket innebär att de utlöser ändrings intervallet för sid nummer som visas. Exempel: om sid nummer 8, 9, 10, 11 och 12 visas och användaren klickar på 8, visas sid numren som ändras till 6, 7, 8, 9 och 10. Och det finns ett liknande Skift till höger om de markerade 12.

### <a name="add-paging-fields-to-the-model"></a>Lägg till växlings fält i modellen

Ha den grundläggande Sök sid lösningen öppen.

1. Öppna modell filen SearchData. cs.

1. Lägg till globala variabler för att stödja sid brytning. I MVC deklareras globala variabler i sin egen statiska klass. **ResultsPerPage** anger antalet resultat per sida. **MaxPageRange** bestämmer antalet synliga sid nummer i vyn. **PageRangeDelta** anger hur många sidor som ska flyttas till vänster eller höger när sid numret längst till vänster eller höger är markerat. Det sistnämnda talet är vanligt vis cirka hälften av **MaxPageRange**. Lägg till följande kod i namn området.

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Om du kör projektet på en enhet med en mindre skärm, till exempel en bärbar dator, kan du överväga att ändra **ResultsPerPage** till 2.

1. Lägg till sid egenskaper i klassen **SearchData** efter egenskapen **searchText** .

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Lägg till en tabell med sid växlings alternativ i vyn

1. Öppna filen index. cshtml och Lägg till följande kod direkt före den avslutande/Body- &lt; &gt; taggen. Den här nya koden visar en tabell med sid alternativ: First, Previous, 1, 2, 3, 4, 5, nästa, sist.

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Vi använder en HTML-tabell för att justera saker på ett snyggt sätt. Alla åtgärder kommer dock från @Html.ActionLink instruktionerna, varje anrop av kontrollanten med en **ny** modell som skapats med olika poster till egenskapen **växling** som vi lade till tidigare.

    De första och sista sid alternativen skickar inte strängar som "First" och "Last", utan skickar istället rätt sid nummer.

1. Lägg till växlings klasser i listan med HTML-format i filen Hotels. CSS. **PageSelected** -klassen kan identifiera den aktuella sidan (genom att använda ett fet format på sid numret) i listan med sid nummer.

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Lägg till en sid åtgärd för kontrollanten

1. Öppna filen HomeController. CS och Lägg till **PageAsync** -åtgärden. Den här åtgärden svarar på något av de valda sid alternativen.

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

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

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    **RunQueryAsync** -metoden visar nu ett syntaxfel på grund av den tredje parametern som vi kommer att komma till i en bit.

    > [!Note]
    > Anropen till **TempData** lagrar ett värde (ett **objekt**) i ett tillfälligt lagrings utrymme, men den här lagringen sparas _bara_ för ett anrop. Om vi lagrar något i temporära data, kommer det att vara tillgängligt för nästa anrop till en kontroll av styrenheten, men kommer oftast att vara borta av anropet. På grund av den här korta livs längd lagrar vi söktext och växlings egenskaper tillbaka i tillfällig lagring varje anrop till **PageAsync**.

1. Uppdatera **indexet (modell)** för att lagra tillfälliga variabler och Lägg till den vänstra sidans sid parameter i **RunQueryAsync** -anropet.

    ```csharp
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
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. Metoden **RunQueryAsync** , som introducerades i föregående lektion, behöver ändras för att lösa syntaxfel. Vi använder fälten **hoppa över**, **storlek** och **IncludeTotalCount** i [**SearchOptions**](/dotnet/api/azure.search.documents.searchoptions) -klassen för att begära endast en sida med resultat, med början vid **Skip** -inställningen. Vi måste också beräkna växlings variablerna för vår vy. Ersätt hela metoden med följande kod.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

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

        return View("Index", model);
    }
    ```

1. Gör slutligen en liten ändring i vyn. Variabeln **resultList. Results. totalCount** kommer nu att innehålla antalet resultat som returneras på en sida (3 i vårt exempel), inte det totala antalet. Eftersom vi anger **IncludeTotalCount** till True innehåller variabeln **resultList. totalCount** det totala antalet resultat. Leta reda på var antalet resultat visas i vyn och ändra det till följande kod.

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > En mindre prestanda träff uppstår när **IncludeTotalCount** anges till true, eftersom den här summan måste beräknas av Azure kognitiv sökning. Med komplexa data uppsättningar finns det en varning om att det returnerade värdet är en _uppskattning_. Eftersom hotell Sök sökkorpus är liten är den korrekt.

### <a name="compile-and-run-the-app"></a>Kompilera och köra appen

Välj nu **starta utan fel sökning** (eller tryck på tangenten F5).

1. Sök efter en sträng som returnerar massor av resultat (till exempel "WiFi"). Kan du enkelt bläddra igenom resultaten?

    ![Numrerad växling via "pool"-resultat](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. Försök att klicka på längst till höger och sedan längst till vänster på sidan. Justerar sid numren på rätt sätt för att centrera sidan som du är på?

1. Är alternativen "First" och "sista" användbara? Vissa kommersiella sökmotorer använder dessa alternativ och andra inte.

1. Gå till sista resultat sidan. Den sista sidan är den enda sidan som kan innehålla färre än **ResultsPerPage** resultat.

    ![Undersöker den sista sidan i "WiFi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. Skriv "stad" och klicka på Sök. Inga sid alternativ visas om resultatet är färre än en sida.

    ![Söker efter "stad"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Spara projektet och fortsätt till nästa avsnitt för att få en alternativ form av växling.

## <a name="extend-your-app-with-infinite-scrolling"></a>Utöka din app med oändlig rullning

Oändlig rullning utlöses när en användare rullar en lodrät rullnings list till det sista av resultaten som visas. I det här fallet görs ett anrop till Sök tjänsten för nästa resultat sida. Om det inte finns några fler resultat returneras ingenting och den lodräta rullnings listen ändras inte. Om det finns fler resultat läggs de till på den aktuella sidan och rullnings listen ändras för att visa att fler resultat är tillgängliga.

En viktig punkt att observera är att den aktuella sidan inte ersätts, utan utökas för att visa ytterligare resultat. En användare kan alltid rulla tillbaka till det första resultatet av sökningen.

Om du vill implementera oändlig rullning börjar vi med projektet innan något av de rullande elementen för sid nummer har lagts till. I GitHub är det här [FirstAzureSearchApp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) -lösningen.

### <a name="add-paging-fields-to-the-model"></a>Lägg till växlings fält i modellen

1. Lägg först till en **växlings** egenskap i **SearchData** -klassen (i modell filen SearchData. CS).

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    Den här variabeln är en sträng som innehåller "Nästa" om nästa sida med resultat ska skickas, eller vara null för den första sidan i en sökning.

1. I samma fil och i namn området lägger du till en global variabel klass med en egenskap. I MVC deklareras globala variabler i sin egen statiska klass. **ResultsPerPage** anger antalet resultat per sida. 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Lägg till en lodrät rullnings List i vyn

1. Leta upp avsnittet i filen index. cshtml som visar resultatet (det börjar med **@if (modell! = null)**).

1. Ersätt avsnittet med koden nedan. Det nya **&lt; div &gt;** -avsnittet är runt området som ska kunna rullas och lägger till både ett **overflow-y-** attribut och ett anrop till en **onscroll** -funktion som kallas "rullnings Bart ()", som så.

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. Direkt under slingan, efter &lt; taggen/div &gt; , lägger du till den **rullnings bara** funktionen.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    Instruktionen **IF** i skriptet ovan testar om användaren har rullat längst ned i den lodräta rullnings listen. Om de har det, görs ett anrop till **hem** styrenheten till en åtgärd som kallas **NextAsync**. Ingen annan information behövs av kontrollanten, den kommer att returnera nästa sida med data. Dessa data formateras sedan med identiska HTML-format som den ursprungliga sidan. Om inga resultat returneras, läggs inget till och behålls som de är.

### <a name="handle-the-next-action"></a>Hanterar nästa åtgärd

Det finns bara tre åtgärder som måste skickas till kontrollanten: den första körningen av appen, som anropar **index ()**, den första sökningen av användaren, som anropar **index (modell)** och sedan de efterföljande anropen för fler resultat via **Next (modell)**.

1. Öppna start kontroll filen och ta bort **RunQueryAsync** -metoden från den ursprungliga självstudien.

1. Ersätt **index-åtgärden (modell)** med följande kod. Nu hanterar det **växlings** fältet när det är null, eller anges till "Next", och hanterar anropet till Azure kognitiv sökning.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Precis som med den numrerade växlings metoden använder vi Sök inställningarna **hoppa över** och **storlek** för att begära att bara de data vi behöver returneras.

1. Lägg till **NextAsync** -åtgärden till hem styrenheten. Observera hur den returnerar en lista, varje hotell som lägger till två element i listan: ett hotell namn och en hotell beskrivning. Det här formatet är inställt på att matcha den **rullnings bara** funktionens användning av returnerade data i vyn.

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Om du får ett syntaxfel på **list &lt; strängen &gt;** lägger du till följande **med hjälp av** direktiv i enhets filens huvud.

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilera och köra ditt projekt

Välj nu **starta utan fel sökning** (eller tryck på tangenten F5).

1. Ange en term som ger massor av resultat (till exempel "pool") och testa den lodräta rullnings listen. Utlöses en ny resultat sida?

    ![Oändlig rullning genom "pool"-resultat](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > För att se till att en rullnings List visas på den första sidan måste den första sidan med resultat skilja sig från höjden på det utrymme som de visas i. I vårt exempel **. box1** har höjden 30 bild punkter, **. box2** har en höjd på 100 bild punkter _och_ en botten marginal på 24 bild punkter. Varje post använder alltså 154 bild punkter. Tre poster tar upp 3 x 154 = 462 bild punkter. För att säkerställa att en lodrät rullnings List visas måste en höjd till visnings utrymmet vara inställd på mindre än 462 bild punkter, även 461 fungerar. Det här problemet inträffar bara på den första sidan, efter att en rullnings List är säker på att den ska visas. Raden som ska uppdateras är: **&lt; div ID = "myDiv" Style = "width: 800px; height: 450px; spill-y: Scroll;" onscroll = "rullad ()" &gt;**.

1. Bläddra nedåt längst ned i resultaten. Observera hur all information finns nu på sidan med en vy. Du kan bläddra hela vägen överst utan att utlösa några Server anrop.

Mer avancerade oändliga rullnings bara system kan använda mus hjulet eller liknande andra mekanism för att utlösa inläsningen av en ny resultat sida. Vi kommer inte att ta bort oändlig bläddring i de här självstudierna, men det har en särskild snabb knapp som du kan använda för att undvika extra musklick och du kanske vill undersöka andra alternativ.

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Numrerad växling är användbart för sökningar där ordningen på resultaten är något godtycklig, vilket innebär att det kan vara något intressant för användarna på de senare sidorna.
* Oändlig rullning är användbart när resultat ordningen är särskilt viktig. Om resultatet till exempel beställs på avståndet från mitten av en ort i destinationen.
* Numrerad sid indelning gör det lättare att navigera. En användare kan till exempel komma ihåg att ett intressant resultat var på sidan 6, men det finns ingen sådan enkel referens i oändlig rullning.
* Oändlig rullning har ett enkelt sätt att rulla upp och ned utan sid nummer för att klicka på.
* En viktig funktion för oändlig rullning är att resultat läggs till på en befintlig sida, inte att ersätta sidan, vilket är effektivt.
* Temporär lagring behålls bara för ett samtal och måste återställas för att klara ytterligare samtal.

## <a name="next-steps"></a>Nästa steg

Växling är grundläggande för en Sök upplevelse. Med rätt sid indelning är nästa steg att förbättra användar upplevelsen ytterligare genom att lägga till typ i förväg-sökningar.

> [!div class="nextstepaction"]
> [C#-självstudie: Lägg till komplettering och förslag – Azure Kognitiv sökning](tutorial-csharp-type-ahead-and-suggestions.md)