---
title: Granska resursanvändningen i Azure-tjänster med REST API
description: Lär dig hur du använder Azure REST-API:er för att granska resursanvändning i Azure-tjänster. Skapa en kostnadshanteringsrapport och filter för specifika resurstyper.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: ed1d360b5e816627bf161f479addcd6e0eb776a1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058857"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Granska Azure-resursanvändning med hjälp av REST API

Azure Cost Management-API:er hjälper dig att granska och hantera förbrukningen av dina Azure-resurser.

I den här artikeln lär du dig att skapa en daglig rapport som genererar ett dokument med kommaavgränsade värden där information visas om din timvisa användning. Du lär dig även att anpassa rapporten med hjälp av filter så att du kan köra frågor mot användningen av virtuella datorer, databaser och taggade resurser i en Azure-resursgrupp.

>[!NOTE]
> Cost Management-API:et är för närvarande i privat förhandsversion.

## <a name="create-a-basic-cost-management-report"></a>Skapa en grundläggande kostnadshanteringsrapport

Använd åtgärden `reports` i Cost Management-API:et för att definiera hur kostnadsrapportering skapas samt var rapporterna ska publiceras.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Parametern `{subscriptionGuid}` är obligatorisk och ska innehålla ett prenumerations-ID som kan läsas med hjälp av de autentiseringsuppgifter som anges i API-token. 

Parametern `{reportName}` anger rapportens namn. Om du vill hämta en lista över rapportnamn kan du använda åtgärden Reports_List för att hämta en lista: `/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`. Visa exempel på utdata på [GitHub](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json).

Följande huvuden krävs:

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*| Krävs. Ange till `application/json`. |  
|*Auktorisering:*| Krävs. Ange till giltig `Bearer`-token. |

Konfigurera rapportens parametrar i HTTP-begärandetexten. I exemplet nedan är rapporten inställd på att genereras varje dag när den är aktiv. En CSV-fil skrivs till en Azure Storage-blobcontainer och innehåller information om timvisa kostnader för alla resurser i en resursgrupp `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Filtypen

## <a name="filtering-reports"></a>Filtrera rapporter

I avsnitten `filter` och `dimensions` i begärandetexten vid skapande av en rapport kan du fokusera på kostnaderna för specifika resurstyper. Föregående begärandetext visar hur du filtrerar efter alla resurser i en region.

### <a name="get-all-compute-usage"></a>Hämta all beräkningsanvändning

Använd dimensionen `ResourceType` för att rapportera kostnader för virtuella Azure-datorer i din prenumeration i alla regioner.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines",
                "Microsoft.Compute/virtualMachines"
        ]
    }
}
```

### <a name="get-all-database-usage"></a>Hämta all databasanvändning

Använd dimensionen `ResourceType` för att rapportera kostnader för Azure SQL Database i din prenumeration i alla regioner.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ]
    }
}
```

### <a name="report-on-specific-instances"></a>Rapportera om specifika instanser

Med dimensionen `Resource` kan du rapportera kostnader för specifika resurser.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Ändra tidsramar

Ange definitionen av `timeframe` till `Custom` för att ange en tidsram utanför de inbyggda alternativen Hittills den här veckan och Hittills den här månaden.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Nästa steg
- [Komma igång med Azure REST API](/rest/api/azure/)