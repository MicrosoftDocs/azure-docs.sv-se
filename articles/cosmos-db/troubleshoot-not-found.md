---
title: Felsöka Azure Cosmos DB inte hittade undantag
description: Lär dig hur du diagnostiserar och åtgärdar inte hittade undantag.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 22cce2c620d23ab477de5d92bb8c6d4f5ef5a493
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425132"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB inte hittade undantag
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP-statuskoden 404 anger att resursen inte längre finns.

## <a name="expected-behavior"></a>Förväntat beteende
Det finns många giltiga scenarier där ett program förväntar sig en kod 404 och hanterar scenariot korrekt.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Ett undantag som inte hittades returnerades för ett objekt som ska finnas eller som finns
Här följer möjliga orsaker till att en status kod 404 returneras om objektet ska finnas eller finns.

### <a name="the-read-session-is-not-available-for-the-input-session-token"></a>Läsningssessionen är inte tillgänglig för inmatningssessionens token

#### <a name="solution"></a>Lösning:
1. Uppdatera din aktuella SDK till den senaste versionen som är tillgänglig. De vanligaste orsakerna till det här felet har åtgärd ATS i de senaste SDK-versionerna.

### <a name="race-condition"></a>Konkurrenstillstånd
Det finns flera SDK-klient instanser och läsningen skedde innan skrivningen.

#### <a name="solution"></a>Lösning:
1. Standard kontot är konsekvens för Azure Cosmos DB är konsekvens på sessionen. När ett objekt skapas eller uppdateras, returnerar svaret en session-token som kan skickas mellan SDK-instanser för att garantera att Read-begäran läser från en replik med den ändringen.
1. Ändra [konsekvens nivån](./consistency-levels.md) till en [starkare nivå](./consistency-levels.md).

### <a name="invalid-partition-key-and-id-combination"></a>Ogiltig kombination av partitionsnyckel och ID
Kombinationen av partitionsnyckel och ID är ogiltig.

#### <a name="solution"></a>Lösning:
Åtgärda den program logik som orsakar felaktig kombination. 

### <a name="invalid-character-in-an-item-id"></a>Ogiltigt Character i ett objekt-ID
Ett objekt infogas i Azure Cosmos DB med ett [ogiltigt Character](/dotnet/api/microsoft.azure.documents.resource.id#remarks) i objekt-ID: t.

#### <a name="solution"></a>Lösning:
Ändra ID till ett annat värde som inte innehåller specialtecknen. Om du ändrar ID: t inte är ett alternativ kan du base64 koda ID: t för att undanta specialtecknen. Base64 kan fortfarande skapa ett namn med ett ogiltigt Character/som måste ersättas.

Objekt som redan har infogats i behållaren för ID kan ersättas med hjälp av RID-värden i stället för namnbaserade referenser.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes).Replace('/', '!');

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Time to Live rensa
Värdet för egenskapen [Time to Live (TTL)](./time-to-live.md) har angetts för objektet. Objektet tömdes eftersom TTL-egenskapen hade upphört att gälla.

#### <a name="solution"></a>Lösning:
Ändra egenskapen TTL för att förhindra att objektet rensas.

### <a name="lazy-indexing"></a>Lazy-indexering
Den [Lazy-indexeringen](index-policy.md#indexing-mode) har inte fångats upp.

#### <a name="solution"></a>Lösning:
Vänta tills indexeringen har fångats upp eller ändra indexerings principen.

### <a name="parent-resource-deleted"></a>Överordnad resurs har tagits bort
Databasen eller behållaren som objektet finns i har tagits bort.

#### <a name="solution"></a>Lösning:
1. [Återställ](./configure-periodic-backup-restore.md#request-restore) den överordnade resursen eller återskapa resurserna.
1. Skapa en ny resurs som ersätter den borttagna resursen.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. behållare/samlings namn är Skift läges känsliga
Behållare/samlings namn är Skift läges känsliga i Cosmos DB.

#### <a name="solution"></a>Lösning:
Se till att använda det exakta namnet när du ansluter till Cosmos DB.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK.
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md).
* [Diagnostisera och Felsök](troubleshoot-java-sdk-v4-sql.md) problem när du använder Azure Cosmos DB Java v4 SDK.
* Lär dig mer om prestanda rikt linjer för [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
