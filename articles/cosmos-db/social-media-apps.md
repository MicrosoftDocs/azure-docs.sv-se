---
title: 'Azure Cosmos DB design mönster: appar för sociala medier'
description: Lär dig mer om ett design mönster för sociala nätverk genom att dra nytta av flexibiliteten i lagringen för Azure Cosmos DB och andra Azure-tjänster.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 329c4b40f11b36de80581d4a1396813bc8de5c73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010324"
---
# <a name="going-social-with-azure-cosmos-db"></a>Bli social med Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Boende i ett massivt sammankopplat samhälle innebär att du vid en viss tidpunkt kommer att bli en del av ett **socialt nätverk**. Du använder sociala nätverk för att hålla kontakt med vänner, kollegor, familj eller ibland att dela din passion med personer som har vanliga intressen.

Som ingenjörer eller utvecklare kan du ha funderat på hur dessa nätverk lagrar och sammankopplar dina data. Eller så kanske du har gjort en uppgift för att skapa eller skapa ett nytt socialt nätverk för en bestämd nischmarknader-marknad. Det är när den viktiga frågan uppstår: Hur lagras alla dessa data?

Anta att du skapar ett nytt och blankt socialt nätverk där användarna kan publicera artiklar med relaterade medier, t. ex. bilder, videor eller till och med musik. Användare kan kommentera inlägg och ge poäng för klassificering. Det kommer att finnas ett flöde av inlägg som användarna kan se och interagera med på huvud sidan för landning av webbplatser. Den här metoden går inte att göra komplext i första hand, men för enkelhetens skull ska vi stanna där. (Du kan gå in i anpassade användar flöden som påverkas av relationer, men det går bortom målet för den här artikeln.)

Hur lagrar du dessa data och var?

Du kan ha erfarenhet av SQL-databaser eller ha ett begrepp för [Relations data modellering](https://en.wikipedia.org/wiki/Relational_model). Du kan börja rita något på följande sätt:

:::image type="content" source="./media/social-media-apps/social-media-apps-sql.png" alt-text="Diagram som illustrerar en relativ Relations modell" border="false":::

En perfekt normaliserad och ganska data struktur... som inte skalar.

Jag får inte fel, jag har arbetat med SQL-databaser hela min livs längd. De är fantastiska, men precis som varje mönster, praxis och program varu plattform är det inte perfekt för alla scenarier.

Varför är inte SQL det bästa valet i det här scenariot? Nu ska vi titta på strukturen för ett enda inlägg. Om jag ville visa inlägget på en webbplats eller ett program måste jag göra en fråga med... genom att gå med i åtta tabeller (!) kan du bara visa ett enda inlägg. Nu kan du Visa en ström med inlägg som laddas dynamiskt och visas på skärmen, och du kan se var jag ska.

Du kan använda en enorma SQL-instans med tillräckligt mycket kraft för att lösa tusentals frågor med många kopplingar för att hantera ditt innehåll. Men varför skulle du, när en enklare lösning finns?

## <a name="the-nosql-road"></a>NoSQL-vägen

Den här artikeln hjälper dig att utforma din sociala plattforms data med Azures NoSQL databas [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) kostnads effektivt. Du får också information om hur du använder andra Azure Cosmos DB funktioner som [Gremlin-API: et](../cosmos-db/graph-introduction.md). Genom att använda en [NoSQL](https://en.wikipedia.org/wiki/NoSQL) Metod, lagra data, i JSON-format och tillämpa [avnormalisering](https://en.wikipedia.org/wiki/Denormalization), kan det tidigare komplexa inlägget omvandlas till ett enda [dokument](https://en.wikipedia.org/wiki/Document-oriented_database):

```json
{
    "id":"ew12-res2-234e-544f",
    "title":"post title",
    "date":"2016-01-01",
    "body":"this is an awesome post stored on NoSQL",
    "createdBy":User,
    "images":["https://myfirstimage.png","https://mysecondimage.png"],
    "videos":[
        {"url":"https://myfirstvideo.mp4", "title":"The first video"},
        {"url":"https://mysecondvideo.mp4", "title":"The second video"}
    ],
    "audios":[
        {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
        {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
    ]
}
```

Och kan vara med en enda fråga och utan kopplingar. Den här frågan är mycket enkel och enkel, och budget kräver färre resurser för att uppnå ett bättre resultat.

Azure Cosmos DB ser till att alla egenskaper indexeras med automatisk indexering. Automatisk indexering kan till och med [anpassas](index-policy.md). Med den schema fria metoden kan vi lagra dokument med olika och dynamiska strukturer. Kanske imorgon du vill att inlägg ska ha en lista över kategorier eller hashtagg som är kopplade till dem? Cosmos DB hanterar de nya dokumenten med de tillagda attributen utan extra arbete som krävs av oss.

Kommentarer på ett inlägg kan behandlas som andra inlägg med en överordnad egenskap. (Den här övningen fören klar objekt mappningen.)

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":User2,
    "parent":"ew12-res2-234e-544f"
}

{
    "id":"asd2-fee4-23gc-jh67",
    "title":"Ditto!",
    "date":"2016-01-03",
    "createdBy":User3,
    "parent":"ew12-res2-234e-544f"
}
```

Och alla sociala interaktioner kan lagras på ett separat objekt som räknare:

```json
{
    "id":"dfe3-thf5-232s-dse4",
    "post":"ew12-res2-234e-544f",
    "comments":2,
    "likes":10,
    "points":200
}
```

Att skapa feeds är bara en fråga om att skapa dokument som innehåller en lista med post-ID: n med en bestämd prioritetsordning:

```json
[
    {"relevance":9, "post":"ew12-res2-234e-544f"},
    {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
    {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
]
```

Du kan ha en "senaste" ström med inlägg sorterade efter skapande datum. Eller så kan du ha en "hetaste" ström med dessa inlägg med fler gillar under de senaste 24 timmarna. Du kan till och med implementera en anpassad data ström för varje användare baserat på logik som följare och intressen. Det skulle fortfarande vara en lista över inlägg. Det är en fråga om hur du skapar listorna, men läsnings prestandan förblir förhindrad. När du har skaffat en av de här listorna skickar du en enskild fråga till Cosmos DB med hjälp av [nyckelordet i](sql-query-keywords.md#in) för att hämta sidor med inlägg i taget.

Feed-strömmarna kan skapas med hjälp av [Azure App tjänsters](https://azure.microsoft.com/services/app-service/) bakgrunds processer: [WebJobs](../app-service/webjobs-create.md). När ett inlägg har skapats kan bakgrunds bearbetningen utlösas med hjälp av [Azure Storage](https://azure.microsoft.com/services/storage/) [köer](../storage/queues/storage-dotnet-how-to-use-queues.md) och WebJobs som utlöses med hjälp av [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)och implementerar post spridningen i strömmar baserat på din egen anpassade logik.

Punkter och gilla över ett inlägg kan bearbetas på ett uppskjutet sätt med samma metod för att skapa en konsekvent miljö.

Följare är trickier. Cosmos DB har en gräns för dokument storlek och läsning/skrivning av stora dokument kan påverka skalbarheten för ditt program. Så du kan tänka på att lagra följare som ett dokument med den här strukturen:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "followersOf": "dse4-qwe2-ert4-aad2",
    "followers":[
        "ewr5-232d-tyrg-iuo2",
        "qejh-2345-sdf1-ytg5",
        //...
        "uie0-4tyg-3456-rwjh"
    ]
}
```

Den här strukturen kan fungera för en användare med några tusentals följare. Om vissa kändis ansluts till rangordningarna kommer den här metoden att leda till en stor dokument storlek, och det kan slutligen gå över dokumentets storleks begränsning.

För att lösa det här problemet kan du använda ett blandat tillvägagångs sätt. Som en del av dokumentet med användar statistik kan du lagra antalet följare:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "user": "dse4-qwe2-ert4-aad2",
    "followers":55230,
    "totalPosts":452,
    "totalPoints":11342
}
```

Du kan lagra det faktiska diagrammet över följare med hjälp av Azure Cosmos DB [GREMLIN API](../cosmos-db/graph-introduction.md) för att skapa [hörn](http://mathworld.wolfram.com/GraphVertex.html) för varje användare och [kanter](http://mathworld.wolfram.com/GraphEdge.html) som bevarar relationerna "A-följer-B". Med Gremlin-API: et kan du hämta följare av en viss användare och skapa mer komplexa frågor för att föreslå personer gemensamt. Om du lägger till i grafen för de innehålls kategorier som människor gillar eller åtnjuter, kan du börja väv-upplevelser som innehåller Smart innehålls identifiering, föreslå innehåll som de personer som du följer eller hitta personer som du kanske har mycket gemensamt med.

Användar statistik dokumentet kan fortfarande användas för att skapa kort i för hands versionerna användar gränssnitt eller snabb profil.

## <a name="the-ladder-pattern-and-data-duplication"></a>Mönstret "stega" och data duplicering

Som du kanske har lagt märke till i JSON-dokumentet som hänvisar till ett inlägg finns det många förekomster av en användare. Och om du har gissat dig, innebär dessa dubbletter att den information som beskriver en användare, med denna denormalisering, kan finnas på mer än en plats.

Om du vill tillåta snabbare frågor kommer du att ådra sig data duplicering. Problemet med den här sidan är att om en användares data ändras vid en viss åtgärd, måste du hitta alla aktiviteter som användaren någonsin gjorde och uppdatera dem. Är du inte opraktiskt, rätt?

Du kommer att lösa det genom att identifiera de viktiga attributen för en användare som du visar i ditt program för varje aktivitet. Om du visuellt visar ett inlägg i programmet och bara visar skaparen: s namn och bild, varför lagrar alla användares data i attributet "createdBy"? Om du för varje kommentar bara visar användarens bild behöver du faktiskt inte resten av användarens information. Det är där något som jag kallar "steg mönster" blir inblandat.

Vi tar med användar information som exempel:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "address":"742 Evergreen Terrace",
    "birthday":"1983-05-07",
    "email":"john@doe.com",
    "twitterHandle":"\@john",
    "username":"johndoe",
    "password":"some_encrypted_phrase",
    "totalPoints":100,
    "totalPosts":24
}
```

Genom att titta på den här informationen kan du snabbt identifiera vilken som är viktig och inte, vilket innebär att du skapar en "stege":

:::image type="content" source="./media/social-media-apps/social-media-apps-ladder.png" alt-text="Diagram över ett steg-mönster" border="false":::

Det minsta steget kallas för en UserChunk, den minsta delen av information som identifierar en användare och den används för dataduplicering. Genom att minska den duplicerade data storleken till endast den information som du kommer att "Visa" kan du minska risken för enorma uppdateringar.

Det mittersta steget kallas för användaren. Det är fullständiga data som kommer att användas på de flesta prestanda beroende frågor på Cosmos DB, mest använda och kritiska. Den innehåller den information som representeras av en UserChunk.

Det största är den utökade användaren. Den innehåller viktig användar information och andra data som inte behöver läsas snabbt eller som har en eventuell användning, t. ex. inloggnings processen. Dessa data kan lagras utanför Cosmos DB i Azure SQL Database eller Azure Storage tabeller.

Varför skulle du dela användaren och till och med lagra den här informationen på olika platser? På grund av en prestanda punkt visar de större dokumenten costlier frågorna. Se till att dokumenten är tunna, med rätt information för att göra alla dina prestanda beroende frågor för ditt sociala nätverk. Lagra den andra extra informationen för andra scenarier som fullständig profil redigering, inloggningar och data utvinning för användnings analys och Big data initiativ. Du bryr mig inte om data insamlingen för Data utvinning är långsammare, eftersom den körs på Azure SQL Database. Du har problem med att dina användare har en snabb och smidig upplevelse. En användare som lagras på Cosmos DB skulle se ut som den här koden:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "username":"johndoe"
    "email":"john@doe.com",
    "twitterHandle":"\@john"
}
```

Och ett inlägg skulle se ut så här:

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":{
        "id":"dse4-qwe2-ert4-aad2",
        "username":"johndoe"
    }
}
```

När en redigering uppstår där ett segment-attribut påverkas kan du enkelt hitta de berörda dokumenten. Använd bara frågor som pekar på de indexerade attributen, till exempel `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"` , och sedan uppdaterar segmenten.

## <a name="the-search-box"></a>Rutan Sök

Användarna kommer att generera, som tur är, mycket innehåll. Och du bör kunna göra det möjligt att söka efter och hitta innehåll som kanske inte finns direkt i deras innehålls strömmar, kanske på grund av att du inte följer de här skapare eller om du kanske bara försöker hitta det gamla inlägget du sex månader sedan.

Eftersom du använder Azure Cosmos DB kan du enkelt implementera en sökmotor med [Azure kognitiv sökning](https://azure.microsoft.com/services/search/) på några minuter utan att ange någon annan kod än Sök processen och användar gränssnittet.

Varför är den här processen så enkel?

Azure Kognitiv sökning implementerar vad de anropar [indexerare](/rest/api/searchservice/Indexer-operations), bakgrunds processer som hookar i dina data lager och lägger till, uppdaterar eller tar bort objekt i indexen. De stöder en [Azure SQL Database indexerare](/archive/blogs/kaevans/indexing-azure-sql-database-with-azure-search), [Azure blobs-indexerare](../search/search-howto-indexing-azure-blob-storage.md) och Thankfully [Azure Cosmos db indexerare](../search/search-howto-index-cosmosdb.md). Över gången till information från Cosmos DB till Azure Kognitiv sökning är enkelt. Båda teknikerna lagrar information i JSON-format, så du behöver bara [skapa ditt index](../search/search-what-is-an-index.md) och mappa attributen från dina dokument som du vill indexera. Klart! Beroende på storleken på dina data kommer allt innehåll att vara tillgängligt för att kunna sökas på några minuter av den bästa Sök-som-tjänst-lösningen i moln infrastrukturen.

Om du vill ha mer information om Azure Kognitiv sökning kan du gå [till Hitchhikers guide för att söka](/archive/blogs/mvpawardprogram/a-hitchhikers-guide-to-search).

## <a name="the-underlying-knowledge"></a>Den underliggande kunskapen

När du har lagrat allt det här innehållet som växer och växer varje dag kan du tänka på följande: Vad kan jag göra med all den här data strömmen från mina användare?

Svaret är enkelt: du kan använda det för att arbeta och lära dig av det.

Men vad kan du lära dig? Några enkla exempel är [sentiment-analys](https://en.wikipedia.org/wiki/Sentiment_analysis), innehålls rekommendationer baserat på en användares preferenser eller till och med en automatiserad Content moderator som ser till att det innehåll som publiceras av ditt sociala nätverk är säkert för familjen.

Nu när jag har fått en anslutning kommer du förmodligen att behöva vissa doktors i matematik vetenskap för att extrahera dessa mönster och information från enkla databaser och filer, men du skulle vara fel.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)en del av [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), är en fullständigt hanterad moln tjänst som gör att du kan skapa arbets flöden med hjälp av algoritmer i ett enkelt dra-och-släpp-gränssnitt, koda dina egna algoritmer i [R](https://en.wikipedia.org/wiki/R_\(programming_language\))eller använda några av de redan skapade och redo att använda API: er, till exempel: [textanalys](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), Content moderator eller [rekommendationer](https://gallery.azure.ai/Solution/Recommendations-Solution).

För att uppnå någon av dessa Machine Learning scenarier kan du använda [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) för att mata in information från olika källor. Du kan också använda [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) för att bearbeta informationen och generera utdata som kan bearbetas av Azure Machine Learning.

Ett annat tillgängligt alternativ är att använda [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) för att analysera användarens innehåll. Du kan inte bara förstå dem bättre (genom att analysera vad de skriver med [API för textanalys](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), men du kan också identifiera oönskat eller mogna innehåll och agera i enlighet med [API för visuellt innehåll](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services innehåller många färdiga lösningar som inte kräver någon typ av Machine Learning kunskap att använda.

## <a name="a-planet-scale-social-experience"></a>En social upplevelse på planet nivå

Det finns en sista, men minst, viktig artikel som jag måste åtgärda: **skalbarhet**. När du utformar en arkitektur bör varje komponent skalas separat. Du kommer att behöva bearbeta mer data, eller så vill du ha en större geografisk täckning. Thankfully för att uppnå båda uppgifterna är en **nyckel färdig upplevelse** med Cosmos dB.

Cosmos DB stöder dynamisk partitionering direkt. Det skapar automatiskt partitioner baserat på en viss **partitionsnyckel**, som definieras som ett attribut i dina dokument. Du måste göra en korrekt partitionsnyckel i design läge. Mer information finns i [partitionering i Azure Cosmos DB](partitioning-overview.md).

För en social erfarenhet måste du justera din partitionerings strategi med hur du frågar och skriver. (Läsningar inom samma partition är till exempel önskvärda och Undvik "heta fläckar" genom att sprida skrivningar på flera partitioner.) Vissa alternativ är: partitioner baserade på en temporal nyckel (dag/månad/vecka), efter innehålls kategori, efter geografiskt område eller per användare. Allt det är verkligen beroende av hur du ska fråga data och visa data i din sociala erfarenhet.

Cosmos DB kommer att köra dina frågor (inklusive [agg regeringar](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) över alla partitioner transparent, så du behöver inte lägga till någon logik när dina data växer.

Med tiden kan du komma att växa i trafik och resursförbrukning (mätt i [ru: er](request-units.md)eller enheter för programbegäran) ökar. Du kommer att läsa och skriva oftare när användar basen växer. Användar basen kommer att börja skapa och läsa mer innehåll. Det är därför viktigt att kunna **skala ditt data flöde** . Det är enkelt att öka din ru: er. Du kan göra det med några klick på Azure Portal eller genom [att utfärda kommandon via API: et](/rest/api/cosmos-db/replace-an-offer).

:::image type="content" source="./media/social-media-apps/social-media-apps-scaling.png" alt-text="Skala upp och definiera en partitionsnyckel":::

Vad händer om saker fortfarande kommer att bli bättre? Anta att användare från en annan region, ett land eller ett kontinents meddelande till din plattform och börjar använda den. Vad är en fantastisk överraskning!

Men vänta! Du inser snart att deras upplevelse med din plattform inte är optimal. De är så långt bort från din operativa region att svars tiden är Terrible. Det är självklart att du inte vill avsluta. Om det bara fanns ett enkelt sätt att **Utöka din globala räckvidd**? Det finns!

Med Cosmos DB kan du [Replikera dina data globalt](../cosmos-db/tutorial-global-distribution-sql-api.md) och transparent med ett par klick och automatiskt välja bland de tillgängliga regionerna från din [klient kod](../cosmos-db/tutorial-global-distribution-sql-api.md). Den här processen innebär också att du kan ha [flera områden för växling vid fel](high-availability.md).

När du replikerar dina data globalt måste du se till att dina klienter kan dra nytta av den. Om du använder en webb-frontend eller åtkomst till API: er från mobila klienter kan du distribuera [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) och klona dina Azure App Service på alla önskade regioner, med hjälp av en prestanda konfiguration som stöder din utökade globala täckning. När klienterna har åtkomst till klient-eller API: er kommer de att dirigeras till närmast App Service, vilket i sin tur ansluter till den lokala Cosmos DB repliken.

:::image type="content" source="./media/social-media-apps/social-media-apps-global-replicate.png" alt-text="Lägga till global täckning på din sociala plattform" border="false":::

## <a name="conclusion"></a>Slutsats

Den här artikeln tar lite lätt till alternativen för att skapa sociala nätverk helt och hållet i Azure med tjänster med låg kostnad. den ger resultat genom att uppmuntra användningen av en lagrings lösning med flera lager och data distribution som kallas "stega".

:::image type="content" source="./media/social-media-apps/social-media-apps-azure-solution.png" alt-text="Diagram över interaktion mellan Azure-tjänster för sociala nätverk" border="false":::

Sanningen är att det inte finns någon silver punkt för den här typen av scenarier. Det är den synergieffekt som skapats av kombinationen av fantastiska tjänster som gör det möjligt för oss att skapa fantastiska upplevelser: hastigheten och friheten hos Azure Cosmos DB att tillhandahålla ett bra socialt program, intelligensen bakom en lösning för första klass som Azure Kognitiv sökning, flexibiliteten i Azure App tjänster som värd för inte ens oberoende program men kraftfulla bakgrunds processer och den utbyggbara Azure Storage och Azure SQL Database för lagring av enorma mängder data och den analytiska kraften hos Azure Machine Lär dig att skapa kunskap och information som kan ge feedback till dina processer och hjälpa oss att leverera rätt innehåll till rätt användare.

## <a name="next-steps"></a>Nästa steg

Mer information om användnings fall för Cosmos DB finns i [vanliga Cosmos DB användnings fall](use-cases.md).