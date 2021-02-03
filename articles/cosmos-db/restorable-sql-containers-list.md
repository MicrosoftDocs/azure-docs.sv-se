---
title: Visa lista återställas SQL API-behållare i Azure Cosmos DB att använda REST API
description: Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB SQL-behållare under en angiven databas. Detta hjälper i scenariot där behållaren har tagits bort av misstag.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 90018d3e1b793575830ba34756ad685927612006
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527953"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Visa lista återställas SQL API-behållare i Azure Cosmos DB att använda REST API

Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB SQL-behållare under en angiven databas. Detta hjälper i scenariot där behållaren har tagits bort av misstag. Detta API kräver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörighet

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Med valfria parametrar:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>URI-parametrar

| Name | I | Krävs | Typ | Description |
| --- | --- | --- | --- | --- |
| **Instans** | path | True |sträng| InstanceId-GUID för ett återställas-databas konto. |
| **sökvägen** | path | True | sträng| Azure Cosmos DB region, med blank steg mellan ord och varje ord med versaler. |
| **subscriptionId** | path | True | sträng| ID för mål prenumerationen. |
| **API-version** | DocumentDB | True | sträng | Den API-version som ska användas för den här åtgärden. |
| **restorableSqlDatabaseRid** | DocumentDB | |sträng| Resurs-ID för SQL-databasen. |

## <a name="responses"></a>Svar

| Namn | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| Åtgärden har slutförts. |
| Andra status koder | [DefaultErrorResponse](#defaulterrorresponse)| Felsvar som beskriver varför åtgärden misslyckades. |

## <a name="examples"></a>Exempel

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Exempel förfrågan**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Exempelsvar**

Status kod: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitioner

| Definition | Beskrivning | | --- || --- | | [Container](#container) | Azure Cosmos DB SQL container Resource-objekt | | [DefaultErrorResponse](#defaulterrorresponse) | Ett fel svar från tjänsten. | | [ErrorResponse](#errorresponse) | Fel svar. | | [OperationType](#operationtype) | Enum för att ange händelsens åtgärds typ. | | [Resurs](#resource) | Resursen för en Azure Cosmos DB SQL container event | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | En Azure Cosmos DB SQL container event | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Egenskaperna för en Azure Cosmos DB SQL container event | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | Svar på list åtgärden som innehåller SQL container-händelser och deras egenskaper. |

### <a name="container"></a><a id="container"></a>Container

Resurs objekt för Azure Cosmos DB SQL-behållare

| **Namn**  |  **Typ**  |  **Beskrivning** | | --- || --- | ---| | _etag | sträng | En systemgenererad egenskap som representerar den resurs-etag som krävs för optimistisk concurrency-kontroll. | | _rid | sträng | En systemgenererad egenskap. En unik identifierare. | | _self | sträng | En systemgenererad egenskap som anger den adresser bara sökvägen till behållar resursen. | | _ts | | En systemgenererad egenskap som anger den senast uppdaterade tidsstämpeln för resursen. | | ID | sträng | Namnet på Azure Cosmos DB SQL-container |

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
| Skapa |sträng|händelse för skapande av behållare|
| Ta bort |sträng|borttagnings händelse för behållare|
| Ersätt |sträng|händelse för ändring av behållare|
| SystemOperation |sträng|container ändrings händelse utlöst av systemet. Den här händelsen har inte initierats av användaren|

### <a name="resource"></a><a id="resource"></a>Resurs

Resursen för en Azure Cosmos DB SQL container-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| _rid |sträng| En systemgenererad egenskap. En unik identifierare. |
| container |[Container](#container)| Resurs objekt för Azure Cosmos DB SQL-behållare |
| eventTimestamp |sträng| Tiden då container händelsen inträffade. |
| operationType |[Åtgärdstyp](#operationtype)| Åtgärds typen för den här behållar händelsen. |
| ownerId |sträng| SQL-behållarens namn. |
| ownerResourceId |sträng| Resurs-ID för SQL-containern. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

En Azure Cosmos DB SQL container-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | ---
| ID |sträng| Det unika resurs-ID: n för den Azure Resource Manager resursen. |
| name |sträng| Namnet på den Azure Resource Manager resursen. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| Egenskaperna för en SQL container-händelse. |
| typ |sträng| Typ av Azure-resurs. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Egenskaperna för en Azure Cosmos DB SQL container-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| resource |[Resurs](#resource)| Resursen för en Azure Cosmos DB SQL container-händelse |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

Svar på list åtgärden som innehåller SQL container-händelser och deras egenskaper.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| värde |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Lista över SQL container-händelser och deras egenskaper. |

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över återställas-databaser](restorable-mongodb-databases-list.md)  i Azure Cosmos DB API för MongoDB med hjälp av REST API.
* [Resurs modell](continuous-backup-restore-resource-model.md) för kontinuerligt säkerhets kopierings läge.