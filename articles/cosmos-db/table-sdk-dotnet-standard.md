---
title: Azure Cosmos DB Tabell-API .NET standard SDK & resurser
description: Lär dig allt om Azure Cosmos DB Tabell-API och .NET standard SDK, inklusive versions datum, datum för indragningen och ändringar som gjorts mellan varje version.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: f41a895aef3476386b8b530fc3e265ffaa21e8c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597612"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB tabell .NET standard-API: Hämta och viktig information
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Länkar  |
|---|---|
|**SDK-nedladdning**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Exempel**|[Cosmos DB Tabell-API .NET-exempel](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Snabbstart**|[Snabbstart](create-table-dotnet.md)|
|**Självstudie**|[Självstudie](tutorial-develop-table-dotnet.md)|
|**Aktuellt ramverk som stöds**|[Microsoft .NET standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Rapportera problem**|[Rapportera problem](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Viktig information för 2.0.0-serien
2.0.0-serien använder sig av beroendet av [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), med prestanda förbättringar och namn områdes konsolidering för att Cosmos DB slut punkten.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0 – för hands version
* första för hands versionen av 2.0.0 Table SDK som tar beroendet av [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), med prestanda förbättringar och namn områdes konsolidering för att Cosmos DB slut punkten. Det offentliga API: t är oförändrat.

## <a name="release-notes-for-100-series"></a>Viktig information för 1.0.0-serien
1.0.0-serien tar beroende av [Microsoft.Azure.DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* Lägg till stöd för att ange TTL-egenskapen om den är cosmosdb-slutpunkt 
* Princip för princip för återförsök vid timeout och uppgift avbrutet undantag
* Åtgärda återkommande aktivitet avbrutna undantag visas i ASP .NET-program
* Korrigera Azure Table Storage Hämta enbart från sekundär slut punkt läge
* Uppdatera `Microsoft.Azure.DocumentDB.Core` beroende version till 2.11.2 som löser tillfälliga null-referensundantag
* Uppdatera `Odata.Core` beroende version till 7.6.4 som korrigerar kompatibilitet med Azure Shell

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* Prestanda förbättring genom att ange standard spårnings nivån för Table SDK till SourceLevels. off, som kan väljas via app.config

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* Introducera ny konfiguration under TableClientConfiguration och Använd rest-utförar för att kommunicera med Cosmos DB Tabell-API

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5 – för hands version
* Felkorrigeringar

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* Felkorrigeringar
* Ange alternativet HttpClientTimeout för RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4 – för hands version
* Felkorrigeringar
* Ange alternativet HttpClientTimeout för RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Felkorrigeringar

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Allmän tillgänglighets utgåva

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0 – för hands version
* Ändringar har gjorts i hur CloudTableClient kan konfigureras. Det tar nu ett TableClientConfiguration-objekt under konstruktion. TableClientConfiguration tillhandahåller olika egenskaper för att konfigurera klient beteendet beroende på om mål slut punkten är Cosmos DB Tabell-API eller Azure Storage Tabell-API.
* Stöd har lagts till för TableQuery för att returnera resultat i sorterad ordning i en anpassad kolumn. Den här funktionen stöds bara för Cosmos DB tabell slut punkter.
* Stöd har lagts till för att exponera RequestCharges på olika resultat typer. Den här funktionen stöds bara för Cosmos DB tabell slut punkter.

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1 – för hands version
* Lägg till stöd för SAS-token, åtgärder för TablePermissions, ServiceProperties och ServiceStats mot Azure Storage tabell slut punkter. 
   > [!NOTE]
   > Vissa funktioner i tidigare Azure Storage tabell-SDK: er stöds inte än, t. ex. kryptering på klient sidan.

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0 – för hands version
* Lägg till stöd för grundläggande CRUD-, batch-och fråge åtgärder mot Azure Storage tabell slut punkter. 
   > [!NOTE]
   > Vissa funktioner i tidigare Azure Storage tabell-SDK: er stöds inte än, t. ex. kryptering på klient sidan.

### <a name="091-preview"></a><a name="0.9.1-preview"></a>0.9.1 till och – för hands version
* Azure Cosmos DB Table .NET standard SDK är ett plattforms oberoende .NET-bibliotek som ger effektiv åtkomst till tabell data modellen på Cosmos DB. Den här inledande versionen stöder en fullständig uppsättning tabell-och entitets-CRUD + fråge funktioner med liknande API: er som [Cosmos DB Table SDK för .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Azure Storage tabell slut punkter stöds inte ännu i 0.9.1 till och-Preview-versionen.

## <a name="release-and-retirement-dates"></a>Datum för lansering och indragning
Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Detta plattforms oberoende .NET standard-bibliotek [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) kommer att ersätta .NET Framework-biblioteket [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>2.0.0-serien
| Version | Utgivningsdatum | Förfallodatum |
| --- | --- | --- |
| [2.0.0 – för hands version](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0-serien
| Version | Utgivningsdatum | Förfallodatum |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 september 2019 |--- |
| [1.0.5 – för hands version](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4 – för hands version](#1.0.4-preview) |26 juli 2019 |--- |
| 1.0.2 – för hands version |2 maj 2019 |--- |
| [1.0.1](#1.0.1) |19 april 2019 |--- |
| [1.0.0](#1.0.0) |13 mars 2019 |--- |
| [0.11.0 – för hands version](#0.11.0-preview) |5 mars 2019 |--- |
| [0.10.1 – för hands version](#0.10.1-preview) |22 januari 2019 |--- |
| [0.10.0 – för hands version](#0.10.0-preview) |18 december 2018 |--- |
| [0.9.1 till och – för hands version](#0.9.1-preview) |18 oktober 2018 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Azure Cosmos DB Tabell-API finns i [Introduktion till Azure Cosmos DB tabell-API](table-introduction.md).
