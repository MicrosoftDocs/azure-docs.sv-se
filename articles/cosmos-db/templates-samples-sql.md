---
title: Azure Resource Manager mallar för Azure Cosmos DB Core (SQL API)
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: 7163658024d150a7c5d75c3b3ac0b6b6b29cd3cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105037316"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-mallar för Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln innehåller bara exempel på Azure Resource Manager mallar för Core-API-konton (SQL). Du kan också hitta mall exempel för API: er för [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md)och [Table](templates-samples-table.md) .

## <a name="core-sql-api"></a>Core (SQL) API

|**Mall**|**Beskrivning**|
|---|---|
|[Skapa ett Azure Cosmos-konto, en databas, container med autoskalning av data flöde](manage-with-templates.md#create-autoscale) | Den här mallen skapar ett kärn-API-konto (SQL) i två regioner, en databas och container med autoskalning av data flöde. |
|[Skapa ett Azure Cosmos-konto, en databas, container med analys lager](manage-with-templates.md#create-analytical-store) | Den här mallen skapar ett huvud-API-konto i en region med en behållare som kon figurer ATS med analytiskt TTL aktiverat och alternativet för att använda manuell eller autoskalning av data flöde. |
|[Skapa ett Azure Cosmos-konto, en databas, behållare med standard (manuell) data flöde](manage-with-templates.md#create-manual) | Den här mallen skapar ett kärn-API-konto (SQL) i två regioner, en databas och en behållare med standard data flöde. |
|[Skapa ett Azure Cosmos-konto, en databas och en behållare med en lagrad procedur, utlösare och UDF](manage-with-templates.md#create-sproc) | Den här mallen skapar ett Core (SQL) API-konto i två regioner med en lagrad procedur, utlösare och UDF för en behållare. |
|[Skapa ett Azure Cosmos-konto med Azure AD-identitet, roll definitioner och roll tilldelning](manage-with-templates.md#create-rbac) | Den här mallen skapar ett kärn-API-konto (SQL) med Azure AD-identitet, roll definitioner och roll tilldelning på ett huvud namn för tjänsten. |
|[Skapa en privat slut punkt för ett befintligt Azure Cosmos-konto](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Den här mallen skapar en privat slut punkt för ett befintligt API-konto i Azure Cosmos Core (SQL) i ett befintligt virtuellt nätverk. |
|[Skapa ett Azure Cosmos-konto på den kostnads fria nivån](manage-with-templates.md#free-tier) |  Den här mallen skapar ett API-konto för Azure Cosmos DB Core (SQL) på den kostnads fria nivån. |

Se [Azure Resource Manager referens för Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) sidan för referens dokumentationen.
