---
title: Använd Azure CLI för att konfigurera kontinuerlig säkerhets kopiering och tidpunkts återställning i Azure Cosmos DB.
description: Lär dig hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure CLI.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377337"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Konfigurera och hantera kontinuerlig säkerhets kopiering och tidpunkts återställning (för hands version) – med Azure CLI
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB funktionen för återställning av tidpunkt (för hands version) hjälper dig att återställa från en oavsiktlig ändring i en behållare, för att återställa ett borttaget konto, en databas eller en behållare eller för att återställa till en region (där säkerhets kopior fanns). Med läget för kontinuerlig säkerhets kopiering kan du återställa till en viss tidpunkt inom de senaste 30 dagarna.

Den här artikeln beskriver hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure CLI.

## <a name="install-azure-cli"></a><a id="install"></a>Installera Azure CLI

1. Installera den senaste versionen av Azure CLI

   * Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) eller högre version än 2.17.1.
   * Om du redan har installerat CLI kör `az upgrade` du kommandot för att uppdatera till den senaste versionen. Det här kommandot fungerar bara med CLI-versionen som är större än 2,11. Om du har en tidigare version använder du länken ovan för att installera den senaste versionen.

1. Installera `cosmosdb-preview` CLI-tillägget.

   * Kommandona för återställning av tidpunkt är tillgängliga under `cosmosdb-preview` tillägg.
   * Du kan installera det här tillägget genom att köra följande kommando: `az extension add --name cosmosdb-preview`
   * Du kan avinstallera tillägget genom att köra följande kommando: `az extension remove --name cosmosdb-preview`

1. Logga in och välj din prenumeration

   * Logga in på ditt Azure-konto med `az login` kommandot.
   * Välj den prenumeration som krävs med hjälp av `az account set -s <subscriptionguid>` kommandot.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Etablera ett SQL API-konto med kontinuerlig säkerhets kopiering

För att etablera ett SQL API-konto med kontinuerlig säkerhets kopiering ska ett extra argument `--backup-policy-type Continuous` skickas tillsammans med det vanliga etablerings kommandot. Följande kommando är ett exempel på ett enda regions Skriv konto `pitracct2` som heter med kontinuerlig säkerhets kopierings policy som skapats i regionen *västra USA* under *myrg* resurs grupp:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Etablera ett Azure Cosmos DB-API för MongoDB-konto med kontinuerlig säkerhets kopiering

Följande kommando visar ett exempel på ett enda regions Skriv konto `pitracct3` som heter med kontinuerlig säkerhets kopierings princip som skapade regionen *västra USA* under *myrg* resurs grupp:

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Utlös en återställnings åtgärd med CLI

Det enklaste sättet att utlösa en återställning är genom att utfärda kommandot Restore med namnet på mål kontot, käll kontot, platsen, resurs gruppen, tidsstämpeln (i UTC) och eventuellt databas-och behållar namn. Här följer några exempel på hur du utlöser återställnings åtgärden:

1. Skapa ett nytt Azure Cosmos DB konto genom att återställa från ett befintligt konto.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Skapa ett nytt Azure Cosmos DB konto genom att bara återställa valda databaser och behållare från ett befintligt databas konto.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Räkna upp återställas-resurser för SQL API

Uppräknings kommandon som beskrivs nedan hjälper dig att identifiera de resurser som är tillgängliga för återställning vid olika tidsstämplar. Dessutom ger de också en feed av viktiga händelser för återställas-kontot,-databasen och container resurserna.

**Lista alla konton som kan återställas i den aktuella prenumerationen**

Kör följande CLI-kommando för att visa en lista över alla konton som kan återställas i den aktuella prenumerationen

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

Svaret innehåller alla databas konton (både Live och borttaget) som kan återställas och de regioner som de kan återställas från:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Precis som `CreationTime` eller `DeletionTime` för kontot finns det `CreationTime` också en eller `DeletionTime` för regionen. Med de här tiderna kan du välja rätt region och ett giltigt tidsintervall för att återställa till den regionen.

**Visa en lista över alla versioner av databaser i ett Live Database-konto**

Genom att visa alla versioner av databaserna kan du välja rätt databas i ett scenario där den faktiska tiden det finns en okänd databas.

Kör följande CLI-kommando för att visa en lista över alla versioner av-databaser. Det här kommandot fungerar bara med Live-konton. `instanceId` `location` Parametrarna och hämtas från-och- `name` `location` egenskaperna i `az cosmosdb restorable-database-account list` kommandots svar. InstanceId-attributet är också en egenskap för det käll databas konto som återställs:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Detta kommando visar nu när en databas har skapats och tagits bort.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Visa en lista över alla versioner av SQL-behållare för en databas i ett Live Database-konto**

Använd följande kommando för att visa en lista över alla versioner av SQL-behållare. Det här kommandot fungerar bara med Live-konton. `databaseRid`Parametern är `ResourceId` av den databas som du vill återställa. Det är värdet för `ownerResourceid` attributet som finns i `az cosmosdb sql restorable-database list` kommandots svar.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Detta kommando utdata visar en lista över åtgärder som utförs på alla behållare i den här databasen:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Hitta databaser eller behållare som kan återställas vid en bestämd tidsstämpel**

Använd följande kommando för att hämta en lista över databaser eller behållare som kan återställas vid en bestämd tidsstämpel. Det här kommandot fungerar bara med Live-konton.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Räkna upp återställas-resurser för MongoDB API-konto

Uppräknings kommandon som beskrivs nedan hjälper dig att identifiera de resurser som är tillgängliga för återställning vid olika tidsstämplar. Dessutom ger de också en feed av viktiga händelser för återställas-kontot,-databasen och container resurserna. Precis som med SQL API kan du använda `az cosmosdb` kommandot men med `mongodb` parametern som parameter i stället för `sql` . Dessa kommandon fungerar bara för Live-konton.

**Visa en lista över alla versioner av MongoDB-databaser i ett Live Database-konto**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Visa en lista över alla versioner av MongoDB-samlingar i en databas i ett Live Database-konto**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Visa en lista över alla resurser i ett MongoDB Database-konto som är tillgängliga för återställning vid en specifik tidsstämpel och region**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Resurs modell för kontinuerligt säkerhets kopierings läge](continuous-backup-restore-resource-model.md)
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
