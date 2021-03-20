---
title: 'Pausa, återuppta och skala med REST API: er för dedikerad SQL-pool (tidigare SQL DW)'
description: 'Hantera beräknings kraften för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics via REST-API: er.'
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375909"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>REST API: er för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

REST API: er för att hantera data bearbetning för dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.

## <a name="scale-compute"></a>Skala beräkning

Om du vill ändra informations lager enheter använder du REST API [skapa eller uppdatera databas](/rest/api/sql/databases/createorupdate) . I följande exempel anges data lager enheter till DW1000 för databasen MySQLDW, som finns på Server-serverns Server. Servern finns i en Azure-resurs grupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>Pausa beräkning

Om du vill pausa en databas använder du [pausa databas](/rest/api/sql/databases/pause) REST API. I följande exempel pausas en databas med namnet Database02 som finns på en server med namnet Server01. Servern finns i en Azure-resurs grupp med namnet ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Återuppta beräkning

Om du vill starta en databas använder du databasen för att [återuppta](/rest/api/sql/databases/resume) REST API. I följande exempel startas en databas med namnet Database02 som finns på en server med namnet Server01. Servern finns i en Azure-resurs grupp med namnet ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Kontrol lera databas tillstånd

> [!NOTE]
> Kontrol lera att databas statusen för närvarande är ONLINE när databasen är klar med arbets flödet online, vilket leder till anslutnings fel. Du kan behöva lägga till en fördröjning på 2 till 3 minuter i program koden om du använder det här API-anropet för att utlösa anslutnings försök.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Hämta underhålls schema

Kontrol lera det underhålls schema som har angetts för en dedikerad SQL-pool (tidigare SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Ange underhålls schema

Ange och uppdatera ett underhålls schema för en befintlig dedikerad SQL-pool (tidigare SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Hantera beräkning](sql-data-warehouse-manage-compute-overview.md).
