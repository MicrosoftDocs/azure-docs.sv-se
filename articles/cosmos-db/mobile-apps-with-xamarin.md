---
title: 'Självstudie: utveckla mobil program med Xamarin och Azure Cosmos DB'
description: 'Självstudie: en själv studie kurs som skapar ett Xamarin iOS-, Android-eller Forms-program med hjälp av Azure Cosmos DB. Azure Cosmos DB är en snabb, världsomfattande molndatabas för mobilappar.'
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: d1164c14b406fb66638e6a5333fe38180272aa14
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359344"
---
# <a name="tutorial-build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Självstudie: utveckla mobil program med Xamarin och Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

De flesta mobilappar måste lagra data i molnet och Azure Cosmos DB är en molndatabas för mobilappar. Det har allt som krävs av Mobile Developer. Den är en fullständigt hanterad databas som en tjänst som kan skalas på begäran. Den överför dina data till tillämpningsprogrammet transparent, oavsett var dina användare befinner sig i världen. Med hjälp av [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) kan du aktivera Xamarin-mobilappar så att de kan interagera direkt med Azure Cosmos DB, utan mellannivå.

Den här artikeln innehåller en självstudiekurs för att skapa mobilappar med Xamarin och Azure Cosmos DB. Du hittar källkoden för hela självstudiekursen på [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin), inklusive information om hur du hanterar användare och behörigheter.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Azure Cosmos DB-funktioner för mobilappar
Azure Cosmos DB innehåller följande viktiga funktioner för utvecklare av mobilappar:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-for-mobile.png" alt-text="Azure Cosmos DB-funktioner för mobilappar":::

* Omfattande frågor för schemalösa data. Azure Cosmos DB lagrar data som schemalösa JSON-dokument i heterogena samlingar. Den erbjuder [omfattande och snabba frågor](./sql-query-getting-started.md) utan att du behöver bekymra dig om scheman eller index.
* Högt dataflöde. Det tar bara några millisekunder att läsa och skriva dokument med Azure Cosmos DB. Utvecklare kan specificera vilket dataflöde de behöver och Azure Cosmos DB erbjuder ett serviceavtal med 99,99 % tillgänglighet för alla konton med tillgång till en eller flera regioner med konsekvensmodellen ”relaxed” (avslappnad), samt 99,999 % läsningstillgänglighet för alla databaskonton med tillgång till flera regioner.
* Obegränsad skalning. Dina Azure Cosmos-behållare [växer när din app växer](partitioning-overview.md). Du kan starta med ett liten datastorlek och ett dataflöde på ett par hundra förfrågningar per sekund. Dina samlingar eller databaser kan växa till flera petabyte data och ett högt dataflöde med hundratals miljoner förfrågningar per sekund.
* Globalt distribuerad. Användare av mobilappar är ofta på resande fot över hela världen. Azure Cosmos DB är en [globalt distribuerad databas](distribute-data-globally.md). Klicka på kartan för att göra data tillgängliga för användarna.
* Inbyggd omfattande autentisering. Med Azure Cosmos DB kan du enkelt implementera populära mönster som [data per användare](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems) eller delade data för flera användare utan kod för komplex anpassad autentisering.
* Geospatiala frågor. Många mobilappar erbjuder idag geo-baserade upplevelser. Med förstklassigt stöd för [geospatiala typer](./sql-query-geospatial-intro.md) gör Azure Cosmos DB det enkelt att skapa sådana upplevelser.
* Binära bilagor. Dina appdata innehåller ofta binära blobar. Inbyggt stöd för bifogade filer gör Azure Cosmos DB till det enda du behöver för din app.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Självstudiekurs för Azure Cosmos DB och Xamarin
Följande självstudiekurs visar hur du skapar en mobilapp med Xamarin och Azure Cosmos DB. Du hittar källkoden för hela självstudiekursen på [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin).

### <a name="get-started"></a>Kom igång
Det är lätt att komma igång med Azure Cosmos DB. Gå till Azure Portal och skapa ett nytt Azure Cosmos DB-konto. Klicka på fliken **snabb start** . Ladda ned Xamarin formulär att göra-listan som redan är ansluten till ditt Azure Cosmos DB-konto. 

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-quickstart.png" alt-text="Azure Cosmos DB snabbstart för mobilappar":::

Eller om du har en befintlig Xamarin-app kan du lägga till [Azure Cosmos DB NuGet-paketet](sql-api-sdk-dotnet-core.md). Azure Cosmos DB stöder delade bibliotek för Xamarin. iOS, Xamarin. Android och Xamarin Forms.

### <a name="work-with-data"></a>Arbeta med data
Dina dataposter lagras i Azure Cosmos DB som schemalösa JSON-dokument i heterogena samlingar. Du kan lagra dokument med olika strukturer i samma samling:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Du kan använda språkintegrerade frågor över schemalösa data i dina Xamarin-projekt:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Lägga till användare
Precis som många kom igång-exempel, Azure Cosmos DB exemplet som du laddade ned autentiseringen till tjänsten med hjälp av en primär nyckel hårdkodad i appens kod. Den här standardinställningen är inte bra för appar du tänker dig ska kunna köras var som helst utom på din lokala emulator. Om en obehörig användare erhöll den primära nyckeln kan alla data på ditt Azure Cosmos DBs konto komprometteras. Istället vill du att appen bara ska få åtkomst till poster som hör till den inloggade användaren. Azure Cosmos DB gör att utvecklare kan bevilja läsbehörighet eller läs- och skrivbehörighet för programmet till en samling, en uppsättning dokument grupperade efter en partitionsnyckel eller ett visst dokument. 

Följ dessa anvisningar om du vill ändra att göra-appen till en app med en att göra-lista för flera användare: 

  1. Lägg till inloggningen i appen med hjälp av Facebook, Active Directory eller en annan provider.

  2. Skapa en Azure Cosmos DB UserItems-samling med **/userId** som partitionsnyckel. Genom att ange partitionsnyckeln för samlingen kan Azure Cosmos DB skala oändligt när antalet appanvändare växer, utan att hastigheten för frågorna påverkas.

  3. Lägg till asynkron Azure Cosmos DB-meddelandekö för resurstoken. Detta enkla webb-API autentiserar användare och utfärdar tillfälliga token för inloggade användare som bara ger åtkomst till dokument inom deras partition. I det här exemplet finns den asynkrona meddelandekön för resurstoken i App Service.

  4. Ändra appen så att den asynkrona meddelandekön för resurstoken autentiseras med Facebook och begär resurstoken för inloggade Facebook-användare. Du kan sedan komma åt deras data i samlingen UserItems.  

Du hittar en fullständig kodsamling för det här mönstret under den [asynkrona meddelandekön för resurstoken på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems). Det här diagrammet visar lösningen:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png" alt-text="Azure DB Cosmos-användare och asynkron meddelandekö för behörigheter" border="false":::

Om du vill att två användare ska ha, till gång till samma att göra-lista, kan du lägga till ytterligare behörigheter i åtkomsttoken i Resource token Broker.

### <a name="scale-on-demand"></a>Skala på begäran
Azure Cosmos-DB är en hanterad databas som en tjänst. När användarbasen växer behöver du inte tänka på att behöva etablera virtuella datorer eller att öka kärnorna. Allt du behöver tala om för Azure Cosmos DB är hur många åtgärder per sekund (dataflöde) din app behöver. Du kan ange dataflödet via fliken **Skala** genom att använda ett mått på dataflödet som kallas enheter för programbegäran (RU) per sekund. Till exempel kräver en Läs åtgärd på ett 1-KB-dokument ett RU. Du kan också lägga till aviseringar till måttet för **dataflöde** för att övervaka trafiktillväxten och programmässigt ändra dataflödet när aviseringar utlöses.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png" alt-text="Azure DB Cosmos skalar dataflödet på begäran":::

### <a name="go-planet-scale"></a>Världsomfattande skalning
Allt eftersom din app blir populärare kan du få användare över hela världen. Eller så kanske du vill vara förbered för oförutsedda händelser. Gå till Azure Portal och öppna ditt Azure Cosmos DB-konto. Klicka på kartan för att kontinuerligt replikera dina data till flera regioner över hela världen. Den här funktionen gör dina data tillgängliga oavsett var användarna är. Du kan också lägga till redundans-principer som extra säkerhet.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png" alt-text="Azure DB Cosmos skalar över geografiska områden" border="false":::

Grattis! Du har slutfört lösningen och har en mobilapp med Xamarin och Azure Cosmos DB. Cordova-appar byggs på liknande sätt med hjälp av Azure Cosmos DB JavaScript SDK och inbyggda iOS-/Android-appar med hjälp av Azure Cosmos DB REST API:er.

## <a name="next-steps"></a>Nästa steg
* Visa källkoden för [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin).
* Ladda ned [Cosmos Azure DB .NET Core SDK](sql-api-sdk-dotnet-core.md).
* Hitta fler kodexempel för [.NET-program](sql-api-dotnet-samples.md).
* Lär dig mer om [Azure Cosmos DB omfattande frågefunktioner](./sql-query-getting-started.md).
* Lär dig mer om [geospatialt stöd i Azure Cosmos DB](./sql-query-geospatial-intro.md).