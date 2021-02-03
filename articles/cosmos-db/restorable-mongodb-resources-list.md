---
title: Visa en lista över återställas-resurser i Azure Cosmos DB API för MongoDB med hjälp av REST API
description: Returnera en lista över databas-och samlings kombination som finns på kontot vid den aktuella tidsstämpeln och platsen. Detta hjälper i scenarier att verifiera vilka resurser som finns på den aktuella tidsstämpeln och platsen.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dc90bfb6325831276b3c6171b73aebfa2877cf68
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527923"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Visa en lista över återställas-resurser i Azure Cosmos DB API för MongoDB med hjälp av REST API

Returnera en lista över databas-och samlings kombination som finns på kontot vid den aktuella tidsstämpeln och platsen. Detta hjälper i scenarier att verifiera vilka resurser som finns på den aktuella tidsstämpeln och platsen. Detta API kräver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörighet.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

Med valfria parametrar:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI-parametrar

| Name | I | Krävs | Typ | Description |
| --- | --- | --- | --- | --- |
| **Instans** | path | True |sträng| InstanceId-GUID för ett återställas-databas konto. |
| **sökvägen** | path | True | sträng| Azure Cosmos DB region, med blank steg mellan ord och varje ord med versaler. |
| **subscriptionId** | path | True | sträng| ID för mål prenumerationen. |
| **API-version** | DocumentDB | True | sträng | Den API-version som ska användas för den här åtgärden. |
| **restoreLocation** | DocumentDB | |sträng| Den plats där återställas-resurserna finns. |
| **restoreTimestampInUtc** | DocumentDB | |sträng| Tidsstämpeln när återställas-resurserna fanns. |

## <a name="responses"></a>Svar

| Namn | Typ | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| Åtgärden har slutförts. |
| Andra status koder | [DefaultErrorResponse](#defaulterrorresponse)| Felsvar som beskriver varför åtgärden misslyckades. |


## <a name="examples"></a>Exempel

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Exempel förfrågan**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Exempelsvar**

Status kod: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definitioner

| Definition | Beskrivning | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Vissa databaser som ska återställas. | | [DefaultErrorResponse](#defaulterrorresponse) | Ett fel svar från tjänsten. | | [ErrorResponse](#errorresponse) | Fel svar. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | Svar på list åtgärden som innehåller återställas-SQL-resurser. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Vissa databaser som ska återställas.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| collectionNames |sträng []| Namnen på samlingarna som är tillgängliga för återställning. |
| Databas |sträng| Namnet på databasen som är tillgänglig för återställning. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

Svar på list åtgärden som innehåller återställas-Azure Cosmos DB-API: t för MongoDB-resurser.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| värde |[DatabaseRestoreResource](#databaserestoreresource)[]| Lista över återställas Azure Cosmos DB API för MongoDB-resurser, inklusive databas-och samlings namn. |

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över återställas-databaser](restorable-mongodb-databases-list.md)  i Azure Cosmos DB API för MongoDB med hjälp av REST API.
* [Resurs modell](continuous-backup-restore-resource-model.md) för kontinuerligt säkerhets kopierings läge.