---
title: Visa lista återställas SQL API-resurser i Azure Cosmos DB med REST API
description: Returnera en lista över databas-och behållar kombination som finns på kontot vid den aktuella tidsstämpeln och platsen. Detta hjälper i scenarier att verifiera vilka resurser som finns på den aktuella tidsstämpeln och platsen.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: e31423ae5afd4955ebe7acf1d456496f15f14a6b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537297"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Visa lista återställas SQL API-resurser i Azure Cosmos DB med REST API

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Returnera en lista över databas-och behållar kombination som finns på kontot vid den aktuella tidsstämpeln och platsen. Detta hjälper i scenarier att verifiera vilka resurser som finns på den aktuella tidsstämpeln och platsen. Detta API kräver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` behörighet.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Med valfria parametrar:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
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
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| Åtgärden har slutförts. |
| Andra status koder | [DefaultErrorResponse](#defaulterrorresponse)| Felsvar som beskriver varför åtgärden misslyckades. |

## <a name="examples"></a>Exempel

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Exempel förfrågan**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Exempelsvar**

Status kod: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
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

| Definition | Beskrivning | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Vissa databaser som ska återställas. | | [DefaultErrorResponse](#defaulterrorresponse) | Ett fel svar från tjänsten. | | [ErrorResponse](#errorresponse) | Fel svar. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | Svar på list åtgärden som innehåller återställas-SQL-resurser. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

Svar på list åtgärden som innehåller återställas-SQL-resurser.

| **Namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| värde |[DatabaseRestoreResource](#databaserestoreresource)[]| Lista över återställas-SQL-resurser, inklusive databas-och samlings namn. |

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över återställas-databaser](restorable-sql-databases-list.md) i Azure Cosmos SQL API med REST API.
* [Resurs modell](continuous-backup-restore-resource-model.md) för kontinuerligt säkerhets kopierings läge.