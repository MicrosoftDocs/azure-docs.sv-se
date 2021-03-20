---
title: Modellerings data i Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Lär dig mer om data modellering i NoSQL-databaser, skillnader mellan modellerings data i en Relations databas och en dokument databas.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a141177846def9c94216684c1083d0d336eeda1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333264"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Data modellering i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

När schema fria databaser, t. ex. Azure Cosmos DB, gör det mycket enkelt att lagra och fråga ostrukturerade och delvis strukturerade data, bör du ägna lite tid åt att tänka på din data modell för att få ut mesta möjliga av tjänsten när det gäller prestanda och skalbarhet och lägsta kostnad.

Hur lagras data? Hur kommer ditt program att hämta och fråga data? Är ditt program skrivskyddat eller så är det skrivskyddat?

När du har läst den här artikeln kan du svara på följande frågor:

* Vad är data modellering och varför ska jag bry mig?
* Hur fungerar modellerings data i Azure Cosmos DB olika för en Relations databas?
* Hur gör jag för att Express data relationer i en icke-relationell databas?
* När ska jag bädda in data och när jag länkar till data?

## <a name="embedding-data"></a>Bädda in data

När du börjar att modellera data i Azure Cosmos DB försöker hantera dina entiteter som **fristående objekt** som representeras som JSON-dokument.

För jämförelse ska vi först se hur vi kan modellera data i en Relations databas. I följande exempel visas hur en person kan lagras i en Relations databas.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="Relations databas modell" border="false":::

När du arbetar med relations databaser, är strategin att normalisera alla dina data. Att normalisera dina data innebär vanligt vis att ta en entitet, till exempel en person, och dela upp den i diskreta komponenter. I exemplet ovan kan en person ha flera kontakt informations poster, samt flera adress poster. Kontakt uppgifter kan delas vidare genom att ytterligare extrahera vanliga fält som en typ. Samma sak gäller för adress, varje post kan vara av typen *Start* eller *företag*.

Den GUID som är lokal vid normaliserade data är att **undvika att lagra redundanta data** på varje post och i stället referera till data. I det här exemplet, för att läsa en person, med alla sina kontakt uppgifter och adresser, måste du använda kopplingar för att effektivt skriva tillbaka (eller avnormalisera) dina data vid körning.

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

Att uppdatera en enskild person med sin kontakt information och adresser kräver Skriv åtgärder i flera enskilda tabeller.

Nu ska vi ta en titt på hur vi kan modellera samma data som en självständig entitet i Azure Cosmos DB.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

Genom att använda metoden ovan har vi **avnormaliserat** person posten, genom att **bädda in** all information som är relaterad till den här personen, till exempel kontakt uppgifter och adresser, i ett *enda JSON* -dokument.
Eftersom vi inte är begränsade till ett fast schema har vi också flexibiliteten att göra saker som att ha kontakt uppgifter om olika former.

Att hämta en fullständig person post från databasen är nu en **enda Läs åtgärd** mot en enda behållare och för ett enda objekt. Att uppdatera en person post, med deras kontakt information och adresser, är också en **enda Skriv åtgärd** mot ett enda objekt.

Genom att avnormalisera data kan ditt program behöva utfärda färre frågor och uppdateringar för att utföra vanliga åtgärder.

### <a name="when-to-embed"></a>När du ska bädda in

I allmänhet använder du inbäddade data modeller när:

* Det finns **inneslutna** relationer mellan entiteter.
* Det finns **en-till-lite-** relationer mellan entiteter.
* Det finns inbäddade data som **ändras sällan**.
* Det finns inbäddade data som inte kommer att växa **utan bindning**.
* Det finns inbäddade data som **frågas ofta tillsammans**.

> [!NOTE]
> Normalt ger denormaliserade data modeller bättre **Läs** prestanda.

### <a name="when-not-to-embed"></a>När inte ska bäddas in

Även om en tumregel i Azure Cosmos DB är att avnormalisera allt och bädda in alla data i ett enda objekt, kan det leda till vissa situationer som bör undvikas.

Ta det här JSON-kodfragmentet.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

Detta kan vara vad en post-entitet med inbäddade kommentarer skulle se ut om vi modellerar en typisk blogg eller CMS, system. Problemet i det här exemplet är att kommentars mat ris är **obunden**, vilket innebär att det inte finns någon (praktisk) gräns för antalet kommentarer som kan ha ett enskilt inlägg. Detta kan bli ett problem eftersom storleken på objektet kan växa oändligt stor.

Eftersom storleken på objektet ökar möjligheten att överföra data via kabeln, samt läsning och uppdatering av objektet, i skala, kommer att påverkas.

I det här fallet är det bättre att överväga följande data modell.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

Den här modellen har de tre senaste kommentarerna som är inbäddade i post-behållaren, som är en matris med en fast uppsättning attribut. De andra kommentarerna är grupperade i batchar med 100 kommentarer och lagras som separata objekt. Storleken på batch valdes som 100 eftersom vårt fiktiva program tillåter att användaren läser in 100 kommentarer i taget.  

Ett annat fall där inbäddning av data inte är en bra idé är när inbäddade data används ofta över-objekt och ändras ofta.

Ta det här JSON-kodfragmentet.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

Detta kan representera en persons aktie portfölj. Vi har valt att bädda in aktie informationen i varje portfölj dokument. I en miljö där relaterade data ändras ofta, t. ex. ett program för börs handel, kommer att bädda in data som ändras ofta, vilket innebär att du ständigt uppdaterar varje portfölj dokument varje gång som ett lager säljs.

Börs *Zaza* kan handla flera hundra gånger på en dag och tusentals användare kan ha *Zaza* i sin portfölj. Med en data modell som ovan måste vi uppdatera många tusentals dokument i portföljen många gånger varje dag som leder till ett system som inte skalar bra.

## <a name="referencing-data"></a>Referens data

Inbäddning av data fungerar snyggt i många fall, men det finns scenarier när du avnormaliserar dina data, vilket leder till fler problem än det är värt. Vad gör vi nu?

Relations databaser är inte den enda plats där du kan skapa relationer mellan entiteter. I en dokument databas kan du ha information i ett dokument som relaterar till data i andra dokument. Vi rekommenderar inte att du skapar system som bättre lämpar sig för en Relations databas i Azure Cosmos DB eller andra dokument databaser, men enkla relationer är fina och kan vara användbara.

I JSON nedan valde vi att använda exemplet på en aktie portfölj från tidigare men den här gången refererar vi till aktie-objektet i portföljen i stället för att bädda in det. På det här sättet, när Stock-objektet ändras ofta under dagen, är det enda dokumentet som behöver uppdateras.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

En omedelbar nack delar med den här metoden är om ditt program krävs för att visa information om varje aktie som hålls kvar när en persons portfölj visas. i det här fallet skulle du behöva göra flera resor till databasen för att läsa in informationen för varje aktie dokument. Här har vi fattat ett beslut om att förbättra effektiviteten vid Skriv åtgärder, vilket sker ofta under dagen, men i sin tur har komprometterats på de Läs åtgärder som eventuellt har mindre påverkan på prestandan i det aktuella systemet.

> [!NOTE]
> Normaliserade data modeller **kan kräva mer rund turer** till servern.

### <a name="what-about-foreign-keys"></a>Vad gäller sekundär nycklar?

Eftersom det för närvarande inte finns någon begreppet constraint, sekundär nyckel eller annat, är alla relationer mellan dokument i dokumenten effektiva "svaga länkar" och kommer inte att verifieras av själva databasen. Om du vill se till att de data som ett dokument refererar till verkligen finns, måste du göra det i programmet eller genom att använda Server sidans utlösare eller lagrade procedurer på Azure Cosmos DB.

### <a name="when-to-reference"></a>Vid referens

I allmänhet använder du normaliserade data modeller när:

* Representerar **en-till-många-** relationer.
* Representerar **många-till-många** -relationer.
* Relaterade data **ändringar ofta**.
* Det gick inte att **binda** data som refereras till.

> [!NOTE]
> Normalt ger normalt bättre **Skriv** prestanda.

### <a name="where-do-i-put-the-relationship"></a>Var ska jag spara relationen?

Tillväxten för relationen avgör i vilket dokument som referensen ska lagras.

Om vi tittar på den JSON nedan som modellerar utgivare och böcker.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

Om antalet böcker per utgivare är litet med begränsad tillväxt kan det vara praktiskt att lagra bok referensen i utgivar dokumentet. Men om antalet böcker per utgivare är obundet, skulle den här data modellen leda till föränderligt, växande matriser, som i exempel utgivarens dokument ovan.

Att byta saker runt en bit skulle resultera i en modell som fortfarande representerar samma data, men som nu undviker de här stora föränderligt-samlingarna.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

I exemplet ovan har vi släppt den obegränsade samlingen i utgivar dokumentet. I stället har vi bara en referens till utgivaren på varje bok dokument.

### <a name="how-do-i-model-manymany-relationships"></a>Hur gör jag för att modell många: många relationer?

I en Relations databas *många: många* relationer modelleras ofta med kopplings tabeller som bara kopplar ihop poster från andra tabeller.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="Koppla tabeller" border="false":::

Du kanske är frestad att replikera samma sak som med dokument och skapar en data modell som ser ut ungefär så här.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

Detta fungerar. Men om du läser in en författare med deras böcker eller läser in en bok med sin upphovs man, kräver det alltid minst två ytterligare frågor mot databasen. En fråga till det kopplade dokumentet och en annan fråga för att hämta det faktiska dokumentet som ansluts.

Om all den här kopplings tabellen utförs, limmar du samman två data, sedan varför de inte släpper dem helt?
Tänk på följande.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

Om jag har en författare vet jag direkt vilka böcker de har skrivit, och omvänt om jag hade ett bok dokument inläst för att känna till ID: na för författaren. Detta sparar den mellanliggande frågan mot kopplings tabellen, vilket minskar antalet Server fördröjningar som programmet måste göra.

## <a name="hybrid-data-models"></a>Hybrid data modeller

Vi har nu tittat på inbäddning (eller avnormaliserar) och refererar till (eller normaliserar) data, var och en har sina båda sidor och var och en har kompromisser som vi har sett.

Det behöver inte alltid vara antingen eller så är det inte alltid scaredt att blanda lite.

Baserat på programmets specifika användnings mönster och arbets belastningar kan det finnas fall där blandade inbäddade och refererade data är meningsfulla och kan leda till enklare program logik med färre Server förskjutnings resor samtidigt som du behåller en bra prestanda nivå.

Överväg följande JSON.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

Här har vi (främst) följt den inbäddade modellen, där data från andra entiteter är inbäddade i dokumentet på den översta nivån, men andra data refereras till.

Om du tittar på bok dokumentet kan vi se några intressanta fält när vi tittar på matrisen med författare. Det finns ett `id` fält som är det fält vi använder för att referera till ett författar dokument, standard praxis i en normaliserad modell, men det finns även `name` och `thumbnailUrl` . Vi kunde ha fastnat med `id` och lämnat programmet för att få ytterligare information som behövs från respektive författar dokument med hjälp av länken "länk", men eftersom appens författare visar författarens namn och en miniatyr bild med varje bok som visas kan vi spara en tur och retur-begäran till servern per bok i en lista genom att avnormalisera **vissa** data från författaren.

Om författarens namn har ändrats eller om han ville uppdatera sitt foto måste vi gå och uppdatera varje bok som de någonsin publicerat, men för vår app, baserat på antagandet att författare inte ändrar sina namn ofta, är detta ett acceptabelt design beslut.  

I exemplet finns det **förberäknade mängd** värden för att spara dyrbar bearbetning vid en Läs åtgärd. I exemplet är några av de data som är inbäddade i författar dokumentet data som beräknas i körnings tid. Varje gång en ny bok publiceras skapas ett bok dokument **och** fältet countOfBooks anges till ett beräknat värde baserat på antalet bok dokument som finns för en viss författare. Den här optimeringen skulle vara lämplig för att läsa tunga system där vi kan ge beräkningar av skrivningar för att optimera läsningar.

Möjligheten att ha en modell med förberäknade fält görs möjlig eftersom Azure Cosmos DB stöder **transaktioner med flera dokument**. Många NoSQL-butiker kan inte utföra transaktioner i flera dokument och därför bör du tänka igenom design beslut, till exempel "alltid bädda in allt", på grund av den här begränsningen. Med Azure Cosmos DB kan du använda utlösare på Server sidan eller lagrade procedurer som infogar böcker och uppdaterar författare i en syra transaktion. Nu **behöver du inte bädda** in allt i ett dokument bara för att vara säker på att dina data är konsekventa.

## <a name="distinguishing-between-different-document-types"></a>Skilja mellan olika dokument typer

I vissa fall kanske du vill blanda olika dokument typer i samma samling. Detta är vanligt vis fallet när du vill att flera, relaterade dokument ska sitta i samma [partition](partitioning-overview.md). Du kan till exempel skicka både böcker och bok granskningar i samma samling och partitionera den av `bookId` . I sådana fall vill du vanligt vis lägga till dokument med ett fält som identifierar deras typ för att skilja dem åt.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>Nästa steg

Den största takeaways från den här artikeln är att förstå att data modellering i en schema fri värld är lika viktigt som någonsin.

Precis som det inte finns något enkelt sätt att representera en del av data på en skärm finns det inget sätt att modellera dina data på ett enkelt sätt. Du måste förstå ditt program och hur det kommer att skapa, förbruka och bearbeta data. Sedan kan du ange om du vill skapa en modell som hanterar de omedelbara behoven i ditt program genom att använda några av de rikt linjer som anges här. När dina program behöver ändras kan du dra nytta av flexibiliteten i en schema fri databas för att kunna använda ändringen och utveckla din data modell enkelt.

Mer information om Azure Cosmos DB finns på tjänstens [dokumentations](https://azure.microsoft.com/documentation/services/cosmos-db/) sida.

Information om hur du Shard dina data över flera partitioner finns i [partitionera data i Azure Cosmos DB](partitioning-overview.md).

Information om hur du kan modellera och partitionera data på Azure Cosmos DB med hjälp av ett verkligt exempel finns i [ data modellering och partitionering – ett Real-World exempel](how-to-model-partition-example.md).
