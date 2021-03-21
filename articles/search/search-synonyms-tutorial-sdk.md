---
title: C#-exempel på synonymer
titleSuffix: Azure Cognitive Search
description: I det här C#-exemplet lär du dig hur du lägger till synonym funktionen i ett index i Azure Kognitiv sökning. En synonymkarta är en lista med motsvarande termer. Fält med stöd för synonymer expanderar frågor till att innehålla det användarangivna uttrycket och alla relaterade synonymer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704664"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Exempel: Lägg till synonymer för Azure Kognitiv sökning i C #

Med synonymer kan du utöka en fråga genom att matcha mot termer som anses betyda samma sak som den angivna söktermen. Du kanske vill att söktermen ”bil” även ska matcha dokument som innehåller termen ”fordon”. 

I Azure Kognitiv sökning definieras synonymer i en *synonym mappning* genom *mappnings regler* som associerar motsvarande villkor. I det här exemplet beskrivs viktiga steg för att lägga till och använda synonymer med ett befintligt index.

I det här exemplet får du lära dig att:

> [!div class="checklist"]
> * Skapa en synonym mappning med [klassen SynonymMap](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Ange [egenskapen SynonymMapsName](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) för fält som ska ha stöd för frågans expansion via synonymer.

Du kan fråga ett synonymt-aktiverat fält som vanligt. Det krävs ingen ytterligare frågesyntax som krävs för att komma åt synonymer.

Du kan skapa flera synonymmappningar, publicera dem som en resurs på tjänstnivå tillgänglig för alla index och sedan referera till den mappning som ska användas på fältnivå. Vid tidpunkten för frågan, förutom att söka i ett index, gör Azure Kognitiv sökning en sökning i en synonym karta, om ett sådant anges i fält som används i frågan.

> [!NOTE]
> Synonymer kan skapas program mässigt, men inte i portalen.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien behöver du följande:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Kognitiv sökning-tjänst](search-create-service-portal.md)
* [Azure.Search.Documents-paket](https://www.nuget.org/packages/Azure.Search.Documents/)

Om du inte är bekant med .NET-klient biblioteket kan du läsa om [hur du använder Azure kognitiv sökning i .net](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Exempelkod

Du hittar den fullständiga käll koden för exempel programmet som används i det här exemplet på [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Översikt

Innan-och-efter-frågor används för att visa värdet för synonymer. I det här exemplet kör ett exempel program frågor och returnerar resultat på ett exempel på "Hotels"-index som är ifyllt med två dokument. Först kör programmet Sök frågor med hjälp av termer och fraser som inte visas i indexet. För det andra aktiverar koden synonym funktionen och sedan återskapar samma frågor, den här tiden returnerar resultat baserat på matchningar i synonym kartan. 

I koden nedan kan du se flödet som en helhet.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>”Före”-frågor

Kör sökningar med termerna ”five star”, ”internet” och ”economy AND hotel” i `RunQueriesWithNonExistentTermsInIndex`.

Fras frågor, till exempel "fem stjärnor", måste omges av citat tecken, och kan också behöva escape-tecken beroende på din klient.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Inget av de två indexerade dokumenten innehåller villkoren, så vi får följande utdata från det första `RunQueriesWithNonExistentTermsInIndex` :  **inget dokument matchade**.

## <a name="enable-synonyms"></a>Aktivera synonymer

Efter att frågorna har körts, aktiverar exempel koden synonymer. Att aktivera synonymer är en tvåstegsprocess. Börja med att definiera och överföra synonym regler. Sedan konfigurerar du fälten genom att använda dem. Processen beskrivs i `UploadSynonyms` och `EnableSynonymsInHotelsIndex`.

1. Lägg till en synonymmappning i söktjänsten. I `UploadSynonyms` definierar vi fyra regler i synonymmappningen desc-synonymmap och laddar upp mappningen till tjänsten.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Konfigurera sökbara fält så att synonymmappningen används i indexdefinitionen. I `AddSynonymMapsToFields` aktiverar vi synonymer för de två fälten `category` och `tags` genom att ange namnet på synonymmappningen för egenskapen `SynonymMapNames`.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   När du lägger till en synonymmappning behöver du inte skapa indexet på nytt. Du kan lägga till en synonymmappning för tjänsten och sedan göra tillägg till befintliga fältdefinitioner i dina index så att den nya synonymmappningen används. Indexet tillgänglighet påverkas inte om du lägger till nya attribut. Samma sak gäller om du inaktiverar synonymer för ett fält. Du kan helt enkelt ange en tom lista för egenskapen `SynonymMapNames`.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>”Efter”-frågor

När synonymmappningen har laddats upp och indexet har uppdaterats så att mappningen används ger det andra anropet av `RunQueriesWithNonExistentTermsInIndex` följande resultat:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

Den första frågan hittar dokument via regeln `five star=>luxury`. Den andra frågan utökar sökningen med `internet,wifi` och den tredje med både `hotel, motel` och `economy,inexpensive=>budget`, och därför hittas de matchande dokumenten.

Du får en helt ny sökupplevelse när du lägger till synonymer. I det här exemplet kunde inte de ursprungliga frågorna returnera meningsfulla resultat trots att dokumenten i vårt index var relevanta. Genom att aktivera synonymer kan vi expandera ett index med termer som används ofta utan att behöva ändra underliggande data i indexet.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa efter ett exempel är genom att ta bort resurs gruppen som innehåller Azure Kognitiv sökning-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. I portalen finns resurs gruppens namn på sidan Översikt i Azure Kognitiv sökning-tjänsten.

## <a name="next-steps"></a>Nästa steg

I det här exemplet demonstreras funktionen synonymer i C#-kod för att skapa och publicera mappnings regler och sedan anropa synonym kartan på en fråga. Mer information finns i referensdokumentationen för [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) och [REST-API:t](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Så här använder du synonymer i Azure Kognitiv sökning](search-synonyms.md)