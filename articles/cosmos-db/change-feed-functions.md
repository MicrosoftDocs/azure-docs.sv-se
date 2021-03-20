---
title: Använda Azure Cosmos DB ändra feed med Azure Functions
description: Använd Azure Functions för att ansluta till Azure Cosmos DB ändra feed. Senare kan du skapa återaktiverade Azure-funktioner som aktive ras för varje ny händelse.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7f4903cf29f15132db91e47d78efe5a556efd937
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340251"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Händelsebaserade arkitekturer utan server med Azure Cosmos DB och Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Functions är det enklaste sättet att ansluta till [ändrings flödet](change-feed.md). Du kan skapa små reaktiva Azure Functions som aktive ras automatiskt vid varje ny händelse i din Azure Cosmos-behållares ändrings flöde.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="Server lösa händelsebaserade funktioner som fungerar med Azure Functions utlösare för Cosmos DB" border="false":::

Med [Azure Functions-utlösaren för Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)kan du utnyttja skalningen av [processen för byte](./change-feed-processor.md)och pålitlig händelse identifiering utan att behöva underhålla någon [arbets infrastruktur](./change-feed-processor.md). Fokusera bara på din Azure Function-logik utan att oroa dig över resten av pipeline för händelse-källa. Du kan till och med blanda utlösaren med andra [Azure Functions bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> För närvarande stöds Azure Functions utlösare för Cosmos DB endast för användning med Core-API: et (SQL).

## <a name="requirements"></a>Krav

Om du vill implementera ett Server lös händelsebaserat flöde måste du:

* **Den övervakade behållaren**: den övervakade behållaren är den Azure Cosmos-behållare som övervakas, och den lagrar data som ändrings flödet genereras från. Eventuella infogningar, uppdateringar av den övervakade behållaren visas i behållarens ändrings flöde.
* **Leasing container**: Lease-containern upprätthåller tillstånd för flera och dynamiska Azure Function-instanser i Server och möjliggör dynamisk skalning. Denna Lease-behållare kan skapas manuellt eller skapas automatiskt av Azure Functions-utlösaren för Cosmos DB. Om du vill skapa en låne container automatiskt anger du flaggan *CreateLeaseCollectionIfNotExists* i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Partitionerade leasing behållare måste ha en definition av `/id` partitionsnyckel.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Skapa din Azure Functions-utlösare för Cosmos DB

Att skapa en Azure-funktion med en Azure Functions-utlösare för Cosmos DB stöds nu i alla Azure Functions IDE-och CLI-integrationer:

* [Visual Studio-tillägg](../azure-functions/functions-develop-vs.md) för Visual Studio-användare.
* [Visual Studio Code-tillägg](/azure/developer/javascript/tutorial-vscode-serverless-node-01) för Visual Studio Code-användare.
* Och slutligen [Core CLI-verktyg](../azure-functions/functions-run-local.md#create-func) för en plattforms oberoende IDE oberoende-upplevelse.

## <a name="run-your-trigger-locally"></a>Kör din utlösare lokalt

Du kan köra din [Azure-funktion lokalt](../azure-functions/functions-develop-local.md) med [Azure Cosmos DB emulatorn](./local-emulator.md) för att skapa och utveckla serverbaserade händelsebaserade flöden utan en Azure-prenumeration eller utan kostnad.

Om du vill testa Live-scenarier i molnet kan du [prova Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan att något kredit kort eller någon Azure-prenumeration krävs.

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta att lära dig mer om ändrings flöden i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Sätt att läsa ändrings flöde](read-change-feed.md)
* [Använda biblioteket Change feed processor](change-feed-processor.md)
* [Så här arbetar du med biblioteket Change feed processor](change-feed-processor.md)
* [Databas bearbetning utan server med hjälp av Azure Cosmos DB och Azure Functions](serverless-computing-database.md)
