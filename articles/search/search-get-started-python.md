---
title: 'Snabb start: skapa ett Sök index i python'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar ett sökindex, läser in data och kör frågor med python, Jupyter Notebook och Azure.Documents. Sök efter python i klient biblioteket.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 8b9c4792fa6dbdc70f657ce3c5f1757473a22fda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225225"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Snabb start: skapa ett Azure Kognitiv sökning-index i python med Jupyter Notebook

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
>

Skapa en bärbar dator som skapar, läser in och skickar frågor till ett Azure Kognitiv sökning-index med python och [biblioteket Azure-Search-Documents](/python/api/overview/azure/search-documents-readme) i Azure SDK för python. Den här artikeln förklarar hur du skapar en antecknings bok steg för steg. Du kan också [Hämta och köra en färdig Jupyter python-anteckningsbok](https://github.com/Azure-Samples/azure-search-python-samples).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster och verktyg krävs för den här snabb starten.

* [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), vilket ger python 3. x och Jupyter Notebook.

* [Azure-Search-Documents-paket](https://pypi.org/project/azure-search-documents/)

* [Skapa en Sök tjänst](search-create-service-portal.md) eller [Sök efter en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda den kostnads fria nivån för den här snabb starten. 

## <a name="copy-a-key-and-url"></a>Kopiera en nyckel och en URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-rest/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-cognitive-search"></a>Ansluta till Azure Kognitiv sökning

I den här uppgiften startar du Jupyter Notebook och kontrollerar att du kan ansluta till Azure Kognitiv sökning. Du gör detta genom att begära en lista över index från din tjänst. I Windows med Anaconda3 kan du använda Anaconda-navigatören för att starta en bärbar dator.

1. Skapa en ny python3 Notebook.

1. I den första cellen läser du in biblioteken från Azure SDK för python, inklusive [Azure-Search-Documents](/python/api/azure-search-documents).

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. I den andra cellen skriver du in de begär ande element som ska vara konstanter på varje begäran. Ange namnet på din Sök tjänst, administrations-API-nyckel och API-nyckel för frågor, som kopieras i föregående steg. Den här cellen konfigurerar också de klienter som ska användas för vissa åtgärder: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) för att skapa ett index och [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) för att fråga ett index.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. I den tredje cellen kör du en delete_index-åtgärd för att rensa din tjänst för befintliga *hotell starts* index. Genom att ta bort indexet kan du skapa ett annat *hotell-snabb starts* index med samma namn.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Kör varje steg.

## <a name="1---create-an-index"></a>1 – Skapa ett index

Obligatoriska element i ett index inkluderar ett namn, en fält samling och en nyckel. Fält samlingen definierar strukturen för ett logiskt *Sök dokument*, som används för att läsa in data och returnera resultat. 

Varje fält har ett namn, typ och attribut som avgör hur fältet används (till exempel om det är full text sökbar, filtrerings bar eller hämtnings bar i Sök resultaten). I ett index måste ett av fälten av typen anges `Edm.String` som *nyckel* för dokument identitet.

Det här indexet heter "Hotels-snabb start" och innehåller fält definitionerna som visas nedan. Det är en del av ett större [hotell index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) som används i andra genom gångar. Vi trimmade det i den här snabb starten för det kortfattat.

1. I nästa cell klistrar du in följande exempel i en cell för att ange schemat.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. Formulera begäran i en annan cell. Den här create_index begäran riktar in index samlingen för Sök tjänsten och skapar en [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) baserat på det index schema som du angav i föregående cell.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Kör varje steg.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 Läs in dokument

Om du vill läsa in dokument skapar du en dokument samling med hjälp av en [index åtgärd](/python/api/azure-search-documents/azure.search.documents.models.indexaction) för åtgärds typen (Ladda upp, sammanfoga och ladda upp). Dokumenten kommer från [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) på GitHub.

1. I en ny cell, ange fyra dokument som stämmer överens med index schemat. Ange en uppladdnings åtgärd för varje dokument.

    ```python
    documents = [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    ```  

1. Formulera begäran i en annan cell. Den här upload_documents begäran riktar in dokument samlingen för hotell-snabb starts indexet och skickar dokumenten som tillhandahålls i föregående steg till Kognitiv sökning-indexet.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Kör varje steg för att skicka dokumenten till ett index i din Sök tjänst.

## <a name="3---search-an-index"></a>3 – Söka i ett index

Det här steget visar hur du frågar ett index med **Sök** -metoden i [search. client-klassen](/python/api/azure-search-documents/azure.search.documents.searchclient).

1. Följande steg kör en tom sökning ( `search=*` ) och returnerar en lista med en lista med en lista (Sök poäng = 1,0) av godtyckliga dokument. Eftersom det inte finns några kriterier inkluderas alla dokument i resultaten. Den här frågan skriver bara ut två av fälten i varje dokument. Den lägger också `include_total_count=True` till för att få ett antal dokument (4) i resultatet.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Nästa fråga lägger till hela villkor i Sök uttrycket ("WiFi"). Den här frågan anger att resultaten bara innehåller fälten i `select` instruktionen. Genom att begränsa de fält som kommer tillbaka minimeras mängden data som skickas tillbaka över kabeln och minskar Sök fördröjningen.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Använd sedan ett filter uttryck och bara returnera de hotellen med en klassificering som är större än 4, sorterade i fallande ordning.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. Lägg till `search_fields` i omfattnings frågan som matchar ett enskilt fält.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. FACET är etiketter som kan användas för att skapa fasett-navigerings struktur. Den här frågan returnerar fasetter och antal för kategorin.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. I det här exemplet ska du söka efter ett särskilt dokument baserat på dess nyckel. Du skulle normalt vilja returnera ett dokument när en användare klickar på ett dokument i ett Sök resultat.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. I det här exemplet ska vi använda funktionen Autoavsluta. Detta används vanligt vis i en sökruta för att hjälpa till att automatiskt slutföra eventuella matchningar som användaren skriver i sökrutan.

   När indexet skapades skapades även en förslags ställare med namnet "TG" som en del av begäran. En förslags definition anger vilka fält som kan användas för att hitta möjliga matchningar till förslags begär Anden. I det här exemplet är dessa fält "Taggar", "adress/ort", "adress/land". Om du vill simulera automatisk komplettering måste du skicka bokstaven "sa" som en del av en sträng. Metoden för automatisk komplettering av [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) skickar tillbaka potentiella villkors matchningar.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>Rensa

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Som en förenkling använder den här snabb starten en förkortad version av hotell indexet. Du kan skapa en fullständig version för att prova fler intressanta frågor. Om du vill hämta den fullständiga versionen och alla 50-dokument kör du guiden **Importera data** och väljer *hotell-exempel* från de inbyggda exempel data källorna.

> [!div class="nextstepaction"]
> [Snabb start: skapa ett index i Azure Portal](search-get-started-portal.md)