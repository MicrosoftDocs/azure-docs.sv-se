---
title: Azure Table Storage-stöd i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB Tabell-API och Azure Storage tabeller fungerar tillsammans genom att dela samma tabell data modell med åtgärder
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 01/08/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: c2b2a9a03a654e8ccba58e62a1d017cd11db5abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050771"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Utveckla med Azure Cosmos DB Table API:t och Azure Table Storage
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB Table API:t och Azure Table Storage delar samma tabelldatamodell och exponerar samma åtgärder för att skapa, ta bort, uppdatera och fråga via sina SDK:er.

> [!NOTE]
> [Läget för Server lös kapacitet](serverless.md) är nu tillgängligt på Azure Cosmos DB tabell-API.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Utveckla med Azure Cosmos DB Table API:t

Just nu har [Azure Cosmos DB Table API:t](table-introduction.md) fyra SDK:er tillgängliga för utveckling: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Det här biblioteket har NET Standard som mål och har samma klasser och metodsignaturer som den offentliga versionen av [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), men har även funktioner för att ansluta till Azure Cosmos DB-konton via Tabell-API. Användare av .NET Framework Library [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) rekommenderas för att uppgradera till [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) eftersom det är i underhålls läge och kommer att bli inaktuellt snart.

* [Python SDK](table-sdk-python.md): den nya Azure Cosmos DB python SDK är den enda SDK som stöder Azure Table Storage i python. Detta SDK fungerar med både Azure Table Storage och Azure Cosmos DB Table API:t.

* [Java SDK](table-sdk-java.md): den här Azure Storage SDK kan ansluta till Azure Cosmos DB-konton med hjälp av tabell-API.

* [Node.js SDK](table-sdk-nodejs.md): den här Azure Storage SDK kan ansluta till Azure Cosmos DB-konton med hjälp av tabell-API.


Ytterligare information om hur du arbetar med Table API:t finns i artikeln [Vanliga frågor och svar: utveckla med Table API](table-api-faq.md).

## <a name="developing-with-azure-table-storage"></a>Utveckla med Azure Table Storage

Azure Table storage har de här SDK:erna tillgängliga för utveckling:

- [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft. Azure. Storage. File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft. Azure. Storage. Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)och [Microsoft. Azure. Storage. common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) libraries gör att du kan arbeta med Azure Table Storage-tjänsten. Om du använder Tabell-API i Azure Cosmos DB kan du i stället använda [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) -biblioteket.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Azure Cosmos DB Table SDK för python stöder tjänsten Table Storage (eftersom Azure Table Storage och Cosmos DB är Tabell-API dela samma funktioner och funktioner, och i en ansträngning för att factorize vår SDK-utvecklings ansträngningar rekommenderar vi att du använder denna SDK).
- [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java). Den här Azure Storage SDK:n tillhandahåller ett klientbibliotek i Java för att använda Azure Table-lagring.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Detta SDK erbjuder ett Node.js-paket och ett webbläsarkompatibelt JavaScript-klientbiblioteket för att använda lagringstabelltjänsten.
- [AzureRmStorageTable PowerShell-modulen](https://www.powershellgallery.com/packages/AzureRmStorageTable). Den här PowerShell-modulen har cmdletar för att arbeta med lagringstabeller.
- [Azure Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp/). Det här biblioteket låter dig skapa program mot Azure Storage.
- [Azure Storage Table-klientbiblioteket för Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Det här projektet tillhandahåller ett Ruby-paket som gör det enkelt att komma åt Azure Table-lagringstjänster.
- [Azure Storage Table PHP-klientbibliotek](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Det här projektet tillhandahåller ett PHP-klientbibliotek som gör det enkelt att komma åt Azure Table-lagringstjänster.


   





