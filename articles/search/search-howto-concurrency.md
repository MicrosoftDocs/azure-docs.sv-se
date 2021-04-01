---
title: Hantera samtidiga skrivningar till resurser
titleSuffix: Azure Cognitive Search
description: Använd optimistisk samtidighet för att undvika konflikter i luften vid uppdateringar eller borttagningar till Azure Kognitiv sökning index, indexerare, data källor.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 52260facc1a458a3ef18b067e6d52e189f5d329c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881762"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Hantera samtidighet i Azure Kognitiv sökning

När du hanterar Azure Kognitiv sökning-resurser, till exempel index och data källor, är det viktigt att uppdatera resurserna på ett säkert sätt, särskilt om resurser kan nås samtidigt av olika komponenter i ditt program. När två klienter samtidigt uppdaterar en resurs utan samordning är det möjligt att tävla. För att förhindra detta erbjuder Azure Kognitiv sökning en *optimistisk samtidighets modell*. Det finns inga lås på en resurs. I stället finns det en ETag för varje resurs som identifierar resurs versionen så att du kan formulera begär Anden som förhindrar oavsiktliga överskrivning.

> [!Tip]
> Konceptuell kod i en [exempel C#-lösning](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) förklarar hur samtidighets kontroller fungerar i Azure kognitiv sökning. Koden skapar villkor som anropar samtidighets kontroll. Att läsa [kodfragmentet nedan](#samplecode) kan vara tillräckligt för de flesta utvecklare, men om du vill köra det redigerar du appsettings.jspå för att lägga till tjänst namnet och en Admin-API-nyckel. Med en tjänst-URL `http://myservice.search.windows.net` , är tjänstens namn `myservice` .

## <a name="how-it-works"></a>Så här fungerar det

Optimistisk samtidighet implementeras genom åtkomst villkors kontroller i API-anrop som skriver till index, indexerare, data källor, färdighetsuppsättningar och synonymMap-resurser.

Alla resurser har en [*Entity-tagg (etag)*](https://en.wikipedia.org/wiki/HTTP_ETag) som tillhandahåller objekt versions information. Genom att först kontrol lera ETag kan du undvika samtidiga uppdateringar i ett typiskt arbets flöde (Hämta, ändra lokalt, uppdatera) genom att se till att resursens ETag matchar din lokala kopia.

+ REST API använder en [etag](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) i begär ande huvudet.

+ .NET SDK anger ETag genom ett accessCondition-objekt, vilket anger [If-Match | If-Match-none-huvud](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) på resursen. Objekt som använder ETags, till exempel [SynonymMap. etag](/dotnet/api/azure.search.documents.indexes.models.synonymmap.etag) och [SearchIndex. etag](/dotnet/api/azure.search.documents.indexes.models.searchindex.etag), har ett accessCondition-objekt.

Varje gång du uppdaterar en resurs ändras dess ETag automatiskt. När du implementerar samtidiga hantering, kommer allt du gör att införa ett villkor för den uppdateringsbegäran som kräver att fjär resursen har samma ETag som kopian av resursen som du ändrade på klienten. Om en samtidig process redan har ändrat fjär resursen, stämmer inte ETagen överens med villkoret och begäran Miss fungerar med HTTP 412. Om du använder .NET SDK visas manifestet som en `CloudException` där `IsAccessConditionFailed()` tilläggs metoden returnerar true.

> [!Note]
> Det finns bara en mekanism för samtidighet. Den används alltid oavsett vilket API som används för resurs uppdateringar.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Användnings fall och exempel kod

Följande kod visar accessCondition-kontroller för nyckel uppdaterings åtgärder:

+ Det gick inte att uppdatera om resursen inte längre finns
+ Det går inte att uppdatera om resurs versionen ändras

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Exempel kod från [DotNetETagsExplainer-program](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```csharp
class Program
{
    // This sample shows how ETags work by performing conditional updates and deletes
    // on an Azure Cognitive Search index.
    static void Main(string[] args)
    {
        IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
        IConfigurationRoot configuration = builder.Build();

        SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

        Console.WriteLine("Deleting index...\n");
        DeleteTestIndexIfExists(serviceClient);

        // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
        // of the resource you're working on. When you first create a resource such as an index, its ETag is
        // empty.
        Index index = DefineTestIndex();
        Console.WriteLine(
            $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

        // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
        // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
        // blank ETag.
        Console.WriteLine("Creating index...\n");
        index = serviceClient.Indexes.Create(index);
        
        Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

        // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
        // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
        // succeed if the index already exists.
        index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
        index =
            serviceClient.Indexes.CreateOrUpdate(
                index,
                accessCondition: AccessCondition.GenerateIfExistsCondition());

        Console.WriteLine(
            $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

        // More importantly, ETags protect you from concurrent updates to the same resource. If another
        // client tries to update the resource, it will fail as long as all clients are using the right
        // access conditions.
        Index indexForClient1 = index;
        Index indexForClient2 = serviceClient.Indexes.Get("test");

        Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
        Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

        // Client 1 successfully updates the index.
        indexForClient1.Fields.Add(new Field("a", DataType.Int32));
        indexForClient1 =
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient1,
                accessCondition: AccessCondition.IfNotChanged(indexForClient1));

        Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

        // Client 2 tries to update the index, but fails, thanks to the ETag check.
        try
        {
            indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient2,
                accessCondition: AccessCondition.IfNotChanged(indexForClient2));

            Console.WriteLine("Whoops; This shouldn't happen");
            Environment.Exit(1);
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine("Client 2 failed to update the index, as expected.");
        }

        // You can also use access conditions with Delete operations. For example, you can implement an
        // atomic version of the DeleteTestIndexIfExists method from this sample like this:
        Console.WriteLine("Deleting index...\n");
        serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

        // This is slightly better than using the Exists method since it makes only one round trip to
        // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
        // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
        // Azure Cognitive Search is idempotent.

        // And we're done! Bye!
        Console.WriteLine("Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

    private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
    {
        string searchServiceName = configuration["SearchServiceName"];
        string adminApiKey = configuration["SearchServiceAdminApiKey"];

        SearchServiceClient serviceClient =
            new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
        return serviceClient;
    }

    private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("test"))
        {
            serviceClient.Indexes.Delete("test");
        }
    }

    private static Index DefineTestIndex() =>
        new Index()
        {
            Name = "test",
            Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
        };
    }
}
```

## <a name="design-pattern"></a>Design mönster

Ett design mönster för att implementera optimistisk samtidighet bör innehålla en slinga som försöker få åtkomst till villkors kontroll, ett test för åtkomst villkoret och hämtar en uppdaterad resurs innan du försöker återställa ändringarna igen.

Det här kodfragmentet illustrerar hur en synonymMap läggs till i ett index som redan finns. Den här koden är från [synonym C#-exemplet för Azure kognitiv sökning](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/v10/DotNetHowToSynonyms).

Kodfragmentet hämtar indexet "Hotels", kontrollerar objekt versionen vid en uppdaterings åtgärd, genererar ett undantag om villkoret Miss lyckas och försöker sedan utföra åtgärden igen (upp till tre gånger), med början från index hämtning från servern för att hämta den senaste versionen.

```csharp
private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
{
    int MaxNumTries = 3;

    for (int i = 0; i < MaxNumTries; ++i)
    {
        try
        {
            Index index = serviceClient.Indexes.Get("hotels");
            index = AddSynonymMapsToFields(index);

            // The IfNotChanged condition ensures that the index is updated only if the ETags match.
            serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

            Console.WriteLine("Updated the index successfully.\n");
            break;
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
        }
    }
}

private static Index AddSynonymMapsToFields(Index index)
{
    index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
    index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
    return index;
}
```

## <a name="next-steps"></a>Nästa steg

Försök att ändra andra exempel för att träna ETags-eller AccessCondition-objekt.

+ [Sök-dotNet-kom igång på GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Den här lagrings platsen innehåller projektet "DotNetEtagsExplainer".

+ [Azure-Search-dotNet-samples på GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples) innehåller fler C#-exempel.

## <a name="see-also"></a>Se även

+ [Vanliga HTTP-förfrågningar och svarshuvuden](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
+ [HTTP-statuskoder](/rest/api/searchservice/http-status-codes)
+ [Index åtgärder (REST API)](/rest/api/searchservice/index-operations)