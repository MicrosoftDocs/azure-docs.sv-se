---
title: Azure CLI-exempel för Azure Cosmos DB Gremlin-API
description: Azure CLI-exempel för Azure Cosmos DB Gremlin-API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ff557e5ca0769b68cb74f4b790e3678da7c3c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342121"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-gremlin-api"></a>Azure CLI-exempel för Azure Cosmos DB Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure Cosmos DB. Använd länkarna till höger för att navigera till API-/regionsspecifika exempel. Vanliga exempel är samma för alla API: er. Referens sidor för alla Azure Cosmos DB CLI-kommandon är tillgängliga i [Azure CLI-referensen](/cli/azure/cosmosdb). Du kan också hitta Azure Cosmos DB CLI-skript exempel i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

De här exemplen kräver Azure CLI version 2.12.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Vanliga exempel

Dessa exempel gäller alla Azure Cosmos DB API: er

|Uppgift | Beskrivning |
|---|---|
| [Lägga till eller redundansväxla regioner](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Lägg till en region, ändra prioritet för redundans, Utlös en manuell redundansväxling.|
| [Konto nycklar och anslutnings strängar](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Visa lista med konto nycklar, skrivskyddade nycklar, återskapa nycklar och lista anslutnings strängar.|
| [Skydda med IP-brandvägg](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto med IP-brandvägg konfigurerad.|
| [Säkert nytt konto med tjänst slut punkter](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto och skydda med tjänst slut punkter.|
| [Skydda det befintliga kontot med tjänstens slut punkter](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Uppdatera ett Cosmos-konto för att skydda med tjänst slut punkter när under nätet slutligen har kon figurer ATS.|
|||

## <a name="gremlin-api-samples"></a>Gremlin API-exempel

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto, en databas och en graf](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en graf för Gremlin-API. |
| [Skapa ett Azure Cosmos-konto, en databas och en graf med autoskalning](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en graf med autoskalning för Gremlin-API. |
| [Data flödes åtgärder](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard-genomflöde i en databas och graf.|
| [Lås resurser från borttagning](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||
