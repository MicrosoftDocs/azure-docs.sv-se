---
title: Visa lista återställas Database Accounts using Azure Cosmos DB REST API
description: Visar en lista över alla återställas Azure Cosmos DB databas konton som är tillgängliga under en prenumeration.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 442f7e3abdf065377c78f71f003733ee295b312a
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527897"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Visa lista återställas Database Accounts using Azure Cosmos DB REST API

Visar en lista över alla återställas Azure Cosmos DB databas konton som är tillgängliga under prenumerationen. Det här anropet kräver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` behörighet.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-parametrar

| Name | I | Krävs | Typ | Description |
| --- | --- | --- | --- | --- |
| **subscriptionId** | path | True | sträng| ID för mål prenumerationen. |
| **API-version** | DocumentDB | True | sträng | Den API-version som ska användas för den här åtgärden. |

## <a name="responses"></a>Svar

| Namn | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| Åtgärden har slutförts. |
| Andra status koder | [DefaultErrorResponse](#defaulterrorresponse)| Felsvar som beskriver varför åtgärden misslyckades. |

## <a name="examples"></a>Exempel

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Exempel förfrågan**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Exempelsvar**

Status kod: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definitioner

|Definition  | Description|
| --- | --- |
| [ApiType](#apitype) | Enum för att ange API-typen för återställas-databas kontot. |
| [DefaultErrorResponse](#defaulterrorresponse) | Ett fel svar från tjänsten. |
| [ErrorResponse](#errorresponse) | Fel svar. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Ett Azure Cosmos DB återställas Database-konto. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | Svar på list åtgärden som innehåller databas kontona för återställas och deras egenskaper. |
| [RestorableLocationResource](#restorablelocationresource) | Egenskaper för det regionala återställas-kontot. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enum för att ange API-typen för återställas-databas kontot.

| **Namn** | **Typ** |
| --- | --- |
| Cassandra |sträng|
| Gremlin |sträng|
| GremlinV2 |sträng|
| MongoDB |sträng|
| SQL |sträng|
| Tabell |sträng|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Ett Azure Cosmos DB återställas Database-konto.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| ID |sträng| Det unika resurs-ID: n för den Azure Resource Manager resursen. |
| location |sträng| Platsen för resurs gruppen som resursen tillhör. |
| name |sträng| Namnet på Resource Manager-resursen. |
| egenskaper. accountName |sträng| Namnet på det globala databas kontot |
| egenskaper. apiType |[ApiType](#apitype)| API-typen för återställas Database-kontot. |
| egenskaper. creationTime |sträng| Skapande tiden för återställas-databas kontot (ISO-8601-format). |
| egenskaper. deletionTime |sträng| Tiden då återställas-databasens konto har tagits bort (ISO-8601-format). |
| egenskaper. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Lista över regioner som databas kontot kan återställas från. |
| typ |sträng| Typ av Azure-resurs. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

Svar på list åtgärden som innehåller databas kontona för återställas och deras egenskaper.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| värde |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Lista över återställas Database-konton och deras egenskaper. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Egenskaper för det regionala återställas-kontot.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| creationTime |sträng| Skapande tiden för det regionala återställas Database-kontot (ISO-8601-format). |
| deletionTime |sträng| Tiden då det regionala återställas har tagits bort (ISO-8601-format). |
| locationName |sträng| Platsen för det regionala återställas-kontot. |
| regionalDatabaseAccountInstanceId |sträng| Instans-ID för det regionala återställas-kontot. |

## <a name="next-steps"></a>Nästa steg

* [Återställas Database-konton – lista efter plats.](restorable-database-accounts-list-by-location.md)
* [Resurs modell](continuous-backup-restore-resource-model.md) för kontinuerligt säkerhets kopierings läge.