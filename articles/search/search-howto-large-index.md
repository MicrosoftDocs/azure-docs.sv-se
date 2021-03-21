---
title: Indexera stor data uppsättning med inbyggda indexerare
titleSuffix: Azure Cognitive Search
description: Strategier för stor data indexering eller beräknings intensiv indexering via batch-läge, omkällor och tekniker för schemalagd, parallell och distribuerad indexering.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: b4f54aff78526ba52e56ed9f4cf1feddf40fa69b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358400"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Så här indexerar du stora data uppsättningar i Azure Kognitiv sökning

Azure Kognitiv sökning stöder [två grundläggande metoder](search-what-is-data-import.md) för att importera data till ett sökindex *: att* skicka data till indexet program mässigt eller att peka en [Azure kognitiv sökning-indexerare](search-indexer-overview.md) på en data källa som stöds för att *Hämta* data.

Allteftersom data volymer växer eller bearbetningen behöver ändras kanske du upptäcker att enkla eller standard index strategier inte längre är praktiska. För Azure Kognitiv sökning finns det flera metoder för att få större data mängder, från hur du strukturerar en begäran om data uppladdning, för att använda en produktspecifik indexerare för schemalagda och distribuerade arbets belastningar.

Samma tekniker gäller även för långvariga processer. I synnerhet är de steg som beskrivs i [Parallel index](#parallel-indexing) användbara för beräknings intensiva indexering, till exempel bild analys eller naturligt språk bearbetning i en [AI-pipeline](cognitive-search-concept-intro.md).

I följande avsnitt beskrivs tekniker för att indexera stora mängder data med både push-API och indexerare.

## <a name="use-the-push-api"></a>Använd push-API: et

När du skickar data till ett index med hjälp av [Lägg till dokument REST API](/rest/api/searchservice/addupdate-or-delete-documents) eller [IndexDocuments-metoden](/dotnet/api/azure.search.documents.searchclient.indexdocuments)finns det flera viktiga överväganden som påverkar indexerings hastigheten. Dessa faktorer beskrivs i avsnittet nedan och sträcker sig från att ställa in tjänst kapacitet till kod optimeringar.

Mer information och kod exempel som illustrerar indexering av push-modell finns i [Självstudier: optimera indexerings hastigheter](tutorial-optimize-indexing-push-api.md).

### <a name="capacity-of-your-service"></a>Tjänstens kapacitet

Som ett första steg granskar du egenskaperna och [gränserna](search-limits-quotas-capacity.md) för den nivå där du etablerade tjänsten. En av de viktigaste skillnaderna mellan pris nivåerna är storlek och hastighet för partitioner, som har direkt påverkan på indexerings hastigheten. Om du etablerade din Sök tjänst på en nivå som inte är tillräcklig för arbets belastningen kan det vara den enklaste och mest effektiva lösningen för att öka indexerings data flödet genom att uppgradera till en ny nivå.

När du är nöjd med nivån kan nästa steg vara att öka antalet partitioner. Resursallokeringen kan justeras ned efter en inledande indexerings körning för att minska den totala kostnaden för att köra tjänsten.

> [!NOTE]
> Att lägga till ytterligare repliker kan också öka indexerings hastigheten men det är inte garanterat. Å andra sidan kommer ytterligare repliker att öka den volym som Sök tjänsten kan hantera. Repliker är också en viktig komponent för att få ett [service avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
>
> Innan du lägger till partition/repliker eller uppgraderar till en högre nivå bör du ta hänsyn till den monetära kostnaden och tilldelnings tiden. Att lägga till partitioner kan öka indexerings hastigheten avsevärt men att lägga till/ta bort dem kan ta var som helst från 15 minuter till flera timmar. Mer information finns i dokumentationen om justering av [kapacitet](search-capacity-planning.md).
>

### <a name="review-index-schema"></a>Granska index schema

Schemat för ditt index spelar en viktig roll i indexerings data. De fler fälten som du har och fler egenskaper som du anger (till exempel *sökbar*, *fasettable* eller *filtrerings* bara) bidrar till att öka indexerings tiden. I allmänhet bör du bara skapa och ange de fält som du faktiskt behöver i ett sökindex.

> [!NOTE]
> Undvik att lägga till icke-frågedata till ett index för att hålla nere dokument storleken. Bilder och andra binära data är inte direkt sökbara och bör inte lagras i indexet. Om du vill integrera data som inte går att köra i Sök resultaten bör du definiera ett fält som inte går att söka i som lagrar en URL-referens till resursen.

### <a name="check-the-batch-size"></a>Kontrol lera batchstorleken

En av de enklaste mekanismerna för att indexera en större data uppsättning är att skicka flera dokument eller poster i en och samma begäran. Så länge hela nytto lasten är under 16 MB kan en begäran hantera upp till 1000 dokument i en Mass överförings åtgärd. Dessa begränsningar gäller oavsett om du använder [Lägg till dokument REST API](/rest/api/searchservice/addupdate-or-delete-documents) eller [metoden INDEXDOCUMENTS](/dotnet/api/azure.search.documents.searchclient.indexdocuments) i .NET SDK. För båda API: erna kommer du att paketera 1000-dokument i bröd texten för varje begäran.

Att använda batchar för att indexera dokument kommer att förbättra indexerings prestanda avsevärt. Att fastställa den optimala batchstorleken för dina data är en viktig komponent i optimering av indexerings hastigheter. De två primära faktorerna som påverkar den optimala batchstorleken är:

+ Schemat för ditt index
+ Storleken på dina data

Eftersom den optimala batchstorleken är beroende av ditt index och dina data, är det bästa sättet att testa olika batch-storlekar för att avgöra vilka resultat som finns i de snabbaste indexerings hastigheterna för ditt scenario. Den här [självstudien](tutorial-optimize-indexing-push-api.md) innehåller exempel kod för att testa batch-storlekar med hjälp av .NET SDK. 

### <a name="number-of-threadsworkers"></a>Antal trådar/arbetare

För att dra full nytta av Azures Kognitiv söknings indexerings hastigheter måste du förmodligen använda flera trådar för att skicka begär Anden om batch-indexering samtidigt till tjänsten.  

Det optimala antalet trådar avgörs av:

+ Nivån för din Sök tjänst
+ Antalet partitioner
+ Storleken på batcharna
+ Schemat för ditt index

Du kan ändra det här exemplet och testa med olika antal trådar för att fastställa det optimala antalet trådar för ditt scenario. Men så länge du har flera trådar som körs samtidigt, bör du kunna utnyttja de flesta av effektivitets vinsterna. 

> [!NOTE]
> När du ökar nivån på din Sök tjänst eller ökar partitionerna bör du också öka antalet samtidiga trådar.

När du ramperar de begär Anden som når Sök tjänsten kan du stöta på [HTTP-statuskod](/rest/api/searchservice/http-status-codes) som indikerar att begäran inte lyckades fullständigt. Under indexeringen är två vanliga HTTP-status koder:

+ **503 tjänsten är inte tillgänglig** – det här felet innebär att systemet är hårt belastat och att din begäran inte kan bearbetas just nu.
+ **207 multi-status** – det här felet innebär att vissa dokument lyckades, men att minst en misslyckades.

### <a name="retry-strategy"></a>Återförsöksstrategi

Om ett fel inträffar ska förfrågningarna göras om med en [exponentiell backoff-strategi för återförsök](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Azure Kognitiv söknings .NET SDK försöker automatiskt 503s och andra misslyckade förfrågningar, men du måste implementera din egen logik för att försöka igen 207s. Verktyg med öppen källkod, till exempel [Polly](https://github.com/App-vNext/Polly) , kan också användas för att implementera en strategi för återförsök.

### <a name="network-data-transfer-speeds"></a>Hastighet för nätverks data överföring

Överföringshastigheten för nätverks data kan vara en begränsnings faktor vid indexering av data. Indexering av data i Azure-miljön är ett enkelt sätt att påskynda indexeringen.

## <a name="use-indexers-pull-api"></a>Använda indexerare (pull-API)

[Indexerare](search-indexer-overview.md) används för att crawla Azure-datakällor som stöds för sökbart innehåll. Även om det är särskilt avsett för storskalig indexering är flera indexerings funktioner särskilt användbara för att kunna använda större data uppsättningar:

+ Med Schemaläggaren kan du regelbundet indexera index med jämna mellanrum så att du kan sprida ut det över tid.
+ Schemalagd indexering kan återupptas vid den senast kända stopp punkten. Om en data källa inte är fullständigt crawlad inom ett 24-timmarsformat, kommer indexeraren att återuppta indexeringen på dag två på var den slutade.
+ Att partitionera data i mindre enskilda data källor möjliggör parallell bearbetning. Du kan dela upp källdata i mindre komponenter, t. ex. i flera behållare i Azure Blob Storage, och sedan skapa motsvarande flera [data käll objekt](/rest/api/searchservice/create-data-source) i Azure kognitiv sökning som kan indexeras parallellt.

> [!NOTE]
> Indexerare är data källa-/regionsspecifika, så användning av en indexerare-metod är endast livskraftig för valda data källor på Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="check-the-batchsize-argument-on-create-indexer"></a>Kontrol lera argumentet batchSize i skapa indexerare

Som med push-API: et kan du med indexerare konfigurera antalet objekt per batch. För indexerare som baseras på [skapa indexerare REST API](/rest/api/searchservice/Create-Indexer)kan du ange argumentet för att `batchSize` Anpassa den här inställningen för att bättre matcha egenskaperna för dina data. 

Standardvärden för batch-storlek är data källa. Azure SQL Database och Azure Cosmos DB har en standard grupp storlek på 1000. Azure Blob-indexering ställer däremot in batchstorleken vid 10 dokument i redovisningen av den större genomsnittliga dokument storleken. 

### <a name="scheduled-indexing"></a>Schemalagd indexering

Schemaläggning av indexering är en viktig mekanism för att bearbeta stora data mängder, samt långsamma processer som bild analys i en kognitiv Sök pipeline. Indexerings bearbetningen fungerar i 24-timmarsformat. Om bearbetningen inte kan slutföras inom 24 timmar kan beteendet för indexerings schemaläggningen fungera på din fördel. 

Enligt design börjar schemalagd indexering i bestämda intervall, med ett jobb som normalt slutförs innan nästa schemalagda intervall återupptas. Men om bearbetningen inte slutförs inom intervallet stoppas indexeraren (på grund av att tids gränsen uppnåddes). Vid nästa intervall återupptas bearbetningen där det senast slutade, med systemet som håller koll på var det inträffar. 

I praktiska villkor kan du använda ett 24-timmarsformat för index belastningar som sträcker sig över flera dagar. När indexeringen återupptas för nästa 24-timmars cykel, startas den om i det senast fungerande dokumentet. På så sätt kan en indexerare arbeta på ett sätt genom ett dokument efter släpning under en serie dagar tills alla obearbetade dokument har bearbetats. Mer information om hur du ställer in scheman i allmänhet finns i [skapa indexerare REST API](/rest/api/searchservice/Create-Indexer) eller se [hur du schemalägger indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Parallell indexering

En parallell indexerings strategi baseras på indexering av flera data källor i dem samtidigt, där varje data käll definition anger en delmängd av data. 

För icke-rutinmässigt, beräknings intensiva indexerings krav, till exempel OCR på skannade dokument i en kognitiv Sök pipeline, bild analys eller naturligt språk bearbetning – en parallell indexerings strategi är ofta den rätta metoden för att slutföra en långvarig process på kortast möjliga tid. Om du kan eliminera eller minska förfrågningar om förfrågningar är parallell indexering av en tjänst som inte samtidigt hanterar frågor det bästa strategi alternativet för att arbeta via en stor del av långsam bearbetning av innehåll. 

Parallell bearbetning har följande element:

+ Dela upp käll data mellan flera behållare eller flera virtuella mappar i samma behållare. 
+ Mappa varje mini-data till en egen [data källa](/rest/api/searchservice/create-data-source), kopplade till sin egen [indexerare](/rest/api/searchservice/create-indexer).
+ För kognitiv sökning refererar du till samma [färdigheter](/rest/api/searchservice/create-skillset) i varje indexare-definition.
+ Skriv till samma mål Sök index. 
+ Schemalägg alla indexerare att köras samtidigt.

> [!NOTE]
> I Azure Kognitiv sökning kan du inte tilldela enskilda repliker eller partitioner för indexering eller bearbetning av frågor. Systemet fastställer hur resurser används. För att förstå påverkan på frågeresultat kan du prova parallell indexering i en test miljö innan du rullar den till produktions miljön.  

### <a name="how-to-configure-parallel-indexing"></a>Så här konfigurerar du parallell indexering

För indexerare är bearbetnings kapaciteten löst baserat på ett under system för indexerare för varje tjänst enhet (SU) som används av Sök tjänsten. Det går att använda flera samtidiga indexerare i Azure Kognitiv sökning-tjänster som är etablerade på Basic-eller standard-nivåerna med minst två repliker. 

1. På sidan **Översikt** för search service instrument panel på [Azure Portal](https://portal.azure.com)kontrollerar du **pris nivån** för att bekräfta att den kan hantera parallell indexering. Både Basic-och standard-nivån erbjuder flera repliker.

2. Du kan köra så många indexerare parallellt som antalet Sök enheter i din tjänst. I **Inställningar**  >  **skala**, [öka repliker](search-capacity-planning.md) eller partitioner för parallell bearbetning: en ytterligare replik eller partition för varje Indexer-arbetsbelastning. Lämna ett tillräckligt antal för den befintliga frågesträngen. Att offra frågor mot arbets belastningar för indexering är inte ett utmärkt kompromiss.

3. Distribuera data till flera behållare på en nivå som Azure Kognitiv sökning indexerare kan komma åt. Detta kan vara flera tabeller i Azure SQL Database, flera behållare i Azure Blob Storage eller flera samlingar. Definiera ett data käll objekt för varje tabell eller behållare.

4. Skapa och schemalägga flera indexerare som ska köras parallellt:

   + Anta en tjänst med sex repliker. Konfigurera sex indexerare, var och en mappad till en data källa som innehåller en-sjätte data uppsättning för en 6-vägs uppdelning av hela data uppsättningen. 

   + Peka varje indexerare till samma index. För kognitiva Sök arbets belastningar kan du peka varje indexerare till samma färdigheter.

   + I varje indexare-definition schemalägger du samma kör tids körnings mönster. Till exempel `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` skapas ett schema på 2018-05-15 på alla indexerare, som körs med åtta timmars intervall.

Vid den schemalagda tiden börjar alla indexerare köra, läsa in data, tillämpa berikar (om du har konfigurerat en kognitiv Sök pipeline) och skriver till indexet. Azure Kognitiv sökning låser inte indexet för uppdateringar. Samtidiga skrivningar hanteras med ett nytt försök om en viss skrivning inte lyckas vid första försöket.

> [!Note]
> När du ökar repliker bör du överväga att öka antalet partitioner om index storleken projiceras för att öka markant. Partitioner lagrar segment av indexerat innehåll. Ju fler partitioner du har, desto mindre är sektorn som var och en måste lagra.

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexering i portalen](search-import-data-portal.md)
+ [Azure SQL Database indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Kognitiv sökning](search-security-overview.md)