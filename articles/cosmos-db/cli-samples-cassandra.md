---
title: Azure CLI-exempel för Azure Cosmos DB API för Cassandra
description: Azure CLI-exempel för Azure Cosmos DB API för Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2dbae196b981b792b47e6c12e500398f772d8451
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342138"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Azure CLI-exempel för Azure Cosmos DB API för Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

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

## <a name="cassandra-api-samples"></a>API för Cassandra exempel

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto, ett tecken utrymme och en tabell](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, ett blank steg och en tabell för API för Cassandra. |
| [Skapa ett Azure Cosmos-konto, nyckel utrymme och tabell med autoskalning](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, nyckel utrymme och tabell med autoskalning för API för Cassandra. |
| [Data flödes åtgärder](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard-genomflöde i ett nyckel utrymme och en tabell.|
| [Lås resurser från borttagning](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||
