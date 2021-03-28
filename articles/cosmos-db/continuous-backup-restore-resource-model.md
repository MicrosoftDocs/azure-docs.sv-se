---
title: Resurs modell för den Azure Cosmos DB funktionen för återställning av tidpunkt.
description: I den här artikeln beskrivs resursmodellen för Azure Cosmos DB-funktionen för återställning till tidpunkt. I artikeln beskrivs också de parametrar som kan användas för den kontinuerliga säkerhetskopieringen och de resurser som kan återställas i Azure Cosmos DB-API:t för SQL- och MongoDB-konton.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 065127fbeaabc415dd9a5fbe74f90d5060909d5d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641034"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Resurs modell för Azure Cosmos DB funktionen för återställning av tidpunkt (för hands version)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln förklaras resurs modellen för Azure Cosmos DB funktionen för återställning av tidpunkt (för hands version). I artikeln beskrivs också de parametrar som kan användas för den kontinuerliga säkerhetskopieringen och de resurser som kan återställas i Azure Cosmos DB-API:t för SQL- och MongoDB-konton.

## <a name="database-accounts-resource-model"></a>Databas kontots resurs modell

Databas kontots resurs modell uppdateras med några extra egenskaper som stöd för de nya återställnings scenarierna. Egenskaperna är **BackupPolicy, CreateMode och RestoreParameters.**

### <a name="backuppolicy"></a>BackupPolicy

En ny egenskap i säkerhets kopierings principen på konto nivå med namnet `Type` under `backuppolicy` parameter aktiverar kontinuerliga säkerhets kopierings-och återställnings funktioner vid en viss tidpunkt. Det här läget kallas **kontinuerlig säkerhets kopiering**. I den offentliga för hands versionen kan du bara ange det här läget när du skapar kontot. När den är aktive rad kommer alla behållare och databaser som skapats i det här kontot att ha kontinuerliga säkerhets kopierings-och återställnings funktioner för tidpunkter aktiverade som standard.

> [!NOTE]
> För närvarande är funktionen för automatisk återställning tillgänglig i en offentlig för hands version och den är tillgänglig för Azure Cosmos DB API för MongoDB och SQL-konton. När du har skapat ett konto med kontinuerligt läge kan du inte växla det till ett periodiskt läge.

### <a name="createmode"></a>CreateMode

Den här egenskapen anger hur kontot skapades. De möjliga värdena är *standard* och *restore*. Om du vill utföra en återställning ställer du in det här värdet på *Återställ* och anger lämpliga värden i `RestoreParameters` egenskapen.

### <a name="restoreparameters"></a>RestoreParameters

`RestoreParameters`Resursen innehåller information om återställnings åtgärden, inklusive konto-ID, tid att återställa och vilka resurser som behöver återställas.

|Egenskapens namn |Description  |
|---------|---------|
|restoreMode  | Återställnings läget ska vara *PointInTime* |
|restoreSource   |  InstanceId för det käll konto som återställningen ska initieras från.       |
|restoreTimestampInUtc  | Tidpunkt i UTC till vilken kontot ska återställas. |
|databasesToRestore   | Lista med `DatabaseRestoreSource` objekt som anger vilka databaser och behållare som ska återställas. Om det här värdet är tomt återställs hela kontot.   |

**DatabaseRestoreResource** – varje resurs representerar en enskild databas och alla samlingar under den databasen.

|Egenskapens namn |Description  |
|---------|---------|
|Databas | Namnet på databasen |
| collectionNames| Listan över behållare under den här databasen |

### <a name="sample-resource"></a>Exempel resurs

Följande JSON är en exempel databas konto resurs med kontinuerlig säkerhets kopiering aktive rad:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Återställas-resurser

Det finns en uppsättning nya resurser och API: er som hjälper dig att identifiera viktig information om resurser, som kan återställas, platser där de kan återställas och tidsstämplar när viktiga åtgärder utfördes på dessa resurser.

> [!NOTE]
> Alla API: er som används för att räkna upp dessa resurser kräver följande behörigheter:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Återställas Database-konto

Den här resursen innehåller en databas konto instans som kan återställas. Databas kontot kan antingen vara ett borttaget eller ett Live-konto. Den innehåller information som du kan använda för att hitta det käll databas konto som du vill återställa.

|Egenskapens namn |Description  |
|---------|---------|
| ID | Resursens unika identifierare. |
| accountName | Namnet på den globala databasens konto. |
| creationTime | Tiden i UTC när kontot skapades.  |
| deletionTime | Tiden i UTC när kontot togs bort.  Det här värdet är tomt om kontot är Live. |
| apiType | Azure Cosmos DB kontots API-typ. |
| restorableLocations | Listan över platser där kontot fanns. |
| restorableLocations: locationName | Region namnet för det regionala kontot. |
| restorableLocations: regionalDatabaseAccountInstanceI | GUID för det regionala kontot. |
| restorableLocations: creationTime | Tiden i UTC när det regionala kontot skapades.|
| restorableLocations: deletionTime | Tiden i UTC när det regionala kontot togs bort. Det här värdet är tomt om det regionala kontot är Live.|

Om du vill hämta en lista över alla återställas-konton, se [återställas Database Accounts-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorabledatabaseaccounts/list) eller [återställas Database Accounts-List by location](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorabledatabaseaccounts/listbylocation) artiklar.

### <a name="restorable-sql-database"></a>Återställas SQL-databas

Varje resurs innehåller information om en Mutations händelse, till exempel skapande och borttagning som inträffat på SQL Database. Den här informationen kan hjälpa dig i scenarier där databasen har tagits bort av misstag och om du behöver ta reda på när händelsen inträffade.

|Egenskapens namn |Description  |
|---------|---------|
| eventTimestamp | Tiden i UTC när databasen skapas eller tas bort. |
| ownerId | Namnet på SQL-databasen. |
| ownerResourceId | Resurs-ID för SQL-databasen|
| operationType | Åtgärds typen för den här databas händelsen. Följande är möjliga värden:<br/><ul><li>Skapa: händelse för skapande av databas</li><li>Ta bort: händelse för borttagning av databas</li><li>Ersätt: databas ändrings händelse</li><li>SystemOperation: händelse för databas ändring som utlöses av systemet. Den här händelsen har inte initierats av användaren</li></ul> |
| databas |Egenskaperna för SQL Database vid tidpunkten för händelsen|

Om du vill hämta en lista över alla databas mutationer, se artikeln [återställas SQL-databaser-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqldatabases/list) .

### <a name="restorable-sql-container"></a>Återställas SQL-behållare

Varje resurs innehåller information om en Mutations händelse, till exempel skapande och borttagning som inträffat på SQL-behållaren. Den här informationen kan vara till hjälp i scenarier där behållaren har ändrats eller tagits bort, och om du behöver ta reda på när händelsen inträffade.

|Egenskapens namn |Description  |
|---------|---------|
| eventTimestamp    | Tiden i UTC när den här behållar händelsen inträffade.|
| ownerId| SQL-behållarens namn.|
| ownerResourceId   | Resurs-ID för SQL-containern.|
| operationType | Åtgärds typen för den här behållar händelsen. Följande är möjliga värden: <br/><ul><li>Skapa: händelse för skapande av behållare</li><li>Ta bort: händelse för borttagning av behållare</li><li>Replace: händelse för ändring av behållare</li><li>SystemOperation: händelse för container ändring som utlöses av systemet. Den här händelsen har inte initierats av användaren</li></ul> |
| container | Egenskaperna för SQL-behållaren vid tidpunkten för händelsen.|

Om du vill hämta en lista över alla behållar mutationer under samma databas kan du läsa artikeln [återställas SQL containers-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqlcontainers/list) .

### <a name="restorable-sql-resources"></a>Återställas SQL-resurser

Varje resurs representerar en enda databas och alla behållare under den databasen.

|Egenskapens namn |Description  |
|---------|---------|
| Databas  | Namnet på SQL-databasen.
| collectionNames   | Listan över SQL-behållare under den här databasen.|

Om du vill hämta en lista över SQL Database-och container-kombination som finns på kontot vid den aktuella tidsstämpeln och platsen, se artikeln [återställas SQL Resources-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablesqlresources/list) .

### <a name="restorable-mongodb-database"></a>Återställas MongoDB-databas

Varje resurs innehåller information om en Mutations händelse, till exempel skapande och borttagning som har inträffat i MongoDB-databasen. Den här informationen kan vara till hjälp i scenariot där databasen har tagits bort av misstag och användaren måste ta reda på när händelsen inträffade.

|Egenskapens namn |Description  |
|---------|---------|
|eventTimestamp| Tiden i UTC när den här databas händelsen inträffade.|
| ownerId| Namnet på MongoDB-databasen. |
| ownerResourceId   | Resurs-ID för MongoDB-databasen. |
| operationType |   Åtgärds typen för den här databas händelsen. Följande är möjliga värden:<br/><ul><li> Skapa: händelse för skapande av databas</li><li> Ta bort: händelse för borttagning av databas</li><li> Ersätt: databas ändrings händelse</li><li> SystemOperation: händelse för databas ändring som utlöses av systemet. Den här händelsen har inte initierats av användaren </li></ul> |

Om du vill hämta en lista över alla databas mutationer, se artikeln [återställas MongoDB databases-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbdatabases/list) .

### <a name="restorable-mongodb-collection"></a>Återställas MongoDB-samling

Varje resurs innehåller information om en Mutations händelse, till exempel skapande och borttagning som inträffat i MongoDB-samlingen. Den här informationen kan hjälpa dig i scenarier där samlingen har ändrats eller tagits bort, och användaren måste ta reda på när händelsen inträffade.

|Egenskapens namn |Description  |
|---------|---------|
| eventTimestamp |Tiden i UTC när den här samlings händelsen inträffade. |
| ownerId| Namnet på MongoDB-samlingen. |
| ownerResourceId   | Resurs-ID för MongoDB-samlingen. |
| operationType |Åtgärds typen för den här samlings händelsen. Följande är möjliga värden:<br/><ul><li>Skapa: händelse för skapande av samling</li><li>Ta bort: händelse för borttagning av samling</li><li>Replace: händelse för mängd ändring</li><li>SystemOperation: samlings ändrings händelse som utlöses av systemet. Den här händelsen har inte initierats av användaren</li></ul> |

Om du vill hämta en lista över alla behållar mutationer under samma databas kan du läsa artikeln [återställas MongoDB Collections-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbcollections/list) .

### <a name="restorable-mongodb-resources"></a>Återställas MongoDB-resurser

Varje resurs representerar en enda databas och alla samlingar under den databasen.

|Egenskapens namn |Description  |
|---------|---------|
| Databas  |Namnet på MongoDB-databasen. |
| collectionNames | Listan med MongoDB-samlingar under den här databasen. |

Om du vill hämta en lista över alla kombinationer av MongoDB-databaser och samlingar som finns på kontot vid den aktuella tidsstämpeln och platsen, se artikeln [återställas MongoDB Resources-List](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/restorablemongodbresources/list) .

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
