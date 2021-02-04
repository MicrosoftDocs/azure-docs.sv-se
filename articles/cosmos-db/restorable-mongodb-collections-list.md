---
title: Visa lista återställas Collections in Azure Cosmos DB MongoDB API-REST API
description: Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB MongoDB-samlingar under en speciell databas. Detta hjälper i scenariot där behållaren har tagits bort av misstag.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537551"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Visa en lista över återställas-samlingar i Azure Cosmos DB API för MongoDB med hjälp av REST API

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Visa händelse matningen av alla mutationer som gjorts på alla Azure Cosmos DB-API: er för MongoDB-samlingar under en speciell databas. Detta hjälper i scenariot där behållaren har tagits bort av misstag. Detta API kräver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörighet

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Med valfria parametrar:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>URI-parametrar

| Name | I | Krävs | Typ | Description |
| --- | --- | --- | --- | --- |
| **Instans** | path | True |sträng| InstanceId-GUID för ett återställas-databas konto. |
| **sökvägen** | path | True | sträng| Azure Cosmos DB region, med blank steg mellan ord och varje ord med versaler. |
| **subscriptionId** | path | True | sträng| ID för mål prenumerationen. |
| **API-version** | DocumentDB | True | sträng | Den API-version som ska användas för den här åtgärden. |
| **restorableMongodbDatabaseRid** | DocumentDB | |sträng| Resurs-ID för Azure Cosmos DB-API för MongoDB-databasen. |

## <a name="responses"></a>Svar

| Namn | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| Åtgärden har slutförts. |
| Andra status koder | [DefaultErrorResponse](#defaulterrorresponse)| Felsvar som beskriver varför åtgärden misslyckades.|

## <a name="examples"></a>Exempel

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Exempel förfrågan**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Exempelsvar**

Status kod: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitioner

|Definition  | Description|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Ett fel svar från tjänsten. |
| [ErrorResponse](#errorresponse) | Fel svar. |
| [Åtgärdstyp](#operationtype) | Enum för att ange händelsens åtgärds typ. |
| [Resurs](#resource) | Resurs för en Azure Cosmos DB-API för samlings händelse för MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Ett Azure Cosmos DB-API för händelse av MongoDB-samling |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | Egenskaperna för ett Azure Cosmos DB-API för händelse av MongoDB-samling |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | Svar på list åtgärden som innehåller samlings händelser och deras egenskaper. |

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
| Skapa |sträng|Skapa samlings händelse|
| Ta bort |sträng|borttagnings händelse för samling|
| Ersätt |sträng|samlings ändrings händelse|

### <a name="resource"></a><a id="resource"></a>Resurs

Resurs för en Azure Cosmos DB MongoDB Collection-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| _rid |sträng| En systemgenererad egenskap. En unik identifierare. |
| eventTimestamp |sträng| Tiden då den här samlings händelsen skedde. |
| operationType |[Åtgärdstyp](#operationtype)|  Åtgärds typen för den här samlings händelsen. |
| ownerId |sträng| Namnet på MongoDB-samlingen.|
| ownerResourceId |sträng| Resurs-ID för MongoDB-samlingen. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

En Azure Cosmos DB samlings händelse för MongoDB

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| ID |sträng| Det unika resurs-ID: n för den Azure Resource Manager resursen. |
| name |sträng| Namnet på Resource Manager-resursen. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| Egenskaperna för en samlings händelse. |
| typ |sträng| Typ av Azure-resurs. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Egenskaperna för en Azure Cosmos DB MongoDB Collection-händelse

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| resource | [Resurs](#resource)| Resurs för en Azure Cosmos DB-API för samlings händelse för MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

Svar på list åtgärden som innehåller samlings händelser och deras egenskaper.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| värde |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Lista över Azure Cosmos DB-API för MongoDB samlings händelser och deras egenskaper. |

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över återställas-databaser](restorable-mongodb-databases-list.md)  i Azure Cosmos DB API för MongoDB med hjälp av REST API.
* [Resurs modell](continuous-backup-restore-resource-model.md) för kontinuerligt säkerhets kopierings läge.