---
title: Migrera från biblioteket Change feed processor till Azure Cosmos DB .NET v3 SDK
description: Lär dig hur du migrerar ditt program från att använda biblioteket Change feed processor till Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ce2d4d3ad3ae349718f01584ec077b18e11e4f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341271"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrera från biblioteket Change feed processor till Azure Cosmos DB .NET v3 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln beskrivs de steg som krävs för att migrera ett befintligt programs kod som använder [biblioteket Change feed processor](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) till funktionen [ändra feed](change-feed.md) i den senaste versionen av .NET SDK (kallas även .net v3 SDK).

## <a name="required-code-changes"></a>Nödvändiga kod ändringar

.NET v3 SDK har flera viktiga ändringar, följande är de viktigaste stegen för att migrera ditt program:

1. Konvertera `DocumentCollectionInfo` instanserna till `Container` referenser för behållare för övervakade och lån.
1. Anpassningar som används `WithProcessorOptions` bör uppdateras för användning `WithLeaseConfiguration` och `WithPollInterval` intervall, `WithStartTime` [för start tid](./change-feed-processor.md#starting-time)och för `WithMaxItems` att definiera maximalt antal objekt.
1. Ställ in `processorName` på på `GetChangeFeedProcessorBuilder` för att matcha värdet som kon figurer ATS på `ChangeFeedProcessorOptions.LeasePrefix` eller Använd `string.Empty` annars.
1. Ändringarna levereras inte längre som en `IReadOnlyList<Document>` , i stället är det `IReadOnlyCollection<T>` där `T` är en typ som du måste definiera, det finns ingen bas objekts klass längre.
1. Om du vill hantera ändringarna behöver du inte längre någon implementering, i stället måste du [definiera ett ombud](change-feed-processor.md#implementing-the-change-feed-processor). Ombudet kan vara en statisk funktion eller, om du behöver underhålla tillstånd över körningar, kan du skapa en egen klass och skicka en instans metod som ombud.

Till exempel, om den ursprungliga koden för att bygga Change feed-processorn ser ut så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Den migrerade koden kommer att se ut så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

Och delegaten kan vara en statisk metod:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Tillstånds-och Lease container

Precis som i biblioteket för ändrings matnings processor använder funktionen ändra feed i .NET v3 SDK en [container behållare](change-feed-processor.md#components-of-the-change-feed-processor) för att lagra status. Men scheman är olika.

SDK v3-processen för att ändra feed identifierar alla gamla biblioteks tillstånd och migrerar den till det nya schemat automatiskt vid den första körningen av den migrerade program koden. 

Du kan på ett säkert sätt stoppa programmet med den gamla koden, migrera koden till den nya versionen, starta det migrerade programmet och eventuella ändringar som hände när programmet stoppades, hämtas och bearbetas av den nya versionen.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användnings exempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om att ändra flödes processor i följande artiklar:

* [Översikt över ändring av flödes processor](change-feed-processor.md)
* [Använda ändringsflödesövervakaren](how-to-use-change-feed-estimator.md)
* [Starttid för ändringsflödesprocessor](./change-feed-processor.md#starting-time)