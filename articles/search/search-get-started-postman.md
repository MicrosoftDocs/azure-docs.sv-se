---
title: 'Snabb start: skapa ett Sök index i Postman med hjälp av REST API: er'
titleSuffix: Azure Cognitive Search
description: 'I den här REST API snabb starten lär du dig att anropa Azure Kognitiv sökning REST-API: er med Postman och exempel data och definitioner.'
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/17/2020
ms.openlocfilehash: 04619df8009aca3fecf317481d030280d5532281
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510920"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Snabb start: skapa ett Azure Kognitiv sökning-index i Postman med hjälp av REST API: er
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Den här artikeln förklarar hur du formulerar REST API begär Anden interaktivt med [Azure KOGNITIV sökning REST-API: er](https://docs.microsoft.com/rest/api/searchservice) och en API-klient för att skicka och ta emot förfrågningar. Med en API-klient och dessa instruktioner kan du skicka begär Anden och Visa svar innan du skriver någon kod.

I artikeln används Postman-programmet. Du kan [Hämta och importera en Postman-samling](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) om du föredrar att använda fördefinierade begär Anden. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Följande tjänster och verktyg krävs för den här snabb starten. 

+ [Postman Desktop-appen](https://www.getpostman.com/) används för att skicka begär anden till Azure kognitiv sökning.

+ [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-cognitive-search"></a>Ansluta till Azure Kognitiv sökning

I det här avsnittet använder du önskat webb verktyg för att konfigurera anslutningar till Azure Kognitiv sökning. Varje verktyg behåller information om begär ande huvud för sessionen, vilket innebär att du bara behöver ange API-nyckel och innehålls typ en gång.

För något av verktygen måste du välja ett kommando (GET, POST, placering och så vidare), ange en URL-slutpunkt och för vissa uppgifter tillhandahåller du JSON i bröd texten i begäran. Ersätt Sök tjänst namnet (din-SEARCH-SERVICE-NAME) med ett giltigt värde. Lägg till `$select=name` för att returnera bara namnet på varje index. 

> `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name`

Lägg märke till HTTPS-prefixet, namnet på tjänsten, namnet på ett objekt (i det här fallet samlingen indexs) och [API-versionen](search-api-versions.md). API-versionen är en obligatorisk, gemen sträng som anges som `?api-version=2020-06-30` för den aktuella versionen. API-versionerna uppdateras regelbundet. När du inkluderar API-versionen för varje begäran får du fullständig kontroll över vilken version som används.  

Rubrik sammansättning för begäran innehåller två element: `Content-Type` och som `api-key` används för att autentisera till Azure kognitiv sökning. Ersätt administrations-API-nyckeln (din-AZURE-SEARCH-ADMIN-API-nyckel) med ett giltigt värde. 

```http
api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
Content-Type: application/json
```

I Postman formulerar du en begäran som ser ut som på följande skärm bild. Välj **Hämta** som kommando, ange URL och klicka på **Skicka**. Det här kommandot ansluter till Azure Kognitiv sökning, läser samlingen Indexes och returnerar HTTP-statuskod 200 på en lyckad anslutning. Om din tjänst redan har index, innehåller svaret även index definitioner.

![URL och rubrik för Postman-begäran](media/search-get-started-postman/postman-url.png "URL och rubrik för Postman-begäran")

## <a name="1---create-an-index"></a>1 – Skapa ett index

I Azure Kognitiv sökning skapar du vanligt vis indexet innan du läser in det med data. [Create index-REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) används för den här aktiviteten. 

URL: en utökas till att inkludera `hotels` index namnet.

Så här gör du i Postman:

1. Ändra kommandot som ska **läggas**till.

2. Kopiera i denna URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30` .

3. Ange index definitionen (kopierings klar kod anges nedan) i bröd texten i begäran.

4. Klicka på **Skicka**.

![Indexera JSON-dokument i brödtext i begäran](media/search-get-started-postman/postman-request.png "Indexera JSON-dokument i brödtext i begäran")

### <a name="index-definition"></a>Indexdefinition

Fält samlingen definierar dokument strukturen. Varje dokument måste ha dessa fält och varje fält måste ha en datatyp. Sträng fält används i full texts ökning. Om du behöver använda numeriska data som sökbara måste du omvandla numeriska data som strängar.

Vilka åtgärder som tillåts fastställs av fältattributen. Med REST API:er kan du använda standardåtgärder i stor utsträckning. Alla strängar blir till exempel sökbara, hämtningsbara, filtrerbara och fasettbara som standard. Ofta behöver du bara ange attribut när du behöver stänga av ett beteende.

```json
{
    "name": "hotels-quickstart",  
    "fields": [
        {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
        {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
        {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
        {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
        {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
        {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Address", "type": "Edm.ComplexType", 
        "fields": [
        {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
        {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
        ]
     }
  ]
}
```

När du skickar denna begäran får du ett HTTP 201-svar som anger att indexet har skapats. Du kan kontrollera detta i portalen, men observera att portalsidan uppdateras med bestämda intervall, så det kan dröja någon minut eller två innan informationen dyker upp.

> [!TIP]
> Om HTTP 504 returneras kontrollerar du att HTTPS används i URL:en. Om HTTP 400 eller 404 returneras kontrollerar du att alla fält har kopierats och klistrats in korrekt i begärandetexten. HTTP 403 tyder vanligen på problem med API-nyckeln (antingen en ogiltig nyckel eller ett syntaxproblem när API-nyckeln angavs).

## <a name="2---load-documents"></a>2 Läs in dokument

Att skapa ett index och att fylla det, är två separata steg. I Azure Kognitiv sökning innehåller indexet alla sökbara data. I det här scenariot tillhandahålls data som JSON-dokument. REST API för att [lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) används för den här uppgiften. 

URL: en utökas till att omfatta `docs` samlingarna och `index` åtgärden.

Så här gör du i Postman:

1. Ändra kommandot till **post**.

2. Kopiera i denna URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30` .

3. Ange JSON-dokumenten (koden för kopierings klar nedan) i bröd texten i begäran.

4. Klicka på **Skicka**.

![JSON-dokument i brödtext i begäran](media/search-get-started-postman/postman-docs.png "JSON-dokument i brödtext i begäran")

### <a name="json-documents-to-load-into-the-index"></a>JSON-dokument som ska läsas in i indexet

Begärandetexten innehåller fyra dokument som ska läggas till i hotellindexet.

```json
{
    "value": [
    {
    "@search.action": "upload",
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
    "Category": "Boutique",
    "Tags": [ "pool", "air conditioning", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1970-01-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY",
        "PostalCode": "10022",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "2",
    "HotelName": "Twin Dome Motel",
    "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
    "Category": "Boutique",
    "Tags": [ "pool", "free wifi", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1979-02-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "140 University Town Center Dr",
        "City": "Sarasota",
        "StateProvince": "FL",
        "PostalCode": "34243",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Category": "Resort and Spa",
    "Tags": [ "air conditioning", "bar", "continental breakfast" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "2015-09-20T00:00:00Z",
    "Rating": 4.80,
    "Address": 
        {
        "StreetAddress": "3393 Peachtree Rd",
        "City": "Atlanta",
        "StateProvince": "GA",
        "PostalCode": "30326",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "4",
    "HotelName": "Sublime Cliff Hotel",
    "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
    "Category": "Boutique",
    "Tags": [ "concierge", "view", "24-hour front desk service" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1960-02-06T00:00:00Z",
    "Rating": 4.60,
    "Address": 
        {
        "StreetAddress": "7400 San Pedro Ave",
        "City": "San Antonio",
        "StateProvince": "TX",
        "PostalCode": "78216",
        "Country": "USA"
        }
    }
  ]
}
```

Om några sekunder bör du se ett HTTP 201-svar i listan över sessioner. Detta anger att dokumenten har skapats. 

Om ett 207-svar returneras misslyckades uppladdningen av minst ett dokument. Om ett 404-svar returneras beror det på ett syntaxfel i begärandehuvudet eller i begärandetexten. Kontrollera att du har ändrat slutpunkten så att den inkluderar följande: `/docs/index`.

> [!Tip]
> För vissa utvalda datakällor kan du välja en alternativ *indexerare* för att förenkla och minska mängden kod som krävs för indexering. Mer information finns i [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Indexeringsåtgärder).


## <a name="3---search-an-index"></a>3 – Söka i ett index

Nu när ett index och en dokument uppsättning har lästs in kan du skicka frågor mot dem med hjälp av [Sök dokument REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

URL: en utökas till att innehålla ett frågeuttryck som anges med Sök operatorn.

Så här gör du i Postman:

1. Ändra kommandot som ska **hämtas**.

2. Kopiera i denna URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2020-06-30` .

3. Klicka på **Skicka**.

Den här frågan är tom och returnerar antalet dokument i Sök resultatet. Begäran och svar bör se ut som skärmbilden från Postman nedan efter att du klickat på **Skicka**. Statuskoden ska vara 200.

 ![Hämta med Sök sträng på URL: en](media/search-get-started-postman/postman-query.png "Hämta med Sök sträng på URL: en")

Testa några andra exempel frågor för att få en känsla för syntaxen. Du kan göra en strängs ökning, orda Grant $filter frågor, begränsa resultat uppsättningen, begränsa sökningen till vissa fält med mera.

Byt ut den aktuella URL: en till dem nedan och klicka på **Skicka** varje gång för att visa resultatet.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2020-06-30

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2020-06-30

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2020-06-30

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2020-06-30
```

## <a name="get-index-properties"></a>Hämta index egenskaper
Du kan också använda [Hämta statistik](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) för frågor om antal dokument och index storlek: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2020-06-30
```

`/stats`Om du lägger till i din URL returneras index information. Din begäran i Postman borde se ut som på bilden nedan. Svaret innehåller ett dokumentantal och det diskutrymme som används uttryckt i byte.

 ![Hämta index information](media/search-get-started-postman/postman-system-query.png "Hämta index information")

Observera att syntaxen för API-versionen ser annorlunda ut här. Denna begäran använder `?` för att lägga till API-versionen. Den `?` separerar URL-sökvägen från frågesträngen, medan & separerar varje namn = värde-par i frågesträngen. I den här frågan är API-versionen det första och enda objektet i frågesträngen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du utför grundläggande uppgifter kan du gå vidare med ytterligare REST API samtal för mer avancerade funktioner, till exempel indexerare eller konfigurera [en kognitiv Sök pipeline](cognitive-search-tutorial-blob.md). I nästa steg rekommenderar vi följande länk:

> [!div class="nextstepaction"]
> [REST-självstudie: indexera och Sök i halv strukturerade data (JSON-blobbar) i Azure Kognitiv sökning](search-semi-structured-data.md)
