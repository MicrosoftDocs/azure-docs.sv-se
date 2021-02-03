---
title: 'Snabb start: skapa ett Sök index i Java Script'
titleSuffix: Azure Cognitive Search
description: I den här snabb starten för Java Script, lär dig hur du skapar ett index, läser in data och kör frågor på Azure Kognitiv sökning med hjälp av Java Script
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/02/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7d31201f9d1a4519538aba3ac57ddfd340d936d1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509409"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Snabb start: skapa ett Azure Kognitiv sökning-index med hjälp av Java Script SDK
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


Använd [Java Script/TYPSCRIPT SDK för Azure kognitiv sökning](/javascript/api/overview/azure/search-documents-readme) för att skapa ett Node.js-program i Java Script som skapar, läser in och skickar frågor till ett sökindex.

Den här artikeln visar hur du skapar programmet steg för steg. Du kan också [Ladda ned käll koden och data](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) och köra programmet från kommando raden.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar har du följande verktyg och tjänster:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [Sök efter en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ [Node.js](https://nodejs.org) och [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) eller en annan IDE


## <a name="set-up-your-project"></a>Konfigurera projektet

Börja med att hämta slut punkten och nyckeln för Sök tjänsten. Skapa sedan ett nytt projekt med NPM enligt beskrivningen nedan.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kopiera en nyckel och slut punkt

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomst nyckel på varje begäran. Som ett första steg hittar du API-nyckeln och URL: en som ska läggas till i projektet. Du kommer att ange båda värdena när du skapar klienten i ett senare steg.

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten, som krävs om du skapar eller tar bort objekt. Det finns två utbytbara primär-och sekundär nycklar. Du kan använda något av alternativen.

   ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-rest/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

### <a name="create-a-new-npm-project"></a>Skapa ett nytt NPM-projekt

Börja med att öppna VS Code och dess [integrerade Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) eller en annan terminal, till exempel kommando tolken Node.js.

1. Skapa en utvecklings katalog och ge den namnet `quickstart` :

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Initiera ett tomt projekt med NPM genom att köra 

    ```cmd
    npm init
    ```
     Acceptera standardvärdena, förutom för licensen, som du bör ange till "MIT". 

3. Installera `@azure/search-documents` , [Java Script/Typscript SDK för Azure kognitiv sökning](/javascript/api/overview/azure/search-documents-readme).

    ```cmd
    npm install @azure/search-documents
    ```

4. Installera `dotenv` , som används för att importera miljövariabler som vårt tjänst namn och API-nyckel.
    ```cmd
    npm install dotenv
    ```

5. Bekräfta att du har konfigurerat projekten och dess beroenden genom att kontrol lera att din  **package.jspå** filen ser ut ungefär som i följande JSON-fil:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Skapa en fil **. kuvert** för att lagra Sök tjänst parametrarna:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Ersätt `<search-service-name>` värdet med namnet på Sök tjänsten. Ersätt `<search-admin-key>` med det nyckel värde du registrerade tidigare. 

### <a name="create-indexjs-file"></a>Skapa index.js-fil

Nu skapar vi en **index.js** -fil, som är den huvud fil som kommer att vara värd för vår kod.

Överst i den här filen importerar vi `@azure/search-documents` biblioteket:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Därefter måste du kräva att `dotenv` paketet läses i parametrarna från **. miljö** -filen på följande sätt:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

Med våra importerade och miljövariabler på plats är vi redo att definiera huvud funktionen.

De flesta funktioner i SDK är asynkrona så vi gör vår huvud funktion `async` . Vi inkluderar även en `main().catch()` under huvud funktionen för att fånga och logga eventuella fel som påträffas:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

Med det på plats är vi redo att skapa ett index.

## <a name="1---create-index"></a>1 – Skapa index 

Skapa en fil **hotels_quickstart_index.jspå**.  Den här filen definierar hur Azure Kognitiv sökning fungerar med de dokument som ska läsas in i nästa steg. Varje fält identifieras av en `name` och har en angiven `type` . Varje fält har också en serie med indexfiler som anger om Azure Kognitiv sökning kan söka, filtrera, sortera och fasett vid fältet. De flesta fält är enkla data typer, men vissa, som `AddressType` är komplexa typer, som gör att du kan skapa omfattande data strukturer i ditt index.  Du kan läsa mer om [vilka data typer](/rest/api/searchservice/supported-data-types) och index-attribut som beskrivs i [skapa index (rest)](/rest/api/searchservice/create-index). 

Lägg till följande för att **hotels_quickstart_index.jspå** eller [Ladda ned filen](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Med vår index definition på plats vill vi importera  **hotels_quickstart_index.js** överst i **index.js** så att huvud funktionen kan komma åt index definitionen.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

I huvud funktionen skapar vi en `SearchIndexClient` , som används för att skapa och hantera index för Azure kognitiv sökning. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Nu vill vi ta bort indexet om det redan finns. Det här är en vanlig metod för test-/demonstrations kod.

Vi gör detta genom att definiera en enkel funktion som försöker ta bort indexet.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

För att köra funktionen extraherar vi index namnet från index definitionen och skickar det `indexName` tillsammans med `indexClient` till- `deleteIndexIfExists()` funktionen.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Därefter är det dags att skapa indexet med- `createIndex()` metoden.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Kör exemplet

Nu är du redo att köra exemplet. Använd ett terminalfönster för att köra följande kommando:

```cmd
node index.js
```

Om du har [hämtat käll koden](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) och inte har installerat de nödvändiga paketen kör du `npm install` först.

Du bör se en serie meddelanden som beskriver de åtgärder som utförs av programmet. 

Öppna **översikten** över Sök tjänsten i Azure Portal. Välj fliken **index** . Du bör se något som liknar följande:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Skärm bild av Azure Portal, översikt över Sök tjänsten, fliken index" border="false":::

I nästa steg ska du lägga till data i indexet. 

## <a name="2---load-documents"></a>2 Läs in dokument 


I Azure Kognitiv sökning är dokument data strukturer som båda är indata för indexering och utdata från frågor. Du kan skicka dessa data till indexet eller använda en [indexerare](search-indexer-overview.md). I det här fallet kommer vi program mässigt att skicka dokumenten till indexet.

Dokument indata kan vara rader i en databas, blobar i Blob Storage eller, som i det här exemplet, JSON-dokument på disk. Du kan antingen ladda ned [hotels.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) eller skapa egna **hotels.jspå** filen med följande innehåll:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Precis som vi gjorde med indexDefinition, måste vi också importera `hotels.json` överst i **index.js** så att data kan nås i vår huvud funktion.

```javascript
const hotelData = require('./hotels.json');
```


För att indexera data i sökindexet, måste vi nu skapa en `SearchClient` . När används `SearchIndexClient` för att skapa och hantera ett index, `SearchClient` används för att ladda upp dokument och fråga indexet.

Det finns två sätt att skapa en `SearchClient` . Det första alternativet är att skapa en `SearchClient` från början:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Du kan också använda `getSearchClient()` metoden för `SearchIndexClient` att skapa `SearchClient` :

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Nu när klienten har definierats laddar du upp dokumenten i Sök indexet. I det här fallet använder vi `mergeOrUploadDocuments()` metoden, som laddar upp dokumenten eller sammanfogar dem med ett befintligt dokument om det redan finns ett dokument med samma nyckel.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Kör programmet igen med `node index.js` . Du bör se en något annorlunda uppsättning meddelanden från de som du såg i steg 1. Den här gången *finns indexet* och du bör se ett meddelande om att ta bort det innan appen skapar det nya indexet och publicerar data till det. 

Innan vi kör frågorna i nästa steg ska du definiera en funktion som väntar på att programmet ska vänta i en sekund. Detta görs bara för test-/demonstrations syfte för att säkerställa att indexeringen är klar och att dokumenten är tillgängliga i indexet för våra frågor.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Om du vill att programmet ska vänta i en sekund anropar du `sleep` funktionen som nedan:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 – Söka i ett index

När ett index skapas och dokument laddas upp är du redo att skicka frågor till indexet. I det här avsnittet ska vi skicka fem olika frågor till Sök indexet för att demonstrera olika delar av fråge funktionen som är tillgänglig för dig.

Frågorna skrivs i en `sendQueries()` funktion som vi anropar i huvud funktionen på följande sätt:

```javascript
await sendQueries(searchClient);
```

Frågor skickas med hjälp av- `search()` metoden `searchClient` . Den första parametern är Sök texten och den andra parametern är eventuella ytterligare sökalternativ.

Den första sökningen `*` , som motsvarar att söka allt och välja tre fält i indexet. Det är bäst att `select` använda de fält som du behöver eftersom du kan lägga till svars tid för dina frågor genom att dra tillbaka onödiga data.

`searchOptions`För den här frågan har även `includeTotalCount` angetts till `true` , vilket returnerar antalet matchande resultat som hittas.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

Återstående frågor som beskrivs nedan bör också läggas till i `sendQueries()` funktionen. De är separerade här för läsbarhet.

I nästa fråga anger vi Sök termen `"wifi"` och inkluderar även ett filter för att endast returnera resultat där tillstånd är lika med `'FL'` . Resultaten sorteras också efter Hotellets `Rating` .

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Därefter är sökningen begränsad till ett enda sökbart fält med hjälp av `searchFields` parametern. Det här är ett bra alternativ för att göra frågan mer effektiv om du vet att du bara är intresse rad av matchningar i vissa fält. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Ett annat vanligt alternativ att inkludera i en fråga är `facets` . Med ansikte kan du bygga ut filter i användar gränssnittet så att det blir lättare för användarna att veta vilka värden de kan filtrera ned till.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Den slutgiltiga frågan använder- `getDocument()` metoden i `searchClient` . På så sätt kan du effektivt hämta ett dokument med hjälp av nyckeln. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Kör exemplet

Kör programmet med `node index.js` . Förutom föregående steg skickas frågorna och resultaten som skrivs till-konsolen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här snabb starten för Java Script arbetade du genom en serie aktiviteter för att skapa ett index, läsa in det med dokument och köra frågor. 

Om du redan har en bakgrund i Azure Kognitiv sökning kan du använda det här exemplet som en utgångs punkt för att testa förslag (Skriv-Ahead-eller Autoavsluta-frågor), filter och fasett-navigering. Om du är nybörjare på Azure Kognitiv sökning rekommenderar vi att du testar andra självstudier för att utveckla en förståelse för vad du kan skapa. Vår [dokumentationssida](https://azure.microsoft.com/documentation/services/search/) innehåller fler resurser. 

> [!div class="nextstepaction"]
> [Bygg en reagerar klient del för Azure Kognitiv sökning](https://github.com/dereklegenzoff/azure-search-react-template)