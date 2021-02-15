---
title: Översikt över indexeraren
titleSuffix: Azure Cognitive Search
description: Crawla Azure SQL Database, SQL-hanterad instans, Azure Cosmos DB eller Azure-lagring för att extrahera sökbara data och fylla ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: a274e96defa8b6b74c046923d87f198029399dd4
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098103"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexerare i Azure Cognitive Search

En *indexerare* i Azure kognitiv sökning är en Crawler som extraherar sökbara text och metadata från en extern Azure-datakälla och fyller ett sökindex med fält-till-fält-mappningar mellan källdata och ditt index. Den här metoden kallas ibland för "pull-modell" eftersom tjänsten hämtar data i utan att du behöver skriva kod som lägger till data i ett index. Indexerare styr även [AI-anrikningen](cognitive-search-concept-intro.md) i kognitiv sökning, och integrerar extern bearbetning av innehåll en väg till ett index.

Indexerare är enbart Azure, med enskilda indexerare för [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Table Storage](search-howto-indexing-azure-tables.md) och [Blob Storage](search-howto-indexing-azure-blob-storage.md). När du konfigurerar en indexerare anger du en data källa (ursprung), samt ett index (mål). Flera källor, till exempel Blob Storage, har ytterligare konfigurations egenskaper som är speciella för innehålls typen.

Du kan köra indexerare på begäran eller enligt ett återkommande data uppdaterings schema som körs så ofta som var femte minut. För frekventa uppdateringar krävs en [push-modell](search-what-is-data-import.md) som samtidigt uppdaterar data i både Azure-kognitiv sökning och den externa data källan.

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan använda en indexerare som enda metod för data inmatning eller som en del av en kombination av tekniker som läser in och alternativt omvandlar eller utvärderar innehåll på väg. I följande tabell sammanfattas huvud scenarierna.

| Scenario |Strategi |
|----------|---------|
| Enskild data Källa | Det här mönstret är det enklaste: en data källa är den enda innehålls leverantören för ett sökindex. Från källan identifierar du ett fält som innehåller unika värden som ska fungera som dokument nyckel i Sök indexet. Det unika värdet kommer att användas som en identifierare. Alla andra käll fält mappas implicit eller uttryckligen till motsvarande fält i ett index. </br></br>En viktig sak är att värdet för en dokument nyckel kommer från data källan. En Sök tjänst genererar inte nyckel värden. Vid efterföljande körningar läggs inkommande dokument med nya nycklar till, medan inkommande dokument med befintliga nycklar antingen sammanfogas eller skrivs över, beroende på om index fält är null eller har fyllts i. |
| Flera data källor | Ett index kan ta emot innehåll från flera källor, där varje körning ger nytt innehåll från en annan källa. </br></br>Ett resultat kan vara ett index som får dokument när varje indexerare har körts, med hela dokument som har skapats helt från varje källa. Dokument 1-100 kommer till exempel från Blob Storage, dokument 101-200 från Azure SQL och så vidare. Utmaningen för det här scenariot är att designa ett index schema som fungerar för alla inkommande data och en dokument nyckel struktur som är enhetlig i Sök indexet. Internt kan de värden som unikt identifierar ett dokument metadata_storage_path i en BLOB-behållare och en primär nyckel i en SQL-tabell. Du kan föreställa dig att en eller båda källorna måste ändras för att tillhandahålla nyckel värden i ett gemensamt format, oavsett innehållets ursprung. I det här scenariot bör du förvänta dig att utföra en viss nivå av för bearbetning för att homogenisera data så att de kan hämtas till ett enda index. </br></br>Ett alternativt resultat kan vara Sök efter dokument som delvis fyllts i den första körningen och som sedan fylls i automatiskt genom efterföljande körningar för att hämta värden från andra källor. Fält 1-10 är till exempel från Blob Storage, 11-20 från Azure SQL och så vidare. Utmaningen med det här mönstret ser till att varje indexerings körning är riktad mot samma dokument. Sammanslagning av fält i ett befintligt dokument kräver en matchning på dokument nyckeln. En demonstration av det här scenariot finns i [Självstudier: index från flera data källor](tutorial-multiple-data-sources.md). |
| Flera indexerare | Om du använder flera data källor kan du också behöva flera indexerare om du behöver variera körnings tid parametrarna, schemat eller fält mappningarna. Även om flera indexerare-data käll uppsättningar kan riktas mot samma index, måste du vara noggrann med indexerare körningar som kan skriva över befintliga värden i indexet. Om en andra indexerare-data källa är riktad mot samma dokument och fält kommer alla värden från den första körningen att skrivas över. Fält värden ersätts fullständigt. en indexerare kan inte sammanfoga värden från flera körningar till samma fält.</br></br>Ett annat användnings fall Kognitiv sökning för flera index är [utanför regionen](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Du kan ha kopior av samma Sök index i olika regioner. Om du vill synkronisera innehåll i Sök index kan du få flera indexerare att hämta från samma data källa, där varje indexerare är riktad mot ett annat sökindex.</br></br>[Parallell indexering](search-howto-large-index.md#parallel-indexing) av mycket stora data mängder kräver också en strategi för flera index. Varje indexerare riktar sig till en delmängd av data. |
| Innehålls omvandling | Kognitiv sökning stöder valfria [AI-anriknings](cognitive-search-concept-intro.md) beteenden som lägger till bild analys och naturlig språk bearbetning för att skapa ett nytt sökbart innehåll och en ny struktur. AI-berikning är indexerare driven, via en ansluten [färdigheter](cognitive-search-working-with-skillsets.md). För att utföra AI-berikning behöver indexeraren fortfarande ett index och en Azure-datakälla, men i det här scenariot lägger till färdigheter-bearbetning till indexerare-körning. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

Indexerare söker efter data lager i Azure.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (för hands version)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL-hanterad instans](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server på Azure Virtual Machines](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

Indexerings anslutningar till fjärranslutna data källor kan göras med hjälp av standard Internet anslutningar (offentliga) eller krypterade privata anslutningar när du använder virtuella Azure-nätverk för klient program. Du kan också konfigurera anslutningar för att autentisera med hjälp av en betrodd tjänst identitet. Mer information om säkra anslutningar finns i [bevilja åtkomst via privata slut punkter](search-indexer-securing-resources.md#granting-access-via-private-endpoints) och [ansluta till en data källa med hjälp av en hanterad identitet](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Indexerings steg

Vid en inledande körning kommer en indexerare att läsas i alla data som anges i tabellen eller behållaren när indexet är tomt. Vid efterföljande körningar kan indexeraren vanligt vis identifiera och hämta bara de data som har ändrats. För BLOB-data är ändrings identifieringen automatisk. För andra data källor som Azure SQL eller Cosmos DB måste ändrings identifiering vara aktiverat.

För varje dokument som den tar emot implementerar eller koordinerar en indexerare flera steg, från dokument hämtning till den sista sökmotorn för indexering. Om du vill kan en indexerare också instrumentell vid körning av färdigheter och utdata, förutsatt att en färdigheter har definierats.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexerings steg" border="false":::

### <a name="stage-1-document-cracking"></a>Steg 1: dokument sprickor

Dokument sprickor är processen att öppna filer och extrahera innehåll. Beroende på typen av data källa kommer indexeraren att försöka utföra olika åtgärder för att extrahera potentiellt indexerat innehåll.  

Exempel:  

+ När dokumentet är en post i en [Azure SQL-datakälla](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)extraherar indexeraren varje fält för posten.
+ När dokumentet är en PDF-fil i en [Azure Blob Storage data källa](search-howto-indexing-azure-blob-storage.md)extraherar indexeraren text, bilder och metadata.
+ När dokumentet är en post i en [Cosmos db data källa](search-howto-index-cosmosdb.md)extraherar indexeraren fälten och under fälten från Cosmos DB dokumentet.

### <a name="stage-2-field-mappings"></a>Steg 2: fält mappningar 

En indexerare extraherar text från ett käll fält och skickar det till ett målfält i ett index eller kunskaps lager. När fält namn och typer är klara, är sökvägen avmarkerad. Du kan dock vilja ha olika namn eller typer i utdata, och i så fall måste du berätta för indexeraren hur fältet ska mappas. 

Det här steget inträffar efter dokument sprickor, men före omvandlingar när indexeraren läser från käll dokumenten. När du definierar en [fält mappning](search-indexer-field-mappings.md)skickas värdet för käll fältet som-är till mål fältet utan ändringar. 

### <a name="stage-3-skillset-execution"></a>Steg 3: färdigheter-körning

Färdigheter-körning är ett valfritt steg som anropar inbyggd eller anpassad AI-bearbetning. Du kanske behöver det för OCR (optisk tecken läsning) i form av bild analys om käll informationen är en binär avbildning eller om du behöver språk översättning om innehållet är på olika språk. 

Oavsett transformeringen är färdigheter-körningen där berikning sker. Om en indexerare är en pipeline kan du tänka på en [färdigheter](cognitive-search-defining-skillset.md) som en "pipeline i pipelinen".

### <a name="stage-4-output-field-mappings"></a>Steg 4: mappningar för utgående fält

Om du inkluderar en färdigheter måste du förmodligen inkludera mappningar för utdatakolumner. Utdatan från en färdigheter är egentligen ett träd med information som kallas för ett *berikat dokument*. Med mappningar av utdata fält kan du välja vilka delar av trädet som ska mappas till fält i ditt index. Lär dig hur du [definierar mappningar av utdata-fält](cognitive-search-output-field-mapping.md).

Fält mappningar associerar orda Grant-värden från data källan till mål fälten och anger indexeraren för att koppla de transformerade värdena i det berikade dokumentet till mål fälten i indexet. Till skillnad från fält mappningar, som betraktas som valfria, så behöver du alltid definiera en mappning av utdata fält för alla transformerat innehåll som måste finnas i ett index.

Nästa bild visar en exempel på en [fel söknings session](cognitive-search-debug-session.md) för indexerare: dokument sprickor, fält mappningar, färdigheter körning och fält mappningar för utdata.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="exempel på en felsökningssession" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Grundläggande arbetsflöde

Indexerare kan erbjuda funktioner som är unika för datakällan. I detta avseende varierar vissa aspekter av indexerarna och datakällskonfigurationen kan variera efter indexerartyp. Alla indexerare delar dock samma grundläggande sammansättning och krav. De steg som är gemensamma för alla indexerare beskrivs nedan.

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Indexerare kräver ett *data käll* objekt som innehåller en anslutnings sträng och eventuellt autentiseringsuppgifter. Skapa resursen genom att anropa klassen [skapa data källa (rest)](/rest/api/searchservice/create-data-source) eller [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) .

Datakällor konfigureras och hanteras oberoende av indexerarna som använder dem, vilket innebär att en datakälla kan användas av flera indexerare för att läsa in mer än ett index i taget.

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index

En indexerare automatiserar vissa uppgifter som rör datapåfyllning, men att skapa ett index är vanligtvis inte en av dem. Som krav måste du ha ett fördefinierat index med fält som matchar de i din externa datakälla. Fält måste matcha efter namn och datatyp. Om inte kan du [Definiera fält mappningar](search-indexer-field-mappings.md) för att upprätta associationen. Mer information om att strukturera ett index finns i [skapa ett index (rest)](/rest/api/searchservice/Create-Index) eller [SearchIndex-klass](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Indexerare kan inte generera ett index åt dig, men du kan få hjälp av guiden **Importera data** i portalen. I de flesta fall kan guiden härleda ett indexschema från befintliga metadata i källan, vilket skapar ett preliminärt indexschema som du kan redigera direkt när guiden är aktiv. När indexet har skapats i tjänsten är ytterligare redigeringar i portalen i huvudsak begränsade till tillägg av nya fält. Överväg att använda guiden för att skapa, men inte revidera, ett index. I [steg-för-steg-beskrivningen för portalen](search-get-started-portal.md) kan du få en praktisk genomgång.

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Steg 3: skapa och kör (eller Schemalägg) indexeraren

En indexerare körs när du först [skapar en indexerare](/rest/api/searchservice/Create-Indexer) på Sök tjänsten. Det är bara när du skapar eller kör en indexerare som tar reda på om data källan är tillgänglig eller om färdigheter är giltig. Efter den första körningen kan du köra den på nytt med hjälp av [Kör Indexer](/rest/api/searchservice/run-indexer), eller så kan du [definiera ett återkommande schema](search-howto-schedule-indexers.md). 

Du kan övervaka indexerings status i portalen eller genom att [Hämta indexerings status-API](/rest/api/searchservice/get-indexer-status). Du bör också [köra frågor på indexet](search-query-create.md) för att kontrol lera att resultatet är det du förväntade dig.

## <a name="next-steps"></a>Nästa steg

Nu när du har lanserat, är ett nästa steg att granska indexerare egenskaper och parametrar, schemaläggning och övervakning av indexerare. Alternativt kan du gå tillbaka till listan över [data källor som stöds](#supported-data-sources) för mer information om en speciell källa.

+ [Skapa indexerare](search-howto-create-indexers.md)
+ [Återställa och köra indexerare](search-howto-run-reset-indexers.md)
+ [Schema för indexerare](search-howto-schedule-indexers.md)
+ [Definiera fält mappningar](search-indexer-field-mappings.md)
+ [Övervaka indexerings status](search-howto-monitor-indexers.md)