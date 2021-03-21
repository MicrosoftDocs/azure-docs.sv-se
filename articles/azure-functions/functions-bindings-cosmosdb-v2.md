---
title: Azure Cosmos DB bindningar för functions 2. xD och högre
description: Förstå hur du använder Azure Cosmos DB utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106934"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Cosmos DB utlösare och bindningar för Azure Functions 2. x och högre översikt

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2 och högre](functions-bindings-cosmosdb-v2.md)

I den här uppsättningen artiklar förklaras hur du arbetar med [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) -bindningar i Azure Functions 2. x och högre. Azure Functions stöd för utlösare, indata och utgående bindningar för Azure Cosmos DB.

| Action | Typ |
|---------|---------|
| Köra en funktion när ett Azure Cosmos DB-dokument skapas eller ändras | [Utlösare](./functions-bindings-cosmosdb-v2-trigger.md) |
| Läsa ett Azure Cosmos DB dokument | [Binda in](./functions-bindings-cosmosdb-v2-input.md) |
| Spara ändringar i ett Azure Cosmos DB dokument  |[Utgående bindning](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Den här referensen gäller för [Azure Functions version 2. x och högre](functions-versions.md).  Information om hur du använder dessa bindningar i functions 1. x finns [Azure Cosmos DB bindningar för Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Den här bindningen hade ursprungligen namnet DocumentDB. I functions version 2. x och högre är utlösaren, bindningarna och paketet alla namngivna Cosmos DB.

## <a name="supported-apis"></a>API:er som stöds

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg] rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ./functions-bindings-register.md
[Tillägg för Azure-verktyg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x-appar har automatiskt en referens till [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x.

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när ett Azure Cosmos DB-dokument skapas eller ändras (utlösare)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Läsa ett Azure Cosmos DB dokument (ingående bindning)](./functions-bindings-cosmosdb-v2-input.md)
- [Spara ändringar i ett Azure Cosmos DB dokument (utgående bindning)](./functions-bindings-cosmosdb-v2-output.md)