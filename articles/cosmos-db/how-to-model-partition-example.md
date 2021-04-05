---
title: Modeller och partitionera data på Azure Cosmos DB med ett verkligt exempel
description: Lär dig att modellera och partitionera ett verkligt globalt exempel med hjälp av Azure Cosmos DB Core API
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: d2f35ae7a6110acb2ca89bdaeb487eddabf84923
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185826"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Så här modellerar och partitionerar du data i Azure Cosmos DB med ett verkligt exempel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln bygger på flera Azure Cosmos DB koncept som [data modellering](modeling-data.md), [partitionering](partitioning-overview.md)och [etablerade data flöden](request-units.md) för att demonstrera hur du kan hantera en verklig data Designs övning.

Om du vanligt vis arbetar med relations databaser har du förmodligen skapat vanor och intuitions om hur du skapar en data modell. På grund av de specifika begränsningarna, men även de unika fördelarna med Azure Cosmos DB, översätter de flesta av dessa metod tips inte bra och kan dra dig till underoptimala lösningar. Målet med den här artikeln är att vägleda dig genom hela processen med att modellera ett verkligt användnings fall på Azure Cosmos DB, från objekt modellering till enhetens samplacering och container partitionering.

[Hämta eller Visa en community-genererad källkod](https://github.com/jwidmer/AzureCosmosDbBlogExample) som illustrerar begreppen från den här artikeln. Det här kod exemplet drogs av en community-deltagare och Azure Cosmos DB-teamet stöder inte underhållet.

## <a name="the-scenario"></a>Scenariot

I den här övningen ska vi fundera över domänen för en blogg plattform där *användarna* kan skapa *inlägg*. Användarna kan också *gilla* och lägga till *kommentarer* till dessa inlägg.

> [!TIP]
> Vi har markerat några ord i *kursiv stil*. dessa ord identifierar typen av "saker" som vår modell kommer att behöva manipulera.

Lägga till fler krav i vår specifikation:

- En front sida visar ett flöde med nyligen skapade inlägg,
- Vi kan hämta alla inlägg för en användare, alla kommentarer till ett inlägg och alla gillar för ett inlägg,
- Inlägg returneras med användar namnet för sina författare och antalet kommentarer och gillar de har,
- Kommentarer och gillar returneras också med användar namnet för de användare som har skapat dem,
- När de visas som listor, måste inlägg innehålla en trunkerad Sammanfattning av innehållet.

## <a name="identify-the-main-access-patterns"></a>Identifiera huvud åtkomst mönster

För att kunna starta ger vi en viss struktur till vår inledande specifikation genom att identifiera vår lösnings åtkomst mönster. När du skapar en data modell för Azure Cosmos DB är det viktigt att förstå vilka begär Ande som en modell måste ha för att se till att modellen kommer att hantera dessa begär Anden effektivt.

För att det ska bli lättare att följa den övergripande processen kategoriserar vi dessa olika begär Anden som antingen kommandon eller frågor, vilket lånar vissa ord listor från [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) där kommandon är Skriv begär Anden (det vill säga att det är för att uppdatera systemet) och frågor är skrivskyddade begär Anden.

Här är en lista över begär Anden som vår plattform kommer att ha för att exponera:

- **[C1]** Skapa/redigera en användare
- **[Q1]** Hämta en användare
- **[C2]** Skapa/redigera ett inlägg
- **[Q2]** Hämta ett inlägg
- **[Q3]** Lista en användares inlägg i kort form
- **[C3]** Skapa en kommentar
- **[Q4]** Lista ett posts kommentarer
- **[C4]** Som ett inlägg
- **[Q5]** Lista en posts gilla
- **[Q6]** Lista de *x* senaste inlägg som skapats i kort form (feed)

I det här skedet har vi inte funderat på information om vad varje entitet (användare, post osv.) kommer att innehålla. Det här steget är vanligt vis bland de första som ska hanteras när du utformar mot ett Relations lager, eftersom vi måste ta reda på hur dessa entiteter kommer att översättas i tabeller, kolumner, sekundär nycklar osv. Det är mycket mindre problem med en dokument databas som inte tvingar fram schema vid skrivning.

Det främsta skälet till varför det är viktigt att identifiera åtkomst mönstren från början är att den här listan över förfrågningar kommer att vara vår test-svit. Varje gång vi itererar över vår data modell kommer vi att gå igenom varje begäran och kontrol lera dess prestanda och skalbarhet. Vi beräknar de enheter för programbegäran som förbrukas i varje modell och optimerar dem. Alla dessa modeller använder standard indexerings principen och du kan åsidosätta den genom att indexera vissa egenskaper, vilket ytterligare kan förbättra RU-förbrukningen och svars tiden.

## <a name="v1-a-first-version"></a>V1: en första version

Vi börjar med två behållare: `users` och `posts` .

### <a name="users-container"></a>Användarcontainer

Den här behållaren lagrar endast användar objekt:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Vi partitionerar den här behållaren av `id` , vilket innebär att varje logisk partition inom behållaren bara kommer att innehålla ett objekt.

### <a name="posts-container"></a>Behållare för inlägg

Den här behållaren är värd för inlägg, kommentarer och gilla:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

Vi partitionerar den här behållaren av `postId` , vilket innebär att varje logisk partition i behållaren innehåller ett inlägg, alla kommentarer för det inlägget och alla gillar för det inlägget.

Observera att vi har infört en `type` egenskap i de objekt som lagras i den här behållaren för att skilja mellan de tre typerna av entiteter som den här behållaren är värd för.

Vi har också valt att referera till relaterade data i stället för att bädda in dem (mer information om dessa begrepp finns i [det här avsnittet](modeling-data.md) ):

- Det finns ingen övre gräns för hur många inlägg som en användare kan skapa,
- inlägg kan vara godtyckligt långa,
- Det finns ingen övre gräns för hur många kommentarer och gillar ett inlägg som kan ha,
- Vi vill kunna lägga till en kommentar eller gilla ett inlägg utan att behöva uppdatera själva posten.

## <a name="how-well-does-our-model-perform"></a>Hur bra fungerar vår modell?

Nu är det dags att utvärdera prestanda och skalbarhet för den första versionen. För var och en av de begär Anden som identifierades tidigare, mäter vi svars tiden och hur många enheter den förbrukar. Detta mått görs mot en dummy-datauppsättning som innehåller 100 000 användare med 5 till 50 inlägg per användare och upp till 25 kommentarer och 100 gilla per post.

### <a name="c1-createedit-a-user"></a>Cell Skapa/redigera en användare

Den här begäran är enkel att implementera när vi bara skapar eller uppdaterar ett objekt i `users` behållaren. Begäran kommer att spridas snyggt över alla partitioner tack vare `id` partitionsnyckel.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Skriva ett enskilt objekt till behållaren användare" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 7 MS | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>Bästa Hämta en användare

Hämtning av en användare görs genom att läsa motsvarande objekt från `users` behållaren.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Hämta ett enskilt objekt från behållaren användare" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>C2 Skapa/redigera ett inlägg

På samma sätt som **[C1]** behöver vi bara skriva till `posts` behållaren.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Skriva ett enskilt objekt till behållaren inlägg" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 9 MS | 8,76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>Q2 Hämta ett inlägg

Vi börjar med att hämta motsvarande dokument från `posts` behållaren. Men det är inte tillräckligt, enligt vår specifikation, som vi också måste samla in användar namnet för postens författare och antalet kommentarer och hur många gillar det här inlägget, vilket kräver tre ytterligare SQL-frågor som ska utfärdas.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Hämta ett inlägg och aggregera ytterligare data" border="false":::

Var och en av de ytterligare frågorna filtrerar på partitionsnyckel för dess respektive behållare, vilket är exakt vad vi vill maximera prestanda och skalbarhet. Men vi måste behöva utföra fyra åtgärder för att returnera ett enda inlägg, så vi ska förbättra det i en nästa iteration.

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 9 MS | 19,54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>Q3 Lista en användares inlägg i kort form

Först måste vi hämta de önskade inläggen med en SQL-fråga som hämtar de inlägg som motsvarar den specifika användaren. Men vi måste också utfärda fler frågor för att sammanställa författarens användar namn och antalet kommentarer och gillar.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="Hämta alla inlägg för en användare och aggregera ytterligare data" border="false":::

Den här implementeringen visar många nack delar:

- frågorna som sammanfattar antalet kommentarer och gillar måste utfärdas för varje post som returneras av den första frågan,
- huvud frågan filtrerar inte på behållarens partitions nyckel `posts` , vilket leder till en fläkt och en partitions ökning i behållaren.

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 130 MS | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>C3 Skapa en kommentar

En kommentar skapas genom att skriva motsvarande objekt i `posts` behållaren.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Skriva ett enskilt objekt till behållaren inlägg" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 7 MS | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Kvartalet Lista ett posts kommentarer

Vi börjar med en fråga som hämtar alla kommentarer för det inlägget och återigen måste du samla in användar namn separat för varje kommentar.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Hämta alla kommentarer för ett inlägg och aggregera ytterligare data" border="false":::

Även om huvud frågan filtrerar på behållarens partitionsnyckel, lägger till användar namn för att aggregera de övergripande prestandan separat. Vi ska förbättra det senare.

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 23 MS | 27,72 RU | ⚠ |

### <a name="c4-like-a-post"></a>C4 Som ett inlägg

Precis som **[C3]** skapar vi motsvarande objekt i `posts` behållaren.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Skriva ett enskilt objekt till behållaren inlägg" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 6 MS | 7,05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Lista en posts gilla

Precis som **[Q4]** frågar vi efter det inlägget och sammanställer sedan sina användar namn.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Hämta alla gillar för ett inlägg och aggregera ytterligare data" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 59 MS | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista de x senaste inlägg som skapats i kort form (feed)

Vi hämtar de senaste inläggen genom att fråga `posts` behållaren sorterad efter fallande skapande datum, sedan aggregera användar namn och antal kommentarer och gilla för varje inlägg.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="Hämtar de senaste inläggen och aggregerar ytterligare data" border="false":::

En gång till, filtrerar den ursprungliga frågan inte på `posts` behållarens partitionsnyckel, som utlöser en kostsam fläkt. Det här är ännu sämre eftersom vi riktar in sig på en mycket större resultat uppsättning och sorterar resultaten med en `ORDER BY` -sats, vilket gör det dyrare i termer av enheter för programbegäran.

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 306 MS | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Återspeglar prestandan hos v1

Vi tittar på de prestanda problem som vi möter i föregående avsnitt, men vi kan identifiera två huvud klasser av problem:

- vissa förfrågningar kräver att flera frågor utfärdas för att samla in alla data som vi behöver returnera,
- vissa frågor filtrerar inte på partitionsnyckel för de behållare som de är riktade till, vilket leder till en fläkt som hindrar vår skalbarhet.

Låt oss lösa var och en av dessa problem, och börja med det första.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: introduktionen av avnormalisering för att optimera Läs frågor

Anledningen till varför vi måste utfärda ytterligare förfrågningar i vissa fall beror på att resultatet av den första begäran inte innehåller alla data som vi behöver returnera. När du arbetar med en icke-relationell data lagring som Azure Cosmos DB, löses den här typen av problem ofta genom att avnormalisera data i vår data uppsättning.

I vårt exempel ändrar vi publicerings objekt för att lägga till användar namnet för postens författare, antalet kommentarer och antalet gillar:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Vi ändrar även kommentaren och som objekt för att lägga till användar namnet för den användare som har skapat dem:

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>Avnormaliserar kommentarer och like-antal

Vad vi vill uppnå är att varje gång vi lägger till en kommentar eller en liknande ökar vi också `commentCount` eller `likeCount` i motsvarande inlägg. När vår `posts` behållare är partitionerad av `postId` , det nya objektet (kommentar eller gilla) och dess motsvarande inlägg i samma logiska partition. Därför kan vi använda en [lagrad procedur](stored-procedures-triggers-udfs.md) för att utföra åtgärden.

Nu när du skapar en kommentar (**[C3]**), i stället för att bara lägga till ett nytt objekt i `posts` behållaren, så anropar vi följande lagrade procedur i den behållaren:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Den här lagrade proceduren hämtar ID för inlägget och texten för den nya kommentaren som parametrar, sedan:

- hämtar inlägget
- ökar `commentCount`
- ersätter inlägget
- lägger till den nya kommentaren

Eftersom lagrade procedurer körs som atomiska transaktioner förblir värdet för `commentCount` och det faktiska antalet kommentarer alltid synkroniserat.

Vi vill naturligtvis anropa en liknande lagrad procedur när du lägger till nya gillar att öka `likeCount` .

### <a name="denormalizing-usernames"></a>Avnormaliserar användar namn

Användar namn kräver en annan metod eftersom användare inte bara är i olika partitioner, men i en annan behållare. När vi måste normalisera data mellan partitioner och behållare kan vi använda käll behållarens [ändrings flöde](change-feed.md).

I vårt exempel använder vi ändrings flödet för `users` behållaren för att reagera när användare uppdaterar sina användar namn. När detta inträffar sprider vi ändringen genom att anropa en annan lagrad procedur på `posts` behållaren:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Avnormalisera användar namn i behållaren inlägg" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Den här lagrade proceduren använder ID: t för användaren och användarens nya användar namn som parametrar, sedan:

- hämtar alla objekt `userId` som matchar (som kan vara inlägg, kommentarer eller gilla)
- för var och en av dessa objekt
  - ersätter `userUsername`
  - ersätter objektet

> [!IMPORTANT]
> Den här åtgärden är kostsam eftersom den kräver att den här lagrade proceduren körs på varje partition i `posts` behållaren. Vi förutsätter att de flesta användare väljer ett lämpligt användar namn under registreringen och inte någonsin ändrar det, så att den här uppdateringen körs mycket sällan.

## <a name="what-are-the-performance-gains-of-v2"></a>Vad är prestanda vinster i v2?

### <a name="q2-retrieve-a-post"></a>Q2 Hämta ett inlägg

Nu när vår avnormalisering är på plats behöver vi bara hämta ett enskilt objekt för att hantera denna begäran.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Hämta ett enskilt objekt från behållaren inlägg" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Kvartalet Lista ett posts kommentarer

Här igen kan vi frigöra de extra begär Anden som hämtade användar namnen och som slutförs med en enda fråga som filtrerar på partitionsnyckel.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Hämta alla kommentarer för ett inlägg" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 4 MS | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Lista en posts gilla

Exakt samma situation vid registrering av gillar.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Hämta alla gillar för ett inlägg" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 4 MS | 8,92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: se till att alla begär Anden är skalbara

Titta på våra övergripande prestanda förbättringar, det finns fortfarande två begär Anden som vi inte har optimerat fullständigt: **[Q3]** och **[Q6]**. De är förfrågningar som avser frågor som inte filtrerar på partitionsnyckel för de behållare som de är riktade till.

### <a name="q3-list-a-users-posts-in-short-form"></a>Q3 Lista en användares inlägg i kort form

Den här begäran har redan nytta av förbättringarna som introducerades i v2, vilket frigör ytterligare frågor.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Diagram som visar frågan för att lista en användares inlägg i kort form." border="false":::

Men den återstående frågan filtreras fortfarande inte i `posts` behållarens partitionsnyckel.

Det är enkelt att tänka på den här situationen:

1. Den här begäran *måste* filtreras på `userId` eftersom vi vill hämta alla inlägg för en viss användare
1. Det fungerar inte bra eftersom det körs mot `posts` behållaren, som inte har partitionerats av `userId`
1. Vi kommer att lösa våra prestanda problem genom att utföra den här begäran mot en behållare som *har* partitionerats av `userId`
1. Det visar att vi redan har en sådan behållare: `users` containern!

Vi introducerar därför en andra nivå av avnormalisering genom att duplicera hela inlägg till `users` behållaren. Genom att göra det får vi en kopia av våra inlägg, endast partitionerade utmed olika dimensioner, vilket gör dem mer effektiva att hämta `userId` .

`users`Behållaren innehåller nu 2 typer av objekt:

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Tänk på följande:

- Vi har introducerat ett `type` fält i objektet User för att skilja användare från inlägg,
- Vi har också lagt till ett `userId` fält i användarobjektet, vilket är överflödigt med `id` fältet men det krävs eftersom `users` behållaren nu har partitionerats av `userId` (och inte `id` som tidigare)

För att uppnå den avnormaliseringen ska vi återigen använda ändrings flödet. Den här gången reagerar vi på ändrings flödet för `posts` behållaren för att skicka in nya eller uppdaterade inlägg till `users` behållaren. Eftersom registrering av inlägg inte kräver att deras fullständiga innehåll returneras kan vi trunkera dem i processen.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Avnormaliserar inlägg i användar behållaren" border="false":::

Vi kan nu dirigera vår fråga till `users` behållaren, filtrera på behållarens partitionsnyckel.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Hämtar alla inlägg för en användare" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 4 MS | 6,46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista de x senaste inlägg som skapats i kort form (feed)

Vi måste ta itu med en liknande situation här: även när du har behållit ytterligare frågor som inte behövs av avnormaliseringen som introducerades i v2, filtreras inte den återstående frågan på behållarens partitionsnyckel:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Diagram som visar frågan för att visa en lista över de x senaste inlägg som skapats i kort form." border="false":::

På samma sätt måste du maximera den här begärans prestanda och skalbarhet kräver att den bara träffar en partition. Detta är det enda eftersom vi bara behöver returnera ett begränsat antal objekt. för att kunna fylla i vår blogg plattforms start sida behöver vi bara hämta de 100 senaste inläggen, utan att behöva gå över hela data uppsättningen.

För att optimera den senaste begäran introducerar vi en tredje behållare i vår design, som helt dedikeras för att betjäna denna begäran. Vi avnormaliserar våra inlägg till den nya `feed` behållaren:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Den här behållaren är partitionerad av `type` , som alltid kommer att finnas `post` i våra objekt. Detta säkerställer att alla objekt i den här behållaren kommer att sitta i samma partition.

För att uppnå avnormaliseringen behöver vi bara ansluta till pipelinen Change feed som vi tidigare har lanserat för att skicka inlägg till den nya behållaren. En viktig sak att tänka på är att vi måste se till att vi endast lagrar 100 senaste inlägg. Annars kan behållarens innehåll växa utanför den maximala storleken på en partition. Detta görs genom att anropa en [efter utlösare](stored-procedures-triggers-udfs.md#triggers) varje gång ett dokument läggs till i behållaren:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Avnormaliserar inlägg i feed-behållaren" border="false":::

Här är innehållet i den post-trigger som trunkerar samlingen:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Det sista steget är att omdirigera vår fråga till vår nya `feed` behållare:

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="Hämtar de senaste inläggen" border="false":::

| **Svarstid** | **Avgift för RU** | **Prestanda** |
| --- | --- | --- |
| 9 MS | 16,97 RU | ✅ |

## <a name="conclusion"></a>Slutsats

Nu ska vi titta på de övergripande prestanda-och skalbarhets förbättringar som vi har lanserat i olika versioner av vår design.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **Cell** | 7 MS/5,71 RU | 7 MS/5,71 RU | 7 MS/5,71 RU |
| **Bästa** | 2 MS/1 RU | 2 MS/1 RU | 2 MS/1 RU |
| **C2** | 9 MS/8,76 RU | 9 MS/8,76 RU | 9 MS/8,76 RU |
| **Q2** | 9 MS/19,54 RU | 2 MS/1 RU | 2 MS/1 RU |
| **Q3** | 130 MS/619,41 RU | 28 MS/201,54 RU | 4 MS/6,46 RU |
| **C3** | 7 MS/8,57 RU | 7 MS/15,27 RU | 7 MS/15,27 RU |
| **Kvartalet** | 23 MS/27,72 RU | 4 MS/7,72 RU | 4 MS/7,72 RU |
| **C4** | 6 MS/7,05 RU | 7 MS/14,67 RU | 7 MS/14,67 RU |
| **[Q5]** | 59 MS/58,92 RU | 4 MS/8,92 RU | 4 MS/8,92 RU |
| **[Q6]** | 306 MS/2063,54 RU | 83 MS/532,33 RU | 9 MS/16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Vi har optimerat ett Läs-tung-scenario

Du kanske har märkt att vi har koncentrerat våra ansträngningar för att förbättra prestandan hos Läs begär Anden (frågor) vid kostnad för Skriv åtgärder (kommandon). I många fall utlöser Skriv åtgärder nu efterföljande denormalisering genom ändrings flöden, vilket gör dem mer dyra och längre att materialisera.

Detta är motiverat av det faktum att en blogg plattform (till exempel de flesta sociala appar) är Read-stor, vilket innebär att antalet Läs begär Anden som den har att betjäna är vanligt vis beställningar som är högre än antalet skriv förfrågningar. Det är därför klokt att göra Skriv förfrågningar dyrare att köra för att låta Läs begär Anden bli billigare och bättre utföra.

Om vi tittar på den mest extrema optimering som vi har gjort har **[Q6]** gått från 2000 + ru: er till bara 17 ru: er; Vi har uppnått detta genom att avnormalisera inlägg till en kostnad av ca 10 ru: er per objekt. Eftersom vi skulle tjäna mycket fler feed-begäranden än att skapa eller uppdatera inlägg, är kostnaden för den här avnormaliseringen försumbar och den totala besparingarna.

### <a name="denormalization-can-be-applied-incrementally"></a>Avnormalisering kan appliceras stegvis

De skalbara förbättringar som vi har utforskat i den här artikeln omfattar denormalisering och duplicering av data i data uppsättningen. Det bör noteras att dessa optimeringar inte behöver placeras på dag 1. Frågor som filtrerar på partitionsnyckel fungerar bättre i stor skala, men frågor med kors partitioner kan vara helt acceptabla om de anropas sällan eller mot en begränsad data uppsättning. Om du bara skapar en prototyp eller om du vill starta en produkt med en liten och kontrollerad användar bas kan du förmodligen få bättre förbättringar för senare. Det är viktigt att du [övervakar](use-metrics.md) modellens prestanda så att du kan bestämma om och när det är dags att ta med dem.

Den ändrings matning som vi använder för att distribuera uppdateringar till andra behållare lagrar alla uppdateringar permanent. Detta gör det möjligt att begära alla uppdateringar sedan du skapade behållaren och bootstrap-denormaliserade vyer som en engångs åtgärd, även om systemet redan har mycket data.

## <a name="next-steps"></a>Nästa steg

Efter introduktionen till praktisk data modellering och partitionering kanske du vill kontrol lera följande artiklar för att granska de koncept som vi har täckt:

- [Arbeta med databaser, containrar och objekt](account-databases-containers-items.md)
- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
- [Ändra feed i Azure Cosmos DB](change-feed.md)
