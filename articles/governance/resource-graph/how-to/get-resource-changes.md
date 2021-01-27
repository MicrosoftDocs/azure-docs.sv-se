---
title: Hämta resursändringar
description: Lär dig hur du hittar när en resurs har ändrats, hämta en lista över de egenskaper som har ändrats och utvärdera differenserna.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: 58dcb7256b0876d5e7fa9d7569db102538f92bab
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917430"
---
# <a name="get-resource-changes"></a>Hämta resursändringar

Resurser ändras genom daglig användning, omkonfiguration och till och med omdistribution.
Ändringen kan komma från en person eller en automatiserad process. De flesta ändringar är av design, men ibland är det inte. Med de senaste 14 dagarna i ändrings historiken kan du med Azure Resource Graph:

- Hitta när ändringar upptäcktes i en Azure Resource Manager-egenskap
- Information om varje resurs ändring finns i egenskaper för egenskaps ändring
- Se en fullständig jämförelse av resursen före och efter den identifierade ändringen

Ändrings identifiering och information är värdefull i följande exempel scenarier:

- Under incident hantering kan du förstå _potentiellt_ relaterade ändringar. Fråga efter ändrings händelser under en angiven tids period och utvärdera ändrings informationen.
- Upprätthålla en databas för konfigurations hantering, som kallas en CMDB, uppdaterad. I stället för att uppdatera alla resurser och deras fullständiga egenskaps uppsättningar enligt en schemalagd frekvens får du bara det som ändrades.
- Förstå vilka andra egenskaper som kan ha ändrats när en resurs ändrade kompatibilitetstillstånd. Utvärdering av dessa ytterligare egenskaper kan ge insikter om andra egenskaper som kan behöva hanteras via en Azure Policy-definition.

Den här artikeln visar hur du samlar in den här informationen via resurs Diagramets SDK. Information om hur du kan se den här informationen i Azure Portal finns i Azure Policys [ändrings historik](../../policy/how-to/determine-non-compliance.md#change-history) eller [ändrings historik](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)för Azure aktivitets loggen. Mer information om ändringar i dina program från infrastruktur skiktet hela vägen till program distribution finns i [använda program ändrings analys (för hands version)](../../../azure-monitor/app/change-analysis.md) i Azure Monitor.

> [!NOTE]
> Ändrings informationen i resurs diagrammet är för Resource Manager-egenskaper. Information om hur du spårar ändringar i en virtuell dator finns i Azure Automation [ändrings spårning](../../../automation/change-tracking/overview.md) eller Azure policys [gäst konfiguration för virtuella datorer](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Ändrings historiken i Azure Resource Graph finns i en offentlig för hands version.

## <a name="find-detected-change-events-and-view-change-details"></a>Hitta identifierade ändrings händelser och Visa ändrings information

Det första steget i att se vad som har ändrats på en resurs är att hitta ändrings händelserna som är relaterade till resursen inom ett tidsintervall. Varje ändrings händelse innehåller också information om vad som har ändrats i resursen. Det här steget görs genom **resourceChanges** REST-slutpunkten.

**ResourceChanges** -slutpunkten accepterar följande parametrar i begär ande texten:

- **resourceId** \[ krävs \] : Azure-resursen för att leta efter ändringar på.
- **intervall** \[ krävs \] : en egenskap med _Start_ -  och slutdatum för när du ska söka efter en ändrings händelse med **Zulu Time Zone (Z)**.
- **fetchPropertyChanges** (valfritt): en boolesk egenskap som anger om Response-objektet innehåller egenskaps ändringar.

Exempel på begär ande text:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Med ovanstående begär ande text är REST API-URI: n för **resourceChanges** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Svaret ser ut ungefär som i det här exemplet:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Varje identifierad ändrings händelse för **resourceId** har följande egenskaper:

- **changeId** – det här värdet är unikt för den resursen. **ChangeId** -strängen kan ibland innehålla andra egenskaper, men den garanterar bara att vara unik.
- **beforeSnapshot** – innehåller **snapshotId** och **tidsstämpel** för den resurs ögonblicks bild som togs innan en ändring upptäcktes.
- **afterSnapshot** – innehåller **snapshotId** och **tidsstämpel** för den resurs ögonblicks bild som togs efter en ändring upptäcktes.
- **ändrings typs** – beskriver vilken typ av ändring som har identifierats för hela ändrings posten mellan **beforeSnapshot** och **afterSnapshot**. Värdena är: _skapa_, _Uppdatera_ och _ta bort_. **PropertyChanges** egenskaps mat ris ingår endast när **ändrings typs** är _Update_.
- **propertyChanges** – denna egenskaps mat ris innehåller information om alla resurs egenskaper som har uppdaterats mellan **beforeSnapshot** och **afterSnapshot**:
  - **PropertyName** -namnet på den resurs egenskap som har ändrats.
  - **changeCategory** – beskriver vad som gjorde ändringen. Värdena är: _system_ och _användare_.
  - **ändrings typs** – beskriver vilken typ av ändring som har identifierats för den enskilda resurs egenskapen.
    Värden är: _Infoga_, _Uppdatera_ och _ta bort_.
  - **beforeValue** – värdet för resurs egenskapen i **beforeSnapshot**. Visas inte när **ändrings typs** är _insert_.
  - **afterValue** – värdet för resurs egenskapen i **afterSnapshot**. Visas inte när **ändrings typs** är _Remove_.

## <a name="compare-resource-changes"></a>Jämför resurs ändringar

Med **changeId** från **resourceChanges** -slutpunkten används **resourceChangeDetails** REST-slutpunkten för att hämta före och efter ögonblicks bilder av resursen som ändrades.

**ResourceChangeDetails** -slutpunkten kräver två parametrar i begär ande texten:

- **resourceId**: Azure-resursen för att jämföra ändringar på.
- **changeId**: den unika ändrings händelsen för **resourceId** som samlats in från **resourceChanges**.

Exempel på begär ande text:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Med ovanstående begär ande text är REST API-URI: n för **resourceChangeDetails** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Svaret ser ut ungefär som i det här exemplet:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** och **afterSnapshot** var och en anger tiden då ögonblicks bilden togs och egenskaperna vid denna tidpunkt. Ändringen skedde mellan dessa ögonblicks bilder. Titta på det föregående exemplet. vi kan se att egenskapen som ändrades var **supportsHttpsTrafficOnly**.

Jämför resultaten genom att antingen använda egenskapen **ändringar** i **resourceChanges** eller utvärdera **innehålls** delen av varje ögonblicks bild i **resourceChangeDetails** för att fastställa skillnaden. Om du jämför ögonblicks bilderna visas alltid **tidsstämpeln** som en differens trots att den förväntas.

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
- Lär dig mer om hur du [utforskar resurser](../concepts/explore-resources.md).
- Vägledning om hur du arbetar med frågor med hög frekvens finns i [rikt linjer för begränsade begär Anden](../concepts/guidance-for-throttled-requests.md).
