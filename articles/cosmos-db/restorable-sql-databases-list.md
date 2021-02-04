---
title: Visa lista återställas SQL API-databaser i Azure Cosmos DB med REST API
description: Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB SQL-databaser under återställas-kontot. Detta hjälper i scenariot där databasen har tagits bort av misstag för att hämta borttagnings tiden.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539509"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Visa lista återställas SQL API-databaser i Azure Cosmos DB med REST API

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB SQL-databaser under återställas-kontot. Detta hjälper i scenariot där databasen har tagits bort av misstag för att hämta borttagnings tiden. Detta API kräver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörighet

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-parametrar

| Name | I | Krävs | Typ | Description |
| --- | --- | --- | --- | --- |
| **Instans** | path | True |sträng| InstanceId-GUID för ett återställas-databas konto. |
| **sökvägen** | path | True | sträng| Azure Cosmos DB region, med blank steg mellan ord och varje ord med versaler. |
| **subscriptionId** | path | True | sträng| ID för mål prenumerationen. |
| **API-version** | DocumentDB | True | sträng | Den API-version som ska användas för den här åtgärden. |

## <a name="responses"></a>Svar

| Namn | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| Åtgärden har slutförts. |
| Andra status koder | [DefaultErrorResponse](#defaulterrorresponse)| Felsvar som beskriver varför åtgärden misslyckades. |

## <a name="examples"></a>Exempel

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Exempelförfrågan**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Exempelsvar**

Status kod: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitioner

| Definition | Beskrivning | | --- || --- | | [Databas](#database) | Azure Cosmos DB SQL Database-resurs objekt | | [DefaultErrorResponse](#defaulterrorresponse) | Ett fel svar från tjänsten. | | [ErrorResponse](#errorresponse) | Fel svar. | | [OperationType](#operationtype) | Enum för att ange händelsens åtgärds typ. | | [Resurs](#resource) | Resursen för en Azure Cosmos DB SQL Database-händelse | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | En Azure Cosmos DB SQL Database-händelse | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Egenskaperna för en Azure Cosmos DB SQL Database-händelse | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | Svar på list åtgärden som innehåller SQL Database-händelser och deras egenskaper. |

### <a name="database"></a><a id="database"></a>Databas

Azure Cosmos DB SQL Database-resurs objekt

| **Namn**  |  **Typ**  |  **Beskrivning** | | --- || --- | ---| | _colls | sträng | En systemgenererad egenskap som anger den adresser bara sökvägen för samlings resursen. | | _etag | sträng | En systemgenererad egenskap som representerar den resurs-etag som krävs för optimistisk concurrency-kontroll. | | _rid | sträng | En systemgenererad egenskap. En unik identifierare. | | _self | sträng | En systemgenererad egenskap som anger den adresser bara sökvägen till databas resursen. | | _ts | | En systemgenererad egenskap som anger den senast uppdaterade tidsstämpeln för resursen. | | _users | sträng | En systemgenererad egenskap som anger den adresser bara sökvägen till användarens resurs. | | ID | sträng | Azure Cosmos DB SQL-databasens namn |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Ett fel svar från tjänsten.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| fel | [ErrorResponse](#errorresponse)| Fel svar. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Fel svar.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| kod |sträng| Felkod. |
| meddelande |sträng| Fel meddelande som anger varför åtgärden misslyckades. |

### <a name="operationtype"></a><a id="operationtype"></a>Åtgärdstyp

Enum för att ange händelsens åtgärds typ.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| Skapa |sträng|händelse för att skapa databas|
| Ta bort |sträng|händelse för borttagning av databas|
| Ersätt |sträng|händelse för databas ändring|
| SystemOperation |sträng|databas ändrings händelse utlöst av systemet. Den här händelsen har inte initierats av användaren|

### <a name="resource"></a><a id="resource"></a>Resurs

Resurs för en Azure Cosmos DB SQL Database-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| _rid |sträng| En systemgenererad egenskap. En unik identifierare. |
| databas |[Databas](#database)| Azure Cosmos DB SQL Database-resurs objekt |
| eventTimestamp |sträng| Tiden då den här databas händelsen inträffade. |
| operationType |[Åtgärdstyp](#operationtype)| Åtgärds typen för den här databas händelsen. |
| ownerId |sträng| Namnet på SQL-databasen. |
| ownerResourceId |sträng| Resurs-ID för SQL-databasen. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

En Azure Cosmos DB SQL Database-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| ID |sträng| Det unika resurs-ID: n för den Azure Resource Manager resursen. |
| name |sträng| Namnet på den Azure Resource Manager resursen. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| Egenskaperna för en SQL Database-händelse. |
| typ |sträng| Typ av Azure-resurs. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Egenskaperna för en Azure Cosmos DB SQL Database-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| resource |[Resurs](#resource)| Resurs för en Azure Cosmos DB SQL Database-händelse |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

Svar på list åtgärden som innehåller SQL Database-händelser och deras egenskaper.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| värde |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Lista över SQL Database-händelser och deras egenskaper. |

## <a name="next-steps"></a>Nästa steg

* [Visa lista över återställas-behållare](restorable-sql-containers-list.md) i Azure Cosmos DB SQL API med REST API.
* [Resurs modell](continuous-backup-restore-resource-model.md) för kontinuerligt säkerhets kopierings läge.