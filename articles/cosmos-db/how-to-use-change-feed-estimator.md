---
title: Använd uppskattningen Change feed-Azure Cosmos DB
description: Lär dig hur du använder en Change feed-uppskattning för att analysera förloppet för din ändrings flödes processor
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a44557d15f437317c2b5fa659ab8d4ca3c208edf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339851"
---
# <a name="use-the-change-feed-estimator"></a>Använd uppskattningen ändra feed
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln beskrivs hur du kan övervaka förloppet för dina [byte av Change feed processor](./change-feed-processor.md) när de läser ändrings flödet.

## <a name="why-is-monitoring-progress-important"></a>Varför är övervaknings förloppet viktigt?

Processorn för ändrings flöden fungerar som en pekare som rör sig framåt i [ändrings flödet](./change-feed.md) och levererar ändringarna i en ombuds implementering. 

Din distribution av Change feed processor kan bearbeta ändringar i en viss takt baserat på de resurser som är tillgängliga, t. ex. CPU, minne, nätverk och så vidare.

Om den här hastigheten är långsammare än den hastighet med vilken dina ändringar sker i din Azure Cosmos-behållare börjar processorn att vänta.

Att identifiera det här scenariot hjälper dig att förstå om vi behöver skala vår distribution av Change feed processor.

## <a name="implement-the-change-feed-estimator"></a>Implementera uppskattningen ändra feed

Precis som med [change feed-processorn](./change-feed-processor.md)fungerar föruppskattningen ändra feed som en push-modell. Uppskattningen mäter skillnaden mellan det senast bearbetade objektet (definieras av statusen för behållaren lån) och den senaste ändringen i behållaren och push-överför värdet till ett ombud. Intervallet då mätningen utförs kan också anpassas med standardvärdet 5 sekunder.

Exempel: om din Change feed-processor definieras som detta:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Det korrekta sättet att initiera en uppskattare för att mäta att processorn skulle använda `GetChangeFeedEstimatorBuilder` så här:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Där både processorn och uppskattningen delar samma `leaseContainer` namn.

De andra två parametrarna är ombudet, som kommer att få ett nummer som representerar **hur många ändringar som väntar på att läsas** av processorn och det tidsintervall som du vill att den här mätningen ska vidtas.

Ett exempel på ett ombud som tar emot uppskattningen är:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Du kan skicka denna uppskattning till din övervaknings lösning och använda den för att förstå hur din förloppet fungerar över tid.

> [!NOTE]
> Det går inte att distribuera uppskattningen för att byta feed som en del av din process för ändrings flöden, och inte heller vara en del av samma projekt. Det kan vara oberoende och köras i en helt annan instans. Du behöver bara använda samma namn och låne konfiguration.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användnings exempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om att ändra flödes processor i följande artiklar:

* [Översikt över ändring av flödes processor](change-feed-processor.md)
* [Starttid för ändringsflödesprocessor](./change-feed-processor.md#starting-time)