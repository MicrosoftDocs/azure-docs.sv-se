---
title: Visa en lista över återställas-databaser i Azure Cosmos DB API för MongoDB med hjälp av REST API
description: Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB MongoDB-databaser under återställas-kontot. Detta hjälper i scenariot där databasen har tagits bort av misstag för att hämta borttagnings tiden.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 64288f67728e59c32c662a6640d60daf52c53fe1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527928"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Visa en lista över återställas-databaser i Azure Cosmos DB API för MongoDB med hjälp av REST API

Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB MongoDB-databaser under återställas-kontot. Detta hjälper i scenariot där databasen har tagits bort av misstag för att hämta borttagnings tiden. Detta API kräver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörighet

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| Åtgärden har slutförts. |
| Andra status koder | [DefaultErrorResponse](#defaulterrorresponse)| Felsvar som beskriver varför åtgärden misslyckades.|

## <a name="examples"></a>Exempel

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Exempel förfrågan**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Exempelsvar**

Status kod: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitioner

| Definition | Beskrivning | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Ett fel svar från tjänsten. | | [ErrorResponse](#errorresponse) | Fel svar. | | [OperationType](#operationtype) | Enum för att ange händelsens åtgärds typ. | | [Resurs](#resource) | Resursen för ett Azure Cosmos DB-API för MongoDB-databas händelse | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Ett Azure Cosmos DB-API för MongoDB databas händelse | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | Egenskaperna för ett Azure Cosmos DB-API för MongoDB-databas händelse | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | Svar på list åtgärden som innehåller Azure Cosmos DB-API: et för MongoDB databas händelser och deras egenskaper. |

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

### <a name="resource"></a><a id="resource"></a>Resurs

Resurs för en Azure Cosmos DB-API för databas händelsen MongoDB

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| _rid |sträng| En systemgenererad egenskap. En unik identifierare. |
| eventTimestamp |sträng| Tiden då den här databas händelsen inträffade. |
| operationType |[Åtgärdstyp](#operationtype)| Åtgärds typen för den här databas händelsen.  |
| ownerId |sträng| Namnet på Azure Cosmos DB-API: et för MongoDB-databasen.|
| ownerResourceId |sträng| Resurs-ID Azure Cosmos DB API för MongoDB Database. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

En Azure Cosmos DB-API för MongoDB-databas-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| ID |sträng| Det unika resurs-ID: n för den Azure Resource Manager resursen. |
| name |sträng| Namnet på Resource Manager-resursen. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| Egenskaperna för ett Azure Cosmos DB-API för MongoDB-databas-händelse. |
| typ |sträng| Typ av Azure-resurs. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

Egenskaperna för ett Azure Cosmos DB-API för MongoDB-databas-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| resource |[Resurs](#resource)| Resurs för en Azure Cosmos DB-API för databas händelsen MongoDB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

Svar på list åtgärden som innehåller databas händelserna och deras egenskaper.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| värde |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Lista över databas händelser och deras egenskaper. |

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över återställas-resurser](restorable-mongodb-resources-list.md)  i Azure Cosmos DB API för MongoDB med hjälp av REST API.
* [Visa en lista över återställas-samlingar](restorable-mongodb-collections-list.md)  i Azure Cosmos DB API för MongoDB med hjälp av REST API.
* [Resurs modell](continuous-backup-restore-resource-model.md) för kontinuerligt säkerhets kopierings läge.